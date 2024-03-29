# 67373 视频推荐系统

目录：

[1. 读取数据](#1)

[2. 创建 codebook](#2)

[3. 使用 Bag of Words 表示数据](#3)

[4. 创建训练集和测试集](#4)

[5. 使用 Self Organizing Map 聚类](#5)

<h2 id="1">1. 读取数据</h2>

从 [dataset](dataset/dataset.txt) 文件夹里读取数据：

```
...
[2021-06-26] 直播回放,视频-直播回放,1000299135,17232.62,2021-06-26T20:10:32Z,fbfd75a8565548729695211ecaa7d4f6
童话镇,视频-唱歌视频,1000284761,262.006,2021-06-26T19:42:52Z,0c969febd3b14e17b8166cad069d5e6f
我们,视频-唱歌视频,1000284761,215.75,2021-06-26T19:42:43Z,eca9a80de506458396db96343871c4a9
大龄文艺女青年之歌,视频-唱歌视频,1000284761,226.0,2021-06-26T19:42:35Z,63e429e8c91f4651b19367196e42d1c5
...
```

每一列分别代表了： title, cate_name, cate_id, duration, creation_time, video_id

<h2 id="2">2. 创建 codebook</h2>

把数据集中前两列（即视频标题，视频分类名）的字符串用集合表示，创建一个 codebook。简单的数据集例如：

```
童话镇,视频-唱歌视频,...
阿婆说,视频-唱歌视频,...
弦上有春秋,视频-唱歌视频,...
```

则 codebook 为：

```
["童","话","镇","阿","婆","说","弦","上","有","春","秋","视","频","-","唱","歌"]
```

<h2 id="3">3. 使用 Bag of Words 表示数据</h2>

方便起见使用 one-hot 表示向量，例如：

```
童话镇,视频-唱歌视频,...
```

则表示的向量表示为

```
[1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1]
```

<h2 id="4">4. 创建训练集和测试集</h2>

使用了 sklearn 的 [train_test_split()](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html) 方法划分训练集和测试集。

<h2 id="5">5. 使用 Self Organizing Map 聚类</h2>

使用的库：[minisom](https://github.com/JustGlowing/minisom)

因为用 one-hot 表示的向量维度是和 codebook 的长度有关，所以维度很大，这里用了自组织映射把该向量从高维度映射到了 SOM 平面。这样一来，高维度数据向量可以用该数据在 SOM 平面的坐标（m, n）表示。训练完成后，一个坐标代表一个 cluster，一个 cluster 里聚集了相似的数据，例如：

```
{
    ...,
    (0, 1): [13, 188, 217],
    (1, 0): [14],
    (0, 6): [15, 135, 219],
    (3, 0): [16],
    (5, 3): [17],
    (6, 8): [18, 100],
    (6, 0): [19],
    (2, 9): [20, 78, 116, 197, 210, 223, 225, 226],
    (3, 8): [21, 85, 99, 129, 134, 220, 221, 229],
    (1, 5): [22, 143, 201],
    (5, 8): [23, 131, 150],
    (1, 8): [24, 184],
    (5, 5): [25, 72, 127, 230],
    (6, 6): [26, 204],
    (3, 7): [27, 71, 87, 181],
    ...
}
```

字典里 key 为 SOM 平面的坐标，value 为数据在训练集中的索引数组。
