---
layout: post
title: 恶意代码分类训练预测篇
category: opinion
description: malcode classification training&test part
---

## 写在前面

在上一篇[恶意代码分类数据篇][]的基础之上，已经得到了数据集样本的opcode，接下来需要根据opcode生成n-gram的opcode。

在开始之前，需要说明一下，算是填上一篇的坑，上一篇中只提到了针对恶意样本进行反汇编，提取opcode，没有说明对良性文件(benign file)进行处理，这也是自己在实际操作过程中遇到的问题。我们不仅仅要对数据集中的恶意样本进行处理，同时还需要对benign文件进行处理，这样我们才能够得到一个统一的数据集。这部分数据集中，包含用来训练的训练数据集(training dataset)，和用来进行测试预测的测试数据集(test dataset)。其中，训练数据集(training dataset)根据自己需要，还可以再分成实际用来训练的训练数据集(training dataset)和用来在训练过程中进行校验的数据集(validation dataset)。对于训练数据集的划分不是必须的，主要是看自己的需求。

另外，关于上一篇提取到的opcode和bytecode，通过阅读文章和自己的思考，以及和老板的讨论，我们认为opcode和bytecode是两种模型下的不同特征，不能放在一起使用，但是可以分别使用opcode和bytecode训练成两个分类器，来对未知文件进行分析。在无法识别未知文件，得不到文件的反汇编代码，提取不到opcode的情况下，可以采用bytecode的特征，对未知文件进行分析。因为本文主要是分析n-gram opcode，所以解析来的特征都是采用opcode作为唯一特征进行训练和预测。使用bytecode特征进行训练的方法和opode类似，不作赘述。

接下来进入正题。

## n-gram OpCode

为了后续计算的方便，以及数据的重复利用和统计分析，更重要的是防止电脑挂掉。因为我处理数据的电脑在跑数据的过程中就挂掉了，导致数据都重新跑了一遍，所以最好还是分步进行各个实验步骤，并将实验结果及时保存和备份。

### n-gram opcode

首先是生成n-gram，这里方法比较简单，python读取文件之后得到的是一个列表，因为之前opcode是每行一个进行存储的，所以对列表遍历一遍进行截取就可以了。 完整代码可以参考[gengram.py][]

```
def genGram(content, filename):
    end = len(content)
    strgram = ''

    for i in range(0, end):
        bigram = content[i: i + __GRAM_SIZE__]
        strgram += str(bigram).replace('[', '').replace(']', '').replace('\\n', '').replace('\'', '').replace(' ', '') + '\n'
        # strgram += str(bigram) + '\n'

```

### n-gram opcode with tf

在得到n-gram opcode之后，就需要计算每个term，即n-gram opcode的tf了。tf，即term frequency，表示文档中term出现的次数。还有一个归一化的tf(normalized term frequency)，这个在接下来会用到，因为每个文档长短不已，term数量也不一样，如果只是按照term的个数来进行比较，长文档和短文档没有办法一起比较，所以把tf归一化。归一化方法就是用当前term在当前文档中出现的次数，除以当前文档中出现次数最多的term的次数，不是除以文档中term的总数。

除了计算单个文档中的词频，还需要计算term在整个文档中的频率，用df来表示。df，即document frequency，表示包含term的文档数。还有一个指标衡量term的重要性——idf，因为在本部分没有用到，就不做讲解。

计算单个文档中的tf,可以通过python中的dict来实现

```
freq = dict()
for line in content:
    freq[line.strip()] = freq.get(line.strip(), 0) + 1

maxterm = max(freq.values())

```

计算所有文档中term的tf，以及df，和上文类似

```
def getTotalTF(content, tf, df):
    tmp = copy.deepcopy(df)
    for line in content:
        tf[line.strip()] = tf.get(line.strip(), 0) + 1
        df[line.strip()] = tmp.get(line.strip(), 0) + 1

```

完整代码参考[gramfreq.py][]

## feature

计算完每个文档的tf，并且得到所有term(n-gram opcode)的tf，df之后，我们需要对这些term进行分析，选取一部分term作为特征。这部分特征要尽可能保证能够表示文件，同时又不至于很多文档中不包含这些特征，导致得到的文档向量含有很多0，最后形成一个稀疏矩阵。所以，我们可以根据term的df，选取top 1000的特征作为基础特征，然后再从这部分特征中选取300个term作为特征。

首先，完成term的初始化，根据df选取top 1000的term。我们可以先对term按照df进行排序，然后再进行选取

```
def initFeature(orifeapath):
    with open(orifeapath) as orifile:
        lines = orifile.readlines()

    sortedLines = sorted(lines, lambda x, y: cmp(int(x.split('----')[4]), int(y.split('----')[4])), reverse=True)

    with open(desfilepath, 'w') as desfile:
        desfile.writelines(sortedLines)


def selectFeature(filepath, feasize):
    with open(filepath) as orifile:
        lines = orifile.readlines()

    secfeature = lines[0:feasize]

    with open(desfilepath, 'w') as desfile:
        desfile.writelines(secfeature)

```

根据文章中的实验，根据df选取300个特征的方法简单，可以效果也比较好，所以这里直接使用上述方法选取top 300的term作为特征。

## vector

提取出特征之后，我们需要对每个文件进行向量化表示，并对文档进行标注。

因为之前对文档的tf进行分析时看到，有些大文件中的term的tf非常小，很多使用科学计数法表示，在从文件读取出来之后不好处理，所以在这部分进行向量表示时，统一将tf放大1000倍。因为是对所有文档统一处理，所以对结果没有影响。

生成文档vector的核心代码如下

```
def createMatrix(desfilepath, fealist, tfpath, type):
    tmppath = os.path.join(tfpath, type)
    filelist = os.listdir(tmppath)
    for item in filelist:
        filepath = os.path.join(tmppath, item)
        vector = '----'.join([item, str(getVector(fealist, filepath)).replace('[', '').replace(']', '').replace('\'', ''), '1' if type == 'benign' else '2'])
        with open(desfilepath, 'a+') as desfile:
            desfile.writelines(vector + '\n')


def getVector(fealist, filepath):
    tmplist = []

    with open(filepath) as orifile:
        lines = orifile.readlines()

    sortedLines = sorted(lines, lambda x, y: cmp(int(x.split('----')[1]), int(y.split('----')[1])), reverse=True)
    maxtf = int(sortedLines[0].split('----')[1])

    for feature in fealist:
        tmplist.append('0')
        for line in lines:
            line = line.split('----')
            if(feature == line[0]):
                tmplist[-1] = str(int(line[1]) / maxtf * 1000)

    return tmplist

```

完整代码参考[creatematrix.py][]

## training

在将文档进行向量化表示之后，就可以将其引入分类器中进行训练，本次选用`scikit-learn`库中的svm作为分类器进行训练。因为我们数据量比较大，每次都重新训练代价比较高，所以我们可以将训练好的分类器保存下来，在以后进行测试和预测的时候，直接从文件加载就可以。很方便的是，`scikit-learn`中提供了该问题的解决方法，使用`joblib`就可以完成。

训练代码如下

```
def train(filepath):
    data = []
    labels = []
    with open(filepath) as ifile:
        for line in ifile:
            line = line.split('----')
            tokens = [i.strip().replace('\'', '') for i in line[1].split(',')]
            data.append([float(i) for i in tokens])
            labels.append(line[-1][0])
    x = np.array(data)
    labels = np.array(labels)
    y = np.zeros(labels.shape)
    y[labels == '1'] = 1

    ''' SVM '''
    clf_linear = svm.SVC(kernel='linear').fit(x, y)
    model_path = os.path.join(
        __classifier_model_path__, 'train_model_clf_linear.m')
    joblib.dump(clf_linear, model_path)

```

测试代码如下

```
def test(filepath):
    # check output file
    desfiledir = os.path.join(BASEPATH, 'test')
    checkDir(desfiledir)

    desfilepath = os.path.join(desfiledir, '2_gram_test_result')
    checkFile(desfilepath)

    # load model
    # clf = joblib.load('../../data/model/randomforest.pkl')
    model_path = os.path.join(
        __classifier_model_path__, 'train_model_clf_linear.m')
    clf_linear = joblib.load(model_path)

    with open(filepath) as testfile:
        lines = testfile.readlines()

    for line in lines:
        line = line.split('----')
        filename = line[0].strip()
        vector = [float(i.strip().replace('\'', ''))
                  for i in line[1].split(',')]
        fileclass = line[-1][0]
        print '[+] Predicting ', filename
        res_linear = clf_linear.predict(vector)
        res = '----'.join([filename, fileclass,
                           '1' if res_linear[0] == 1 else '2'])
        with open(desfilepath, 'a+') as desfile:
            desfile.writelines(res + '\n')

```

在得到测试结果之后，我们可以对结果进行分析

```
def analyse(filepath):
    with open(filepath) as resfile:
        lines = resfile.readlines()

    tp = 0
    fp = 0
    fn = 0
    tn = 0

    for line in lines:
        line = line.split('----')
        fileclass = line[1]
        # linear svm
        preclss = line[2]

        if(fileclass == '1'):
            if(preclss == '1'):
                tp += 1
            else:
                fn += 1
        else:
            if(preclss == '1'):
                fp += 1
            else:
                tn += 1

    total = tp + tn + fp + fn
    accuray = (tp + tn) / total
    precision = tp / (tp + fp)
    recall = tp / (tp + fn)
    f_measure = (2 * tp) / (2 * tp + fp + fn)

    print 'tp :', tp
    print 'fp :', fp
    print 'tn :', tn
    print 'fn :', fn
    print 'total :', total
    print 'accuray :', accuray
    print 'precision :', precision
    print 'recall :', recall
    print 'f_measure :', f_measure

```

附一张最后的测试结果图
!(testResult)[/images/malware/mal-5.jpg]

## summary

经过这几个月的努(lan)力(duo)，终于把这一部分实验跑完。最后的这篇文档整理的有些仓促，因为着急回家，但是基本全是重点内容吧，等休息一下，再把这期间走过的坑整理一下，补充到里面。总之，经过这部分的实验，确实增长了不少知识。接下来就要思考如何对该模型进行优化，希望自己来年能够作出一些突出的成果。

最后，新年快乐。

[恶意代码分类数据篇]: http://blog.moonsea.ac.cn/Malcode-ngram-opcode
[gengram.py]: https://github.com/moonsea/malcode/blob/master/gengram.py
[gramfreq.py]: https://github.com/moonsea/malcode/blob/master/gramfreq.py
[creatematrix.py]: https://github.com/moonsea/malcode/blob/master/creatematrix.py
