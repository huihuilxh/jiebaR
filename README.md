# jiebaR
1. 分词

jiebaR提供了四种分词模式，可以通过函数worker()来初始化分词引擎，使用函数segment()进行分词。具体使用?worker查看帮助

> hezhao= '落英缤纷我的灵魂和着节奏穿梭行走幻觉爱上这花瓣'
> mixseg =worker() #使用默认参数，混合模型（MixSegment）
> segment(hezhao, mixseg)
#等价于mixseg[text]
#也等价于mixseg <= text
 [1] "落英缤纷" "我"       "的"       "灵魂"     "和"       "着"       "节奏"    
 [8] "穿梭"     "行走"     "幻觉"     "爱上"     "这"       "花瓣" 

支持文件分词，省去读取文件后再进行分词的麻烦
segment('D:/hezhao.txt', mixseg) #自动判断输入文件编码模式，默认文件输出在同目录下。
#等价于mixseg['D:/test.txt']
#也等价于mixseg <= 'D:/test.txt'

 2.分词引擎

在调用worker()函数时，我们实际是在加载jiebaR库的分词引擎。jiebaR库提供了几种分词引擎。
最大概率法（MPSegment）： 
负责根据Trie树构建有向无环图和进行动态规划算法，是分词算法的核心。
> mpseg <- worker('mp') #最大概率法（MPSegment）
> mpseg[hezhao]
 [1] "落英缤纷" "我"       "的"       "灵魂"     "和"       "着"       "节奏"    
 [8] "穿梭"     "行走"     "幻觉"     "爱"       "上"       "这"       "花瓣"   
 
 隐式马尔科夫模型（HMMSegment）： 
是根据基于人民日报等语料库构建的HMM模型来进行分词，主要算法思路是根据(B,E,M,S)四个状态来代表每个字的隐藏状态。 
HMM模型由dict/hmm_model.utf8提供。分词算法即viterbi算法。
> hmmseg <- worker('hmm') #隐式马尔科夫模型（HMMSegment）
> hmmseg[hezhao]
 [1] "落英缤纷" "我"       "的"       "灵魂"     "和"       "着"       "节奏"    
 [8] "穿"       "梭行"     "走"       "幻觉"     "爱上"     "这花瓣"  
 
 混合模型（MixSegment）： 
是四个分词引擎里面分词效果较好的类，结它合使用最大概率法和隐式马尔科夫模型。
> mixseg <- worker('mix') #混合模型（MixSegment）
> mixseg[hezhao]
 [1] "落英缤纷" "我"       "的"       "灵魂"     "和"       "着"       "节奏"    
 [8] "穿梭"     "行走"     "幻觉"     "爱上"     "这"       "花瓣" 
 
 索引模型（QuerySegment）： 
先使用混合模型进行切词，再对于切出来的较长的词，枚举句子中所有可能成词的情况，找出词库里存在。
> queryseg <- worker('query') #索引模型（QuerySegment）
> queryseg[hezhao]
 [1] "落英缤纷" "我"       "的"       "灵魂"     "和"       "着"       "节奏"    
 [8] "穿梭"     "行走"     "幻觉"     "爱上"     "这"       "花瓣" 
 
 3. 标注词性

可以使用 <=.tagger 或者 tag 来进行分词和词性标注，词性标注使用混合模型模型分词，标注采用和 ictclas 兼容的标记法。
> tagseg <- worker('tag')
> tagseg[hezhao]
         i          r         uj          n          c         uz          n 
"落英缤纷"       "我"       "的"     "灵魂"       "和"       "着"     "节奏" 
         v          v          n          x          r          n 
    "穿梭"     "行走"     "幻觉"     "爱上"       "这"     "花瓣" 
    
    4. 提取关键词

关键词提取所使用逆向文件频率（IDF）文本语料库可以切换成自定义语料库的路径，使用方法与分词类似。topn参数为关键词的个数。
> keys = worker('keywords', topn = 2) #参数topn表示提取排在最前的关键词个数
> keys <= hezhao
   12.1089    9.18218 
"落英缤纷"     "穿梭" 

5. simhash计算

对中文文档计算出对应的simhash值。simhash是谷歌用来进行文本去重的算法，现在广泛应用在文本处理中。
Simhash引擎先进行分词和关键词提取，后计算Simhash值和海明距离。
> simhasher = worker("simhash", topn = 2)
> simhasher <= hezhao
$simhash
[1] "3194220345757161277"

$keyword
   12.1089    9.18218 
"落英缤纷"     "穿梭" 

6. 快速模式

无需使用函数worker()，使用默认参数启动引擎，并立即进行分词。使用qseg（quick segmentation），使用默认分词模式，自动建立分词引擎，
类似于ggplot2包里面的qplot函数。
> qseg <= hezhao
 [1] "落英缤纷" "我"       "的"       "灵魂"     "和"       "着"       "节奏"    
 [8] "穿梭"     "行走"     "幻觉"     "爱上"     "这"       "花瓣"    
Warning message:
