---
title: "PyO3をつかってみる＜PythonとRustの連携＞"
emoji: "🐍"
type: "tech"
topics:
  - "Python"
  - "Rust"
published: true
published_at: "2025-02-22 21:42"
---

# 概要

こんにちは！

今回は [PyO3](https://github.com/PyO3/pyo3) についてお話しします。このライブラリは、**Rust** と **Python** をバインディングするための **Rust ライブラリ** です。

つまり、**Rust で Python ライブラリを作成できる** のです！！

私の意見としては、Rust を使いたい理由はその**速さ**です。  
しかし、**Python も使いたい**という気持ちがあり、その問題を **PyO3** が解決してくれます。

一緒に PyO3 をチェックしてみましょう！

---

# 誰に向けた記事か？

- Python ユーザー
- Rust ユーザー
- **Python の処理速度を上げたい**と考えている人

---

# PyO3 とは？

これは、**Rust で Python をバインディングするためのライブラリ** です。  
公式リポジトリはこちら → [https://github.com/PyO3/pyo3](https://github.com/PyO3/pyo3)

---

# どうやって使うの？

**PyO3** を使うには、私は **maturin** の使用をおすすめします。  
[maturin-cli](https://github.com/PyO3/maturin) を使うと、**PyO3** を利用したツールの開発が簡単に始められます。

## インストール方法

```bash
pip install maturin
```

## 新しいプロジェクトを作成する

今回は、**フィボナッチ数列** を生成する新しいプロジェクトを作成してみます。

```bash
maturin new fib_rs
```

プロジェクトを作成すると、以下のデフォルトコードが生成されます。

```rust
use pyo3::prelude::*;

/// 2つの数値の合計を文字列として返す関数
#[pyfunction]
fn sum_as_string(a: usize, b: usize) -> PyResult<String> {
    Ok((a + b).to_string())
}

/// Rust で実装された Python モジュール
#[pymodule]
fn fib_rs(_py: Python, m: &PyModule) -> PyResult<()> {
    m.add_function(wrap_pyfunction!(sum_as_string, m)?)?;
    Ok(())
}
```

次に、**フィボナッチ数列** の関数を作成しましょう。

```rust
use pyo3::prelude::*;

fn fib_result(n: usize) -> usize {
    if n < 2 {
        return  n;
    } else {
        return  fib_result(n-1) + fib_result(n-2);
    }
}

#[pyfunction]
fn fib(n: usize) -> PyResult<usize> {
    Ok(fib_result(n)) 
}

#[pymodule]
fn fib_rs(_py: Python, m: &PyModule) -> PyResult<()> {
    m.add_function(wrap_pyfunction!(fib, m)?)?;
    Ok(())
}
```

---

## プログラムの実行方法

### 1. 仮想環境（venv）の作成

まず、仮想環境を作成しましょう。

```bash
python -m venv .venv
```

### 2. 仮想環境を有効化する

```bash
export VIRTUAL_ENV=.venv
```

### 3. maturin-cli を実行

```bash
maturin develop
🔗 Found pyo3 bindings
--- SKIP ---
🛠  Installed fib_rs-0.1.0
```

これで、**fib_rs ライブラリ** が仮想環境にインストールされました。

---

## 動作確認

仮想環境を有効化します。

```bash
source .venv/bin/activate
```

Python シェルを起動しましょう。

```bash
python
```

以下のコードを実行して、ライブラリが正しく動作するか確認します。

```python
>>> import fib_rs
>>> fib_rs.fib(10)
55
```

うまく動作しました！ 🚀
