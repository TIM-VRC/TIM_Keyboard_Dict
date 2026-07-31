# TIM_Keyboard_Dict

VRChat ワールド向け日本語入力ギミック **【TIM】Keyboard** が実行時に読み込む、
かな漢字変換の辞書データです。

このリポジトリはギミック本体ではなく、**辞書ファイルの配布場所**です。

## なぜ配布しているか

辞書は 5MB あります。ワールドに同梱するとその容量がまるごとワールドに乗り、
**一度も文字を打たない来場者にまで**その負担を強いることになります。

そこで、専用 IME が初めて開かれたときにここから取得する形にしています。
ワールドは 5MB 軽くなり、使わない人の負担は 0 になります。

## ファイル

| | |
|---|---|
| `ime_dict.bin` | ベイク済み辞書（129,252 よみ / 167,005 候補・5.02MB） |
| `MOZC_LICENSE.txt` | 元データのライセンス（BSD-3-Clause） |

## URL

ギミックのセットアップウィンドウ「辞書の配布 URL」へ以下を設定してください。

```
https://raw.githubusercontent.com/tomfuren/TIM_Keyboard_Dict/main/ime_dict.bin
```

GitHub は VRChat の信頼済みドメインなので、**プレイヤー側で
「Allow Untrusted URLs」を有効にする必要はありません**。

## ファイル形式

先頭 24 バイトがヘッダで、以降 4 ブロックが続きます。すべてリトルエンディアン。

```
magic  4B  "TIMD"
ver    4B  int32   形式バージョン（現在 1）
rlen   4B  int32   よみプールの UTF-8 バイト長
slen   4B  int32   表記プールの UTF-8 バイト長
rilen  4B  int32   よみ索引のバイト長
cilen  4B  int32   候補索引のバイト長
---- ここまで 24B ----
よみプール    UTF-8。ユニークなよみを辞書順に連結したもの
表記プール    UTF-8。候補の表記を連結したもの
よみ索引      8B × (よみ数 + 1)   int32 readingStart / int32 candFirst
候補索引      4B × (候補数 + 1)   bit 0-29 surfaceStart / bit 30-31 tag
```

プール内の位置は **文字単位**です（バイト単位ではありません）。
ヘッダのバイト長は UTF-8 から切り出すときにだけ使います。

長さは「次のレコードとの差」で求めます。末尾に番兵レコードが 1 つあるため、
`readingLen(i) = readingStart(i+1) - readingStart(i)` のように引けます。

候補索引のタグは `0 = 表記プールの文字列` / `1 = よみのカタカナ形` /
`2 = よみそのもの`。かな表記の候補は文字列を持たず、実行時によみから生成します。

## ライセンス

辞書データは [mozc](https://github.com/google/mozc) の OSS 辞書
（`src/data/dictionary_oss/`）から生成したもので、**BSD-3-Clause** です。
`MOZC_LICENSE.txt` を必ず参照してください。

Copyright 2010-2018, Google Inc. All rights reserved.
