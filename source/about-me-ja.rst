About Me (日本語版)
===================

主な成果物はここにまとまっている

- `GitHub       <https://github.com/IshitaTakeshi>`__
- `Blog         <https://ishitatakeshi.netlify.com>`__
- `Qiita        <https://qiita.com/IshitaTakeshi>`__
- `SpeakerDeck  <https://speakerdeck.com/ishitatakeshi>`__

ソフトウェア
------------

`Tadataka <https://github.com/IshitaTakeshi/Tadataka>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| 　Visual SLAMの研究開発と産業応用を促進するために開発しているフレームワークである．
| 　現在ではDense Visual Odometryと特徴点ベースのVisual Odometryを使うことができる．

**DVO (Dense Visual Odometry)** [#Steinbrucker_et_al_2011]_ [#Kerl_et_al_2013]_

| 　深度情報および画像の輝度情報からカメラの移動経路を推定することができる．
| 　`手法の詳細な解説 <https://ishitatakeshi.netlify.com/dvo.html>`__

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/oDgBgdHUwOM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

**Feature Based Visual Odometry**

　単眼RGB画像列のみから3次元地図とカメラの軌跡を推定することができる.

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/h4KrMJQDoX4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

**プロジェクトの目的**

　既存のVisual SLAMの実装には次のような問題が存在する．

- 手法ごとに実装が独立している
- 十分にモジュール化されていない
- デバッグがしづらい

　これらの問題は既存のVisual SLAMの研究開発や産業応用を阻害している．私はこれを解決するため，使いやすく，拡張しやすいVisual SLAMフレームワーク "Tadataka" を開発している．

**手法ごとに実装が独立している**

　Visual SLAMには，ORB-SLAM，LSD-SLAM，DSOなど，さまざまな手法が存在する．Visual SLAMのアプリケーションを作成する際には，これらの手法から作りたいアプリケーションに最も適したものを選ぶ必要がある．しかしながら，手法ごとに実装が独立しており，ビルド方法や実行方法がそれぞれ異なるため，アプリケーションに合った手法を選ぶのに手間がかかる．

**十分にモジュール化されていない**

　Visual SLAMの実装の中にはSVOなどのようにきれいにモジュール化されているものも存在するが，先に挙げた手法はモジュール化されていないため，コードを再利用したり拡張したりすることが難しい．

**デバッグがしづらい**

　Visual SLAMを動作させると，カメラ姿勢を正しく推定できなかったり，地図をうまく作れないということが起こり得る．カメラ姿勢推定や地図作成がうまくいかない際には，入力されたデータや処理内容を確認し，どこに原因があるのかを突き止める必要がある．原因を迅速に把握するためには，コードが十分にモジュール化されていることが望ましい．しかしながら，現状のVisual SLAMの実装の多くは，先に述べたようにモジュール化が十分になされていないため，処理に失敗したときにその原因を探ることが難しい．

**Visual SLAMフレームワーク**

| 　こういった問題点を解消するため，私は複数の手法を統一的に扱うことができる，高い拡張性をもったVisual SLAMフレームワークTadatakaを開発している．
| 　たとえばsckit-learnは `SVM` を `RandomForest` と書き換えるだけで中身が全く異なる別々の手法を切り替えることができる．同様に，Tadatakaでは， `LsdSlam` を `OrbSlam` に書き換えるだけで全く異なる手法を切り替えられることを目指している．
| 　各種のDeep Learningフレームワークを用いると，1行書き換えるだけで異なる活性化関数を使うことができる．Visual SLAMをニューラルネットほど高度にモジュール化することは難しいが，Tadatakaでは内部を極力モジュール化し，コードの再利用性と拡張性を高めている．1行書き換えるだけで異なる画像特徴量を使えること，1行書き換えるだけで異なる測光誤差関数を使えることを目指している．

`Sparse Bundle Adjustment <https://github.com/IshitaTakeshi/SBA>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- `詳細な解説 <https://ishitatakeshi.netlify.com/sba.html>`__
- `3D勉強会での発表資料 <https://speakerdeck.com/ishitatakeshi/sparse-bundle-adjustment>`__


.. |sba1| image:: images/reconstruction-without-ba.png
    :width: 100%

.. |sba2| image:: images/reconstruction-with-ba.png
    :width: 100%

.. table:: 図は円筒の復元結果を上から見たものである．左がBundle Adjusmentを適用せずに復元した結果であり，右がBundle Adjustmentを適用しながら復元した結果である．左側は完全に地図が破綻しているのに対して右側は弧が正しく復元できていることがわかる．

    +--------+--------+
    | |sba1| | |sba2| |
    +--------+--------+

| 　Visual Odometryは動作過程で地図やカメラ姿勢に誤差が蓄積していってしまう．この誤差を取り除く操作がBundle Adjustmentである．
| 　Bundle Adjustmentは再投影誤差を誤差の指標とし，これが最小になるような3次元点群座標とカメラ姿勢を求める問題である．
| 　再投影誤差を最小化する3次元点群座標とカメラ姿勢を探索するにはLM法（あるいはGauss-Newton法）が用いられる．LM法の更新過程では誤差関数のHessianの逆行列が要求されるが，3次元復元においてはHessianが非常に巨大になり，逆行列計算のコストが爆発してしまうという問題がある．
| 　Sparse Bundle Adjustment [#Lourakis_et_al_2009]_ は誤差関数のJacobianのスパース性に着目し，Hessianの逆行列の計算コストを大幅に減少させた手法である．

`lsd_slam_noros <https://github.com/IshitaTakeshi/lsd_slam_noros>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| 　LSD-SLAMはdirect methodの代表的な手法として知られているが，公式実装を動作させられる環境を作ることが非常に難しいという問題があった．実際に `Working fork of LSD SLAM? <https://github.com/tum-vision/lsd_slam/issues/274>`__ というissueが公式リポジトリに立てられている．
| 　私はLSD-SLAMを動作させられる環境を作り，Dockerfileを書くことで，LSD-SLAMを動かせる環境を誰でも再現させられるようにした．

`PCANet <https://github.com/IshitaTakeshi/PCANet>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| 　PCANetは名前の通り，ネットワークの重みをPCAによって計算するCNNの一種である．PCAで重みを計算するため，学習が高速に行えるという利点がある [#Chan_et_al_2015]_．

**CuPyにおけるヒストグラム計算**

| 　PCANetはPooling層でヒストグラム計算を行う．PCANet全体を実際に実装して実行速度を計測したところ，Pooling層におけるヒストグラム計算が実行速度のボトルネックになっていることが判明した．私はPCANetの実装にChainerとCuPyを用いたが，ヒストグラム計算は当時CuPyに実装されていなかった．そこで私はヒストグラムをCUDAで実装し，CuPyにpull requsetを送った．このpull requestは後日mergeされ，現在では私が書いたコードがCuPyの中で動作している (`#298 <https://github.com/cupy/cupy/pull/298>`__)．

**Ensemble PCANet**

| 　PCANetは学習こそ速いものの，ネットワークそのものの表現力は弱いという欠点がある．そこで，私はPCANetをBaggingの弱学習器として用いることで精度を向上させる方法を提案した．
| 　Baggingは並列化可能であるため，学習が高速であるというPCANetの利点を活かしつつ，表現力が弱いという欠点を補うことに成功した．この手法を論文にまとめ，2017年に人工知能学会に投稿した(`JSAI 2017 <https://www.ai-gakkai.or.jp/jsai2017/webprogram/2017/paper-504.html>`__)．

`RoadDamageDetector <https://github.com/IshitaTakeshi/RoadDamageDetector>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. image:: images/road-damage-1.png
.. image:: images/road-damage-2.png

| 　道路の損傷を検出することができるSSD(Single Shot Multibox Detector)をベースとしたニューラルネットワークを作成した．
| 　本実験ではSSD内部にある特徴マップ抽出用CNNをVGG16からResNet-101に変更し，実行速度と検出精度を比較した．学習データの不足により検出精度は向上させられなかったが，CPU上での実行速度はVGG16の場合よりも2倍以上高速化できた．
| 　詳細な内容は `Qiita <https://qiita.com/IshitaTakeshi/items/915de731d8081e711ae5>`__ に記載されている．

`Tomasi-Kanade <https://github.com/IshitaTakeshi/Tomasi-Kanade>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
　`手法の詳細な解説 <https://qiita.com/IshitaTakeshi/items/297331b3878e72c65276>`__

.. |tomasi-kanade-input1| image:: images/tomasi-kanade-input-1.png
    :width: 100%

.. |tomasi-kanade-input2| image:: images/tomasi-kanade-input-2.png
    :width: 100%

.. table:: 入力された2次元点群

    +------------------------+------------------------+
    | |tomasi-kanade-input1| | |tomasi-kanade-input2| |
    +------------------------+------------------------+

.. |tomasi-kanade-output1| image:: images/tomasi-kanade-output-1.png
    :width: 100%

.. |tomasi-kanade-output2| image:: images/tomasi-kanade-output-2.png
    :width: 100%

.. table:: 復元結果

    +-------------------------+-------------------------+
    | |tomasi-kanade-output1| | |tomasi-kanade-output2| |
    +-------------------------+-------------------------+

| 　Tomasi-Kanade法は古典的な3次元復元手法のひとつである．
| 　カメラモデルとして正投影を仮定していたり，occlusionに対応できなかったりと，非常に強い制約下でなければ動作しないという欠点はあるものの，アルバイトの業務で有用であったため実装した．

`SCW (Exact Soft Confidence-Weighted Learning) <https://github.com/IshitaTakeshi/SCW>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
| 　SCWはWangらによって提案された線形分類器である [#Wang_et_al_2012]_ ．オンライン学習が可能であるため，省メモリかつ高速に学習を行うことができる．また，分離平面のマージンを大きくとることができるため，精度もよい．
| 　私はこの手法を再現実装し，Pythonパッケージとして公開した．また，Juliaでも同様のものを実装し， `Julia Tokyo #5 <https://juliatokyo.connpass.com/event/21715/>`__ で紹介した．

`TruthFinder <https://github.com/IshitaTakeshi/TruthFinder>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
| 　TruthFinderは，情報とその発信者から，どの情報が信頼できるか，どの発信者が信頼できるかを推定するアルゴリズムである [#Yin_et_al_2008]_．フィンランド留学中に開発した．
| 　フィンランド留学では，病気にかかっている人々のためのSNSアプリに機能を追加するプロジェクトに参加した．私が所属したチームではwiki機能を開発することになった．医療関連の情報を扱うアプリケーションであるため，wikiの情報は信頼できるものでなければならない．このため，私はwikiの情報およびその信頼度を推定するアルゴリズムを実装した．
| 　プロジェクトの期間が短く，またフィンランド語を対象としたアプリケーションなので学習データの量も期待できなかった．このため，Deep Learningをベースとした手法を実装することは諦め，あくまでデモとして単純なアルゴリズムを実装した．
| 　TruthFinderを実装したことにより，情報およびその発信者のリストから，誰が信頼できるか，どの発信者が信頼できるかを推定できることを示せた．

`K-SVD <https://github.com/IshitaTakeshi/KSVD.jl>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. figure:: images/ksvd-mnist.png
   :width: 80%

   文字画像の集合 (入力)

.. figure:: images/ksvd-dictionary.png
   :width: 80%

   得られた辞書

| 　K-SVD [#Aharon_et_al_2006]_ はスパースコーディングにおける辞書を生成するアルゴリズムである．
| 　スパースコーディングでは信号を表現するためのベクトル集合(辞書)が必要となる．入力された信号を辞書内のできるだけ少ない要素の組み合わせで表現できるとき，それはよい辞書であるといえる．K-SVDは入力信号から効率のよい辞書を得るためのアルゴリズムである．
| 　図では手書き文字画像を入力信号とみなし，それを効率よく表現するための辞書を得ている．

`KanaKanjiConversion <https://github.com/IshitaTakeshi/KanaKanjiConversion>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
| 　D言語で日本語入力システム(IME)を途中まで作った．既存のIMEの精度に不満があり，文章中の単語の共起確率に着目すればメモリ消費量を抑えつつ高精度な変換が行えるのではないかという仮定のもとで開発を進めたものである．
| 　「単語の共起情報を利用したかな漢字変換システム」というテーマで2016年度の未踏プロジェクトに応募したが，採択されなかった．当時の応募資料などは `Qiita <https://qiita.com/IshitaTakeshi/items/f2fbaee7ae48644e679e>`__ で公開されている．

`DTrie <https://github.com/IshitaTakeshi/DTrie>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
| 　Trieは辞書表現に用いられる簡潔データ構造の一種である．上記のIMEで辞書を保持するために必要になった．
| 　IMEはできる限り省メモリかつ高速に動作する必要がある．IMEがメモリを2GBも消費してはならないし，変換速度が遅いとユーザーに不快感を与えてしまう．
| 　Trieは効率よく情報を保持することができ，かつ非常に高速にデータを取り出すことができるデータ構造である．
| 　DTrieはD言語で作成したパッケージであるが，Trieの仕組みを学ぶためにPythonで書いたものも公開されている(`Louds-Trie <https://github.com/IshitaTakeshi/Louds-Trie>`__)．


ブログ
------

`The Zen of Python <https://qiita.com/IshitaTakeshi/items/e4145921c8dbf7ba57ef>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| The Zen of PythonはPythonプログラマが持つべき心構えを簡潔に述べたものである．
| StackOverflowに詳細な解説が存在していたので，日本語に翻訳し，ひとつの記事としてまとめた．また，内容を `comb meet up! <https://connpass.com/event/7772/>`__ で解説した．

`カルマンフィルタってなに？ <https://qiita.com/IshitaTakeshi/items/740ac7e9b549eee4cc04>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
　カルマンフィルタはやっていることはそれほど難しくないものの，難しい表現による解説が多いため，よりわかりやすいものを書いて公開した．数式よりも図で説明することで，カルマンフィルタがやっていることを視覚的に説明している．

`機械学習をこれから始める人に押さえておいてほしいこと <https://qiita.com/IshitaTakeshi/items/4607d9f729babd273960>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
　機械学習が盛り上がり始めていた時期に書いた記事である．Googleで「機械学習」と検索するとこの記事がWikipediaの次に出てきたこともあった．

`熱伝導方程式の導出 <https://qiita.com/IshitaTakeshi/items/cf106c139660ef138185>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
　数学の授業で熱伝導方程式を扱ったので，視覚的な説明があるとよいと思い執筆した．

特許
----

`特許6306770 <https://www.j-platpat.inpit.go.jp/web/PU/JPB_6306770/062D067C8381CD29700292EC1ED536D9>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
　料理の画像から具材を見つけ出す手法を考案した

著作
----

* 日経ソフトウェア 2017年 8月号 「Pythonで機械学習」
* 日経ソフトウェア 2020年 5月号 「撮影した物体を3次元データで復元」 (最新号なのでいま書店に行くと売っている)

ハッカソン
----------

* Open Hack U 2014
* JPHacks 2015 Final進出

.. [#Steinbrucker_et_al_2011] Steinbrücker Frank, Jürgen Sturm, and Daniel Cremers. "Real-time visual odometry from dense RGB-D images." Computer Vision Workshops (ICCV Workshops), 2011 IEEE International Conference on. IEEE, 2011.
.. [#Kerl_et_al_2013] Kerl, Christian, Jürgen Sturm, and Daniel Cremers. "Robust odometry estimation for RGB-D cameras." Robotics and Automation (ICRA), 2013 IEEE International Conference on. IEEE, 2013.
.. [#Chan_et_al_2015] Chan, Tsung-Han, et al. "PCANet: A simple deep learning baseline for image classification?." IEEE transactions on image processing 24.12 (2015): 5017-5032.
.. [#Lourakis_et_al_2009] Lourakis, Manolis IA, and Antonis A. Argyros. "SBA: A software package for generic sparse bundle adjustment." ACM Transactions on Mathematical Software (TOMS) 36.1 (2009): 1-30.
.. [#Wang_et_al_2012] Wang, Jialei, Peilin Zhao, and Steven CH Hoi. "Exact soft confidence-weighted learning." arXiv preprint arXiv:1206.4612 (2012).
.. [#Yin_et_al_2008] Yin, Xiaoxin, Jiawei Han, and S. Yu Philip. "Truth discovery with multiple conflicting information providers on the web." IEEE Transactions on Knowledge and Data Engineering 20.6 (2008): 796-808.
.. [#Aharon_et_al_2006] Aharon, Michal, Michael Elad, and Alfred Bruckstein. "K-SVD: An algorithm for designing overcomplete dictionaries for sparse representation." IEEE Transactions on signal processing 54.11 (2006): 4311-4322.
