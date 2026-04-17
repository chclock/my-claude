---
description: Enforce TDD workflow for Kotlin. Write Kotest tests first, then implement. Verify 80%+ coverage with Kover.
---

# Kotlin TDD 命令

此命令使用 Kotest、MockK 和 Kover 强制执行 Kotlin 代码的测试驱动开发方法论。

## 此命令的作用

1. **定义类型/接口**：首先搭建函数签名
2. **编写 Kotest 测试**：创建全面的测试规范（红色）
3. **运行测试**：验证测试因正确的原因失败
4. **实现代码**：编写最少的代码通过（绿色）
5. **重构**：在保持测试绿色的同时改进
6. **检查覆盖率**：使用 Kover 确保 80%+ 覆盖率

## 使用场景

在以下情况下使用 `/kotlin-test`：
- 实现新的 Kotlin 函数或类
- 为现有 Kotlin 代码添加测试覆盖率
- 修复 bug（首先编写失败的测试）
- 构建关键业务逻辑
- 学习 Kotlin 中的 TDD 工作流程

## TDD 循环

```
红色     -> 编写失败的 Kotest 测试
绿色   -> 实现最少的代码通过
重构 -> 改进代码，测试保持绿色
重复  -> 下一个测试用例
```

## 示例会话

````text
用户: /kotlin-test 我需要一个函数来验证用户注册请求

Agent:
# TDD 会话: 注册验证器

## 步骤 1: 定义接口

```kotlin
// validator/RegistrationValidator.kt
package com.example.validator

data class RegistrationRequest(
    val name: String,
    val email: String,
    val password: String,
)

sealed class ValidationResult {
    data object Valid : ValidationResult()
    data class Invalid(val errors: List<String>) : ValidationResult()
}

fun validateRegistration(request: RegistrationRequest): ValidationResult {
    TODO("not implemented")
}
```

## 步骤 2: 编写 Kotest 测试（红色）

```kotlin
// validator/RegistrationValidatorTest.kt
package com.example.validator

import io.kotest.core.spec.style.FunSpec
import io.kotest.matchers.shouldBe
import io.kotest.matchers.types.shouldBeInstanceOf

class RegistrationValidatorTest : FunSpec({
    test("valid registration returns Valid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "alice@example.com",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        result.shouldBeInstanceOf<ValidationResult.Valid>()
    }

    test("blank name returns Invalid") {
        val request = RegistrationRequest(
            name = "",
            email = "alice@example.com",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Name is required")
    }

    test("invalid email returns Invalid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "not-an-email",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Invalid email format")
    }

    test("short password returns Invalid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "alice@example.com",
            password = "short",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Password must be at least 8 characters")
    }

    test("multiple errors returns all errors") {
        val request = RegistrationRequest(
            name = "",
            email = "bad",
            password = "short",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors.size shouldBe 3
    }
})
```

## 步骤 3: 运行测试 - 验证失败

```bash
$ ./gradlew test

RegistrationValidatorTest > valid registration returns Valid FAILED
  kotlin.NotImplementedError: An operation is not implemented

FAILED (5 tests, 0 passed, 5 failed)
```

✓ 测试按预期失败（NotImplementedError）。

## 步骤 4: 实现最少代码（绿色）

```kotlin
// validator/RegistrationValidator.kt
package com.example.validator

private val EMAIL_REGEX = Regex("^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$")
private const val MIN_PASSWORD_LENGTH = 8

fun validateRegistration(request: RegistrationRequest): ValidationResult {
    val errors = buildList {
        if (request.name.isBlank()) add("Name is required")
        if (!EMAIL_REGEX.matches(request.email)) add("Invalid email format")
        if (request.password.length < MIN_PASSWORD_LENGTH) add("Password must be at least $MIN_PASSWORD_LENGTH characters")
    }

    return if (errors.isEmpty()) ValidationResult.Valid
    else ValidationResult.Invalid(errors)
}
```

## 步骤 5: 运行测试 - 验证通过

```bash
$ ./gradlew test

RegistrationValidatorTest > valid registration returns Valid PASSED
RegistrationValidatorTest > blank name returns Invalid PASSED
RegistrationValidatorTest > invalid email returns Invalid PASSED
RegistrationValidatorTest > short password returns Invalid PASSED
RegistrationValidatorTest > multiple errors returns all errors PASSED

PASSED (5 tests, 5 passed, 0 failed)
```

✓ 所有测试通过！

## 步骤 6: 检查覆盖率

```bash
$ ./gradlew koverHtmlReport

Coverage: 100.0% of statements
```

✓ 覆盖率: 100%

## TDD 完成！
````

## 测试模式

### StringSpec（最简单）

```kotlin
class CalculatorTest : StringSpec({
    "add two positive numbers" {
        Calculator.add(2, 3) shouldBe 5
    }
})
```

### BehaviorSpec（BDD）

```kotlin
class OrderServiceTest : BehaviorSpec({
    Given("a valid order") {
        When("placed") {
            Then("should be confirmed") { /* ... */ }
        }
    }
})
```

### 数据驱动测试

```kotlin
class ParserTest : FunSpec({
    context("valid inputs") {
        withData("2026-01-15", "2026-12-31", "2000-01-01") { input ->
            parseDate(input).shouldNotBeNull()
        }
    }
})
```

### 协程测试

```kotlin
class AsyncServiceTest : FunSpec({
    test("concurrent fetch completes") {
        runTest {
            val result = service.fetchAll()
            result.shouldNotBeEmpty()
        }
    }
})
```

## 覆盖率命令

```bash
# 带覆盖率运行测试
./gradlew koverHtmlReport

# 验证覆盖率阈值
./gradlew koverVerify

# CI 的 XML 报告
./gradlew koverXmlReport

# 打开 HTML 报告
open build/reports/kover/html/index.html

# 运行特定测试类
./gradlew test --tests "com.example.UserServiceTest"

# 带详细输出运行
./gradlew test --info
```

## 覆盖率目标

| 代码类型 | 目标 |
|-----------|--------|
| 关键业务逻辑 | 100% |
| 公共 API | 90%+ |
| 通用代码 | 80%+ |
| 生成代码 | 排除 |

## TDD 最佳实践

**要做：**
- 先写测试，再写任何实现
- 每次更改后运行测试
- 使用 Kotest 匹配器以获得富有表现力的断言
- 对 suspend 函数使用 MockK 的 `coEvery`/`coVerify`
- 测试行为，而非实现细节
- 包含边界情况（空、null、最大值）

**不要做：**
- 在测试之前编写实现
- 跳过红色阶段
- 直接测试私有函数
- 在协程测试中使用 `Thread.sleep()`
- 忽略不稳定的测试

## 相关命令

- `/kotlin-build` - 修复构建错误
- `/kotlin-review` - 实现后审查代码
- `/verify` - 运行完整验证循环

## 相关

- 技能: `skills/kotlin-testing/`
- 技能: `skills/tdd-workflow/`
