
This code accompanies the paper

**Wadim Kehl, Fabian Manhardt, Federico Tombari, Slobodan Ilic and Nassir Navab:
SSD-6D: Making RGB-Based 3D Detection and 6D Pose Estimation Great Again. ICCV 2017.**  [(PDF)]( https://www.dropbox.com/s/k6cbdb77d6ubdfv/kehl2017iccv.pdf?dl=0)

![](http://5b0988e595225.cdn.sohucs.com/images/20181013/208d8a02440549ad827704fe0b7c2ad7.jpeg)


    SSD式网络预测的示意图：网络输入299×299 RGB图像，并使用InceptionV4的分支从输入图像生成六个不同比例的特征图。
    然后将每个特征图与经过训练的形状预测核（4 + C + V + R）进行卷积，以确定物体的类别、
    2D边界框、可能的视点的得分和平面内旋转（为构建6D姿势假设）。
    因此，C表示物体的类别数，V表示视点的数量，R表示平面内旋转的类别的数量。
    其他4个值用于细化离散边界框的角以紧密地拟合检测到的对象。

    我们的基础网络来自预先训练的InceptionV4实例[27]并送入彩色图像（调整大小为299×299）以计算多个尺度的特征图。 
    为了获得我们的第一个维数特征图71×71×384，我们在stem内的最后一个池化层之前分支并附加一个'inception-A'块。
    此后，我们在“inception-A”块之后连续分支出35×35×384特征图，在“inception-B”块之后为17×17×1024
    特征图和“inception-C”块之后为9×9×1536特征图。 
    为了覆盖更大规模的物体，我们将网络扩展到另外两个部分。 
    首先，'Reduction-B'后跟两个'Inception-C'块以输出5×5×1024的特征图。 
    第二，一个'Reduction-B'和一个'Inception-C'产生一个3×3×1024的特征图。


    从这里我们遵循SSD的范例。具体而言，这六个特征图中的每一个都与预测内核卷积，
    预测内核应该从特征映射位置回归局部检测。设（ws，hs，cs）为度量s处的宽度、高度和通道深度。
    对于每个尺度，我们训练一个3×3×cs内核，为每个特征图位置提供对象ID、离散视点和平面内旋转的分数。
    由于我们通过此网格引入了离散化误差，因此我们在每个具有不同宽高比的位置创建Bs个边界框。
    此外，我们对其四个角落的细化进行了回归。如果C，V，R分别是对象类别，采样视点和平面内旋转的数量，
    我们为尺度s产生（ws，hs，Bs×（C + V + R + 4））检测图。该网络具有不同形状和大小的总共21222个可能的边界框。
    虽然这可能看起来很多，但由于全卷积设计和良好的真实负行为(?)，我们的方法的实际运行时间非常低，参考图1的示意图。

    Viewpoint scoring versus pose regression:对姿势回归的视点分类的选择是有意的。 
    虽然存在直接旋转回归的工作[19,28]，但早期的实验清楚地表明，分类方法对于检测姿势的任务更可靠。 
    特别是，layers在评估离散视点方面比在输出精确的数字化的平移和旋转方面做得更好。 
    在视点和平面内旋转中的6D姿势的分解是优雅的，并且允许我们更自然地解决问题。
    当新视点呈现新的视觉结构时，平面内旋转视图是同一视图的非线性变换。 
    此外，所有视图的同时评分允许我们解析给定图像位置处的多个检测，例如， 通过接受高于某个阈值的所有视点。
    同样重要的是，这种方法使我们能够以直接的方式处理相似的外观或对称性。



and allows to reproduce parts of our work. Note that due to IP issues we can only 
provide our trained networks and the inference part. This allows to produce the detections and 
the 6D pose pools. **Unfortunately, the code for training as well as 2D/3D refinement cannot be made available.**

In order to use the code, you need to download
* the used datasets (hinterstoisser, tejani) in 
SIXD format (e.g. from [here](http://cmp.felk.cvut.cz/sixd/challenge_2017/) )
* the trained networks from [here](https://www.dropbox.com/sh/08mv6fmzi6dylj2/AACIK-odLEu560UDUs2WY9Sba?dl=0)

and use the run.py script to do the magic. Invoke 'python3 run.py --help' to see the available commands. 
For the correct thresholds you should look at the supplemental material.

Fabian Manhardt now also added a benchmark.py that allows you to properly run the evaluation on a sequence and produce metrics. Note, though, that these numbers are without the final pose refinement.






