========
About Me
========

The oline version can be found at http://ishitatakeshi.netlify.com/about-me

Education
=========

| National Institute of Technology, Tokyo College
| April 2012 - March 2017

| National Institute of Technology, Tokyo College, Advanced Course
| April 2017 - March 2019

Work Experience
===============

`Usagee Inc. <http://usagee.co.jp/>`__
--------------------------------------

-  Research and development of Machine Learinng & Computer Vision models
-  Provideing effective solutions to customers by implementing models & systems

| May 2014 - 2017
| Part-time job

`Cookpad Inc. <https://info.cookpad.com/en>`__
----------------------------------------------

Design and implementation of machine learning models for ingredient recognition from food images.

Patent
~~~~~~

The model I proposed is granted as a patent
`#6306770 <https://www.j-platpat.inpit.go.jp/web/PU/JPB_6306770/062D067C8381CD29700292EC1ED536D9>`__.

| Dec 2016 - Jul 2017
| Part-time job

`DeNA Co., Ltd. <https://dena.com/>`__
--------------------------------------

| April 2017 - present
| Part-time job

`Mitou Program <https://www.ipa.go.jp/jinzai/mitou/2019/gaiyou_s-2.html>`__
---------------------------------------------------------------------------

Development of a Visual SLAM framework

| April 2019 - present


My works
========

My works are available on `GitHub <https://github.com/IshitaTakeshi>`__

1. `Tadataka (under development) <https://github.com/IshitaTakeshi/Tadataka>`__
-------------------------------------------------------------------------------

This project aims to develop a Visual SLAM framework that is flexible and simple to use.

Currently implemented algorithms:

DVO (Dense Visual Odometry)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/oDgBgdHUwOM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


Feature Based Visual Odometry
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. raw:: html

    <iframe width="560" height="315" src="https://www.youtube.com/embed/h4KrMJQDoX4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


2. `RoadDamageDetector <https://github.com/IshitaTakeshi/RoadDamageDetector>`__
-------------------------------------------------------------------------------

.. image:: images/road-damage-1.png
    :width: 800

Road damage detector based on SSD(Single Shot Multibox Detector).
The detailed explanation is at `my Qiita blog page (in Japanese) <https://qiita.com/IshitaTakeshi/items/915de731d8081e711ae5>`__.
Trained models are published along with the source code.

What I did
~~~~~~~~~~

-  Trained SSD(VGG16) on the RoadDamageDataset provided by Maeda et al. (2018) [#Maeda_et_al_2018]_
-  Replaced VGG16 with ResNet-101 and evaluated the performance


3. `PCANet <https://github.com/IshitaTakeshi/PCANet>`__
-------------------------------------------------------

| PCANet is a deep learning network for image classification that trains its weights with PCA,  which is proposed by Chan et al. (2015) [#Chan_et_al_2015]_.
| PCANet requires histogram calculation in the pooling layer, although there was no function support in CuPy.
| I implemented the histogram calculation in CUDA and sent a pull request, and this has been merged into the CuPy repository.
  `#298 <https://github.com/cupy/cupy/pull/298>`__

`Ensemble PCANet <https://github.com/IshitaTakeshi/PCANet/tree/ensemble>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PCANet can train quickly wihle representation ability is not strong. I combined PCANet with Bagging and succeeded to increase the representation ability while keeping the training speed.

4. `SCW <https://github.com/IshitaTakeshi/SCW>`__
-------------------------------------------------
| Reimplementation of SCW (Soft Confidence-Weighted Learning) [#Wang_et_al_2012]_.
| SCW is an online supervised learning algorithm which utilizes all the four salient properties:

- Large margin training
- Confidence weighting
- Capability to handle non-separable data
- Adaptive margin

5. `DTrie <https://github.com/IshitaTakeshi/DTrie>`__
-----------------------------------------------------
The Trie data structure in D language.

.. [#Maeda_et_al_2018] Maeda, Hiroya, et al. "Road damage detection using deep neural networks with images captured through a smartphone." arXiv preprint arXiv:1801.09454 (2018).
.. [#Chan_et_al_2015] Chan, Tsung-Han, et al. "PCANet: A simple deep learning baseline for image classification?." IEEE transactions on image processing 24.12 (2015): 5017-5032.
.. [#Wang_et_al_2012] Wang, Jialei, Peilin Zhao, and Steven CH Hoi. "Exact soft confidence-weighted learning." arXiv preprint arXiv:1206.4612 (2012).
