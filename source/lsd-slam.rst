Notations
----------------

:math:`\dot{\mathbf{x}}` is the homogeneous representation of :math:`\mathbf{x}`.

Depth estimation
----------------

Calculation of :math:`alpha`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the computation below, we assume that coordinates are on the normalized image plane, which implies that the intrinsic matrix is identity (:math:`K = I`).

:math:`\mathbf{x}_{r}` is the matched coordinate on the reference frame.


We set :math:`x_{r}(\lambda) = x_{r}^{0} + \lambda \bar{x}_{r},\,\lambda \in \mathbb{R}`, where :math:`x_{r}^{0}` is the starting point of the epipolar line search, :math:`\bar{x}_{r}` is the search step.

We know that :math:`x_{r}(\lambda)` and :math:`x_{k}` are unther the epipolar constraint. Therefore,

.. math::
    x_{r}(\lambda) = \frac{
        [\frac{1}{d^{-1}_{k}}R \dot{\mathbf{x}}_k + \mathbf{t}]_{x}
    }{
        [\frac{1}{d^{-1}_{k}}R \dot{\mathbf{x}}_k + \mathbf{t}]_{z}
    }
    :label: reprojection

We regard :math:`d^{-1}_{k}` as a function of :math:`\lambda`, i.e. :math:`d^{-1}_{k} = d^{-1}_{k}(\lambda)`. Then :math:`\alpha` is the derivative of :math:`d^{-1}_{k}(\lambda)` with respect to :math:`\lambda`.
Solving the equation :eq:`orthogonality` for :math:`d^{-1}_{k}`, we get

.. math::
    d^{-1}_{k}(\lambda) = \frac{
        x_{r} \mathbf{r}_3^{\top} \dot{\mathbf{x}}_k + \mathbf{r}_1^{\top}\dot{\mathbf{x}}_k
    }{
        t_{x} - x_{r} t_{z}
    }

:math:`\mathbf{r}_i^{\top},\,i \in {1,2,3}` is the :math:`i`-th row of the rotation matrix :math:`R`.

Deriving by :math:`\lambda`, we get :math:`\alpha`.

.. math::
    \alpha
    = \frac{\partial d^{-1}_{k}}{\partial \lambda}
    = \bar{x}_{r} \frac{
        t_{x} \mathbf{r}_{3}^{\top} \dot{\mathbf{x}}_k -
        t_{z} \mathbf{r}_{1}^{\top} \dot{\mathbf{x}}_k
    }{
        (t_{x} - x_{r} t_{z}) ^ 2
    }


Geometric disparity error
-------------------------

As the original paper suggests, we can approximately write

.. math::
    \mathbf{l}_0 + \lambda^{*} \begin{bmatrix} l_x \\ l_y \end{bmatrix}
    = \mathbf{g}_0 + \gamma \begin{bmatrix} -g_y \\ g_x \end{bmatrix}


We set :math:`\mathbf{l} = \begin{bmatrix} l_x \\ l_y \end{bmatrix}` and multiply :math:`\mathbf{g} = \begin{bmatrix} g_x \\ g_y \end{bmatrix}` from the right side. Then we get


.. math::
    (\mathbf{l}_0 + \lambda^{*} \cdot \mathbf{l})^{\top} \mathbf{g}
    &= \mathbf{g}_{0}^{\top} \mathbf{g} \\
    \lambda^{*} \cdot \mathbf{l}^{\top} \mathbf{g}
    &= (\mathbf{g}_0 - \mathbf{l}_0)^{\top} \mathbf{g}\\

Now we can regard :math:`\lambda^{*}` as a function of :math:`\mathbf{l}_0`

.. math::
    \lambda^{*} = \lambda^{*}(\mathbf{l}_0) = \frac{
        (\mathbf{g}_0 - \mathbf{l}_0)^{\top}\mathbf{g}
    }{
        \mathbf{l}^{\top} \mathbf{g}
    }


We can calculate the variance of :math:`\lambda^{*}(\mathbf{l}_0)` from the propagation rule.

.. math::
    \sigma^{2}_{\lambda^{*}(\mathbf{l}_0)}
    &= J_{\mathbf{\lambda}^{*}(\mathbf{l}_0)}
    \begin{bmatrix}
        \sigma^{2}_{l} & 0 \\
        0 & \sigma^{2}_{l}
    \end{bmatrix}
    J^{\top}_{\mathbf{\lambda}^{*}(\mathbf{l}_0)} \\
    J_{\lambda^{*}(\mathbf{l}_0)}
    &= \frac{\mathbf{g}}{\mathbf{g}^{\top} \mathbf{l}} \\
    \sigma^{2}_{\lambda^{*}(\mathbf{l}_0)}
    &= \frac{
        \sigma^{2}_{l} ||\mathbf{g}||^{2}
    }{
        (\mathbf{g} \cdot \mathbf{l})^{2}
    }



Photometric disparity error
---------------------------

.. math::
    \mathbf{\lambda}^{*}(I) = \mathbf{\lambda}_0 + \frac{i_{ref} - I_{p}(\mathbf{\lambda}_0)}{g_{p}}

.. math::
    \sigma^{2}_{\mathbf{\lambda}^{*}(I)} &=
    J_{\mathbf{\lambda}^{*}(I)}
    \begin{bmatrix}
        \sigma^{2}_{i} & 0 \\
        0 & \sigma^{2}_{i}
    \end{bmatrix}
    J^{\top}_{\mathbf{\lambda}^{*}(I)}

.. math::
    J_{\mathbf{\lambda}^{*}(I)} = g^{-1}_{p}

.. math::
    \sigma^{2}_{\mathbf{\lambda}^{*}(I)} =
        \frac{\sigma^{2}_{i}}{||g_{p}||^{2}}

