Graph SLAM
==========

Graph SLAMとは
--------------

Graph SLAMとは、SLAMの最適化問題をグラフ構造で表現する手法である。
カメラ姿勢をノードで表現すると、異なる2時刻の姿勢関係をそれらのノードをつなぐエッジで表現することができる。
ランドマークもノードで表現することができる。これにより、ランドマークとカメラ姿勢の関係をこれらをつなぐエッジで表現することができる。

Graph SLAMによる姿勢推定および地図作成
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| Graph SLAM ではグラフの各エッジに誤差関数を対応させ、すべてのエッジの誤差関数の総和を最小化することで最適な姿勢とランドマーク座標を求める。
| たとえば時刻 :math:`i` の姿勢を :math:`x_{i}` とし、 :math:`i` から  :math:`i+1` までのIMU積分値 :math:`\mathbf{u}_{i+1}` およびオドメトリ推定モデル :math:`\mathbf{g}(\mathbf{x}_{i}, \mathbf{u}_{i+1})` を用いてオドメトリ誤差

.. math::
    || \mathbf{x}_{i+1} - \mathbf{g}(\mathbf{x}_{i}, \mathbf{u}_{i+1}) ||^2

を構成することができる。これを :math:`\mathbf{x}_{i}` と  :math:`\mathbf{x}_{i+1}` の間のエッジに対応させる。


また、 :math:`j` 番目のランドマークを :math:`\mathbf{m}_{j}` とし、投影モデル :math:`\mathbf{h}(\mathbf{x}_{i}, \mathbf{m}_{j})` を用いて再投影誤差

.. math::
    || \mathbf{x}_{i+1} - \mathbf{g}(\mathbf{x}_{i}, \mathbf{u}_{i+1}) ||^2

を計算することができる。これを :math:`\mathbf{x}_{i}` と  :math:`\mathbf{m}_{j}` の間のエッジに対応させる。

これら誤差関数の総和を最小化するエッジの値 :math:`\{\mathbf{x}_{0}, \mathbf{x}_{1}, \mathbf{x}_{2}, \mathbf{x}_{3}, \mathbf{x}_{4}, \mathbf{m}_{1}, \mathbf{m}_{2}\right}` を求める。これが Graph SLAM による姿勢推定および地図作成の問題である。

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
