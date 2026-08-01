# TIM Keyboard 変換辞書

VRChat ワールドギミック **【TIM】Keyboard** が実行時に取得する変換辞書です。
ワールドには同梱せず、必要な言語の 1 本だけをここから取りに行きます。

| ファイル | 言語 | 入力方式 | 元データ | ライセンス |
|---|---|---|---|---|
| `ime_dict.bin` | 日本語 | ローマ字 → かな → 漢字 | [mozc](https://github.com/google/mozc) | BSD-3-Clause |
| `ime_dict_zh_tw.bin` | 台灣華語 | 注音 → 繁體字 | [libchewing 詞庫](https://github.com/chewing/libchewing-data) | LGPL-2.1-or-later |
| `ime_dict_zh_cn.bin` | 簡体中文 | 拼音 → 简体字 | [libpinyin](https://github.com/libpinyin/libpinyin) ほか | GPL-3.0 |
| `ime_dict_en.bin` | 英語 | 入力予測（先頭数文字 → 単語） | [FrequencyWords](https://github.com/hermitdave/FrequencyWords) | MIT |

韓国語は辞書が要りません（ハングルは初声・中声・終声から計算で組み立てられるため）。

英語だけは「変換」ではなく**入力予測**です。読みを単語の先頭数文字にしてあるので、
`hel` と打つと `help / hello / hell …` が候補に出ます。
仕組みは他の言語とまったく同じで、実行時のコードも共通です。

## URL

ギミックのセットアップウィンドウ **① 用意 ▸ 詳しい設定** に、既定で入っています。
手で入れ直す場合は次のとおりです。

```
https://raw.githubusercontent.com/TIM-VRC/TIM_Keyboard_Dict/main/ime_dict.bin
https://raw.githubusercontent.com/TIM-VRC/TIM_Keyboard_Dict/main/ime_dict_zh_tw.bin
https://raw.githubusercontent.com/TIM-VRC/TIM_Keyboard_Dict/main/ime_dict_zh_cn.bin
https://raw.githubusercontent.com/TIM-VRC/TIM_Keyboard_Dict/main/ime_dict_en.bin
```

GitHub は VRChat の信頼済みドメインなので、**プレイヤー側で
「Allow Untrusted URLs」を有効にする必要はありません**。

実行時に落とすのは、そのプレイヤーの言語の **1 本だけ**です。

## 著作権表示

* 日本語辞書 — Copyright Google Inc. / mozc プロジェクト（BSD-3-Clause）
* 台灣華語辞書 — Copyright (c) libchewing Core Team（LGPL-2.1-or-later）
* 簡体中文辞書 — 以下の 3 つを組み合わせたもの
  * 語彙と拼音: [libpinyin](https://github.com/libpinyin/libpinyin)（GPL-3.0。
    一部は android-pinyin-ime 由来で Apache-2.0）
  * 語の頻度: [rime-essay](https://github.com/rime/rime-essay)（LGPL-3.0）
  * 繁 → 簡 の字の対応: [OpenCC](https://github.com/BYVoid/OpenCC)（Apache-2.0）
* 英語辞書 — Copyright (c) 2016 Hermit Dave（MIT）。OpenSubtitles の字幕から
  数えた語の頻度表を元にしている。

ライセンス全文は同じフォルダの `LICENSE-*.txt` にあります。

## 元データと作り方

`.bin` は元データを機械的に変換したものです。変換スクリプトは
【TIM】Keyboard の `Assets/TIM_IME/Tools/` にあります。

```
python build_dict.py --download        # 日本語（mozc）
python build_dict.py

python build_dict_zh.py --download     # 台灣華語（libchewing）
python build_dict_zh.py

python build_dict_zh_cn.py --download  # 簡体中文（libpinyin ほか）
python build_dict_zh_cn.py

python build_dict_en.py --download     # 英語の入力予測（FrequencyWords）
python build_dict_en.py
```

`--download` が元データを取得し、2 回目の実行で `.bin` を作ります。
元データそのものは各プロジェクトの配布元から取得できます（上の表のリンク）。

## 形式

先頭 24 バイトがヘッダ、以降 4 ブロック。

```
magic  4B  "TIMD"
ver    4B  int32
rlen   4B  int32   読みプールのバイト長（UTF-8）
slen   4B  int32   表記プールのバイト長
ridx   4B  int32   読み索引のバイト長
cidx   4B  int32   候補索引のバイト長
```

読みは辞書順に並んでおり、実行時は二分探索で引きます。
詳しくは `Tools/dict_blob.py` の冒頭を参照してください。

## 差し替えについて

このワールドギミックの利用者は、セットアップウィンドウの URL 欄を書き換えて
自分で用意した辞書に差し替えられます。上記のライセンスが求める
「利用者が辞書を置き換えられること」は、この仕組みで満たしています。
