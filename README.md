【各个模块说明】

     main.py:建模的整体流程，详细思路见README文档
     MY_EDA.ipynb：数据分析的notebook
     egg.py:特征工程和特征选择的代码，包括用户画像类、组合画像类、贝叶斯平滑类等类编程文件
     func_lib.py：本项目中用到的自定义函数库集合
     args_adjust.py：模型调参代处


【目录】

1 数据情况 
2 项目目的 
3 项目整体实现思路 
4 细节：数据分析、特征工程、特征选择、用的算法等

1 【数据情况】

2 【项目目的】 
     通过以往的投放广告点击相关的信息【广告的id、广告是谁投的(广告商家信息)、投了什么广告(广告商品信息)、这条广告投在哪里(上下文信息)、是否购买了。】，预测假如所有新的广告投给目标用户(平台上有历史记录的用户)后(假设他点击了)时产生的购买概率，只找出购买概率高的那些广告。
(做的还是对老客户的价值挖掘，当新的预测数据输入时要利用以往(训练集)尽可能多的有用信息(比如用户画像、广告画像、店铺画像)当预测的特征，引入特征的前提要保证数据分布的一致性)

3 【项目整体实现思路】

<1> 画像生成：基础画像、组合画像
    刻画出用户画像、”店铺画像”、”广告商品画像”(三个基础画像里包含了用户、店铺、商品的基本属性特征)、”组合画像”等特征群，生成相应的特征文件,然后把这些历史信息(相应的特征文件)按相应的id merge到训练集和测试集的每条广告里作为特征；
    训练集和测试集的关系？
    1 满足最终的输入数据格式(包括数据类型、特征维度等)一致。(测试集最终的输入格式与训练集一样不是说把测试集的数据套到训练集获取特征的函数模板上!!)；
    2 满足训练集的数据分布情况与测试集一致。

<2> 删除冗余的特征：手动删除+递归消除

   滑窗合理增加训练集

   广告转化率的业务逻辑


4 【细节】：数据分析、特征工程、特征处理、用的算法等

数据分析：MY_EDA.ipynb
    基础信息：
        训练集、
        测试集样本数、
        正负样本情况、
        重要id情况(user_id、店铺id、商品id数)
    占比分布：
        商品出现次数分布占比
        店铺出现次数分布占比
        用户出现次数分布占比
    研究对象不同维度详细信息
        用户不同维度详细信息可视化
        店铺不同维度详细信息可视化
        广告商品不同维度详细信息可视化
    其他与标签值相关的数据分析与可视化(用于特征选择等重要参考)

特征工程：egg.py
一 基础画像:用户画像、店铺画像、广告商品画像
主要提取它们的统计特征、时间特征(主要是时间差)

     用户画像
         统计特征
         常规统计
         用户基本属性：性别、年龄
         用户点击次数
         用户购买次数
         用户对商品的忠诚度：每件商品平均购买次数
         用户对店铺忠诚度：每家店铺平均购买次数
         用户购买次数最多的店铺
         用户购买次数最多的商品类目2
         用户购买平均价格水平
         用户购买的店铺的平均星级
         暴力统计法
         选合理的多个特征并遍历
      ([“is_trade”,”item_id”,”item_category_list”,”item_brand_id”,”item_city_id”,”item_price_level”,”item_sales_level”,”item_collected_level”,”item_pv_level”,“shop_ip”,”shop_review_num_level”,”shop_review_positive”,”shop_star_level”,”shop_score_service”,”shop_score_delivery”,”shop_score_description”])
         对于联系型特征，求count,nunique,max,min,sum,mean,std特征；对于离散特征，
         求count,nunique特征
         时间特征
         用户每天、每小时、每分钟的点击次数
         用户浏览商品的时间间隔
         用户购买商品的时间间隔
         用户购买同一商品的时间间隔
         用户转化率
         ......

     店铺画像
         常规统计
         店铺基本属性
         ......
         暴力统计法
         选合理的多个特征并遍历
         ([“is_trade”,”item_id”,”item_category_list”,”item_brand_id”,”item_city_id”,”item_price_level”,”item_sales_level”,”item_collected_level”,”item_pv_level”,”user_id”,”user_gender_id”,”user_age_level”,”user_occupation_id”,”user_star_id”])
         对于联系型特征，求count,nunique,max,min,sum,mean,std特征；对于离散特征，
         求count,nunique特征
         时间特征
         ......
     广告商品画像
         广告商品的基本属性
         常规统计+暴力统计+时间特征

二 组合画像：用户-店铺、用户-商品、店铺-用户、店铺-商品、商品-用户、商品-店铺

     用户-店铺
         统计特征
         每个用户对每家店铺的点击次数
         每个用户对每家店铺的购买次数
         每个用户在每家店铺的购买商品的平均价格
         ......
         时间特征
         用户在每个店铺每天、每小时、每分钟的点击次数
         用户在每个店铺浏览商品的时间间隔
         用户在每个店铺购买商品的时间间隔
         用户在每个店铺购买同一商品的时间间隔
         ......

三 其他组合

    不同好评率对应的销量情况
    不同商品售卖价格每一个用户的购买量
    不同展现下每个商店流量情况
    不同城市每种商店展现的流量
    每个品牌每个用户的浏览量
    每个商品每家店的浏览量
    ......

四 强特说明

    展现页数
    转化率
    ......

特征处理：

    样本出现数据倾斜、做数据倾斜的几种处理、发现效果没有那么好，直接采取不处理~
    对于每一个生成的画像做相应的特征处理。
    贝叶斯平滑处理：对转化率做贝叶斯平滑
    log处理：对长尾分布做log

特征选择：

    用递归消除做特征选择

模型：   

    单模型：xgboost
    调参：交叉验证  args_adjust.py
    *可以再做模型融合提高分数，用stacking!!!
