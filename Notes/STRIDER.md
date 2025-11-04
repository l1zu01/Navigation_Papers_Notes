### 对这篇文章要解决的VLN任务进行分析：
解决的是在连续环境中、route goal兼容、No prior exploration的VLN任务。

论文在 VLN-CE(R2R-CE, RxR-CE) 数据集上表现出色 ，该数据集提供的是“step-by-step and following instructions”；


### 论文研究动机：

感觉主要有两点：

1.给要预测的waypoint来一个空间结构上的限制(编码空间结构上的先验)，这样可以使得预测结果更稳定和统一，减少迂回和偏差。

2.agent对指令完成进度的了解很关键。很多时候模型对自己任务进度不自知，从而导致一些错误路径。

### 论文提出的方法分析：
Memory building 方式: Map-based Memory+Foundation Model-Based

在论文的Navigable Region Extraction部分，说了它用RGB-D构建了2D top-down plane，就类似于传统的“显式记忆”（如栅格地图）。接着把它变成Region Skeleton，然后用策略选出候选waypoint，就和一些论文的frontier map差不多。

接着把图像和waypoint选项(每一个step都有全景视图，可以针对每个waypoint的方向截取视角)输给VLM(Qwen-VL-max)，让它输出waypoint相应的描述文本。接着把这些所有的候选waypoint文本和上一次得到的feedback f(这是个文本，描述了截止到上一步，agent已经完成了任务进度的多少，以及下一步应该做什么) 以及任务指令L输给LLM(GPT-4o)，让LLM选择一个waypoint，然后底层导航可以生成动作导航过去(这块没细说，不是重点)。

然后根据上一步的observation和这一步的observation，以及子任务描述(也没细说怎么的来)，用VLM生成这一步的feedback f。

整个流程运用大模型的zero-shot，不需要训练。

