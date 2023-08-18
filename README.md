
<div align=center ><img src="resources/logo.png" width="8%" /></div>


# 金融垂直大模型【智海-金磐大模型】

> 智海-金磐(Gloden-GPT)：金融垂直领域的国产大模型


## :scroll: 项目背景
**智海-金磐**是浙江大学和摸象科技联合发布的一个自主研发的垂直金融的语言大模型，目前模型规模7B和13B，可进一步扩展。训练的数据集垂直于零售金融方向，涵盖了金融知识图谱、金融文本、金融对话等多种数据源。

**金磐大模型**的目标是为金融机构提供高效、智能、可信赖的语言服务，包括金融知识问答、金融文本生成、金融对话机器人等多种应用场景。


下面的介绍会从**模型训练**、**模型增强**以及**模型评测**三个方面展开。

## :computer: 模型训练
大语言模型的训练分为两个阶段：

（1）在海量文本语料上的无监督预训练，学习通用的语义表示和世界知识。

（2）在小规模数据上，进行指令微调和基于人类反馈的强化学习，更好地对齐最终任务和人类偏好。

最新研究证明了LLM的几乎所有知识都是在预训练过程中学习到的，只需要有限的指令微调数据就可以生成高质量的回复。因此，基座模型的性能是至关重要的，目前，主流的开源大语言模型主要有三个：[**LLaMA**](https://github.com/facebookresearch/llama)、[**ChatGLM**](https://github.com/THUDM/ChatGLM-6B)和[**BLOOM**](https://huggingface.co/docs/transformers/model_doc/bloom)。下面从训练数据、tokenizer和模型结构上对这三个大语言模型进行比较。

| 模型       | 模型结构       | 训练数据                             | 训练数据量     | 模型参数量                       | 词表大小 |
| ---------- | -------------- | ------------------------------------ | -------------- | -------------------------------- | -------- |
| LLaMA      | Casual decoder | 以英语为主的拉丁语系，不包含中日韩文 | 1T/1.4T tokens | 7B、13B、33B、65B                | 32000    |
| ChatGLM-6B | Prefix decoder | 中英双语，中英文比例为1:1            | 1T tokens      | 6B                               | 130528   |
| Bloom      | Casual decoder | 46种自然语言和13种编程语言，包含中文 | 350B tokens    | 560M、1.1B、1.7B、3B、7.1B、176B | 250880   |


我们的模型基座是[LLaMA v1](https://github.com/facebookresearch/llama/tree/llama_v1)，在此基础上，进行了增量预训练以及指令微调训练。

### :book: 高质量训练数据准备
我们搜集并处理了许多通用，以及金融方面的数据，以满足模型训练的需求。

### 自有数据：
已经拥有百亿级高质量金融数据集，数据集大部分来自浙大智海金磐AI员工在各个银行的工作对话脱敏语料，也包含其它路径获得的数据和AI合成的数据，也包括智海金磐积累的银行零售人工客服对话语料。


### 其他数据来源：
金融领域的基础知识和概念（金融产品的定义、分类、特点、风险收益特征等，以及金融市场的运行机制、监管规则、交易流程等）、金融领域的专业术语和名词以及它们的计算公式和含义。金融领域的市场信息，如金融市场的行情、指数、交易量、涨跌幅等，以及各类金融产品的价格、评级、评价等。
金融领域的案例和经验，如成功或失败的投资策略、风险管理方法、信用评估模型等，以及各类金融事件和新闻的分析和评论。
这些数据可以从以下几个途径获取：
- 公开的金融数据源和平台。
- 来自专业的金融机构权威的金融报告和研究。
- 来自Github或Huggingface等NLP开源平台上的大模型公开训练数据集等。


而清洗和标注数据的方法有以下几种：
- 使用自然语言处理工具和技术，如分词、词性标注、命名实体识别等，来对数据进行预处理和格式化。
- 使用知识图谱技术，如实体链接、关系抽取等，来对数据进行结构化和语义化。
- 使用基于开源Bert的智海金磐自研模型技术，对数据进行标注、分类和聚类等。
- 使用外接开源大模型如GPT3.5和GPT 4 API等，来对数据进行增强和优化。
- 使用人工审核和校验，来对数据进行质量控制和错误纠正。



**通用数据分类：**

| 分类 | 数据名称        | token数量(单位B) |
| ---- | --------------- | ---------------- |
| 网页 | 英文CommonCrawl | 25               |
| 网页 | 中文CommonCrawl | 35               |
| 网页 | 中文c4          | 7                |
| 网页 | 英文C4          | 7                |
| 代码 | 代码            | 3                |
| 百科 | 英文维基        | 5                |
| 百科 | 中文维基        | 12               |
| 百科 | 百度百科        | 3                |
| 书籍 | 英文书籍        | 2                |
| 书籍 | 中文书籍        | 2                |
| 论文 | ArXiv           | 2                |
| 论文 | 中文期刊        | 7                |
| 问答 | StackExchange   | 2                |
| 问答 | 知乎            | 4                |

**金融数据分类：**

| 分类 | 数据名称     | token数量(单位B) |
| ---- | ------------ | ---------------- |
| 金融 | 中文公告     | 24               |
| 金融 | 英文公告     | 8                |
| 金融 | 中文法律法规 | 1                |
| 金融 | 英文法律法规 | 1                |
| 金融 | 英文金融新闻 | 5                |
| 金融 | 中文金融资讯 | 1                |
| 金融 | 中文研报     | 10               |
| 金融 | 英文研报     | 5                |
| 金融 | 百科金融数据 | 15               |
| 金融 | 金融书籍     | 10               |
| 金融 | 中文财经网页 | 40               |
| 金融 | 中文金融问答 | 5                |
| 金融 | 中文金融期刊 | 2                |


### :rocket: 增量预训练

增量预训练的目的是给通用的大模型注入金融领域的知识。

### :dart: 指令微调训练
经过了增量预训练之后，在指令微调阶段，我们对模型进行了指令微调训练，其目的是让大模型具备问答的能力，能够直接与用户进行交流。



## :chart_with_upwards_trend: 模型增强

### :books: 外接知识库
语言模型对事实性问题回答可能不可靠，通过引入内置的领域知识库、客户本地知识库加强查询能使结果更加可信。


### :globe_with_meridians: 搜索增强
在领域知识库和客户本地知识库都失效的情况下，外接互联网搜索引擎作为最后的答案兜底。模型通过搜索引擎检索内容和用户指令自行整合答案，并提供搜索来源，供用户评判来源是否可靠。



## :clipboard: 模型评测
### 通用能力测评：
利用 GPT-4 为模型的生成结果打分，对于每一个问题，我们先获得ChatGPT的回复，以及金磐模型的回复，接着我们将 「ChatGPT 答案 - 候选模型答案」这样的 pair 喂给 GPT-4 打分（满分为 10 分）

### 业务能力测评：
由业务人员直接进行业务问题回答测试并打分，通过反馈调整预训练数据和指令微调数据


## :memo: 参考文献

- Touvron, H., Lavril, T., Izacard, G., Martinet, X., Lachaux, M.A., Lacroix, T., Roziere, B., Goyal, N., Hambro, E., Azhar, F., Rodriguez, A., Joulin, A., Grave, E., & Lample, G. (2023). LLaMA: Open and Efficient Foundation Language Models. arXiv preprint arXiv:2302.13971.

- BigScience. BigScience Language Open-science Open-access Multilingual (BLOOM) Language Model. .





