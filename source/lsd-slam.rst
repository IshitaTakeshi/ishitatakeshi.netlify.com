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
