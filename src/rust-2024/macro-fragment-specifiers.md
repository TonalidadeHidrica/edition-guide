<!--
# Macro Fragment Specifiers
-->

# マクロフラグメント指定子

<!--
## Summary
-->

## 概要

<!--
- The `expr` [fragment specifier] now also supports `const` and `_` expressions.
- The `expr_2021` fragment specifier has been added for backwards compatibility.
-->

- [フラグメント指定子] `expr` が、`const` 式と `_` 式をサポートするようになりました。
- 後方互換性のために、フラグメント指定子 `expr_2021` が追加されました。

<!--
[fragment specifier]: ../../reference/macros-by-example.html#metavariables
-->

[フラグメント指定子]: https://doc.rust-lang.org/reference/macros-by-example.html#metavariables

<!--
## Details
-->

## 詳細

<!--
As new syntax is added to Rust, existing `macro_rules` fragment specifiers are sometimes not allowed to match on the new syntax in order to retain backwards compatibility. Supporting the new syntax in the old fragment specifiers is sometimes deferred until the next edition, which provides an opportunity to update them.
-->

Rust に新しい文法を導入するとき、後方互換性のために既存の `macro_rules` フラグメント指定子を新しい記法の対象外とする場合があります。
すなわち、古いフラグメント指定子が新記法をサポートするのが、次のエディションの更新まで保留されることがあるのです。

<!--
Indeed this happened with [`const` expressions] added in 1.79 and [`_` expressions] added in 1.59. In the 2021 Edition and earlier, the `expr` fragment specifier does *not* match those expressions. This is because you may have a scenario like:
-->

1.79 で導入された [`const` 式]と 1.59 で導入された [`_` 式]では実際にそのような決定がなされました。
2021 エディション以前では、フラグメント指定子 `expr` はこれらの新記法にマッチ**しません**。
理由は以下のコードを考えるとわかります。

<!--
```rust,edition2021
macro_rules! example {
    ($e:expr) => { println!("first rule"); };
    (const $e:expr) => { println!("second rule"); };
}

fn main() {
    example!(const { 1 + 1 });
}
```
-->

```rust,edition2021
macro_rules! example {
    ($e:expr) => { println!("前者"); };
    (const $e:expr) => { println!("後者"); };
}

fn main() {
    example!(const { 1 + 1 });
}
```

<!--
Here, in the 2021 Edition, the macro will match the *second* rule. If earlier editions had changed `expr` to match the newly introduced `const` expressions, then it would match the *first* rule, which would be a breaking change.
-->

2021 エディションでは、マクロは**後者**のルールにマッチします。
2021 以前のエディションで `expr` が新文法である `const` 式にマッチしてしまうようになると、マクロは**前者**のルールにマッチするようになってしまいます。
これは破壊的変更です。

<!--
In the 2024 Edition, `expr` specifiers now also match `const` and `_` expressions. To support the old behavior, the `expr_2021` fragment specifier has been added which does *not* match the new expressions.
-->

2024 エディションから、指定子 `expr` が `const` 式と `_` 式にもマッチするようになりました。
過去の挙動もサポートするために、これらにマッチ**しない**フラグメント指定子 `expr_2021` も新たに提供されています。

<!--
[`const` expressions]: ../../reference/expressions/block-expr.html#const-blocks
[`_` expressions]: ../../reference/expressions/underscore-expr.html
-->

[`const` 式]: https://doc.rust-lang.org/reference/expressions/block-expr.html#const-blocks
[`_` 式]: https://doc.rust-lang.org/reference/expressions/underscore-expr.html

<!--
## Migration
-->

## 移行

<!--
The [`edition_2024_expr_fragment_specifier`] lint will change all uses of the `expr` specifier to `expr_2021` to ensure that the behavior of existing macros does not change. The lint is part of the `rust-2024-compatibility` lint group which is included in the automatic edition migration. In order to migrate your code to be Rust 2024 Edition compatible, run:
-->

[`edition_2024_expr_fragment_specifier`] リントで、`expr` 指定子をすべて `expr_2021` に自動書き換えして、マクロの挙動が変わらないようにできます。
このリントは、自動エディション移行に含まれる `rust-2024-compatibility` リントグループの一部です。
コードを Rust 2024 互換に移行するには、以下を実行します。

```sh
cargo fix --edition
```

<!--
In *most* cases, you will likely want to keep the `expr` specifier instead, in order to support the new expressions. You will need to review your macro to determine if there are other rules that would otherwise match with `const` or `_` and determine if there is a conflict. If you want the new behavior, just revert any changes made by the lint.
-->

**ほとんどの**場合、`expr` 指定子から変えずに新記法をサポートするのが普通でしょう。
マクロの定義を再確認して、`const` や `_` にマッチしうるようなルールの重複がないかを再確認してください。
新記法をサポートしてよければ、リントの自動修正を戻せばよいです。

<!--
Alternatively, you can manually enable the lint to find macros where you may need to update the `expr` specifier.
-->

あるいは、エディション移行ツールを使わずに手動で確認したい場合は、以下のリントをオンにしてください。

<!--
```rust
// Add this to the root of your crate to do a manual migration.
#![warn(edition_2024_expr_fragment_specifier)]
```
-->

```rust
// クレートのトップレベルに以下を追加すると手動移行できる
#![warn(edition_2024_expr_fragment_specifier)]
```

<!--
[`edition_2024_expr_fragment_specifier`]: ../../rustc/lints/listing/allowed-by-default.html#edition-2024-expr-fragment-specifier
-->

[`edition_2024_expr_fragment_specifier`]: https://doc.rust-lang.org/rustc/lints/listing/allowed-by-default.html#edition-2024-expr-fragment-specifier
