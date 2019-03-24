EMアルゴリズムによる多変量t分布のパラメータ推定
===============================================


動機
====

正規分布の最尤推定問題は最小二乗法に帰着される．しかしデータとして正規分布を仮定してしまうと，モデルパラメータの推定結果が外れ値の影響を大きく受けてしまう．正規分布は裾野が小さいためである．
正規分布の代わりにt分布をモデルとして用いると，t分布は裾野が広いため，正規分布よりもロバストな推定を行えることが知られている．

問題点
------

最尤推定は一般的に対数尤度関数を用意し，これを微分して0とおくことでパラメータを推定する．しかし，t分布に対してこれを行うと，閉じた解が得られない．
したがって，t分布に対する最尤推定にはEMアルゴリズムを用いる．

EMアルゴリズム
--------------

EMアルゴリズムは潜在変数を導入することで

.. math::
    \def\Expectation{{\operatorname{E}}}

.. math::
    \begin{align}
        Q(\theta^{(t)}, \theta^{(t-1)})
        &=\sum_{i=1}^{N} \int_{u} p(u \mid \mathbf{x}_{i}; \mathbf{\theta}^{(t-1)}) \log p(\mathbf{x}_{i}, u; \theta^{(t)}) du \\
        &=\sum_{i=1}^{N} \int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2})
        \left[
            C - \frac{1}{2}\log(|\Sigma^{(t)}|) + (\frac{\nu + p}{2} - 1)\log(u)
            - \frac{u}{2}(\nu + s_{i}^{(t)})
        \right] du \\
    \end{align}

.. math::
    \begin{align}
    C &= \frac{\nu}{2} \log(\frac{\nu}{2}) - \frac{p}{2}\log(2\pi) - \log(\Gamma(\frac{\nu}{2})) \\
    s_{i}^{(t)} &= (\mathbf{x}_{i} - \mathbf{\mu}^{(t)})^{\top} {\Sigma^{(t)}}^{-1} (\mathbf{x}_{i} - \mathbf{\mu}^{(t)})
    \end{align}

.. math::
    \begin{alignat}{3}
        Q(\theta^{(t)}, \theta^{(t-1)})
        &=\sum_{i=1}^{N}\Big[&(C - \frac{1}{2}\log(|\Sigma^{(t)}|)) &\int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2}) \,            du \\
        &                    &+ (\frac{\nu + p}{2} - 1)             &\int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2}) \, \log(u) \, du \\
        &                    &- \frac{1}{2}(\nu + s_{i}^{(t)})      &\int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2}) \, u       \, du \Big] \\
    \end{alignat}

.. math::
    1                                                           &= \int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2}) \,            du \\
    \Expectation[\log(u) \mid \mathbf{x}_{i};\, \theta^{(t-1)}] &= \int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2}) \, \log(u) \, du \\
    \Expectation[u       \mid \mathbf{x}_{i};\, \theta^{(t-1)}] &= \int_{u} \Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2}) \, u       \, du \\

.. math::
    \begin{alignat}{3}
        Q(\theta^{(t)}, \theta^{(t-1)})
        &=\sum_{i=1}^{N}\Big[& C - \frac{1}{2}\log(|\Sigma^{(t)}|)
        &                    &+ (\frac{\nu + p}{2} - 1)             &\Expectation[\log(u) \mid \mathbf{x}_{i};\, \theta^{(t-1)}]
        &                    &- \frac{1}{2}(\nu + s_{i}^{(t)})      &\Expectation[u       \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \Big] \\
    \end{alignat}


:math:`\theta^{(t)} = (\mathbf{\mu}^{(t)}, \Sigma^{(t)})` で微分すると

:math:`\Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}]` は定数とみなせるので

 .. math::
    \begin{align}
        \frac{\partial Q}{\partial \mathbf{\mu}^{(t)}}
        &= - \frac{1}{2} \sum_{i=1}^{N} \, \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \, \frac{\partial s_{i}^{(t)}}{\partial \mathbf{\mu}^{(t)}} \\
        &= - \sum_{i=1}^{N} \, \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \, {\Sigma^{(t)}}^{-1} (\mathbf{x}_{i} - \mathbf{\mu}^{(t)}) \\
        &= - {\Sigma^{(t)}}^{-1} \sum_{i=1}^{N} \, \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \, (\mathbf{x}_{i} - \mathbf{\mu}^{(t)}) \\
        &= \mathbf{0} \\
    \end{align}

 .. math::
    \begin{align}
        \mathbf{\mu}^{(t)}
        &= \frac{\sum_{i=1}^{N} \, \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \, \mathbf{x}_{i}}
                {\sum_{i=1}^{N} \, \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}]} \\
    \end{align}

.. math::
    \begin{align}
        \frac{\partial Q}{\partial \Sigma^{(t)}}
        &= -\frac{N}{2} \frac{\partial}{\partial \Sigma^{(t)}}\log(|\Sigma^{(t)}|)
           - \frac{1}{2} \sum_{i=1}^{N} \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \, \frac{\partial s_{i}^{(t)}}{\partial \Sigma^{(t)}} \\
        &= -\frac{N}{2} {\Sigma^{(t)}}^{-1}
           - \frac{1}{2} \sum_{i=1}^{N}
            \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \,
            {\Sigma^{(t)}}^{-1} (\mathbf{x}_{i} - \mathbf{\mu}^{(t)}) (\mathbf{x}_{i} - \mathbf{\mu}^{(t)})^{\top} {\Sigma^{(t)}}^{-1} \\
        &= 0
    \end{align}

.. math::
    \begin{align}
        \Sigma^{(t)}
        &= \frac{1}{N} \sum_{i=1}^{N}
           \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] \,
           (\mathbf{x}_{i} - \mathbf{\mu}^{(t)}) (\mathbf{x}_{i} - \mathbf{\mu}^{(t)})^{\top}
    \end{align}

:math:`u` given :math:`X` はガンマ分布 :math:`\Gamma(\frac{\nu + p}{2}, \frac{\nu + s_{i}^{(t-1)}}{2})` に従う．

:math:`\Gamma(\alpha, \beta)` の期待値は :math:`\alpha / \beta` なので，

.. math::
    \Expectation[u \mid \mathbf{x}_{i};\, \theta^{(t-1)}] = \frac{\nu + p}{\nu + s_{i}^{(t-1)}}

である．


**Expectation**


以上より，多変量t分布に対するEMアルゴリズムを導くことができた．



