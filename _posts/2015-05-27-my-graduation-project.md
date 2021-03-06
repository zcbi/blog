---
layout: post
title: "我的毕设论文草稿"
date: 2015-05-27
categories: 视频编码
---

## 1 绪论

### 1.1 课题研究背景与意义

视觉特征能够简明、有效地表达图像的底层视觉内容，并且在许多全局或局部变换中具有稳定性或变换无关性，因此被广泛地应用在许多领域，诸如图像/视频检索、目标识别和目标追踪等。许多视觉分析任务需要在带宽有限的网络中传输视觉特征，这就要求在达到目标分析效率水平的前提下，尽可能地降低比特预算。

传统的视觉分析应用方案，被称为“Compress-Then-Analysis”（CTA）。CTA基于以下处理步骤：首先，由传感器获取信号数据（静态图像或视频序列），随后信号数据被压缩后经过网络传输到终端节点，并在终端节点进行最后的视觉分析工作。由于获取的信号数据首先进行了有损压缩，因此视觉特征的分析工作建立在有损的数据内容之上，这可能会带来视觉分析工作的准确度和效率的降低[^badeffort1] [^badeffort2]。另外，尽管上述框架已在许多领域得到了高效利用，但仍有大量的视觉分析工作需要传输高质量视觉内容，这在目前的带宽限制下，即使是最先进的分布式视觉分析技术Visual Wireless Senior Networks(VWSNs)，也无法胜任。

在这样的背景之下，另一种被称为“Analysis-Then-Analysis”（ATC）的视觉分析应用方案成为了研究热点。ATC方案基于下述事实，即一些视觉分析工作仅依赖于信号局部特征对视觉内容的简要表达，而不需要像素级别的视觉内容表达。因此理论上ATC方案可以显著降低带宽需求。在ATC方案中，首先由传感器获取到原始数据，随后从原始数据中直接提取视觉特征并进行压缩传输。因为通常网络环境对于带宽有严格的限制，所以对视觉特征的高效压缩便成为了实现ATC方案的关键点。

本文正是基于上述对视觉特征压缩的需求,设计适于特征压缩的熵编码方案, 以期获得明显的压缩性能提升。其研究意义主要有以下几个方面:

- 提高特征压缩效率，可大幅度减少传输视觉特征所需的数据量，实现在有限带宽网络环境中的快速传输；与传输整个视频流相比，传输经高效压缩后的视觉特征占用更少的带宽，同时显著降低了等待数据传输造成的终端视觉分析工作的延迟，这对未来大数据下进行海量数据的特征分析工作提供了有力的支持；
- 本文基于SIFT这一特定视觉特征进行研究，但文章的思路并不仅仅限于某一局部特征，而可以很容易的扩展到其他特征；通过本文对视觉特征的熵编码的方案设计，可以为其他的视觉特征的压缩方案提供借鉴；


### 1.2 国内外研究概况

目前国内外的研究工作大多面向静态图像的特征提取与特征压缩，提出了许多高效的视觉特征编码方案[^14] [^15] [^16] [^17]以及更利于特征压缩的局部特征提取算法[^18] [^19]。

对于视觉特征的局部描述子压缩的相关研究主要有以下几种。一种思路是降维。降维工作的首次尝试是PCA-SIFT[^pca-sift] ，Ke和Sukthankar在梯度邻域上引入了广泛用于降维的主成分分析法(principal component analysis,PCA)，代替固定权重方向直方图规定化梯度，只保留方差较大的维度，获得了更加简洁、稳定的描述子。相较于128维的SIFT，PCA-SIFT的维数只有20甚至更少，大大降低了特征表达的数据量。另外，Gang Hua等人将线性判别分析（linear discriminant analysis，LDA）引入图像局部特征的学习中，利用监督信息将描述子投影到低维空间，达到较小的类内散度和较大的类间散度，这样匹配的描述子之间距离较小，而非匹配的描述子被分离[^hua]。第二类以量化为基础，比较具有代表性的工作是Chandrasekhar等人提出的压缩的梯度直方图描述（com-pressed histogram of gradient，CHoG），通过霍夫曼树编码压缩量化后的梯度直方图，可以显著降低编码比特[^chog]。第三类采用二进制码表示图像。Torralba等人利用机器学习技术将主旨描述子（GIST）紧凑二值化[^torralba]。这是一类比较流行的满足大规模图像搜索需求的描述子压缩方法。在这样的背景之下，致力于通过低码率描述子的设计来达到快速图像检索性能的运动图像专家组（the Moving Picture Experts Group，MPEG）视觉搜索紧凑描述子（compact descriptors for visual search，CDVS）的标准化工作正迅速推进。

然而，许多视觉分析任务 （例如运动估计、运动追踪、3维重构等）处理的视觉特征集提取于视频序列，并非静态图像。这就需要分析从视频序列的每一帧中提取的视觉特征集。为处理如此大规模的特征数据，设计针对视频序列的视觉特征集的高效压缩方案成为了研究的重点。虽然有大量的对特征提取和特征压缩的研究，但大多面向静态图像，在视频序列方面还没有得到广泛的关注。

在该方面的工作主要有：针对基于图像的检索任务和移动增强现实应用，Mina Makar[^standford]提出的时域连续检测子可以有效地进行帧间编码。通过对已传输描述子的数据进行简单地修改，显著降低了传输比特；另一方面，受传统视频编码框架的启发，Luca Baroffio[^milan] 提出了视频序列下特征压缩的编码框架，通过帧间和帧内编码方案，充分挖掘了视觉特征的检测子和描述子的空间冗余和时间冗余。

熵编码中对于算术编码的研究工作,较为成熟的为传统视频的算术编码方案——基于上下文的二进制算术编码 (CABAC)。

早在1948年，香农就提出将信源符号依其出现的概率降序排序，用符号累计概率的二进制值作为对信源的编码，并从理论上论证了它的优越性。1960年，Peter Elias发现无需排序，只要编解码两端使用相同的符号顺序即可，并提出了算术编码的概念。1979年，R.Pasco和J.Rissanen分别用定长的寄存器实现了有限精度的算术编码。1981 年,Rissanen 和 Langdon 实现了二进制编码。1987年，Witten等人发表了一个实用的算术编码程序，即CACM87[^witten]（后用于ITU-T的H.263视频压缩标准）。同期，IBM公司发表了著名的Q-编码器。Q编码器采用归一化操作避免了算术编码区间的无限细分产生的精度表示与操作复杂的问题，并结合归一化过程采用加法和移位近似实现乘法运算。1989 年,Gonzales 给出了一种概率可更 新的自适应算术编码器。该方案的核心思想从 JPEG 中继承而来,但是其上下文模型的定义无法准确反映混合视频编码框架中变换系数的统计特性,因此, 其编码性能与基于变长码的帧间图像的编码效率相比并无显著地提高。

在混合视频编码的诸多标准中,首先给出算术编码框架的 H.263 标准。 H.263 标准定义了一种可选的算术编码器的方案。该算术编码器包含了多个概率估计模型,这些上下文模型可以在编码的过程中自适应的选择。每个模型的 更新过程都采用了较为简单的概率估计模型和上下文模型机制,因此不能有效 的反映概率变化的情况。在 H.264/AVC 的标准化制定中,借鉴M编码器的核心思想，大量的针对算术编码 的优化提案被提出,经过不断地优化,最终构成了标准中较为成熟的算术编码方案 CABAC。

### 1.3 论文研究内容与安排

本文的组织结构如下所示：
- 第一章为绪论部分，主要介绍本课题的研究背景和意义，以及国内外研究综述，最后给出论文的结构安排；
- 第二章介绍熵编码和算术编码的原理，并对H.264/AVC标准中采用的基于上下文的自适应算术编码进行研究。最后，简要介绍视觉特征与SIFT的相关内容；
- 第三章针对视觉特征（以SIFT为例）的特性，构建相应地熵编码框架，并设计实验方案；
- 第四章根据已设计方案进行实验，并对实验结果进行统计分析；
- 最后为全文总结和课题展望。

## 2 关键技术研究

### 2.1 熵编码研究

#### 2.1.1信源的信息熵测度

熵编码是图像、视频等编码系统必不可少的一个关键环节，用于去除信源符号在信息表达上的表示冗余，也称为信息熵冗余或编码冗余。在视觉特征编码系统中，特征信号是经采样量化后离散的灰度值集合，灰度值的取值是有限可数的，所以视觉特征属于离散信源。因此，本文只针对离散信源的熵编码进行讨论。
设信源X是一个离散型随机变量，其字母表为为$\chi$ ,概率密度函数$$p_X(x)=Pr(X=x), x \in \chi$$。那么，信源输出一个信源符号$$x$$，其信息量为

$$I(x) = \log\frac{1}{p_X(x)}$$

其中，$$I(x)$$称为信源符号$$x$$的自信息。自信息量是信源符号$$x$$的概率$$p_X(x)$$的单调递减函数，即$$p_X(x)$$越小，表示不确定性越大，那么$x$传递的信息也就越多。

自信息$$I(x)$$是信源发出某一符号所含有的信息量，信源发出的符号不同，所含有的信息量也不同。定义自信息量的期望值

$$H_1(X) = E_plog\frac{1}{p(X)}=\sum_{x\in\chi}p(x)\log\frac{1}{p(x)}$$

为随机变量$$X$$的熵。信息熵$$H_1(X)$$从平均意义上表示信源的总体信息测度，理解为信源$$X$$每输出一个符号所提供的平均信息量。信息熵取决于信源符号的分布特性，而与符号集无关。
信源输出的是符号序列，信源的N阶熵定义如下：

$$\begin{eqnarray*}
H_N(X) &=& H(X_1,X_2,\cdots,X_N)\\
           &=&-\sum_{[x_1,x_2,\cdots,x_N]\in\chi^N}p(x_1,x_2,\cdots,x_N)\cdot \log\ p(x_1,x_2,\cdots,x_N)
           \end{eqnarray*}$$

其中，$$\chi^N$$表示集合$$\chi$$的N阶笛卡尔积，$$p(x_1,x_2,\cdots,x_N)$$是N个连续符号的联合概率，N阶熵又称为N阶联合熵。

信源的N阶条件熵定义如下：

$$\begin{eqnarray}
H_{C,N}(X) &=& H(X_{N+1}|X_1,X_2,\cdots, X_N) \\
			    &=& -\sum_{[x_1,x_2,\cdots,x_N]\in\chi^N}p(x_1,x_2,\cdots,x_N)\cdot H(X_{N+1}|x_1,x_2,\cdots,x_N)
			    \end{eqnarray}$$

其中，$$X_1,X_2,\cdots,X_N$$称为$$X_{N+1}$$的上下文。

$$H(X_{N+1}|x_1,x_2,\cdots,x_N)=-\sum_{x_{N+1}\in\chi}p(x_{N+1}|x_1,x_2,\cdots,x_N)\cdot \log p(x_{N+1}|x_1,x_2,\cdots,x_N)$$

可以证明，$$H_N(X)/N$$与$$H_{C,N}(X)$$为变量N的非递增函数，且当N趋于无穷大时，它们的极限存在且相等，被称为极限熵，记为$$\overline{H}(X)$$，即有下式成立：

 $$\overline{H}(X) = \lim_{N\to\infty}\frac{1}{N}H_N(X) = \lim_{N\to\infty}H_{C,N}(X)$$

#### 2.1.2无损编码方案

定义某个离散信源$$F={F_n}$$,其中，$$F_n$$是一个随机变量，表示信源输出的第n个符号，$$F_n$$在某一实例化的具体取值记为$$f_n$$，其取值集合为$$\chi = {a_1,a_2,\cdots,a_L}$$。

对于标量无损编码方式，也就是为每个符号分配一个特定的码字，编码该输出符号所需的最小比特率$$\overline{R}_1(F)$$满足以下条件:

$$H_1(F) \leq \overline{R}_1(F) \leq H_1(F) + 1$$

而且，若信源的概率分布符合2的负幂次方分布，即存在一组整数$${m_1,m_2,\cdots,m_L}$$使得$$p(a_i) = 2^{-m_i}$$，那么编码符号$$a_i$$所需要的码字长度为$$-\log p_i = m_i$$，此时编码符号序列的$$\overline{R}_1(F)$$可以取到上式的下界$$H_1(F)$$。

为进一步降低码率，可以采用矢量无损编码方式。对于矢量无损编码方式，就是将连续的N个符号当做一个矢量，对该矢量分配一个码字，则编码该符号序列的每个矢量所需的最小比特数$$\overline{R}_1(F)$$满足以下条件

$$H_N(F) \leq \overline{R}^N(F) \leq H_N(F) + 1$$

若用$$\overline{R}_N(F)=\overline{R}^N/N$$表示每个符号所需要的最小比特数，则

$$H_N(F)/N \leq \overline{R}_N(F) \leq H_N(F)/N + 1/N$$

当$$N\to\infty$$时，有

$$\lim_{N\to\infty}\overline{R}_N(F) = \overline{H}(F)$$

因此，如果把多个符号进行联合编码，其码率可以任意程度地接近信源的极限熵。另一个有效降低编码码率的有效方法是采用条件无损编码。条件无损编码，也就是基于上下文的编码方式。在一些符号序列中，各个符号并不是相互独立的。因此，如果知道当前待编码符号的上下文，那么该符号的概率分布相较于上下文未知时更加不均匀，而在待编码符号的概率分布高度不均匀时，会达到很高的压缩比。

### 2.2算术编码概述

算术编码是用于无损数据压缩的一种熵编码方案。常见的编码方案还有霍夫曼编码。若假定要将字母表中的每个符号编码为整数比特，那么霍夫曼编码确实能达到最小的冗余度。也就是说，在每个符号的概率均为0.5的整数幂次的条件下，霍夫曼编码能达到最优的压缩效果，但这种情况在实际中并不常见，实际上霍夫曼编码对每个符号最多有1个比特的编码冗余。最坏的情况是待编码符号表中某个符号的概率趋于1，虽然该信源平均产生很小的信息，但却需要至少传送1个比特[^arithmetic_coding_for_data_compression]。相反，算术编码就没有上述对每个符号编码整数比特的限制，因此具有更高的编码效率。实际上，对于任何的信源，算术编码的编码效率都能达到理论上熵的界限。

### 2.3 CABAC原理与框架[^cabac]

基于上下文的自适应二进制算术编码（CABAC）是ITU-T/ISO/IEC H.264/AVC和HEVC视频压缩标准的一部分。通过将自适应二进制算术编码与上下文模型联系起来，可以达到很大程度上的自适应和冗余度降低。CABAC框架同时包含了一个创新的低复杂度二元算术编码实现和概率估计方案，有利于软硬件的高效实现。对大量的视频测试集的实验表明，相比于H.264的基于上下文的可变长算术编码（CAVLC），大约有30到38dB的效果增益，平均节省9%~14%的码率。

### 2.4 视觉特征与SIFT概述

视觉特征在一定程度上能够反映图像的内容，可以描述图像所表达的意义。因此，研究视觉特征是进行视觉分析的第一步。一般来说，随着具体应用的不同，选用不同的视觉特征，相应地视觉特征的提取算法也不同，通常认为，一种好的图像视觉特征的提取和表示应具有提取简单、区分能力强、与人的视觉感知相近和鲁棒性好的特点。

视觉特征的提取算法由检测子和描述子两大主要部分组成，其中，检测子标识图像中关键点的位置、尺度等特征，而描述子则提供对每个关键点周围小范围图像上内容的简要表达。尽管近年来，各式各样的描述子被不断提出，但它们都遵循着相似的处理流程，即预平滑、变换和空间池化（spatial pooling ）三个主要处理步骤[^vf]。首先，检测子标识尺度空间内的稳定关键点。使用尺度不变检测子（例如高斯拉普拉斯算子LoG，高斯差分算子DoG等）能够将不同尺寸的图像斑块的关键点联系起来。检测的关键点数量取决于图像内容、检测子类型和参数设置（尺度数量、检测阈值等）。进一步，基于局部梯度计算关键点的主方向。最后，通过关键点的邻域计算相应描述子。许多描述子的建立考虑了关键点的取向并作了相应地补偿，故具有旋转不变性。

本文的主要研究研究对象为尺度不变描述子（scale invariant feature trans-form，SIFT）[^sift] 。SIFT是一种用于局部特征检测和描述的算法。该算法由David Lowe在1999年提出，2004年完善。SIFT特征具有很多优秀的性质。其一，SITF特征对旋转角度、尺度伸缩和亮度变化保持不变性，即使对视角改变或仿射变换，也具有一定的稳定性。其二，SIFT特征所含信息量大，有很好地独特性和显著性，适于在海量数据库下进行快速、有效地匹配。另外，SIFT特征还具有高速型和可扩展性，能够已接近于实时的速度进行匹配。正基于这些特性，SIFT特征及其变体，在目标识别、3D建模、目标追踪等方面得到了成功应用。

SIFT算法主要由以下四部分组成：

1. 尺度空间极值检测：建立尺度空间金字塔，搜索每个尺度上图像的位置，用LoG近似DoG找到局部极值点，即兴趣点；
2. 关键点定位： 通过拟和三维二次函数以精确确定关键点的位置和尺度（达到亚像素精度），同时应为DoG算子会产生较强的边缘响应，因此要进一步去除不稳定的边缘响应点，以增强匹配稳定性、提高抗噪声能力；
3. 方向分配： 基于关键点邻域像素的梯度方向分布特性，为每个关键点计算一个或多个方向（称为主方向或辅方向），依照这个方向做进一步的计算，使算子具备旋转不变性。
4. 建立关键点描述符: 为每个关键点建立基于附近小范围内像素的表达，这其实是对关键点邻域内高斯图像梯度统计结果的表示。首先将坐标轴旋转为关键点的主方向，确保其旋转不变性；其次，通过对关键点周围图像区域分块，计算块内梯度直方图，生成具有独特性的向量，这个向量是该区域图像信息的一种抽象，具有唯一性。为了增强匹配的稳健性，Lowe建议描述子使用在关键点尺度空间内4*4的窗口中计算的8个方向的梯度信息，共4*4*8=128维向量表征。将向量归一化后，进一步去除了光照的影响。


## 3 特征压缩熵编码方案设计

### 3.1 特征压缩编码框架

令$$\Gamma_n$$表示分辨率为$$N_x \times N_y$$的视频序列的第n帧，从$$\Gamma_n$$中提取的局部特征集合为
$$D_n$$。关键点的数量为$$M_n=|D_n|$$。每个视觉特征$$d_{n,i} \in D_n$$ 由两部分组成:

i）4维向量$$\bf{p}_{n,i} = [x,y,\sigma,\theta]^T$$，表示关键点的位置$$(x,y)$$、尺度$$\sigma$$ 以及邻域主方向$$\theta$$;

ii）$$P$$维向量$$\bf{d}_{n,i}$$，表示对应于关键点$$d_{n,i}$$的描述子。

可以看出，特征压缩编码的设计就是为了能高效压缩视觉特征集合$${\{D_n\}}_{n=1}^N$$,其中$$N$$ 表示视频帧数。


### 3.2 熵编码方案总体设计

#### 3.2.1 熵编码

### 3.3 独立模式编码方案设计

### 3.4 帧间模式编码方案设计

## 4 实验结果与分析

### 4.1 数据集

### 4.2 独立模式编码实验结果分析

### 4.3 帧间模式编码实验结果分析

### 4.4 实验总结果

## 5 总结与展望

### 5.1 全文总结

### 5.2 课题展望

## 致谢

## 参考文献

[^14]: V. Chandrasekhar, G. Takacs, D. Chen, S. S. Tsai, J. Singh, and B. Girod, “Transform coding of image feature descriptors,” in Visual Communications and Image Processing, M. Rabbani and R. L. Stevenson, Eds., vol. 7257, no. 1. SPIE, 2009, pp. 725 710+.

[^15]: M. Johnson, “Generalized descriptor compression for storage and matching,” in 21st British Machine Vision Conference. Aberystwyth,Wales, United Kingdom: BMVA Press, 2010, pp. 23.1–23.11, doi:10.5244/C.24.23.

[^16]: M. Stommel, “Binarising SIFT-descriptors to reduce the curse of dimensionality in histogram-based object recognition,” International Journal of Signal and Image Processing, vol. 3, no. 1, pp. 25–36, Mar. 2010.

[^17]: C. Yeo, P. Ahammad, and K. Ramchandran, “Rate-efficient visual correspondences using random projections,” in 15th IEEE International Conference on Image Processing, San Diego, CA, USA, Oct. 2008, pp. 217–220.

[^18]: V. Chandrasekhar, G. Takacs, D. M. Chen, S. S. Tsai, Y. A. Reznik, R. Grzeszczuk, and B. Girod, “Compressed histogram of gradients:A low-bitrate descriptor.” 2012, pp. 384–399.

[^19]: G. Zhao, L. Chen, G. Chen, and J. Yuan, “KPB-SIFT: a compact local feature descriptor,” in International Conference on Multimedia,ser. MM ’10, Firenze, Italy, 2010, pp. 1175–1178.

[^pca-sift]: Y. Ke and R. Sukthankar, PCA-SIFT: A more distinctive representation for local image descriptors, in Proc. IEEE Conf. Computer Vision & Pattern Recognition, 2004

[^milan]: L. Baroffio, M. Cesana, A. Redondi, M. Tagliasacchi, and S. Tubaro, “Coding visual features extracted from video sequences,”IEEE Transactions on Image Processing, vol. 23,no. 5, pp.2262–2276, 2014.

[^badeffort1]: A. Zabala and X. Pons, “Effects of lossy compression on remote sensing image classification of forest areas,” International Journal of Applied Earth Observation and Geoinformation, vol. 13, no. 1, pp. 43–51, 2011.

[^badeffort2]: ——, “Impact of lossy compression on mapping crop areas from remote sensing,” International Journal of Remote Sensing, vol. 34,no. 8, pp. 2796–2813, Apr. 2013.

[^vf]:  M. Brown, G. Hua, and S. A. J. Winder, “Discriminative learning of local image descriptors,” IEEE Transactions on Pattern Analysis and Machine Intelligence, vol. 33, no. 1, pp. 43–57, 2011.

[^sift]: D. G. Lowe, “Distinctive image features from scale-invariant keypoints,” International Journal of Computer Vision, vol. 60, no. 2, pp. 91–110, Nov. 2004.

[^torralba]: A. Torralba, R. Fergus, Y.  Weiss, Small codes and pattern recognition, in Proc. IEEE Conf. Computer Vision & Pattern Recognition, 2008

[^chog]: V. Chandrasekhar, G. Takacs,  D. Chen, S. Tsai, R. Grzeszczuk, B. Girod, CHoG: Compressed histogram of gradients A low bit-rate feature descriptor, in Proc. IEEE Conf. Computer Vision & Pattern Recognition, 2009

[^hua]: S. W. G. Hua, M. Brown, “Discriminant Embedding for Local Im- age Descriptors,” in Proc. of International Conference on Computer Vision (ICCV). IEEE Computer Society, 2007. 

[^stanford]: Makar, Mina, et al. "Interframe Coding of Canonical Patches for Mobile Augmented Reality." ISM. 2012.

[^witten]: Witten, Ian H., Radford M. Neal, and John G. Cleary. "Arithmetic coding for data compression." Communications of the ACM 30.6 (1987): 520-540.

[^cabac]: Marpe, Detlev, Heiko Schwarz, and Thomas Wiegand. "Context-based adaptive binary arithmetic coding in the H. 264/AVC video compression standard." Circuits and Systems for Video Technology, IEEE Transactions on 13.7 (2003): 620-636.

[^arithmetic_coding_for_data_compression]: Ian H. Witten, Radford M. Neal, and John G. Cleary. 1987. Arithmetic coding for data compression. Commun. ACM 30, 6 (June 1987), 520-540.
