# 2020年度自然言語解析基礎第一回レポート

- 提出日: 2020/10/06
- 201811528 春名航亨
  - (知識情報・図書館学類3年)

## はじめに

メジャーな日本語形態素解析ツールの一つである[mecab](https://taku910.github.io/mecab/)のコマンドラインツールをBash上で動かして、その基本的な使い方を理解する.

## 実践

```bash
# まずはオプション無しに単語に入力.
# 形態素と判定されたものが1行ずつ, 以下ののフォーマットで出力されている.
# `表層形\t品詞,品詞細分類1,品詞細分類2,品詞細分類3,活用型,活用形,原形,読み,発音`
$ mecab <<< となりのたけがきにたけたてかけた
となり	名詞,一般,*,*,*,*,となり,トナリ,トナリ
の	助詞,連体化,*,*,*,*,の,ノ,ノ
たけ	名詞,一般,*,*,*,*,たけ,タケ,タケ
が	助詞,格助詞,一般,*,*,*,が,ガ,ガ
き	動詞,自立,*,*,カ変・クル,連用形,くる,キ,キ
に	助詞,格助詞,一般,*,*,*,に,ニ,ニ
たけ	動詞,自立,*,*,一段,連用形,たける,タケ,タケ
た	助動詞,*,*,*,特殊・タ,基本形,た,タ,タ
て	助詞,接続助詞,*,*,*,*,て,テ,テ
かけ	動詞,非自立,*,*,一段,連用形,かける,カケ,カケ
た	助動詞,*,*,*,特殊・タ,基本形,た,タ,タ
EOS
# 面白い機能として未知語推定が行える.
# -x <STR>で未知語にユーザ定義のラベルをつける
$ mecab -x '未' <<< あき竹城のぱぺん
あき竹城	名詞,固有名詞,人名,一般,*,*,あき竹城,アキタケジョウ,アキタケジョー
の	助詞,連体化,*,*,*,*,の,ノ,ノ
ぱぺん	未
EOS
```

## まとめ

mecabを実際に使ってみて、日本語の複雑な形態素解析をコマンド一つで簡単に行えることがわかった。