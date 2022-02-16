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

#### 代码升级内容（Swin_Transformer.py）
1. 将layer_norm1放到attention后面，将layer_norm2放到MLP后面；
   - 在class SwinTransformerBlock里面重新定义self.norm111和self.norm222; 如果norm1和norm2的名称不变的话使用SwinTransformer V1预训练权重会让norm收敛很慢；
   - 在forward函数里面将self.norm111放到self.attn后面，将self.norm222放到self.mlp执行后。
   
2. 将attention中q,k的计算更新为Cosine方式并除以可学习的系数;  
   - 修改class WindowAttention，增加self.Cosqk和self.qk_scale计算attn： attn = self.Cosqk(q, k) * self.qk_scale
   
3. 将Attention中相对位置的Bij计算方式修改为对相对位置进行log—CPB后放入的MLP网络
   - 修改class WindowAttention，通过log-CPB计算Bij相对位置；
   - 增加self.mlp_bias计算相对位置间的bias。
   - index = self.relative_position_index1.reshape([-1]).astype('float32')
   - index = paddle.sign(index) * paddle.log(1 + paddle.abs(index))
   - index = paddle.reshape(index, shape=[-1, 2])
   - relative_position_bias = self.mlp_bias(index)


#### 训练过程

1.  根据PaddleClas介绍内容，下载SwinT B V1的预训练权重；使用AI Studio上面压缩过的数据集Light_ILSVRC2012在四卡脚本任务下训练；
2.  冻结PatchEmbed层，使用配置文件SwinTransformer_base_patch4_window12_96.yaml进行96x96图片size进行预训练，训练20 epochs后验证集精度在42%左右；
3.  基于96x96的训练结果，预训练192x192图片size，训练10 epochs后验证集精度在72%左右；
4.  基于192x192训练结果，预训练384x384图片size，训练5 epochs后验证集精度在82%左右，loss在2.64左右；经过多次调整learning_rate最终验证精度在82.7%一直无法再提高。

#### 总结

1.  前半段很顺利，当使用384x384训练达到精度80%左右后一直提高有限，多次调整方法从头开始训练但是都无法提高。
2.  四卡环境训练一个epoch需要30小时左右，验证过程很慢很难做到全面的排查。
3.  不知道压缩后的数据集影响有多大，未压缩的数据集要重新整理标签所以也没机会验证。
4.  有个小失误，最后阶段PatchEmbed冻结没有放开，本来以为放开了；目前在验证这种情况对精度有多少影响。

