<!--
# Missing macro fragment specifiers
-->

# 未使用のマクロフラグメント指定子

<!--
## Summary
-->

## 概要

<!--
- The [`missing_fragment_specifier`] lint is now a hard error.
-->

- [`missing_fragment_specifier`] リントがエラー扱いされるようになります。

<!--
[`missing_fragment_specifier`]: ../../rustc/lints/listing/deny-by-default.html#missing-fragment-specifier
-->

[`missing_fragment_specifier`]: https://doc.rust-lang.org/rustc/lints/listing/deny-by-default.html#missing-fragment-specifier

<!--
## Details
-->

## 詳細

<!--
The [`missing_fragment_specifier`] lint detects a situation when an **unused** pattern in a `macro_rules!` macro definition has a meta-variable (e.g. `$e`) that is not followed by a fragment specifier (e.g. `:expr`). This is now a hard error in the 2024 Edition.
-->

[`missing_fragment_specifier`] リントは、マクロ定義 (`macro_rules!`) 内の**未使用の**パターン中の（`$e` のような）メタ変数であって、（`:expr` といった）フラグメント指定子のないものを検出します。
2024 エディションからはこのリントはエラー扱いになります。

```rust,compile_fail
macro_rules! foo {
   () => {};
   ($name) => { }; // ERROR: missing fragment specifier
                   // エラー: フラグメント指定子がありません
}

fn main() {
   foo!();
}
```

<!--
Calling the macro with arguments that would match a rule with a missing specifier (e.g., `foo!($name)`) is a hard error in all editions. However, simply defining a macro with missing fragment specifiers is not, though we did add a lint in Rust 1.17.
-->

`foo!($name)` のように、フラグメント指定子のないメタ変数を含むルールにマッチするマクロ呼び出しは、全エディションでエラーです。
一方、そのようなマクロを定義すること自体はエラーではありません（これを検出するリントは 1.17 から追加されています）。

<!--
We'd like to make this a hard error in all editions, but there would be too much breakage right now. So we're starting by making this a hard error in Rust 2024.[^future-incompat]
-->

本当ならこのリントを全エディションでエラー扱いしたいところですが、あまりにも影響が大きすぎるため、ひとまず Rust 2024 以降でだけエラー扱いすることになりました。[^future-incompat]

<!--
[^future-incompat]: The lint is marked as a "future-incompatible" warning to indicate that it may become a hard error in all editions in a future release. See [#40107] for more information.
-->

[^future-incompat]: このリントは "future-incompatible" な（将来的に非互換になる）警告に分類されており、将来のリリースでは全エディションでエラー扱いになる予定であるとされています。
詳細は [#40107] をご参照ください。

[#40107]: https://github.com/rust-lang/rust/issues/40107

<!--
## Migration
-->

## 移行

<!--
To migrate your code to the 2024 Edition, remove the unused matcher rule from the macro. The [`missing_fragment_specifier`] lint is on by default in all editions, and should alert you to macros with this issue.
-->

コードを 2024 エディションに移行するには、上記のような未使用のルールを削除してください。
[`missing_fragment_specifier`] リントは全エディションで有効化されており、このようなマクロに対して警告が出ます。

<!--
There is no automatic migration for this change. We expect that this style of macro is extremely rare. The lint has been a future-incompatibility lint since Rust 1.17, a deny-by-default lint since Rust 1.20, and since Rust 1.82, it has warned about dependencies that are using this pattern.
-->

これに対する自動移行は提供されていません。
このようなマクロはまずないと思われます。
このリントは Rust 1.17 から future-incompatible なリントになり、Rust 1.20 からデフォルトで必ずエラーになり、1.82 からは依存ライブラリ中であっても警告が出るようになっています。
