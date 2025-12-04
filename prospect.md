# ■ これまでに実装済みの機能

## 1. 基本動作

* MIME メールを標準出力へ生成するコマンド `mime-email`
* メール送信は行わず、外部（msmtp 等）へパイプする形式を前提とする
* ヘッダは RFC 5322 の基本構造に従って出力

## 2. 必須オプション

* `--from <メールアドレス>`
* `--to <メールアドレス>`（複数指定可）

## 3. 任意オプション（基本ヘッダ）

* `--subject <subject>`
* `--cc <メールアドレス>`（複数指定可）
* `--bcc <メールアドレス>`（複数指定可、ヘッダにも出力）
* `--date <日付文字列>`（指定がなければ現在時刻を自動生成）

## 4. 本文入力

* 本文ファイルを指定：`mime-email --from ... --to ... body.txt`
* `-` を指定した場合は標準入力を本文とする
* **本文ファイル未指定の場合も標準入力を使用**
* `--` の特別処理

  * `--` 単独 → その後何も指定しなければ stdin
  * `-- file` → file を本文として扱う
  * `-- file1 file2` → エラー

## 5. 添付ファイル

* `--attach <filename>`（複数指定可）
* 添付がある場合は自動的に `multipart/mixed` を生成
* 本文を text/plain のパートとして挿入
* 添付ファイルは base64 でエンコード
* `file --mime-type` を使って Content-Type を推定
  （無ければ `application/octet-stream`）

## 6. エラーメッセージ統一形式

* 全エラーを次の形式で出力

  ```
  Error(<LINENO>)[mime-email]: <詳細>
  ```
* 未知オプション（例：`--foo`）は的確に検出し報告
* 添付ファイル不存在や本文ファイル不存在も同形式で報告

## 7. 複数アドレス対応

* `--to` `--cc` `--bcc` は複数指定可能
* 出力は

  ```
  To: addr1, addr2
  Cc: cc1, cc2
  Bcc: bc1, bc2
  ```

  のように 1 行にまとめて表記

---

# ■ 今後実装したい / 実装候補の機能

## 1. 表示名（display name）対応

* `"山田 太郎" <yamada@example.com>` のような形式を自動生成
* オプション例：

  * `--display-from "山田 太郎"`
  * `--display-to "担当者 様"`
* RFC 2047 エンコードと併せて実装するのが望ましい
* 現状でもユーザが `--from '"Taro"<yamada@example.com>'` のように指定することで動作する。

## 2. RFC 2047（日本語 Subject / 名前のエンコード）

* 日本語 Subject を自動的に

  ```
  =?utf-8?B?xxxxx?=
  ```

  の形式にエンコードする
* display-name とセットで扱う必要がある

## 3. 添付ファイル名の日本語対応（RFC 2231）

* 以下のような出力を自動生成

  ```
  filename*=utf-8''%E5%90%8D%E5%89%8D.xlsx
  ```
* 日本語ファイル名を正しく送れるようにする

## 4. quoted-printable / 7bit / base64 本文エンコード

* 現状は `8bit` のみ
* メールサーバの互換性向上のために

  * `quoted-printable`
  * `base64`
    を選択できるようにする案

## 5. 任意ヘッダの追加

* `--misc "X-Header: Some-Value"`
  のような追加ヘッダ
* 複数指定対応

## 6. 自動 boundary 生成の簡易性向上

* 互換性をより高めるため、ランダム性や prefix の整備

## 7. Content-Type の手動指定

* 添付ごとに MIME タイプを強制指定できる `--attach-type` のようなオプション

## 8. ロギング強化 / verbose

* verbose モードを追加

  * MIME 構造
  * 添付の MIME type
  * boundary
    などを stderr に表示する

* 必要ならteeすれば良いのではという気持ちである
