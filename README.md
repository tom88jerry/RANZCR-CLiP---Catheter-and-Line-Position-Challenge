# RANZCR-CLiP---Catheter-and-Line-Position-Challenge

## Table of contents
* [Goal](#Goal)
* [Challenges](#challenges)
* [Summary](#summary)
* [Lessons learned](#lessons-learned)


My learning journey of catheter and line position challenge. I spent around 2 full weeks on this competition. 

## Goal
To classify the presence and correct placement of tubes on chest x-rays to save lives. 
They are a total of 11 calsses:
ETT - Abnormal - endotracheal tube placement abnormal
ETT - Borderline - endotracheal tube placement borderline abnormal
ETT - Normal - endotracheal tube placement normal
NGT - Abnormal - nasogastric tube placement abnormal
NGT - Borderline - nasogastric tube placement borderline abnormal
NGT - Incompletely Imaged - nasogastric tube placement inconclusive due to imaging
NGT - Normal - nasogastric tube placement borderline normal
CVC - Abnormal - central venous catheter placement abnormal
CVC - Borderline - central venous catheter placement borderline abnormal
CVC - Normal - central venous catheter placement normal
Swan Ganz Catheter Present
It should be noted that it is a multi-label classification, so multiple labels may exist in one single image.

## Challenges 
There are a few challenges in this competition.
1. Imbalanced distribution between each class. There is a huge difference between CVC and other classes. This may create a bias to the prediction. Hence, a weighted loss function or oversampling must be adopted. 
2. Imagenet pretrained weight is not good enough in predicting X-ray image.
3. It requires huge computation resources because of image dimensions (2048 x 2048 pixels). I only have GPU V100, which can only run the image upto 640x640 with a reasonable batch size.

## Summary
I joined the competition towards the end of the competition, many people trained the model on NIH dataset first before training on the competition data. This way it can improve the performance. However, it may cause the data leakage and there are many fault positives in NIH dataset. 
In addition, NIH dataset contains other dieaseses that are not relevant to the competition. Hence, training the teacher model using the competition annotation first then train student using NIH dataset will yield better result. Then, the pretrained weight from NIH dataset can be used to train the competition dataset. 

To tackle the first challenge of imbalanced distribution, I did not use any technique to deal with this because
1. When I checked results of prediction, the problem did not occur in rare classes but it occursed in CVC class (lower accuracy). 
2. If you use external data, it requires hand labelling, which is time consuming and it may create more harm than good because I don't have knowledge of medical and x-ray.

In this tournament, I used pretrained weight from amarli as a starting point due to time constraints. There is only a single mislablled image and a couple of images that are flip between left and right. However, the horizontal flip augmentation can easily handle this. 

In terms of architecture, Resnet200d and Efficientnet are the two popular architectures, so I trained four models based on these two architectures. Although ViT was very success in Cassava competition, it was not suitable for this competition because of the input dimensions.
There is a clear improvement of using larger image in this competition. Top teams are using image larger than 1024 (up to 1536 pixels). 

Since it is multi-label classification, I used BCEWithLogitsLoss and Focal loss functions. 

**My final ranking: Private LB 49/1549 teams 0.972 (Top 4% silver medal🥈) Team 2 members**

## Lessons learned, 
I learned a lot in this competition. 
Firstly, attention modules. I tried different attention modules such as Convolutional Block Attention Module (CBAM), channel attention module, spatial attention module, and efficient channel attention module (ECA).
Secondly, multi-stages training (Distrilled learning) to improve the accuracy.
Thirdly, segmentation. I did not implement this in my model due to limited resouces of GPU, but many competitiors reported a big boost using both segementation and classification. It is very interesting approach.
