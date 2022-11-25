Graph SLAM
==========

Graph SLAMとは
--------------

Graph SLAMとは、SLAMの最適化問題をグラフ構造で表現する手法である。
カメラ姿勢をノードで表現すると、異なる2時刻の姿勢関係をそれらのノードをつなぐエッジで表現することができる。
ランドマークもノードで表現することができる。これにより、ランドマークとカメラ姿勢の関係をこれらをつなぐエッジで表現することができる。


注釈
~~~~

今回は Visual Inertial SLAM を例として確率モデルを記述するが、IMU観測値をたとえば車両への制御入力などに置き換えれば、他のセンサー入力を対象としたSLAMにも応用できる。

運動モデル
~~~~~~~~~~

観測モデル
~~~~~~~~~~

Graph SLAMによる姿勢推定および地図作成
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| Graph SLAM ではグラフの各エッジに誤差関数を対応させ、すべてのエッジの誤差関数の総和を最小化することで最適な姿勢とランドマーク座標を求める。
| たとえば時刻 :math:`i` の姿勢を :math:`\mathbf{x}_{i} \in \mathbb{R}^{6}` とし、 :math:`i` から  :math:`i+1` までのIMU観測値 :math:`\mathbf{u}_{i+1} \in \mathbb{R}^{9}` および運動モデル :math:`\mathbf{g}(\mathbf{x}_{i}, \mathbf{u}_{i+1})` を用いて運動モデルに関する誤差

.. math::
    [\mathbf{x}_{i+1} - \mathbf{g}(\mathbf{x}_{i}, \mathbf{u}_{i+1})]^{\top}Q_{k}^{-1}[\mathbf{x}_{i+1} - \mathbf{g}(\mathbf{x}_{i}, \mathbf{u}_{i+1})]

を構成することができる。ここで :math:`Q_{k}` は誤差の共分散を表す行列である。この誤差 :math:`\mathbf{x}_{i}` と  :math:`\mathbf{x}_{i+1}` の間のエッジに対応させる。


また、 :math:`j` 番目のランドマークを :math:`\mathbf{m}_{j}` 、 :math:`i` 番目の姿勢における :math:`j` 番目のランドマークの観測値を :math:`\mathbf{z}_{ij}` とし、観測モデルを :math:`\mathbf{h}(\mathbf{x}_{i}, \mathbf{m}_{j})` とする。
具体的には、たとえばランドマーク :math:`\mathbf{m}_{j}` は物体の3次元座標、ランドマークの観測値 :math:`\mathbf{z}_{ij}` は画像特徴点、観測モデル :math:`\mathbf{h}(\mathbf{x}_{i}, \mathbf{m}_{j})` は透視投影モデルに置き換えることができる。

これらを用いて再投影誤差

.. math::
    || \mathbf{z}_{ij} - \mathbf{h}(\mathbf{x}_{i}, \mathbf{m}_{j}) ||^2

を計算することができる。これを :math:`\mathbf{x}_{i}` と  :math:`\mathbf{m}_{j}` の間のエッジに対応させる。

これら誤差関数の総和を最小化するエッジの値 :math:`\left\{\mathbf{x}^{*}_{0}, \mathbf{x}^{*}_{1}, \mathbf{x}^{*}_{2}, \mathbf{x}^{*}_{3}, \mathbf{x}^{*}_{4}, \mathbf{m}^{*}_{1}, \mathbf{m}^{*}_{2}\right\}` を求める。これが Graph SLAM による姿勢推定および地図作成の問題である。


確率モデルによる表現
~~~~~~~~~~~~~~~~~~~~

| 確率モデルで表現してみよう。
| 初期時刻 :math:`0` から時刻 :math:`T` までの姿勢を :math:`\mathbf{x}_{0:T} = \{\mathbf{x}_{1},...,\mathbf{x}_{T}\}` 、 :math:`\mathbf{x}_{0:T}` から観測されたランドマークの集合を :math:`\mathbf{m}_{1:N} = \{\mathbf{m}_{1},...,\mathbf{m}_{N}\}` とする。
| また、この間に得られたIMU観測値の集合を :math:`\mathbf{u}_{1:T} = \{\mathbf{u}_{1},...,\mathbf{u}_{T}\}` 、ランドマーク観測値の集合を :math:`Z_{0:T} = \{\mathbf{z}_{ij} \;|\; (i, j) \in S_{0:T}\}` とする。ここで :math:`S_{0:T}` は時刻 :math:`0` から時刻 :math:`T` までの観測可能な姿勢とランドマークの組み合わせを表している。
| 我々の目的は、次の分布を明らかにすることである。

.. math::
    p(\mathbf{x}_{0:T}, \mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T}, Z_{0:T})
    :label: fullslamposterior

| すなわち、IMUやカメラから得られた観測情報を用いて、時刻 :math:`0` から :math:`T` までの姿勢およびその間に観測されたランドマークの分布を求めるという問題である。

状態分布の再帰的推定法の導出
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| すべての観測値から姿勢およびランドマークの分布 :eq:`fullslamposterior` を高精度に求める手法 [#sfm]_ は存在するものの、計算コストが高い。一般的にSLAMは高速に動作することを求められるため、ある事前情報をたよりにして、特定時刻の状態を求めることが一般的である。
| ここでは時刻 :math:`T-1` の状態をたよりにして時刻 :math:`T` の状態を求める確率モデルを導出する。

| まずはベイズの定理に従って :eq:`fullslamposterior` を分解する。

.. math::
   &p(\mathbf{x}_{0:T}, \mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T}, Z_{0:T}) \\
   &= \eta_{T} \; p(Z_{T}\;|\;\mathbf{x}_{0:T},\mathbf{m}_{1:N},\mathbf{u}_{1:N},Z_{0:T-1})\;p(\mathbf{x}_{0:T},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1}) \\
   :label: factored-posterior

ここで :math:`\eta_{T}` は次で計算される定数である。 :math:`\eta_{T}` は観測値 :math:`\mathbf{u}_{1:T},Z_{0:T}` の確率分布のみで構成されているため、一度これらが観測されてしまえば :math:`\eta_{T}` は変化しない。したがって姿勢推定の問題では :math:`\eta_{T}` は定数として扱うことができる。

.. math::
    \eta_{T} = \frac{p(\mathbf{u}_{1:T},Z_{0:T-1})}{p(\mathbf{u}_{1:T},Z_{0:T})}

| さて、 :math:`Z_{T}` は時刻 :math:`T` に得られたランドマークの観測値だが、 :math:`Z_{T}` の分布は同時刻の姿勢 :math:`\mathbf{x}_{T}` およびそこで観測できるランドマークの座標 :math:`\mathbf{m}_{1:N}` にしか依存しない。これは時刻 :math:`i` から観測された :math:`j` 番目のランドマーク :math:`\mathbf{z}_{ij}` を :math:`\mathbf{h}(\mathbf{x}_{i},\mathbf{m}_{j})` でモデル化するためである。したがって、 :math:`Z_{T}` の確率分布を次のように単純化できる。 [#simplify_z_distribution]_

.. math::
    p(Z_{T}\;|\;\mathbf{x}_{0:T},\mathbf{m}_{1:N},\mathbf{u}_{1:N},Z_{0:T-1}) = p(Z_{T}\;|\;\mathbf{x}_{T},\mathbf{m}_{1:N})


:eq:`factored-posterior` のもうひとつの確率についてもベイズ則を適用する。

.. math::
    p(\mathbf{x}_{0:T},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1})
    = p(\mathbf{x}_{T}\;|\;\mathbf{x}_{0:T-1},\mathbf{m}_{1:N},\mathbf{u}_{1:T},Z_{0:T-1})\;
      p(\mathbf{x}_{0:T-1},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1})

我々は時刻 :math:`T` の姿勢 :math:`\mathbf{x}_{T}` をオドメトリ推定モデル :math:`\mathbf{g}(\mathbf{x}_{T-1}, \mathbf{u}_{T})` で予測する。したがって先ほどと同様の議論により、次のような簡略化を行うことができる。

.. math::
    p(\mathbf{x}_{0:T},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1})
    &=
    p(\mathbf{x}_{T}\;|\;\mathbf{x}_{0:T-1},\mathbf{m}_{1:N},\mathbf{u}_{1:T},Z_{0:T-1})\;
    p(\mathbf{x}_{0:T-1},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1}) \\
    &=
    p(\mathbf{x}_{T}\;|\;\mathbf{x}_{T-1},\mathbf{u}_{T})\;
    p(\mathbf{x}_{0:T-1},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1}) \\
    &=
    p(\mathbf{x}_{T}\;|\;\mathbf{x}_{T-1},\mathbf{u}_{T})\;
    p(\mathbf{x}_{0:T-1},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T-1},Z_{0:T-1}) \\

最後の変形は、 :math:`\mathbf{x}_{T-1}` までの姿勢を予測するためには時刻 :math:`T-1` までのIMU観測値があれば十分であることを表している。

これらを総合して式 :eq:`factored-posterior` を再構成すると、時刻 :math:`T-1` における状態分布から時刻 :math:`T` の状態分布を得る式を導くことができる。

.. math::
   p(\mathbf{x}_{0:T}, \mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T}, Z_{0:T})
   &= \eta_{T} \; p(Z_{T}\;|\;\mathbf{x}_{0:T},\mathbf{m}_{1:N},\mathbf{u}_{1:N},Z_{0:T-1})\;p(\mathbf{x}_{0:T},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T},Z_{0:T-1}) \\
   &= \eta_{T} \;
    p(Z_{T}\;|\;\mathbf{x}_{T},\mathbf{m}_{1:N}) \;
    p(\mathbf{x}_{T}\;|\;\mathbf{x}_{T-1},\mathbf{u}_{T})\;
    p(\mathbf{x}_{0:T-1},\mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T-1},Z_{0:T-1}) \\

ある時刻の分布はその前の時刻の分布がわかれば導くことができる。これを繰り返していくと次のようになる。

.. math::
   p(\mathbf{x}_{0:T}, \mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T}, Z_{0:T})
   =\;
    &\eta_{T} \;
    p(Z_{T}\;|\;\mathbf{x}_{T},\mathbf{m}_{1:N}) \;
    p(\mathbf{x}_{T}\;|\;\mathbf{x}_{T-1},\mathbf{u}_{T}) \; \\
    & ... \\
    &\eta_{2} \;
    p(Z_{2}\;|\;\mathbf{x}_{2},\mathbf{m}_{1:N}) \;
    p(\mathbf{x}_{2}\;|\;\mathbf{x}_{1},\mathbf{u}_{2}) \; \\
    &\eta_{1} \;
    p(Z_{1}\;|\;\mathbf{x}_{1},\mathbf{m}_{1:N}) \;
    p(\mathbf{x}_{1}\;|\;\mathbf{x}_{0},\mathbf{u}_{1}) \;
    p(\mathbf{x}_{0}) \\
   =\;
    &\eta_{1:T} \; p(\mathbf{x}_{0})\; \prod_{i=1}^{T} \left[p(Z_{i}\;|\;\mathbf{x}_{i},\mathbf{m}_{1:N}) \; p(\mathbf{x}_{i}\;|\;\mathbf{x}_{i-1},\mathbf{u}_{i})\right] \\
    \text{where}\quad &\eta_{1:T} = \prod_{i=1}^{T} \eta_{i}

この式では時刻 :math:`0` における姿勢の分布を :math:`p(\mathbf{x}_{0})` と置いている。一般的に :math:`\mathbf{x}_{0}` は推定するものではなく基準座標として任意に定めるものであるため、このように置くことができる。ここでは時刻 :math:`0` においてランドマークの座標は全く不明であると仮定しているが、もし何らかの方法でランドマーク座標の分布を事前に得られるのであれば、時刻 :math:`0` の状態分布は :math:`p(\mathbf{x}_{0}, \mathbf{m}_{1:N})` のようになるであろう。

一般的なSLAMの問題ではすべてのランドマークをすべての姿勢から観測できるわけではないため、この仮定を踏まえて上記の式をさらに具体的に次のように書くことができる。

.. math::
   p(\mathbf{x}_{0:T}, \mathbf{m}_{1:N}\;|\;\mathbf{u}_{1:T}, Z_{0:T})
   &=
    \eta_{1:T} \; p(\mathbf{x}_{0})\; \prod_{i=1}^{T} \left[p(Z_{i}\;|\;\mathbf{x}_{i},\mathbf{m}_{1:N}) \; p(\mathbf{x}_{i}\;|\;\mathbf{x}_{i-1},\mathbf{u}_{i})\right] \\
   &=
    \eta_{1:T} \; p(\mathbf{x}_{0})\; \prod_{k=1}^{T} \left[p(\mathbf{x}_{k}\;|\;\mathbf{x}_{k-1},\mathbf{u}_{k})\right] \prod_{(i,j)\in S_{0:T}} p(\mathbf{z}_{ij}\;|\;\mathbf{x}_{i},\mathbf{m}_{j})
   :label: posterior-decomposition

このようにして、 状態分布を推定する問題を、

1. 各時刻におけるオドメトリ :math:`p(\mathbf{x}_{k}\;|\;\mathbf{x}_{k-1},\mathbf{u}_{k}), k = 1,...,T` を推定する問題
2. 各ランドマークの観測値の分布 :math:`p(\mathbf{z}_{ij}\;|\;\mathbf{x}_{i},\mathbf{m}_{j}),\;(i, j) \in S_{0:T}` を求める問題

に変換することができた。

運動モデルによる予測
~~~~~~~~~~~~~~~~~~~~

式 :eq:`posterior-decomposition` において、 :math:`p(\mathbf{x}_{k}\;|\;\mathbf{x}_{k-1},\mathbf{u}_{k})` は、前の時刻の姿勢 :math:`\mathbf{x}_{k-1}` および前の時刻から現在時刻までのIMU観測値 :math:`\mathbf{u}_{k}` を得たときの現在の姿勢の分布である。すなわち、前の時刻の姿勢と姿勢変化の値(あるいは入力値)に基づいた現在の姿勢の予測を表現している。なお、センサ構成によっては :math:`\mathbf{u}_{k}` をアクセルへの入力値や車輪の回転数などとすることもある。

時刻 :math:`k` の姿勢 :math:`\mathbf{x}_{k}` に対して運動モデルの予測 :math:`\mathbf{g}(\mathbf{x}_{k-1}, \mathbf{u}_{k})` の誤差が分散 :math:`Q_{k} \in \mathbb{R}^{6 \times 6}` の正規分布に従うとすると、この分布は

.. math::
    p(\mathbf{x}_{k}\;|\;\mathbf{x}_{k-1},\mathbf{u}_{k}) =
    \frac{1}{\sqrt{(2\pi)^{6} \det(Q_{k})}}
    \exp(-\frac{1}{2}
    \left[\mathbf{x}_{k} - \mathbf{g}(\mathbf{x}_{k-1}, \mathbf{u}_{k})\right]^{\top} Q_{k}^{-1} \left[\mathbf{x}_{k} - \mathbf{g}(\mathbf{x}_{k-1}, \mathbf{u}_{k})\right])

と記述することができる。

観測モデルによる予測
~~~~~~~~~~~~~~~~~~~~

| たとえば時刻 :math:`i` において3番目のランドマーク :math:`\mathbf{m}_{j}` が観測できたとしよう。このランドマークの観測値 :math:`\mathbf{z}_{ij} \in \mathbb{R}_{2}` に対して投影モデル :math:`\mathbf{h}(\mathbf{x}_{i},\mathbf{m}_{j})` を用いて分散 :math:`R_{ij} \in \mathbb{R}^{2\times2}` の正規分布でモデル化すると、

.. math::
    p(\mathbf{z}_{ij} \;|\; \mathbf{x}_{i}, \mathbf{m}_{j}) = \frac{1}{\sqrt{(2\pi)^{2}\det(R_{ij})}}\exp\{-\frac{1}{2}\left[\mathbf{z}_{ij} - \mathbf{h}(\mathbf{x}_{i},\mathbf{m}_{j})\right]^{\top}R_{ij}^{-1}\left[\mathbf{z}_{ij} - \mathbf{h}(\mathbf{x}_{i},\mathbf{m}_{j})\right]\}

となる。この分布は


.. math::
   \mathbf{r} =
   \begin{bmatrix}
        \mathbf{x}_{0} - \mathbf{0} \\
        \mathbf{x}_{1} - \mathbf{g}(\mathbf{x}_{0}, \mathbf{u}_{0}) \\
        \mathbf{x}_{2} - \mathbf{g}(\mathbf{x}_{1}, \mathbf{u}_{1}) \\
        \mathbf{x}_{3} - \mathbf{g}(\mathbf{x}_{2}, \mathbf{u}_{2}) \\
        \mathbf{x}_{4} - \mathbf{g}(\mathbf{x}_{3}, \mathbf{u}_{3}) \\
        \mathbf{z}_{11} - \mathbf{h}(\mathbf{x}_{1}, \mathbf{m}_{1}) \\
        \mathbf{z}_{21} - \mathbf{h}(\mathbf{x}_{2}, \mathbf{m}_{1}) \\
        \mathbf{z}_{22} - \mathbf{h}(\mathbf{x}_{2}, \mathbf{m}_{2}) \\
        \mathbf{z}_{32} - \mathbf{h}(\mathbf{x}_{3}, \mathbf{m}_{2}) \\
        \mathbf{z}_{42} - \mathbf{h}(\mathbf{x}_{4}, \mathbf{m}_{2}) \\
    \end{bmatrix} \\

.. math::
    \mathbf{x} = \left[
        \mathbf{x}_{0}^{\top}\quad
        \mathbf{x}_{1}^{\top}\quad
        \mathbf{x}_{2}^{\top}\quad
        \mathbf{x}_{3}^{\top}\quad
        \mathbf{x}_{4}^{\top}\quad
        \mathbf{m}_{1}^{\top}\quad
        \mathbf{m}_{2}^{\top}\quad
    \right]^{\top}


.. math::
    J = \begin{bmatrix}
     I     &          &          &          &          &          &          \\
    -G_{0} & I        &          &          &          &          &          \\
           & -G_{1}   & I        &          &          &          &          \\
           &          & -G_{2}   & I        &          &          &          \\
           &          &          & -G_{3}   & I        &          &          \\
           & -H_{x11} &          &          &          & -H_{m11} &          \\
           &          & -H_{x21} &          &          & -H_{m21} &          \\
           &          & -H_{x22} &          &          &          & -H_{m22} \\
           &          &          & -H_{x32} &          &          & -H_{m32} \\
           &          &          &          & -H_{x42} &          & -H_{m42} \\
    \end{bmatrix}


.. math::
    J^{\top} J =
   \begin{bmatrix}
    D_{0}  & -G_{0}                &                       &                       &                        &                       &                       \\
    -G_{0} & D_{1}                 &                       &                       &                        & H_{x11}^{\top}H_{m11} &                       \\
           & -G_{1}                & D_{2}                 & -G_{2}                &                        & H_{x21}^{\top}H_{m21} & H_{x22}^{\top}H_{m22} \\
           &                       & -G_{2}                & D_{3}                 & -G_{3}                 &                       & H_{x32}^{\top}H_{m32} \\
           &                       &                       & -G_{3}                & D_{4}                  &                       & H_{x42}^{\top}H_{m42} \\
           & H_{m11}^{\top}H_{x11} & H_{m21}^{\top}H_{x21} &                       &                        & D_{5}                 &                       \\
           &                       & H_{m22}^{\top}H_{x22} & H_{m22}^{\top}H_{x22} &  H_{m42}^{\top}H_{x42} &                       & D_{6}                 \\
   \end{bmatrix}

.. math::
    D_{0} &= I - 2G_{0} + G_{0}^{\top}G_{0}  \\
    D_{1} &= I - 2G_{1} + G_{1}^{\top}G_{1} + H_{x11}^{\top}H_{x11}  \\
    D_{2} &= I - 2G_{2} + G_{2}^{\top}G_{2} + H_{x21}^{\top}H_{x21} + H_{x22}^{\top}H_{x22}  \\
    D_{3} &= I - 2G_{3} + G_{3}^{\top}G_{3} + H_{x32}^{\top}H_{x32} \\
    D_{4} &= I + H_{x42}^{\top}H_{x42} \\
    D_{5} &= H_{m11}^{\top}H_{m11} + H_{m21}^{\top}H_{m21} \\
    D_{6} &= H_{m22}^{\top}H_{m22} + H_{m32}^{\top}H_{m32} + H_{m42}^{\top}H_{m42} \\


.. [#sfm] Structure from Motion と呼ばれる
.. [#simplify_z_distribution] もし、たとえば時刻 :math:`T` において1番目と3番目のランドマークしか観測できないのであれば、 :math:`Z_{T} = \{\mathbf{z}_{T1},\mathbf{z}_{T3}\}` は :math:`\mathbf{x}_{T},\mathbf{m}_{1},\mathbf{m}_{3}` にしか依存しないので :math:`p(Z_{T}\;|\;\mathbf{x}_{0:T},\mathbf{m}_{1:N},\mathbf{u}_{1:N},Z_{0:T-1}) = p(Z_{T}\;|\;\mathbf{x}_{T},\mathbf{m}_{1},\mathbf{m}_{3})` とするべきであるが、ここでは表記の都合上すべてのランドマークを対象として :math:`\mathbf{m}_{1:N}` としている。
