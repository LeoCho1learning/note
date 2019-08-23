## yolov3(darknet)训练log参数解读

```
Region 82 Avg IOU: 0.194206, Class: 0.578165, Obj: 0.598588, No Obj: 0.522044, .5R: 0.000000, .75R: 0.000000,  count: 3
Region 94 Avg IOU: 0.490475, Class: 0.221153, Obj: 0.277844, No Obj: 0.492837, .5R: 0.000000, .75R: 0.000000,  count: 1
Region 106 Avg IOU: -nan, Class: -nan, Obj: -nan, No Obj: 0.411697, .5R: -nan, .75R: -nan,  count: 0
18: 799.041443, 810.360657 avg, 0.000000 rate, 1.096970 seconds, 2304 images
```
region82, region94, region106代表yolov3的输出分别在第82、94、106层，所以log的开头是按82、94、106来交替出现的。

### 输出层的信息
Region 82/94/106——在yolov3中的输出层

Avg IOU——

### Bacth的信息
18——当前的batch数的id

799.041443——这一个batch的loss

810.360657——进行到当前batch的平均loss

0.0000 rate——学习率（这里是由于burn_in参数，对学习有单独的计算方法，因此为0）

1.096970 seconds——当前batch所花费的时间

2304 images——目前参与训练的图片总数

## burn-in

lr = base_lr * power(batch_num/burn_in,pwr)