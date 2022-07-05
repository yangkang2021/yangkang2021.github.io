## deepsort算法简析

https://blog.csdn.net/sgfmby1994/article/details/98517210

yolov3-5做检测 detection
reid做特征提取
deepsort做：检测框位置的卡尔曼滤波预测，特征相似比对， IOu也就是重叠度的计算，级联分类匹配，匈牙利分配算法。

### 距离度量图

### 个人分析图


Simple Online and Realtime Tracking with a Deep Association Metric
具有深度关联指标的简单在线和实时跟踪


总的思路：
1. ioc匹配：只取time_since_update<=1的。连续的几次ioc匹配成功开始跟踪。
2. 特征距离匹配：特征距离矩阵+ 同时卡尔曼距离的==卡方检验阈值==设置
3. 特征距离匹配且time_since_update==1，继续做iou匹配

- 成本矩阵或者效率矩阵：距离矩阵。
- 目的：让box找到自己属于哪个track。
- 数据：每一行是某个track与所有box的距离

track  | box1 | box2
---|---|---
track1 | dist1-1 | dist1-2
track2 | dist2-1 | dist2-2

- 匈牙利匹配结果：

row | col 
---|---
track-row | box-col
track-row | box-col


0. 距离和匹配
- nn_matching：基于历史数据的距离度量
- linear_assignment：
    - matching_cascade:不同优先级的min_cost_matching匹配,time_since_update的优先。
    - ==min_cost_matching:匈牙利算法==
        1. 计算距离成本矩阵
        2. 矩阵的元素超过阈值设置为阈值
        3. 匈牙利HungarianOper
        4. 处理结果
    - ~~gate_cost_matrix~~：成本矩阵的门槛设置

---

1. track
- 成员：
    - 状态
    - 特征：临时存储，会放到nn_matching
    - kal信息
    - time_since_update重复预测失败次数
    - hits:连续匹配成功次数，超过n_init即确认
- 匹配结果处理：
    - 构造函数：新的box就创建
    - update()：匹配成功处理：hits++，==time_since_update清零==
    - mark_missed()：跟丢了处理。删掉：没确认或者==time_since_update超max_age==
- predit()：卡尔曼预测，==time_since_update+1==

---

2. tracker:
- mytracker = new tracker(0.6, nn_budget=100,0.9,20*60*10,5);//20*60*10 十分钟的帧数
- 成员：内部是一个track列表+公共kf
- 构造：tracker(float max_cosine_distance, int nn_budget,float max_iou_distance, int max_age, int n_init)
- max_cosine_distance:余弦度量的最大距离，大于这个距离认为无限大。
- max_iou_distance：0.7,(1-iou)的最大距离，大于这个距离认为无限大。
- max_age=30: 1.track被删除之前最大的丢失跟踪帧数，2. 级联的level
- nn_budget：100，缓存数量，每个trackid对应的缓存特征数目。同时每个对象的多个历史特征比较。
- n_init=3：确认曲目之前的连续检测次数。如果在前n_init帧内发生未命中，则将Track状态设置为“已删除”。



1.预测:遍历track列表，做KF预测
tracker->predict();

2.匹配，结果3类：匹配成功的，轨迹未匹配成的，特征未匹配成功的

2.1 先拿confirmed的track和全部detections用min_cost_matching线性分配匹配。
- 成本矩阵计算gated_matric：特征余弦相似比较，同时用卡尔曼距离做最大值限制
- 级联匹配换句话说就是不同优先级的匹配。会根据time_since_update来对跟踪器分先后顺序，参数小的先来匹配，参数大的后匹配.

2.2 再拿(unconfirmed+前面未匹配且time_since_update==1)的track 和 前面未匹配的detections用min_cost_matching线性分配匹配
- 成本矩阵计算：iou_cost。

3.匹配结果出来
- 匹配成功：tracks[track_idx].update(...)
- 未匹配track：tracks[track_idx].mark_missed()
- 未匹配detect：_initiate_track(detections[detection_idx])

4. 把confime的track的特征记录到nn_matching

---

deepsort：
重识别：POI: Multiple Object Tracking with High Performance Detection and Appearance Feature. In BMTT, SenseTime Group Limited, 2016.
原始版本：https://github.com/nwojke/deep_sort

pytorch版本：https://github.com/ZQPei/deep_sort_pytorch

This is an implement of MOT tracking algorithm deep sort. Deep sort is basicly the same with sort but added a CNN model to extract features in image of human part bounded by a detector. This CNN model is indeed a RE-ID model and the detector used in PAPER is FasterRCNN , and the original source code is HERE.
However in original code, the CNN model is implemented with tensorflow, which I'm not familier with. SO I re-implemented the CNN feature extraction model with PyTorch, and changed the CNN model a little bit. Also, I use YOLOv3 to generate bboxes instead of FasterRCNN.


The original model used in paper is in original_model.py, and its parameter here original_ckpt.t7.
To train the model, first you need download Market1501 dataset or Mars dataset.
Then you can try train.py to train your own parameter and evaluate it using test.py and evaluate.py.

tf版本：https://github.com/LeonLok/Deep-SORT-YOLOv4
训练参考：https://github.com/nwojke/cosine_metric_learning


步骤：
读懂deep_sort_pytorch流程，试试非行人跟踪。
准备yolov3模型+deepsort模型+nms模块编译
主要的代码模块
from detector import build_detector
from deep_sort import build_tracker
核心代码
#1.准备输出：rgb图片
im = cv2.cvtColor(ori_im, cv2.COLOR_BGR2RGB)

#2.目标检测
bbox_xywh, cls_conf, cls_ids = self.detector(im)

#3.过滤出目标检测中的行人
mask = cls_ids == 0
bbox_xywh = bbox_xywh[mask]
bbox_xywh[:, 3:] *= 1.2
cls_conf = cls_conf[mask]

#3. 跟踪
outputs = self.deepsort.update(bbox_xywh, cls_conf, im)

#4.绘制结果box
if len(outputs) > 0:
    bbox_tlwh = []
    bbox_xyxy = outputs[:, :4]
    identities = outputs[:, -1]
    ori_im = draw_boxes(ori_im, bbox_xyxy, identities)

    for bb_xyxy in bbox_xyxy:
        bbox_tlwh.append(self.deepsort._xyxy_to_tlwh(bb_xyxy))

    results.append((idx_frame - 1, bbox_tlwh, identities))

读懂deep_sort_pytorch的训练流程并训练。


工作流程
复习torch基础。
读懂deepsort的RE-ID模型。
对比tf和torch的模型
移植到dnn
移植tracker c++。
自己训练模型：收集数据集和自己采集数据集。
