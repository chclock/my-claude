---
description: Fix Dart analyzer errors and Flutter build failures incrementally. Invokes the dart-build-resolver agent for minimal, surgical fixes.
---

# Flutter 构建与修复

此命令调用 **dart-build-resolver** 代理，使用最小更改逐步修复 Dart/Flutter 构建错误。

## 此命令的作用

1. **运行诊断**：执行 `flutter analyze`、`flutter pub get`
2. **解析错误**：按文件分组并按严重程度排序
3. **逐步修复**：一次一个错误
4. **验证每个修复**：每次更改后重新运行分析
5. **报告总结**：显示已修复的内容和剩余内容

## 使用场景

在以下情况下使用 `/flutter-build`：
- `flutter analyze` 报告错误
- `flutter build` 任何平台失败
- `dart pub get` / `flutter pub get` 因版本冲突失败
- `build_runner` 生成代码失败
- 拉取破坏构建的更改后

## 运行的诊断命令

```bash
# 分析
flutter analyze 2>&1

# 依赖
flutter pub get 2>&1

# 代码生成（如果项目使用 build_runner）
dart run build_runner build --delete-conflicting-outputs 2>&1

# 平台构建
flutter build apk 2>&1
flutter build web 2>&1
```

## 示例会话

````text
用户：/flutter-build

代理：
# Flutter 构建解决方案

## 初始诊断

```
$ flutter analyze
Analyzing project...

  error • lib/features/user/data/user_repository_impl.dart:34:16 •
    A value of type 'String?' can't be assigned to a variable of type 'String'. •
    invalid_assignment

  error • lib/features/cart/presentation/cart_page.dart:58:5 •
    The method 'add' isn't defined for the type 'List<Item>'.
    Try correcting the name to the name of an existing method, or defining a method named 'add'. •
    undefined_method

2 errors found.
```

## 修复 1：空安全

文件：lib/features/user/data/user_repository_impl.dart:34
错误：A value of type 'String?' can't be assigned to type 'String'

更改：
```dart
final id = response.id;
```
为：
```dart
final id = response.id ?? '';
```

```
$ flutter analyze
1 error found.
```

## 修复 2：不可变列表

文件：lib/features/cart/presentation/cart_page.dart:58
错误：The method 'add' isn't defined for the type 'List<Item>'
原因：State 持有一个不可修改的列表；变更通过 Cubit 进行

更改：
```dart
state.items.add(item);
```
为：
```dart
context.read<CartCubit>().addItem(item);
// 注意：Cubit 暴露命名方法（addItem、removeItem）；
// .add(event) 是 BLoC 事件 API——不要混淆它们。
```

```
$ flutter analyze
No issues found!
```

## 最终验证

```
$ flutter test
All tests passed.
```

## 摘要

| 指标 | 数量 |
|--------|-------|
| 已修复的分析错误 | 2 |
| 修改的文件 | 2 |
| 剩余问题 | 0 |

构建状态：通过 ✓
````

## 常见错误修复

| 错误 | 典型修复 |
|-------|-------------|
| `A value of type 'X?' can't be assigned to 'X'` | 添加 `?? default` 或空保护 |
| `The name 'X' isn't defined` | 添加导入或修复拼写错误 |
| `Non-nullable instance field must be initialized` | 添加初始化或 `late` |
| `Version solving failed` | 调整 pubspec.yaml 中的版本约束 |
| `Missing concrete implementation of 'X'` | 实现缺失的接口方法 |
| `build_runner: Part of X expected` | 删除过时的 `.g.dart` 并重建 |

## 修复策略

1. **首先处理分析错误** — 代码必须无错误
2. **其次处理警告分类** — 修复可能导致运行时 bug 的警告
3. **第三处理 pub 冲突** — 修复依赖解析
4. **一次一个修复** — 验证每个更改
5. **最小更改** — 不重构，只修复

## 停止条件

如果出现以下情况，代理将停止并报告：
- 同一错误在 3 次尝试后仍然存在
- 修复引入了更多错误
- 需要架构更改
- 包升级冲突需要用户决定

## 相关命令

- `/flutter-test` — 构建成功后运行测试
- `/flutter-review` — 审查代码质量
- `/verify` — 完整验证循环

## 相关

- 代理：`agents/dart-build-resolver.md`
- 技能：`skills/flutter-dart-code-review/`