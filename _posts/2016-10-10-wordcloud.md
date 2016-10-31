---
layout: post
title: "NLP可视化: 用Python生成词云"
description: ""
category: 机器学习
tags: [ wordcloud, nlp, 可视化]
---
![](http://7xnq5e.com1.z0.glb.clouddn.com/2016-06-26-00-50-34.png)




如果你使用`python`来完成这件事，那么恭喜你，因为已经有现成的库来做这件事情(python 的轮子就是多)直接使用[word_cloud](https://github.com/amueller/word_cloud)的lib就可以很迅速的生成各种很漂亮的词云：
![](http://7xnq5e.com1.z0.glb.clouddn.com/2016-10-24-02-30-59.png)

但是有一个问题，`word_cloud` 对中文的支持不大好，与英文环境不同，英文文本的分词只需要根据空格进行简单的分割就可以了(当然后续还需要其他的处理比如抽取词干)，中文文本的分词则复杂很多了，因为中文文本词与词之间并没有空格的分隔，因此相对来说就会麻烦了一点，说起分词不得不想起很有名的一首断句诗，杜牧这首《清明》:
> 清明时节雨纷纷， 路上行人欲断魂。

> 借问酒家何处有， 牧童遥指杏花村。

不同的断句会有不同的意思:
断句版1：
>清明时节雨纷纷，路上行人欲断魂。
借问酒家：何处有牧童？遥指杏花村。

断句版2：
>清明时节雨、纷纷路上，行人欲断魂。
借问酒家何处？有牧童、遥指杏花村。

上面的例子和中文的分词是有区别的，但从另一方面可以发现中文的不同的分词可以形成不同的词，也会形成不同的意思。
那有什么解决方法呢？ 当然你可以自己来编写一套分词的程序，但是作为一个使用`python`的人，很高兴你可以使用`jieba`（也有java，c++，go版本）分词库，他已经可以帮你完成分词这件事，而你要做的就是把上面的轮子组装起来。是不是很简单呢？
下面是 `jieba` 的官方例子：
```python
# encoding=utf-8
import jieba

seg_list = jieba.cut("我来到北京清华大学", cut_all=True)
print("Full Mode: " + "/ ".join(seg_list))  # 全模式

seg_list = jieba.cut("我来到北京清华大学", cut_all=False)
print("Default Mode: " + "/ ".join(seg_list))  # 精确模式

seg_list = jieba.cut("他来到了网易杭研大厦")  # 默认是精确模式
print(", ".join(seg_list))

seg_list = jieba.cut_for_search("小明硕士毕业于中国科学院计算所，后在日本京都大学深造")  # 搜索引擎模式
print(", ".join(seg_list))
```
我们现在只需要用到的是默认的精确模式就可以了。大概介绍了一下使用的工具，那么我们现在开始一步步去探索。


### 预处理过程
主要是加载`.txt` 文件，和去掉特殊符号(标点符号)


```python
import chardet
import re
import io
class DetectEncodoingError(Exception):
    """Exception raised for errors in the file encoding detect.

    Attributes:
       message -- explanation of the error
    """

    def __init__(self, message):
        self.message = "detect encodings fail %s" % message

def detect_encoding(filename):
    with open(filename, 'r') as f:
        predict = chardet.detect(f.read(100))
        if predict['confidence'] < 0.8:
            print >> sys.stderr, predict
            print >> sys.stderr, "auto detect encodings fail:%s" % filename
            raise DetectEncodoingError(filename)
        return predict


def load_txt(filename):
    predict = detect_encoding(filename)
    encoding = predict['encoding']
    try:
        with io.open(filename,
                     'r',
                     encoding=encoding,
                     errors='ignore') as f:
            # 过滤特殊符号
            text = re.sub(ur'\W+', ' ', f.read(), flags=re.U)
            return ''.join(text)
    except UnicodeDecodeError:
        raise UnicodeDecodeError
```

这里用到了一个`chardet` 和 `io` 库，因为对于计算机来说，字符对它来说只是一个个字节，它并不知道这些字节是什么字符，所以必须你告诉它这些字节使用了什么编码，然后计算机就能根据去查编码表，才能找出对应的字符， 直接使用内置的`open`是很可能会出现问题的。在python2.x的版本中是不支持显示地指定编码的。如果你使用`open`，你最好使用`二进制模式`，然后再解码

```python
f = open('test.txt', 'rb')
content = f.read().decode('utf-8')
```

但是每次传入一个文件都要显示指定它的编码这样会不会太麻烦了， 因此我们引入`chardet`来检测文件的编码， 这样就需要额外读取多一次文件来检测文件的编码， 但是并不需要读取整个文件（当然这样检测的可信度会更高，但一般情况下准确度可以了），还要注意设置`errors='ignore'`可以防止因为个别特殊的字符导致解析错误， 当然这个函数还可以改进，必去使用`open`以二进制的形式打开，读取之后再seek回开始的位置也可以:
```python
f = open('dataset/xiaoshuo/90.txt', 'rb')
predict = chardet.detect(f.read(1000))
if predict['confidence'] < 0.8:
            print >> sys.stderr, predict
            print >> sys.stderr, "auto detect encodings fail:%s" % filename
print predict['encoding']
f.seek(0, 0)
print f.tell()
content = f.read().decode(predict['encoding'])
print content[:100]
```
下面我以小说[三体](https://book.douban.com/subject/2567698/) 作为测试数据。



```python
filename = 'dataset/xiaoshuo/santi.txt'
with open(filename, 'r') as f:
    predict = chardet.detect(f.read(100))
f = io.open(filename,'r', encoding=predict['encoding'], errors='ignore')

# 没有经过处理的text
content = f.read()
# 结果
'''
刘慈欣  三体


　　《三体》终于能与科幻朋友们见面了，用连载的方式事先谁都没有想到，也是无奈之举。之前就题材问题与编辑们仔细商讨过，感觉没有什么问题，但没想到今年是文革三十周年这事儿，
'''
print content[:100]
# 过滤特殊符号，转化为空格
content = re.sub(ur'\W+', ' ', content, flags=re.U)
# 结果
'''
 刘慈欣 三体 三体 终于能与科幻朋友们见面了 用连载的方式事先谁都没有想到 也是无奈之举 之前就题材问题与编辑们仔细商讨过 感觉没有什么问题 但没想到今年是文革三十周年这事儿 单行本一时出不了 也只
'''
print content[:100]
```



### 提取关键词 和 生成词云
从文本中提取主要的关键字 和 对应的频率，有几种思路：

#### 简单的分词，然后统计词的频率(Term Frequency )
如果这个词出现的频率很高，那么说明这个词很重要，这是一个不错的思路，但是，还有一种词叫做[停用词](http://baike.baidu.com/view/3784680.htm),它们才是出现频率很高的词，但并不能对表示这个文档有什么作用，因此我们需要过滤掉这一种词，可以通过一个`停用词`的字典过滤掉这一种词。下面通过`jieba`的cut直接分词，并且过滤听过，并且统计每个词的频率


```python
import jieba
from jieba import analyse
from pprint import pprint
def load_stop_words(filename):
    """ load dictionary file.

    :param filename: The path to the dictionary file
    :return: a list of stop words
    """
    predict = detect_encoding(filename)
    encoding = predict['encoding']
    with io.open(filename, 'r', encoding=encoding, errors='ignore') as f:
        stop_words = set()
        for line in f.readlines():
            stop_words.add(line.strip())
        return stop_words

def filter_stop_words(cut_word_list, stop_words):
        """Filter stop words

        :param cut_word_list: a list of words
        :param stop_words: a list of stop_words
        :return: a list of Filtered words
        """
        seg_list = [
            w for w in cut_word_list if w not in stop_words and w != " "
            ]
        return seg_list

def filter_single_word(words):
    seg_list = [ word for word in words if len(word) > 1]
    return seg_list
# 为了优化结巴的分词效果加载了几个字典
jieba.set_dictionary('dataset/dict/dict.txt.big')
jieba.load_userdict('dataset/dict/custom_words.txt')
jieba.analyse.set_stop_words('dataset/dict/stop_words_filtered.txt')

# 使用上面的 content
keywords = jieba.cut(content)

# 过滤停用词
stop_words = load_stop_words('dataset/dict/stop_words_filtered.txt')
print "stop_words length: %s" % len(stop_words)
keywords = filter_stop_words(keywords, stop_words)
# 过滤长度为1的词，保留长度2以上的词
keywords = filter_single_word(keywords)
words_frequency_dict = dict()

# 统计词频
for word in keywords:
    words_frequency_dict[word] = words_frequency_dict.get(word, 0) + 1
words_frequency = words_frequency_dict.items()
print "words_frequency length: %s" % len(words_frequency)

# 找到词频排前2000的词语
top_n = len(words_frequency) if len(words_frequency) < 2000 else 2000
top_words_frequency = sorted(words_frequency,key=lambda x: x[1],reverse=True)[:top_n]
for word,fre in top_words_frequency[:200]:
    print word, fre
# result
'''
中 3582
说 2748
程心 1499
罗辑 1190
号 967
时 954
世界 901
'''
```

![](http://7xnq5e.com1.z0.glb.clouddn.com/2016-11-01-00-12-40.png)
虽然用了字典，但是出来的结果发现仍然很不理想，比如多了的恨过我们不想要的单字`中 说`等等没有过滤掉的，目前这种方案下我的做法是把单字的都过滤掉。

```python
def filter_single_word(words):
    seg_list = [ word for word in words if len(word) > 1]
    return seg_list
```

![](http://7xnq5e.com1.z0.glb.clouddn.com/2016-11-01-00-12-19.png)
会发现结果好了很多。当然还有一种做法就是过滤完停用词之后，直接调用wordcloud的`generate`方法，传入参数它要求传入字符串并且词与词之间以空格分隔(其实就是英文环境下的字符串)，这个留到后面说。这似乎已经达到我们分词然后统计词频的目的了。但是有没有更好的方案呢？有的，就是下面描述的第二种方法
#### 使用`tf-idf` 和 `textrank`算法提取关键字
`jieba` 和 `snownlp`都实现了 `tf-idf` 和 `textrank` 算法，中文环境可以从两个库选择一个使用
##### `tf-idf`
谈起自动摘要算法首先想到的就是`tf-idf`算法，它的优点在于原理简单易于实现，同时效果也还可以。TF-IDF的主要思想是，如果某个词或短语在一篇文章中出现的频率TF高，并且在其他文章中很少出现，则认为此词或者短语具有很好的类别区分能力，适合用来分类。TF词频(Term Frequency)指的是某一个给定的词语在该文件中出现的次数。IDF反文档频率(Inverse Document Frequency)的主要思想是：如果包含词条的文档越少，IDF越大，则说明词条具有很好的类别区分能力。使用`TF*IDF`可以计算某个关键字在某篇文章里面的重要性。使用`jieba`内置的`tf-idf`实现:


```python
# 为了优化结巴的分词效果加载了几个字典
jieba.set_dictionary('dataset/dict/dict.txt.big')
jieba.load_userdict('dataset/dict/custom_words.txt')
jieba.analyse.set_stop_words('dataset/dict/stop_words_filtered.txt')
# jieba.analyse.set_stop_words(file_name) # file_name为自定义语料库的路径
# jieba.analyse.set_idf_path(file_name) # file_name为自定义语料库的路径
key_words = jieba.analyse.extract_tags(content, topK=2000, withWeight=True, allowPOS=('nb','n','nr', 'ns','a','ad','an','nt','nz','v','d'))
for word,fre in key_words:
    print word, fre
# output
'''
程心 0.118509628715
罗辑 0.0992192021595
太空 0.0446452950132
宇宙 0.0426372100268
智子 0.0415513633001
地球 0.0339421682464
飞船 0.0338160069583
人类 0.0328427196611
太阳 0.0319194633423
舰队 0.0282123894719
世界 0.0260238471191
太阳系 0.0242374910684
...
'''
```

![](http://7xnq5e.com1.z0.glb.clouddn.com/2016-11-01-00-13-21.png)
- `tokK` 为返回几个 TF/IDF 权重最大的关键词，默认值为 20
- `withWeight` 为是否一并返回关键词权重值，默认值为 False
- `allowPOS` 仅包括指定词性的词，我这里只选定了`名词，形容词，动词，和副词`
这里的`idf` 语料库，使用的是默认`jieba`的语料库，如果经常传入的是特定领域的文本，还可以自己统计还可以统计语料库`idf`,然后使用`jieba.analyse.set_idf_path(file_name)`传入。
##### 使用`textrank`算法

TextRank算法基于PageRank，用于为文本生成关键字和摘要。这里就不详细描述了。以后有机会再单独写一篇文章介绍。
使用`jieba`内置的`textrank`实现:


```python
key_words = jieba.analyse.textrank(content, topK=2000, withWeight=True, allowPOS=('nb','n','nr', 'ns','a','ad','an','nt','nz','v','d'))
for word,fre in key_words:
    print word, fre
# output
'''
程心 1.0
罗辑 0.843471900383
人类 0.618534487626
地球 0.572782183812
世界 0.55988227614
宇宙 0.554958083565
太空 0.553576763001
太阳 0.523998338227
舰队 0.434629644619
飞船 0.366429673961
看着 0.357334156714
时间 0.353376075686
发现 0.33500760777
很快 0.329485818738
智子 0.313541956373
信息 0.291310308344
...
'''
```

![](http://7xnq5e.com1.z0.glb.clouddn.com/2016-11-01-00-14-16.png)
#### 生成词云
这里主要调用`wordcloud`包的`generate_from_frequencies` 和 `generate` 方法生成词云


```python
from wordcloud import WordCloud
import matplotlib.pyplot as plt
def show_img(wc):
    plt.figure()
    plt.imshow(wc)
    plt.axis("off")
# 实例化，通过font_path传入一个支持中文的字体
wc = WordCloud(font_path=u"static/fonts/simhei.ttf",
               max_words=2000,
               width=1920,
               height=1080,
               background_color="black",
               margin=5)

# 1、传入[（key，weight）,...]列表生成词云
wc.generate_from_frequencies(key_words)
# 2、传入"key key key key" 字符串生成词云
# wc.generate(content)

# 保存图片到本地
wc.to_file('result/santi/santi_textrank_filtered(nb_n_nr_ns_a_ad_an_nt_nz_v_d).png')
show_img(wc)
```

这里需要注意的一个地方就是要注意传入一个支持中文的字体，不然生成的图片会有很多的`口`,其它的参数可以参考官方的文档


### 应用
- 收集微博用户发的微博或者知乎用户的回答生成词云
- 豆瓣评论生成对电影的词云
- 淘宝或者其它的评论系统
- 小说

### 后续
- 添加新词发现模块
- 提供api服务

### awesome-wordcloud:
- https://github.com/amueller/word_cloud
- https://github.com/FantasRu/WordCloud
- https://github.com/flingjie/words_image
- https://github.com/tejasai97/wordcloud
- https://github.com/fyuanfen/wordcloud
- https://github.com/atizo/PyTagCloud
- https://github.com/vdmitriyev/services-to-wordcloud
- https://github.com/shispt/wordcloud_web


