---
title: 人脸数据库Yale
urlname: face_database_yale
date: 2017-03-09 16:08:41
tags:
 - Face Database
---

Yale人脸数据库在人脸识别论文中频繁用到，因此做人脸识别这方面研究还是存一份这个数据库以备用。在网上可以很容易下载这个数据库，我在浙大蔡登教授的主页上下载了这个数据库（[Four face databases in matlab format][1])，主页上这个数据库有两种大小可以选择下载，一个是图像大小是32x32的数据库，另一个是64x64，本文选择的是32x32。

## 数据集说明

主页上有对这个数据集说明：

> Contains 165 grayscale images in GIF format of 15 individuals. There are 11 images per subject, one per different facial expression or configuration: center-light, w/glasses, happy, left-light, w/no glasses, normal, right-light, sad, sleepy, surprised, and wink.

## 显示人脸图像

主页上提供显示数据库中人脸图像的代码，整理如下：

{% codeblock %}
% load data
load('Yale_32x32.mat');

faceW = 32;
faceH = 32;
numPerLine = 11;
ShowLine = 2;

Y = zeros(faceH*ShowLine, faceW*numPerLine);
for i=0:ShowLine-1
    for j=0:numPerLine-1
        Y(i*faceH+1:(i+1)*faceH, j*faceW+1:(j+1)*faceW) = reshape(fea(i*numPerLine+j+1,:), [faceH, faceW]);
    end
end

imagesc(Y);
colormap(gray);

pause();
{% endcodeblock %}

## 导出数据到文本文件

为了后续使用，我将人脸数据导出到文本文件中，文本每行包含了一张人脸数据以及该人脸对应的类标，写了一个简短的代码：

{% codeblock %}
clear all; close all; clc;

% Load data
% Contains variables 'fea' and 'gnd'
% Each row of 'fea' is a face; 'gnd' is the label
load('Yale_32x32.mat');

output = 'data.txt';
[row, col] = size(fea);

file = fopen(output, 'w');
for i=1:row
    for j=1:col
        fprintf(file, '%d ', fea(i,j));
    end
    fprintf(file, '%d\n', gnd(i));
end

fclose(file);
{% endcodeblock %}

代码运行后生成一个文本文件 __data.txt__。

[1]: http://www.cad.zju.edu.cn/home/dengcai/Data/FaceData.html
