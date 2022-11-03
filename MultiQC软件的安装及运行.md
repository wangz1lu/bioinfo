# MultiQC软件的安装及运行
本篇文章讲述了MultiQC软件的安装及运行，其中需要安装conda和配置conda环境sratoolkit的部分可查阅本作者其他文章

# **一、MultiQC的安装**
本文将通过conda来安装MultiQC，具体安装和环境配置可以参考我的***conda的详细介绍***文章

https://www.wiz.cn/share-plus/note/375a1d40-2746-11ed-a050-f704bb07f03a/7013d84b-dab6-428b-97d0-d78aa392a6a7

## **1.进入自己建立的环境py27**
![image](images/2c68464a-88e8-4884-a059-c22c3b96d36c.jpg)

## **2.conda安装MultiQC**
![image](images/b3b66664-06a9-4fe7-8bea-5acc96c39380.png)

## **3.检查是否安装完成**
![image](images/5a682bc6-684e-4a22-bcd2-94e468bafe21.jpg)

看到版本号，说明安装成功

# **二、MultiQC的运行**
## **1.搜索SSR数据**
在NCBI的SRA数据库进行搜索，例如atha，得到SRR编号

![image](images/43961ef7-91fb-43d0-bddb-41f3292a7e2b.png)

![image](images/ffaefbd7-d19d-494b-bf18-fd521a688b5d.png)

![image](images/9d4f90a4-0361-4ce4-ae48-632fe25a7c02.png)

![image](images/1818d26b-efd9-41de-8c1e-631265c1c7b2.png)

## **2.SRR数据下载及解压**
##### 下载
```bash
(py27) user125@gs91:~/workspace/Biodata$ prefetch SRR12026204
(py27) user125@gs91:~/workspace/Biodata$ prefetch SRR12026205
```
![image](images/c7044c93-5a61-4923-b614-2907a8b9226b.png)

##### 解压
```bash
(py27) user125@gs91:~/workspace/Biodata$ fastq-dump --gzip --split-files SRR12026204 #解压为gz文件，节省空间
(py27) user125@gs91:~/workspace/Biodata$ fastq-dump --gzip --split-files SRR12026204
```
##### 查看结果
![image](images/e85faca2-d1f6-4dbf-baa0-642623f07164.png)

## **3.使用fastqc对数据进行质控**
```bash
(py27) user125@gs91:~/workspace/Biodata$ fastqc SRR12026204_1.fastq.gz
(py27) user125@gs91:~/workspace/Biodata$ fastqc SRR12026205_1.fastq.gz
```
得到分析报告html文件

![image](images/964f420e-01ba-4501-af01-a5b75f70e2ae.png)

## **4.使用MultiQC进行汇总**
```bash
(py27) user125@gs91:~/workspace/Biodata$ multiqc .
```
![image](images/e35f5c35-93d0-41b7-af16-505d098e25b5.png)

## **5.查看结果**
在侧边栏下载**multiqc\_report.html**文件，通过浏览器打开

![image](images/1ed6262e-4d5f-4e58-9b2c-ae9c4870e121.png)

![image](images/ad330612-7b80-4208-990c-4bf4ef181d2e.png)

# **三、结果分析**
## **1.所有样本数据基本情况统计**
%Dups——重复reads的比例

%GC——GC含量占总碱基的比例，比例越小越好

M Seqs——总测序量（单位：millions）

![image](images/8c635a1b-f310-4aea-b71c-0d2bc47bfd74.png)

## **2.序列计数**
黑色代表重复reads的数量，这两个reads重复序列都比较高，说明两个样本序列中有用的reads比较少。

![image](images/adfda18c-f042-424e-bc16-1dc4a01245fd.png)

## **3.每个read各位置碱基的平均测序质量**
横坐标——碱基的位置，纵坐标——质量分数，质量分数=-10log10p（p代表错误率），所以当质量分数为40的时候，p就是0.0001。此时说明测序质量非常好。绿色区间——质量很好，橙色区间——质量合理，红色区间——质量不好。如图可以看出我的2条序列总的质量都比较合理。

![image](images/4c22c2c5-9d8f-4218-b88b-66f91e35ac88.png)

## **4.具有平均质量分数的reads的数量**
横坐标——平均序列质量分数，纵坐标——reads数，绿色区间——质量很好，橙色区间——质量合理，红色区间——质量不好，当峰值小于27时——warning，当峰值小于20时——fail，由此图中可以看出低质量reads占整体reads的比例（估算各颜色区域曲线下面积）。如图可以看出2条序列在28以后都是绿色位置，最高峰在37左右，总体测序质量不错。

![image](images/253f87f3-cb2e-4e34-9792-2a313135c1f3.png)

## **5.每个read各位置碱基ATCG的比列**
reads每个位置的颜色显示由4种颜色的比例混合而成，哪一个碱基的比例大，则趋近于这个碱基所代表的颜色。正常情况下每个位置每种碱基出现的概率是相近的。如图，reads每个位置的ATGC含量比例还算均匀。

![image](images/4237ecda-cead-45bf-9752-8b72f5ea0eef.png)

## **6.reads的平均GC含量**
横坐标——GC含量百分比，纵坐标——数量，正常的样本的GC含量曲线会趋近于正态分布曲线，曲线形状的偏差往往是由于文库的污染或是部分reads构成的子集有偏差，形状接近正态但偏离理论分布的情况提示我们可能有系统偏差。如图，两个样本和正态分布曲线相差很远，说明有文库污染或者部分reads构成的子集有偏差。

![image](images/426018cd-9a3e-4244-9cbe-2fc5a812a663.png)

## **7.每条reads各位置N碱基含量比例**
横坐标——read中的位置，纵坐标——N的数量比，当测序仪器不能辨别某条reads的某个位置到底是什么碱基时，就会产生“N”，统计N的比率。正常情况下，N值非常小。如图，说明测序仪器能辨别每条reads的每个位置。

![image](images/4746cc21-15b2-40f8-af00-ecb333b67934.png)

## **8.序列长度分布**
![image](images/822f6417-b693-407f-ad14-f278cc9c98cb.png)

## **9.每个序列的相对重复水平**
![image](images/4e1b5636-1c0b-4110-a122-3e9018ade65a.png)

两个序列中的重复的reads的程度都还在范围内。

## **10.文库中过表达序列的比例**
横坐标——过表达序列的比例，纵坐标——样本。如图这两个序列中过表达的序列的比例都远远超过1%，如果出现这种情况，不是这种转录本巨量表达，就是样品被污染。

![image](images/003c695c-814d-4926-ab2c-2ff0a65d3164.png)

## **11.接头含量**
如图没有找到超过0.1%的接头序列含量。

![image](images/6650fb05-d150-425f-9c64-11414bb010be.png)

## **12.状态检查**
![image](images/3383bc1a-0bbb-4736-91e6-c54f427f074e.png)



















