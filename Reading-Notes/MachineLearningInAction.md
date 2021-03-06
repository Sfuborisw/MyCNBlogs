<h2>《机器学习实战》 :books: </h2> 

> Peter 著  人民邮电出版社    <a href="https://github.com/wuping5719/PythonTest/tree/master/src/machine_learning">示例代码</a> 

```python
1.知识表示可以采用规则集的形式，也可以采用概率分布的形式，甚至可以是训练样本集中的一个实例。

2.分类和回归属于监督学习，之所以称之为监督学习，是因为这类算法必须知道预测什么，即目标变量的分类信息。

3.与监督学习相对应的是非监督学习，此时数据没有类别信息，也不会给定目标值。在非监督学习中，
将数据集合分成由类似的对象组成的多个类的过程被称为聚类；将寻找描述数据统计值的过程称之为密度估计。

4.确定选择监督学习算法之后，需要进一步确定目标变量类型，如果目标变量是离散型，如是／否、1／2／3、A／B／C 或者
红／黄／黑等，则可以选择分类算法；如果目标变量是连续型的数值，如0.0～100.00、-999～999或者+∞～-∞等，则需要选择回归算法。

5.k近邻算法采用测量不同特征值之间的距离方法进行分类。
  (1) 优点：精度高、对异常值不敏感、无数据输入假定。
  (2) 缺点：计算复杂度高、空间复杂度高。   
  (3) 适用数据范围：数值型和标称型。
  (4) 工作原理：存在一个样本数据集合，也称作训练样本集，并且样本集中每个数据都存在标签，即我们知道样本集中每一数据
与所属分类的对应关系。输入没有标签的新数据后，将新数据的每个特征与样本集中数据对应的特征进行比较，
然后算法提取样本集中特征最相似数据(最近邻)的分类标签。一般来说，我们只选择样本数据集中前 k 个最相似的数据，
这就是 k-近邻算法中 k 的出处，通常 k 是不大于 20 的整数。最后，选择 k 个最相似数据中出现次数最多的分类，作为新数据的分类。
  (5) k 近邻算法的一般流程:
   ① 收集数据：可以使用任何方法。
   ② 准备数据：距离计算所需要的数值，最好是结构化的数据格式。
   ③ 分析数据：可以使用任何方法。
   ④ 训练算法：此步骤不适用于 k 近邻算法。
   ⑤ 测试算法：计算错误率。
   ⑥ 使用算法：首先需要输入样本数据和结构化的输出结果，然后运行 k 近邻算法。判定输入数据分别属于哪个分类，
最后应用对计算出的分类执行后续的处理。

6.使用 Python 导入数据。
  科学计算包 NumPy；运算符模块: kNN.py
  from numpy import *
  import operator
  from os import listdir # 函数 listdir 可以列出给定目录的文件名
  def createDataSet() :
     group = array ([[1.0, 1.1], [1.0, 1.0], [0, 0], [0, 0.1]])
     labels = ['A', 'A', 'B', 'B']
     return group, labels

7.实施 kNN 分类算法: classify0()。
  def classify0(inX,dataSet,labels,k):
    dataSetSize = dataSet.shape[0]
    # (以下三行)距离计算
    diffMat = tile(inX,(dataSetSize,1)) - dataSet
    sqDiffMat = diffMat**2
    sqDisstances = sqDiffMat.sum(axis=1)
    distances = sqDisstances**0.5
    sortedDistIndicies = distances.argsort()
    classCount = {}
    # (以下两行)选择距离最小的k个点
    for i in range(k):
        voteIlable = labels[sortedDistIndicies[i]]
        classCount[voteIlable] = classCount.get(voteIlable, 0) + 1
    sortedClassCount = sorted(classCount.items(), 
    # 排序
        key = operator.itemgetter(1), reverse=True)
    return sortedClassCount[0][0]
  classify0() 函数有 4 个输入参数：用于分类的输入向量是 inX，输入的训练样本集为 dataSet，标签向量为labels，
最后的参数 k 表示用于选择最近邻居的数目，其中标签向量的元素数目和矩阵 dataSet 的行数相同。

8.准备数据：从文本文件中解析数据。
  def file2matrix(filename) :
     fr = open(filename)
     arrayOlines = fr.readlines()
     numberOfLines = len(arrayOlines)
     # 得到文件行数
     returnMat = zeros((numberOfLines, 3))
     # 创建返回的 Numpy 矩阵
     classLabelVector = []
     returnMat = zeros((numberOfLines, 3))  
     classLabelVector = []  
     index =0  
     # 解析文件数据到列表  
     for line in arrayOlines:  
         line = line.strip()  
         listFormLine = line.split('\t')  
         returnMat[index,:] = listFormLine[0:3]  
         classLabelVector.append(int(listFormLine[-1]))  
         index += 1  
     return returnMat, classLabelVector

9.准备数据：归一化特征值。
  def autoNorm(dataSet):
     minVals = dataSet.min(0)      # 每一列的最小值
     maxVals = dataSet.max(0)      # 每一列的最大值
     ranges = maxVals - minVals    # 幅度
     normDataSet = zeros(shape(dataSet))  # 创建一个一样规模的零数组
     m = dataSet.shape[0]          # 取数组的行
     normDataSet = dataSet - tile(minVals, (m,1))  # 减去最小值
     normDataSet = normDataSet / tile(ranges, (m,1))   # 特征值相除
     # 再除以幅度值，实现归一化，tile 功能是创建一定规模的指定数组
     return normDataSet, ranges, minVals

10.测试分类器效果。
   def datingClassTest():     # 分类器针对约会网站的测试代码
     hoRatio = 0.50
     datingDataMat, datingLabels = file2matrix('datingTestSet.txt') # load data set from file
     normMat, ranges, minVals = autoNorm(datingDataMat)
     # 计算测试向量的数量，此步决定哪些数据用于分类器的测试和训练样本
     m = normMat.shape[0]
     numTestVecs = int(m * hoRatio)
     errorCount = 0.0
     # 计算错误率，并输出结果
     for i in range(numTestVecs):
        classifierResult = classify0(normMat[i,:], normMat[numTestVecs:m,:], datingLabels[numTestVecs:m], 3)
        print("the classifier came back with: %d, the real answer is: %d" % (classifierResult, datingLabels[i]))
        if (classifierResult != datingLabels[i]): errorCount += 1.0
     print("the total error rate is: %f" % (errorCount / float(numTestVecs)))
     print(errorCount)
    
   datingDataMat, datingLabels = file2matrix('datingTestSet.txt') # 读取文件数据
   
   datingClassTest()
   Output:
     the classifier came back with: 1, the real answer is: 3
     ...
     the classifier came back with: 1, the real answer is: 3
     the total error rate is: 0.500000
     5.0
   
11.约会网站预测函数。
   def classifyPerson():
     resultList = ["not at all","in small doses","in large doses"]  
     percentTats = float(input("percentage of time spent playing video games? "))  
     ffMiles = float(input("frequent flier miles earned per year? "))  
     iceCream = float(input("liters of ice cream consumed per year? "))  
     datingDataMat, datingLabels = file2matrix("datingTestSet.txt") 
     # 需要对新来的测试集也做归一化，故需要用到 ranges 和 minVals 两个变量  
     normMat,ranges,minVals = autoNorm(datingDataMat)  
     inArr = array([ffMiles, percentTats, iceCream])  
     classifierResult = classify0((inArr - minVals) / ranges, normMat, datingLabels, 3)  
     print ("You will probably like this person: ", resultList[classifierResult - 1])
     
   classifyPerson()
   Output:  
     percentage of time spent playing video games? 100
     frequent flier miles earned per year? 200
     liters of ice cream consumed per year? 10
     You will probably like this person:  in large doses

12.将图像转换为向量：img2vector。
   该函数创建 1 x 1024 的 NumPy 数组，然后打开给定的文件，循环读出文件的前 32 行，并将每行的头 32 个字符值
存储在 NumPy 数组中，最后返回数组。
   def img2vector(filename):
      imgVect = zeros((1, 1024))  
      fr = open(filename)  
      for i in range(32):  
         linestr = fr.readline()  
         for j in range(32):  
           imgVect[0, 32*i + j] = int(linestr[j])  
      return imgVect 

13.使用 k 近邻算法识别手写数字。
   def handwritingClassTest():
      hwLabels = []
      trainingFileList = listdir('trainingDigits')   # 获取目录内容
      m = len(trainingFileList)   # 目录中有多少文件
      trainingMat = zeros((m,1024))    # 创建一个 m 行 1024 列的训练矩阵，该矩阵的每一行存储一个图像
      # 从文件名解析出分类数字，该目录的文件按照规则命名，如文件 9_45.txt 的分类是 9，它是数字 9 的第 45 个实例
      for i in range(m):
         fileNameStr = trainingFileList[i]
         fileStr = fileNameStr.split('.')[0]   
         classNumStr = int(fileStr.split('_')[0])
         hwLabels.append(classNumStr)    # 类代码存储到变量 hwLabels 中
         trainingMat[i,:] = img2vector('trainingDigits/%s' % fileNameStr)
         # 对 testDigits 执行相似操作，但是不载入矩阵，而是使用 classify() 函数测试该目录下的每一个文件
      testFileList = listdir('testDigits')     # iterate through the test set
      errorCount = 0.0
      mTest = len(testFileList)
      for i in range(mTest):
         fileNameStr = testFileList[i]
         fileStr = fileNameStr.split('.')[0]  
         classNumStr = int(fileStr.split('_')[0])
         vectorUnderTest = img2vector('testDigits/%s' % fileNameStr)
         classifierResult = classify0(vectorUnderTest, trainingMat, hwLabels, 3)
         print("the classifier came back with: %d, the real answer is: %d" % (classifierResult, classNumStr))
         if (classifierResult != classNumStr): 
            errorCount += 1.0
      print("\nthe total number of errors is: %d" % errorCount)
      print("\nthe total error rate is: %f" % (errorCount/float(mTest)))

     handwritingClassTest()
     Output:
       the classifier came back with: 0, the real answer is: 0
       ...
       the classifier came back with: 9, the real answer is: 9
     
       the total number of errors is: 8
     
       the total error rate is: 0.080000

14.决策树的一般流程:
   (1) 收集数据：可以使用任何方法。
   (2) 准备数据：树构造算法只适用于标称型数据，因此数值型数据必须离散化。
   (3) 分析数据：可以使用任何方法，构造树完成之后，我们应该检查图形是否符合预期。
   (4) 训练算法：构造树的数据结构。
   (5) 测试算法：使用经验树计算错误率。
   (6) 使用算法：此步骤可以适用于任何监督学习算法，而使用决策树可以更好地理解数据的内在含义。

15.简单鱼鉴定数据集。
  def createDataSet():    # 简单鉴定数据集
     dataSet = [[1, 1, 'yes'],
                [1, 1, 'yes'],
                [1, 0, 'no'],
                [0, 1, 'no'],
                [0, 1, 'no']]
     labels = ['no surfacing','flippers']
     # change to discrete values
     return dataSet, labels
  
16.计算给定数据集的香农熵。
   from math import log
   def calcShannonEnt(dataSet):    # 计算给定数据集的香农熵
      numEntries = len(dataSet)    # 计算数据集中的实例总数
      labelCounts = {}  # 创建数据字典，其键值是最后一列的数值
      for featVec in dataSet:  # the the number of unique elements and their occurance  
         currentLabel = featVec[-1]
         if currentLabel not in labelCounts.keys(): 
            labelCounts[currentLabel] = 0    # 如果当前键值不存在，则扩展字典并将当前键值加入字典
            labelCounts[currentLabel] += 1   # 每一个键值都记录了当前类别的次数
      # 使用所有类标签发生的频率计算类别出现的概率，计算香农熵
      shannonEnt = 0.0
      for key in labelCounts:
         prob = float(labelCounts[key]) / numEntries
         shannonEnt -= prob * log(prob,2)  # log base 2
      return shannonEnt
     
   myDat, labels = createDataSet()
   print(myDat)
   print(calcShannonEnt(myDat))
   Output:
      [[1, 1, 'yes'], [1, 1, 'yes'], [1, 0, 'no'], [0, 1, 'no'], [0, 1, 'no']]
      0.9287712379549449

17.按照给定特征划分数据集。对数据集进行划分，对属性 axis 值为 value 的那部分数据进行挑选。
   def splitDataSet(dataSet, axis, value):
      # 创建新的 list 对象
      retDataSet = []
      for featVec in dataSet:
         if featVec[axis] == value:
             reducedFeatVec = featVec[:axis]   # chop out axis used for splitting
             reducedFeatVec.extend(featVec[axis+1:])
             retDataSet.append(reducedFeatVec)
      return retDataSet
    
   retDataSet = splitDataSet(myDat, 1, 0)
   print(retDataSet)
   Output:
      [[1, 'no']]
   
18.选择最好的数据集划分方式。
   def chooseBestFeatureToSplit(dataSet):
      numFeatures = len(dataSet[0]) - 1      # the last column is used for the labels
      baseEntropy = calcShannonEnt(dataSet)
      bestInfoGain = 0.0; bestFeature = -1
      tableVocabulary = {}
      print("baseEntropy=" + str(baseEntropy))
      for i in range(numFeatures):           # iterate over all the features
         # (以下两行)创建唯一的分类标签列表
         featList = [example[i] for example in dataSet]  # create a list of all the examples of this feature
         uniqueVals = set(featList)          # get a set of unique values
         newEntropy = 0.0
         # (以下五行)计算每种划分方式的信息熵
         for value in uniqueVals:
            subDataSet = splitDataSet(dataSet, i, value)
            prob = len(subDataSet) / float(len(dataSet))
            newEntropy += prob * calcShannonEnt(subDataSet)
         tableVocabulary[i] = newEntropy
         infoGain = baseEntropy - newEntropy  # calculate the info gain; ie reduction in entropy
         if (infoGain > bestInfoGain):        # compare this to the best gain so far
            # 计算最好的信息增益
            bestInfoGain = infoGain           # if better than current best, set to best
            bestFeature = i
      return tableVocabulary                  # finding the min and min is the best choice
   
  table = chooseBestFeatureToSplit(myDat)
  min = 1.7976931348623157e+308
  nick = 0
  for key in table:
     print(str(key) + "->" + str(table[key]))
     if table[key] <= min:
        nick = key
        min = table[key]
  Output:
     baseEntropy = 0.9287712379549449
     0->0.8339850002884626
     1->0.8

19.多数表决决定叶子节点的分类。
   def majorityCnt(classList):
      classCount = {}
      for vote in classList:
         if vote not in classCount.key():
            classCount[vote] = 0;
         classCount[vote] += 1
      # operator 操作键值排序字典
      sortedClassCount = sorted(classCount.iteritems(), key = operator.itemgetter(1), reverse = True)
      return sortedClassCount[0][0]

20.创建树。
   def createTree(dataSet, labels):  
      classList = [example[-1] for example in dataSet] 
      # (以下两行)类别完全相同则停止继续划分，返回类标签-叶子节点
      if classList.count(classList[0]) == len(classList):     
         return classList[0]  
      # (以下两行)遍历完所有特征值时返回出现次数最多的
      if len(dataSet[0]) == 1:  
         return majorityCnt(classList) 
      bestFeat = chooseBestFeatureToSplit(dataSet)  
      bestFeatLabel = labels[bestFeat]  
      myTree = {bestFeatLabel:{}}  
      # 得到列表包含的所有属性值
      del(labels[bestFeat])  
      featValues = [example[bestFeat] for example in dataSet] 
      uniqueVals = set(featValues)  
      for value in uniqueVals:  
         subLabels = labels[:]  
         # 递归调用 createTree() 函数，并且将返回的 tree 插入到 myTree 字典中，  
         # 利用最好的特征划分的子集作为新的 dataSet 传入到 createTree() 函数中。  
         myTree[bestFeatLabel][value] = createTree(splitDataSet(dataSet, bestFeat, value), subLabels)  
      return myTree  
  
   myTree = createTree(myDat, labels)
   print(myTree)
   Output:
      {'flippers': {0: 'no', 1: {'no surfacing': {0: 'no', 1: 'yes'}}}}

21.使用文本注解绘制树节点。
   import matplotlib.pyplot as plt
   # (以下三行)定义文本框和箭头格式
   decisionNode = dict(boxstyle = 'sawtooth', fc = '0.8')
   leafNode = dict(boxstyle = 'round4', fc = '0.8')
   arrow_args = dict(arrowstyle = '<-')
   # (以下两行)绘制带箭头的注解
   def plotNode(nodeTxt, centerPt, parentPt, nodeType):
       createPlot.ax1.annotate(nodeTxt, xy = parentPt, xycoords = 'axes fraction',\
              xytext = centerPt, textcoords = 'axes fraction',\
              va = 'center', ha = 'center', bbox = nodeType, \
              arrowprops = arrow_args)

   # 使用文本注解绘制树节点
   def createPlot0():
       fig = plt.figure(1, facecolor = 'white')
       fig.clf()
       createPlot.ax1 = plt.subplot(111, frameon = False)
       plotNode('a decision node', (0.5, 0.1), (0.1, 0.5), decisionNode)
       plotNode('a leaf node', (0.8, 0.1), (0.3, 0.8), leafNode)
       plt.show()

   createPlot0()

22.获取叶节点的数目和树的层数。
   def getNumLeafs(myTree):
       numLeafs = 0
       firstSides = list(myTree.keys())
       firstStr = firstSides[0]
       secondDict = myTree[firstStr]
       for key in secondDict.keys():
           # (以下三行)测试节点的数据类型是否为字典
           if(type(secondDict[key]).__name__ == 'dict'):
              numLeafs += getNumLeafs(secondDict[key])
           else: numLeafs += 1
       return numLeafs

   def getTreeDepth(myTree):
       maxDepth = 0
       firstSides = list(myTree.keys())
       firstStr = firstSides[0]
       secondDict = myTree[firstStr]
       for key in secondDict.keys():
           if(type(secondDict[key]).__name__ == 'dict'):
              thisDepth = 1 + getTreeDepth(secondDict[key])
           else: thisDepth = 1
           if thisDepth > maxDepth: maxDepth = thisDepth
       return maxDepth

23.输出预先存储的树信息。
   def retrieveTree(i):
       listOfTrees = [{'no surfacing': {0: 'no', 1:{'flippers':{0: 'no', 1: 'yes'}}}},\
                      {'no surfacing': {0: 'no', 1:{'flippers':{0: {'head': {0: 'no', 1: 'yes'}}, 1: 'no'}}}}]
       return listOfTrees[i]
       
   myTree = retrieveTree(1)
   print(getNumLeafs(myTree))
   print(getTreeDepth(myTree))
   Output:
      4
      3

24.绘制一棵完整的树。
  # (以下四行)在父子节点间填充文本信息
  def plotMidText(cntrPt, parentPt, txtString):
      xMid = (parentPt[0] - cntrPt[0]) / 2.0 + cntrPt[0]
      yMid = (parentPt[1] - cntrPt[1]) / 2.0 + cntrPt[1]
      createPlot.ax1.text(xMid, yMid, txtString, va="center", ha="center", rotation=30)

  def plotTree(myTree, parentPt, nodeTxt): # if the first key tells you what feat was split on
      # (以下两行)计算宽和高
      numLeafs = getNumLeafs(myTree)  # this determines the x width of this tree
      depth = getTreeDepth(myTree)
      firstSides = list(myTree.keys())
      firstStr = firstSides[0]     # the text label for this node should be this
      cntrPt = (plotTree.xOff + (1.0 + float(numLeafs)) / 2.0 / plotTree.totalW, plotTree.yOff)
      # 标记子节点属性值
      plotMidText(cntrPt, parentPt, nodeTxt)
      plotNode(firstStr, cntrPt, parentPt, decisionNode)
      secondDict = myTree[firstStr]
      # (以下两行)减小y偏移
      plotTree.yOff = plotTree.yOff - 1.0 / plotTree.totalD
      for key in secondDict.keys():
          # test to see if the nodes are dictonaires, if not they are leaf nodes
          if type(secondDict[key]).__name__ == 'dict':
             plotTree(secondDict[key], cntrPt, str(key))    # recursion
          else:   # it's a leaf node print the leaf node
             plotTree.xOff = plotTree.xOff + 1.0 / plotTree.totalW
             plotNode(secondDict[key], (plotTree.xOff, plotTree.yOff), cntrPt, leafNode)
             plotMidText((plotTree.xOff, plotTree.yOff), cntrPt, str(key))
             plotTree.yOff = plotTree.yOff + 1.0 / plotTree.totalD
       # if you do get a dictonary you know it's a tree, and the first element will be another dict

   def createPlot(inTree):
       fig = plt.figure(1, facecolor='white')
       fig.clf()
       axprops = dict(xticks=[], yticks=[])
       createPlot.ax1 = plt.subplot(111, frameon=False, **axprops)    # no ticks
       # createPlot.ax1 = plt.subplot(111, frameon=False) # ticks for demo puropses
       plotTree.totalW = float(getNumLeafs(inTree))
       plotTree.totalD = float(getTreeDepth(inTree))
       plotTree.xOff = -0.5 / plotTree.totalW;
       plotTree.yOff = 1.0;
       plotTree(inTree, (0.5, 1.0), '')
       plt.show()

   createPlot(myTree)

25.使用决策树的分类函数。
   def classify(inputTree, featLabels, testVecs):
       firstSides = list(myTree.keys())
       firstStr = firstSides[0] 
       secondDict = inputTree[firstStr]
       # 将标签字符串转换为索引
       featIndex = featLabels.index(firstStr)
       for key in secondDict.keys():
           if testVec[featIndex] == key:    # 比较特征值，决策树是根据特征的值划分的
              if type(secondDict[key]).__name__=='dict':  # 比较是否到达叶结点
                 classLabel = classify(secondDict[key], featLabels, testVec)  # 递归调用
              else: classLabel = secondDict[key]
       return classLabel
       
26.使用 pickle 模块存储决策树。
   def storeTree(putTree, filename):  # pickle序列化对象，可以在磁盘上保存对象
       import pickle
       fw = open(filename, 'w')
       pickle.dump(inputTree, fw)
       fw.close()

   def grabTree(filename):  # 并在需要的时候将其读取出来
       import pickle
       fr = open(filename)
       return pickle.load(fr)
 
27.词表到向量的转换函数。
   def loadDataSet():
       postingList=[['my', 'dog', 'has', 'flea', 'problems', 'help', 'please'],
             ['maybe', 'not', 'take', 'him', 'to', 'dog', 'park', 'stupid'],
             ['my', 'dalmation', 'is', 'so', 'cute', 'I', 'love', 'him'],
             ['stop', 'posting', 'stupid', 'worthless', 'garbage'],
             ['mr', 'licks', 'ate', 'my', 'steak', 'how', 'to', 'stop', 'him'],
             ['quit', 'buying', 'worthless', 'dog', 'food', 'stupid']]
       classVec = [0, 1, 0, 1, 0, 1]    # 分别表示标签, 1 代表侮辱性文字, 0 代表正常言论
       return postingList, classVec     # 返回输入数据和标签向量

   def createVocabList(dataSet):
       vocabSet = set([])     # 创建一个空集
       for document in dataSet:
           vocabSet = vocabSet | set(document)  # 创建两个集合的并集
       return list(vocabSet)  # 输出不重复的元素

   def setOfWords2Vec(vocabList, inputSet):   # 判断了一个词是否出现在一个文档当中
       returnVec = [0]*len(vocabList)  # 创建一个其中所含元素都为 0 的向量
       for word in inputSet:
           if word in vocabList:
               returnVec[vocabList.index(word)] = 1
           else: print("the word: %s is not in my Vocabulary!" % word)
       return returnVec   # 输入中的元素在词汇表时，词汇表相应位置为 1，否则为 0
```
