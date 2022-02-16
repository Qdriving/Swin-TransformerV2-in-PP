# Swin Transformer V2: Scaling Up Capacity and Resolution in Paddle

#### 介绍

本文基于飞桨PaddleClas/Swin Transformer代码按照Swin Transformer V2的内容进行修改，修改文件结构目录如下：
- ---ppcls
   - ---arch
     - ---backbone
        - ---model_zoo
           - ---Swin_Transformer.py         ###### 修改的代码部分
            
   - ---config
      - ---ImageNet
         - ---SwinTransformer
            - ---SwinTransformer_base_patch4_window12_96.yaml          #####增加的预训练96x96配置文件
            - ---SwinTransformer_base_patch4_window12_192.yaml         #####增加的预训练192x92配置文件
            - ---SwinTransformer_base_patch4_window12_384.yaml         #####增加的预训练384x384配置文件

#### Swin_Transformer.py 主要修改内容
1. 将layer_norm1放到attention后面，将layer_norm2放到MLP后面；
  
2. 将attention中q,k的计算更新为Cosine方式并除以(乘上)可学习的系数;  
3. 将Attention中相对位置的Bij计算方式修改为对相对位置进行log—CPB后放入的MLP网络


#### 安装教程

1.  xxxx
2.  xxxx
3.  xxxx

#### 使用说明

1.  xxxx
2.  xxxx
3.  xxxx

#### 参与贡献

1.  Fork 本仓库
2.  新建 Feat_xxx 分支
3.  提交代码
4.  新建 Pull Request


#### 特技

1.  使用 Readme\_XXX.md 来支持不同的语言，例如 Readme\_en.md, Readme\_zh.md
2.  Gitee 官方博客 [blog.gitee.com](https://blog.gitee.com)
3.  你可以 [https://gitee.com/explore](https://gitee.com/explore) 这个地址来了解 Gitee 上的优秀开源项目
4.  [GVP](https://gitee.com/gvp) 全称是 Gitee 最有价值开源项目，是综合评定出的优秀开源项目
5.  Gitee 官方提供的使用手册 [https://gitee.com/help](https://gitee.com/help)
6.  Gitee 封面人物是一档用来展示 Gitee 会员风采的栏目 [https://gitee.com/gitee-stars/](https://gitee.com/gitee-stars/)
