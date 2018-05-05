Ishita Takeshi
==============

# Education
Bachelor student of National Institute of Technology, Tokyo College, Advanced Course.  

National Institute of Technology, Tokyo College  
April 2012 - March 2017  

National Institute of Technology, Tokyo College, Advanced Course  
April 2017 - March 2019  

# Work Experience

## [Usagee Inc.](http://usagee.co.jp/)
* Research and develop Machine Learinng & Computer Vision models
* Provide effective solutions to customers by implementing models & systems

May 2014 - present  
Part-time job  

## [Cookpad Inc.](https://info.cookpad.com/en)
Designed and implemented a machine learning model for ingredient recognition from food images.

### Patent
The model I proposed is granted as a patent [#6306770](https://www.j-platpat.inpit.go.jp/web/PU/JPB_6306770/062D067C8381CD29700292EC1ED536D9).

Dec 2016 - Jul 2017  
Part-time job  

# My works
My works are available on [GitHub](https://github.com/IshitaTakeshi)

## 1. [RoadDamageDetector](https://github.com/IshitaTakeshi/RoadDamageDetector)
This is the most recent work, a road damage detector based on SSD(Single Shot Multibox Detector). The detailed explanation is at [my Qiita blog page (in Japanese)](https://qiita.com/IshitaTakeshi/items/915de731d8081e711ae5). The source code is published along with trained models.  

### What I did

* Trained SSD(VGG16) on the RoadDamageDataset
* Investigated how to improve the detection speed while keeping the accuracy
* Replaced VGG16 with ResNet-101

### Details
I implemented a road damage detection system following a paper by [Maeda Hiroya, et al. (2018)](https://arxiv.org/pdf/1801.09454.pdf) using SSD based on VGG16, which is the default setting of ChainerCV, a computer vision library built on top of Chainer.  

After setting the result of the SSD on VGG16 as the baseline, I investigated how to improve the performance of the detector. There were 3 main aspects to improve:

* Improving detection accuracy
* Improving detection speed
* Decreasing the model size

Since the model size was not an imminent issue, I decided to improve the other two aspects. I found a paper by [Jonathan et al. (2017)](https://arxiv.org/abs/1611.10012) and learned that if the base network is replaced with ResNet-101 the detection speed would increase without losing the accuracy.  

I replaced the base network with ResNet-101. The detection accuracy didn't reach the level of SSD(VGG16) because of the insufficient number of training samples, but I could confirm that the detection speed improved 3 times faster on CPU.  

## 2. [Ensemble PCANet](https://github.com/IshitaTakeshi/PCANet/tree/ensemble)
### PCANet
PCANet is a deep learning network for image classification, which can train very quickly because of the feature that calculates filter weights with PCA.  
Since there was no function support of histogram calculation in CuPy, I implemented histogram calculation in CUDA and sent a pull request, and thankfully CuPy commenters wrote tests and merged into master. [#298](https://github.com/cupy/cupy/pull/298)  

### Ensemble PCANet
I focused on the feature that PCANet can be trained very quickly but the representation ability is relatively low compared to "strong" networks such as Network in Network, I combined PCANet with Bagging and succeeded to increase the representation ability while keeping the speed of training, and presented the idea in JSAI2017.  

## 3. [SCW](https://github.com/IshitaTakeshi/SCW)
SCW (Soft Confidence-Weighted Learning) is a light-weight online linear classifier. It was my first experience of reimplementing Machine Learning algorithms from a paper.  
This is implemented in Julia as well: [repository](also://github.com/IshitaTakeshi/SoftConfidenceWeighted.jl).  

## 4. [KanaKanjiConversion](https://github.com/IshitaTakeshi/KanaKanjiConversion)
Kana-kanji conversion is a conversion method from kana characters to kanji characters, the most common way of inputting Japanese text from keyboards, which is realized by Machine Learning.  
The current input method solves the conversion problem by constructing a lattice (a graph in a mathematical context), weighting edges with word-class bigrams, and finding the maximum (minimum) cost path. However, in this method, the model can consider only 2 successive words so the conversion performance is limited.  
I proposed a method that considers cooccurrences of words without consuming memory usage by managing the cooccurrences with a sparse matrix. This idea is applied to IPA Super Creator Program.
Currently, this project is pending because I'm working on many other projects.  

### [DTrie](https://github.com/IshitaTakeshi/DTrie)
Kana-kanji converter is required to reduce memory usage as much as possible since no one uses it if the converter consumes 1GB of RAM. Therefore the dictionary used in the converter has to hold data in a compressed form via a succinct data structure.  
I implemented the Trie data structure to represent the dictionary in the D language and made it a package.  
