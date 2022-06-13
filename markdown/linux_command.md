<!--
title: 【Linux】基本コマンド一覧
tags: Linux,command,console
-->

# 初めに

コンソールでよく使うコマンドについて自分用の忘備録として記事にしておきます。

# コマンド

## cat

ファイルの内容を単純に出力する。

```console
cat file1 file2 ...
```

## ls

ディレクトリの内容を一覧表示する。

```console
ls -l
```

## cp

file1 を file2 へコピーする。

dir という名前のディレクトリへファイルをコピーする。

```console
cp file2 file2(dir)
```

## mv

ファイルを別のディレクトリに移動させる。

ファイルの名前の変更する。

```console
mv file1 file2(dir)
```

## touch

ファイルを作成する。

既に存在する場合は、そのファイルの更新タイムスタンプを更新する。

```console
touch file
```

## rm

ファイルを削除する。

```console
rm file
```

## echo

引数をそのまま標準出力に表示する。

```console
$ echo Hello again.
Hello again.
```

## cd

シェルのカレント・ワーキング・ディレクトリを変更する。
dir を省略するとホームディレクトリに戻ります。

```console
cd dir
```

## mkdir

新たなディレクトリ dir を作成する。

```console
mkdir dir
```

## rmdir

ディレクトリ dir を削除する。

```console
rmdir dir
```
