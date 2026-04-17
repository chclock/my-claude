# Django REST API — 项目 CLAUDE.md

> 这是一个使用 Django REST Framework、PostgreSQL 和 Celery 的真实项目示例。
> 将其复制到项目根目录并根据你的服务进行定制。

## 项目概述

**技术栈：** Python 3.12+、Django 5.x、Django REST Framework、PostgreSQL、Celery + Redis、pytest、Docker Compose

**架构：** 采用领域驱动设计，每个业务领域一个应用。DRF 作为 API 层，Celery 处理异步任务，pytest 用于测试。所有端点返回 JSON——不进行模板渲染。

## 关键规则

### Python 规范

- 所有函数签名都要有类型提示——使用 `from __future__ import annotations`
- 不使用 `print()` 语句——使用 `logging.getLogger(__name__)`
- 使用 f-string 进行字符串格式化，绝不用 `%` 或 `.format()`
- 文件操作使用 `pathlib.Path` 而非 `os.path`
- 使用 isort 排序导入：标准库、第三方、本地（由 ruff 强制执行）

### 数据库

- 所有查询都使用 Django ORM——仅使用 `.raw()` 和参数化查询
- 迁移提交到 git——生产环境不要使用 `--fake`
- 使用 `select_related()` 和 `prefetch_related()` 防止 N+1 查询
- 所有模型必须有 `created_at` 和 `updated_at` 自动字段
- 在 `filter()`、`order_by()` 或 `WHERE` 子句中使用的任何字段都要建立索引

```python
# 错误：N+1 查询
orders = Order.objects.all()
for order in orders:
    print(order.customer.name)  # 每个订单都会访问数据库

# 正确：使用 join 的单一查询
orders = Order.objects.select_related("customer").all()
```

### 认证

- 通过 `djangorestframework-simplejwt` 实现 JWT——访问令牌（15 分钟）+ 刷新令牌（7 天）
- 每个视图都要有权限类——不要依赖默认设置
- 以 `IsAuthenticated` 为基础，添加自定义权限实现对象级访问
- 启用令牌黑名单以支持登出

### 序列化器

- 简单 CRUD 使用 `ModelSerializer`，复杂验证使用 `Serializer`
- 当输入/输出形状不同时，使用分离的读写序列化器
- 在序列化器层进行验证，而不是在视图中——视图应该保持精简

```python
class CreateOrderSerializer(serializers.Serializer):
    product_id = serializers.UUIDField()
    quantity = serializers.IntegerField(min_value=1, max_value=100)

    def validate_product_id(self, value):
        if not Product.objects.filter(id=value, active=True).exists():
            raise serializers.ValidationError("产品未找到或已下架")
        return value

class OrderDetailSerializer(serializers.ModelSerializer):
    customer = CustomerSerializer(read_only=True)
    product = ProductSerializer(read_only=True)

    class Meta:
        model = Order
        fields = ["id", "customer", "product", "quantity", "total", "status", "created_at"]
```

### 错误处理

- 使用 DRF 异常处理器以获得一致的错误响应
- 在 `core/exceptions.py` 中为业务逻辑定义自定义异常
- 永远不要向客户端暴露内部错误详情

```python
# core/exceptions.py
from rest_framework.exceptions import APIException

class InsufficientStockError(APIException):
    status_code = 409
    default_detail = "库存不足，无法完成此订单"
    default_code = "insufficient_stock"
```

### 代码风格

- 代码或注释中不使用表情符号
- 最大行长度：120 个字符（由 ruff 强制执行）
- 类名：PascalCase，函数/变量：snake_case，常量：UPPER_SNAKE_CASE
- 视图保持精简——业务逻辑放在服务函数或模型方法中

## 文件结构

```
config/
  settings/
    base.py              # 共享设置
    local.py             # 开发环境覆盖（DEBUG=True）
    production.py        # 生产环境设置
  urls.py                # 根 URL 配置
  celery.py              # Celery 应用配置
apps/
  accounts/              # 用户认证、注册、个人资料
    models.py
    serializers.py
    views.py
    services.py          # 业务逻辑
    tests/
      test_views.py
      test_services.py
      factories.py       # Factory Boy 工厂类
  orders/                # 订单管理
    models.py
    serializers.py
    views.py
    services.py
    tasks.py             # Celery 任务
    tests/
  products/              # 产品目录
    models.py
    serializers.py
    views.py
    tests/
core/
  exceptions.py          # 自定义 API 异常
  permissions.py         # 共享权限类
  pagination.py          # 自定义分页
  middleware.py          # 请求日志、计时
  tests/
```

## 关键模式

### 服务层

```python
# apps/orders/services.py
from django.db import transaction

def create_order(*, customer, product_id: uuid.UUID, quantity: int) -> Order:
    """创建订单，包含库存验证和付款保留。"""
    product = Product.objects.select_for_update().get(id=product_id)

    if product.stock < quantity:
        raise InsufficientStockError()

    with transaction.atomic():
        order = Order.objects.create(
            customer=customer,
            product=product,
            quantity=quantity,
            total=product.price * quantity,
        )
        product.stock -= quantity
        product.save(update_fields=["stock", "updated_at"])

    # 异步：发送确认邮件
    send_order_confirmation.delay(order.id)
    return order
```

### 视图模式

```python
# apps/orders/views.py
class OrderViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthenticated]
    pagination_class = StandardPagination

    def get_serializer_class(self):
        if self.action == "create":
            return CreateOrderSerializer
        return OrderDetailSerializer

    def get_queryset(self):
        return (
            Order.objects
            .filter(customer=self.request.user)
            .select_related("product", "customer")
            .order_by("-created_at")
        )

    def perform_create(self, serializer):
        order = create_order(
            customer=self.request.user,
            product_id=serializer.validated_data["product_id"],
            quantity=serializer.validated_data["quantity"],
        )
        serializer.instance = order
```

### 测试模式（pytest + Factory Boy）

```python
# apps/orders/tests/factories.py
import factory
from apps.accounts.tests.factories import UserFactory
from apps.products.tests.factories import ProductFactory

class OrderFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = "orders.Order"

    customer = factory.SubFactory(UserFactory)
    product = factory.SubFactory(ProductFactory, stock=100)
    quantity = 1
    total = factory.LazyAttribute(lambda o: o.product.price * o.quantity)

# apps/orders/tests/test_views.py
import pytest
from rest_framework.test import APIClient

@pytest.mark.django_db
class TestCreateOrder:
    def setup_method(self):
        self.client = APIClient()
        self.user = UserFactory()
        self.client.force_authenticate(self.user)

    def test_create_order_success(self):
        product = ProductFactory(price=29_99, stock=10)
        response = self.client.post("/api/orders/", {
            "product_id": str(product.id),
            "quantity": 2,
        })
        assert response.status_code == 201
        assert response.data["total"] == 59_98

    def test_create_order_insufficient_stock(self):
        product = ProductFactory(stock=0)
        response = self.client.post("/api/orders/", {
            "product_id": str(product.id),
            "quantity": 1,
        })
        assert response.status_code == 409

    def test_create_order_unauthenticated(self):
        self.client.force_authenticate(None)
        response = self.client.post("/api/orders/", {})
        assert response.status_code == 401
```

## 环境变量

```bash
# Django
SECRET_KEY=
DEBUG=False
ALLOWED_HOSTS=api.example.com

# 数据库
DATABASE_URL=postgres://user:pass@localhost:5432/myapp

# Redis（Celery 代理 + 缓存）
REDIS_URL=redis://localhost:6379/0

# JWT
JWT_ACCESS_TOKEN_LIFETIME=15       # 分钟
JWT_REFRESH_TOKEN_LIFETIME=10080   # 分钟（7 天）

# 邮件
EMAIL_BACKEND=django.core.mail.backends.smtp.EmailBackend
EMAIL_HOST=smtp.example.com
```

## 测试策略

```bash
# 运行所有测试
pytest --cov=apps --cov-report=term-missing

# 运行特定应用的测试
pytest apps/orders/tests/ -v

# 使用并行执行
pytest -n auto

# 仅运行上次失败的测试
pytest --lf
```

## ECC 工作流

```bash
# 规划
/plan "添加带 Stripe 集成的订单退款系统"

# 使用 TDD 开发
/tdd                    # 基于 pytest 的 TDD 工作流

# 审查
/python-review          # Python 特定代码审查
/security-scan          # Django 安全审计
/code-review            # 常规质量检查

# 验证
/verify                 # 构建、lint、测试、安全扫描
```

## Git 工作流

- `feat:` 新功能，`fix:` bug 修复，`refactor:` 代码重构
- 从 `main` 创建功能分支，需要 PR
- CI：ruff（lint + 格式化）、mypy（类型）、pytest（测试）、safety（依赖检查）
- 部署：Docker 镜像，由 Kubernetes 或 Railway 管理
