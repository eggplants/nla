# 自然言語解析基礎03回(言語モデル) (10/19)

## 単語の予測

- 前の単語列を見て，統計情報から次の単語を推定・先読みしたい

`１３日、...`->`１３日、横浜...`->`１３日、横浜国際総合競技場で...`->`１３日、横浜国際総合競技場で行われたラグビー・ワールドカップで、...`->`１３日、横浜国際総合競技場で行われたラグビー・ワールドカップで、日本代表は接戦の末、スコットランド代表を破った。`

- n-gram言語モデルにより定式化して次に出現しそうな単語を予測
  - n-gramの大規模コーパスにおいての出現頻度を見る

## n-gram言語モデル

- n-gram言語モデル(language model)
  - 文の直前に出現するn-1番目の単語列からのn番目の単語の条件付き出現確率を推定し、単語を予測する統計モデル
  - n-1番目の後に現れやすいn番目の単語を、条件確率を「現れやすさ」と捉えて予測

```ruby
class String
  def n_gram(n)
    self.gsub(/(,)(\w)/, '\1 \2').gsub(/(\w)(\.)/, '\1 \2')
    .split(?\s).each_cons(n).to_a
  end
end

str = "The quick brown fox jumps over the lazy dog."
str.n_gram(3)
# [["The", "quick", "brown"],
#  ["quick", "brown", "fox"],
#  ["brown", "fox", "jumps"],
#  ["fox", "jumps", "over"],
#  ["jumps", "over", "the"],
#  ["over", "the", "lazy"],
#  ["the", "lazy", "dog"],
#  ["lazy", "dog", "."]]
```

- 応用
  - 自動音声認識, 手書き文字認識, スペルチェック, 機械翻訳

- 頻度計算のためのコーパス
  - 一つの文ではなくたくさんのテキスト集合（コーパス）を対象として出現頻度を計算
  - ブラウンコーパス(1992) ：大規模コーパス（英語）
    - 5.8億のトークン（単語）
    - 29万の語形タイプ
  - グーグル（のクロールした文書集合）
    - 1.0兆のトークン（単語）
    - 1,358万の語形タイプ
    - 英語の辞書でも50万のタイプなので，かなり多い
    - 理由：数，スペルミス，名前，頭字語などの存在
    - 英語，日本語，中国語などがある
    - 日本語版では，形態素解析器MeCabを用いて，形態素列を1グラムから7グラムまで計算
      - MeCabの解析結果の誤りは修正していない
      - 日本語版の対象データは，2007年のグーグルのクロール文書

## 言語モデル(language model)

- 次に来る単語を予測するタスク
- 単語列$x^{(1)}, x^{(2)}, ..., x^{(t)}$が与えられた時に次にくる単語$x^{(t+1)}$の確率分布を$P(x^{(t+1)} = w_j | x^{(t)}, ..., x^{(1)})$とする
  - $w_j$: 語彙$V = {w_1, ..., w_{|V|}}$中の単語

## n-gram言語モデルの式

- 「単語$x^{(t+1)}$は，先行する(n-1) 個の単語により決まる」という仮定に基づき出現確率を計算

$$
P(x^{(t+1)} | x^{t}, ..., x^{(1)}) = P(x^{(t+1)} | x^{(t)}, ..., x^{(t-n+2)})
= \frac{P(x^{(t+1)}, x^{(t)}, ..., x^{(t-n+2)})}{P(x^{(t)}, ..., x^{(t-n+2)})}
\approx \frac{Count(x^{(t+1)}, x^{(t)}, ..., x^{(t-n+2)})}{Count(x^{(t)}, ..., x^{(t-n+2)})}
$$

- `P(the | its water is so transparent that)`
  - `= Count(its water is so transparent that the) / Count(its water is so transparent that)`

- 多くの場合，大規模コーパスから，この単純な推定方法でn-gram を計算すると，よい予測ができない
  - 0になったり0/0になったり...

- 確率の連鎖規則と独立性の仮定を使う

## 連鎖規則

- $P(A \hat{} B) = P(A | B) P(B)$

## パープレキシティ・スムージング

### パープレキシティ

- 次の単語への予測分岐数の平均値
- ある言語モデルでコーパス中の各文における次の単語の生起確率の逆数（分岐数）を計算した結果から得られる単語予測分岐数の平均値
- 生起確率$P(w_i|w_{i-1})$の逆数の総積のN方根
- 最小化を行うことで言語モデルの未知コーパスへの予測の最良化に
  - 値が小さいほど予測確率が高

### スムージング

- 訓練データ中に存在しない単語の組み合わせの出現確率を割り当てておく
  - 未知の出現を予測しておく

### Good–Turing推定

- これまでに観測されていない種の対象に遭遇する確率を、異なる種の対象 (有限個だが総数は不明) についての過去の観測情報から推定するための統計的手法
