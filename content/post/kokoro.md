---
title: "常用漢字をポアンカレ埋め込み"
date: 2018-12-02T16:16:29-05:00
draft: false
header-includes:
   - \usepackage{amsmath}
tags: ['日本語','python','学習']
---

各々の書類において、漢字がどのように現れるか比べることで様々な文書がどういう分類に属するかと考えたときポアンカレ埋め込みがもしかしたら役にたつかもしれません。

#### グラフ埋め込みとは

ポアンカレ埋め込みはグラフ埋め込みの一種です。ではグラフ埋め込みとは何か。とりあえずグラフがあるとします。点が何個もあってそのうちの２つの点は線で繋がってたりつながってなかったりします。でもそれ以外のことは何もわかっていません。具体的に言いますと各点には座標が与えられていません。グラフ埋め込みとはグラフの各点に座標を与えるようなことだと僕は理解しています。繋がってる点どうしは近くに、繋がっていない点どうしは離して。これの何が嬉しいかといいますと、より少量のデータで類似関係を表現できるのです。たとえば点が$n$個ですと結合線の数は$C {n \choose 2}$です。一方、$d$次元の空間に埋め込めば位置の遠近によって類似度を表せば良いので点の数$n$のオーダーでデータ量は増えます。$n$が大きければ大きいほど後者の方がオトクなのです。調べたら[こんなの](https://arxiv.org/pdf/1709.07604.pdf)が出てきて、冒頭だけ読んでみての僕の理解です。

#### ポアンカレ埋め込みとは

ポアンカレ埋め込みのポアンカレは人名ですが、ポアンカレの円板モデルを使うのでこう呼びます。[原論文](https://arxiv.org/pdf/1705.08039.pdf)はこちらです。円板ですから二次元でノルムが１より小さいところだけ考えてください。ただこの中の二点間の距離は通常のノルムを使わず、以下のものを使います。
$$
d(u,v)=\text{arcos}(1+\delta(u,v))
$$
ここで
$$
\delta(u,v)=2\frac{\|u-v\|^2}{(1-\|u\|^2)(1-\|v\|^2)}
$$
です。$u$と$v$はこのポアンカレ円板の中の点です。で、ここからがミソなのですが、この距離においては縁に近づけば近づくほど空間が拡大しているのです。ものすごく適当な言い方で本当申し訳ないです。[ここ](https://rare-technologies.com/implementing-poincare-embeddings/)のFigure 3がわかりやすいです。各図の赤点からの距離を色で示しています。これの何が良いかというと木構造のデータの埋め込みにとても適しているのです。

![木構造データの例、[ここ](http://graphstream-project.org/doc/Generators/Overview-of-generators/)より](/images/tree-graph.png)

中央の点にくっついているのが一番上の層です。その五点のすぐ下にくっついているのが第二の層です。第二の層の下にくっついているのが第三の層です。イメージとしては各層が円を成しているイメージです。第$n$層目の点の数が$C\exp(n)$個の場合どうなるでしょうか。普通のノルムだと円周ってのは$2\pi r$なので$n$層目は半径$\exp(n)$のところに置くというような感じにしないとその層の点はギュウギュウに詰め込まれてしまいます。でもポアンカレ円板モデルだと中心から離れれば離れるほど円周ってのは指数関数的に大きくなるのでそこまで円を大きくしなくていいのです。こうやって考えてみると別に前者でもいいような気もしますが、とにかくポアンカレ円板モデルってのは指数関数的に点が増えていく木構造グラフを扱う一つの有効な手法なのです。

#### どうやって埋め込みを見つけるか

以下のような損失関数を考えます。
$$
L=\sum\_{(u,v)\in D} \log \frac{{\exp(-d(u,v))}}{{\sum\_{v' \in N(u)} \exp(-d(u,v'))}}
$$
$D$ってのは繋がってるペアの集合です。$N(u)$ってのは$u$と繋がっていない点の集合です。繋がってる点どうしは近くに、繋がっていない点どうしは遠くに置くとこの関数は大きくなります。ですからまず最初に適当に（それなりに散らばせたりした方がいいという話もある）埋め込んでこの$L$が大きくなるように勾配降下法で更新していきます。埋め込まれた点を$u_1$, $u_2$, $\cdots$, $u_n$とします。これを変えると$L$がどうなるか知りたいわけだから勾配は$(\partial L/\partial u_1,\partial L/\partial u_2,\cdots, \partial L/\partial u_n)^t$です。この勾配ベクトルを$\nabla_E$と表記します。EuclideanのEです。各項はこんな感じです。
$$
\frac{\partial L}{\partial u\_i}=\sum\_{j=1}^n\frac{\partial L}{\partial d(u\_i,u\_j)}\frac{\partial d(u\_i,u\_j)}{\partial u\_i}
$$
なかなか大変そうです。このままじゃあ実はだめです。というのはこの勾配はユークリッド空間のであってポアンカレ円板のじゃないからです。

#### 計量テンソルとは

言葉では説明しにくいですが
$$
d(u,u+\Delta u)^2=\Delta u^t A \Delta u
$$
となる$A$の$\Delta u$を0に極限まで近づけたときの値です。左辺はその距離構造における$u$から発する微小線分の長さの二乗です。右辺はその微小線分の二次形式になってます。つまりポアンカレ円板での距離（の二乗）をユークリッド空間での距離の二次形式で表してます。この$A$が計量テンソルです。テンソルってのは行列の拡張です。注意されたいのはこの$A$が$u$の関数だってことです。求め方ですがまず
$$
\frac{\partial d(u,v)}{\partial v}|\_{v=u}
$$
を求めます。すると以下が得られます。
$$
\frac{2\lim\_{v\rightarrow u} \frac{u-v}{\|u-v\|}}{1-u^t u}
$$
ベクトルで微分してるのでこれもベクトルです。こんなことしていいのかよくわかりませんが$du=\lim_{v\rightarrow u} \frac{u-v}{\|u-v\|}$と置きます。すると自分自身と内積をとって
$$
du^t \frac{4}{(1-u^t u)^2} du
$$
が得られます。だから計量テンソル$A(u)$は対角成分が
$$
\frac{4}{(1-\|u\|^2)^2}
$$
なのかと僕は理解しています。$du$という表記についてはちょっと自身ありませんが。

#### 射影

勾配降下法をそのまま適用すると、ノルム（通常の）が1以上になってしまうかもしれません。ですので円板内に留まらせるために以下の関数を考えます。
$$
\text{proj}(\theta)=
\begin{cases}
&\theta/\|\theta\|(1-\epsilon)\text{ if $\|\theta\|\ge 1$} \\\\[2ex]
&\theta\text{ otherwise}
\end{cases}
$$
縮めてるだけですね。エプシロンはちっちゃな数です。てなわけで以下のような感じに更新します。
$$
\theta\_{t+1}\leftarrow \text{proj} \left( \theta\_t-\eta\_t \frac{(1-\|\theta\_t\|^2)^2}{4}\nabla\_E \right)
$$
縁に近づけば近づくほど
$$
\frac{(1-\|\theta\_t\|^2)^2}{4}
$$
が小さくなることが見て取れると思います。
#### 常用漢字とは

コトバンクで見つけましたブリタニカ国際大百科事典小項目事典によると[常用漢字](https://kotobank.jp/word/%E5%B8%B8%E7%94%A8%E6%BC%A2%E5%AD%97-79941)とは

>法令，公用文書，新聞，雑誌，放送など，一般の社会生活における漢字使用の目安となることを目指したもの

だそうです。最低限知っとかなきゃいけない漢字セットという感じでしょうか。現在2136文字あるそうです。

漢字を解析対象にしようと思ったのは余計な処理をしなくていいからです。日経平均採用銘柄からグラフを、そこからまた埋め込みを作成しようと当初は考えていましたが。同一の会社にも表記にばらつきがあるのでそこの処理が大変そうだったので断念しました。

#### 解析内容

まず青空文庫から「こころ」の[テキストファイル](https://www.aozora.gr.jp/cards/000148/card773.html)をダウンロードします。同時に常用漢字を集計した[テキストファイル](https://github.com/rime-aca/character_set/blob/master/%E5%B8%B8%E7%94%A8%E6%BC%A2%E5%AD%97%E8%A1%A8.txt)もダウンロードします。以下のコードでは「こころ」をスキャンし、各漢字が現れるところをみつけ、その前後１０文字ずつをスライスし、まとめます。その範囲の中に他の漢字が何回現れるか集計します。漢字「甲」の前後に漢字「乙」が現れたなら、「甲」と「乙」は繋がっているとします。これよりグラフを得ます。そしてそのグラフを`gensim`というパッケージを使ってポアンカレ埋め込みしました。

```.py
import numpy as np
import codecs
import matplotlib.pyplot as plt
from gensim.models.poincare import PoincareModel
import matplotlib.font_manager as mfm
from matplotlib.pyplot import figure
import matplotlib as mpl

file=open('kanji.txt','r')
kanji=file.read().split('\n')
file.close()

with codecs.open('kokoro.txt', "r",encoding='ShiftJIS', errors='ignore') as file:
    kokoro=file.read().split('\n')

kokoro=''.join(kokoro)
n=len(kanji)
Count=np.zeros((n+1,n+1))
r=10

ni=0
for i in kanji:
    occur=[s for s, a in enumerate(kokoro) if a == i]
    nj=0
    selection=[]
    for loc in occur:
        selection.append(kokoro[loc-r:loc+r])
    selection=''.join(selection)
    for j in kanji:
        Count[ni,nj]+=selection.count(j)
        nj+=1
    print(i)
    ni+=1

M=Count+Count.T

cut=3
relations=[]
for i in range(n+1):
    for j in range(i+1,n+1):
        if M[i,j]>cut:
            relations.append((kanji[i],kanji[j]))

negs=10
epcs=50
model = PoincareModel(relations, negative=negs,size=2)
model.train(epochs=epcs)

figure(figsize=(80, 60))
font_path = "kaiu.ttf"
mpl.rcParams["font.size"] = 4
prop = mfm.FontProperties(fname=font_path)
for i in list(model.kv.vocab.keys()):
    plt.scatter(model.kv.word_vec(i)[0],model.kv.word_vec(i)[1],s=0.1)
    plt.annotate(i,xy=(model.kv.word_vec(i)[0],model.kv.word_vec(i)[1]),fontproperties=prop)
plt.savefig(str(epcs)+'-'+str(negs)+'-'+str(cut)+'-'+str(r)+'.svg')
```
[ここ](http://cl-hair.hatenablog.com/entry/2018/01/09/181400)を参考にしました。ポアンカレ埋め込みの出力は`gensim.viz.poincare`が`plotly3.0.0>=`に対応すればもっと簡単にかけます。

![「こころ」の漢字たち](/images/1-1.png)


1エポックしか走らせてないので全然だめですね。中央に固まったのが移動しきってません。近くに一回でも漢字が現れれば繋げちゃうのもノイズを混入させてしまってますね。ロスをみると繋がっていないノードたちも見ますが$N(u)$は全部見てるわけじゃなくて部分サンプリングしてます（ネガティブサンプリング）。今回はたったの1 です。当然まだまだですね。ネガティブサンプリングを10、近くに三回以上現れたらつなぐ、エポック50で走らせたのが[こちら](https://github.com/ztyh/kokoro/blob/master/50-10-2-10.svg)です。右クリックから保存してください。拡大して見てください。近くに四回以上現れたら繋ぐ、出現頻度で正規化したのが[こちら](https://github.com/ztyh/kokoro/blob/master/50-10-3-10.svg)です。

#### 応用

各書類の常用漢字を以上のようにして埋め込めば、書類を$2\times 2136$の数字でエンコーディングできます。これを比べることで書類の分類ができるかもしれません。ただ一つ問題があってポアンカレ埋め込みを回転しても、それは同様に優秀な埋め込みなのです。ですので２つの書類の埋め込みに差が大きくても、もしかしたら埋め込みの差ではなく回転の差という本質的ではない差かもしれないのです。漢字を一つ選んで、そこの位置を揃えるっていうふうにしてもいいでしょうが、選ぶ漢字によって解析結果は変化してしまうでしょう。いろんな回転を試して一番いいのを選ぶってのも考えられますが、時間がかかってしまいそうですね。



