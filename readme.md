# 简介
zipline_chstock 为zipine 的本地化化版本，用于对于国内二级市场进行专业的回测研究。

使用python2.7进行改写。使用本zipline时，请注意python 版本。

注意各类包的版本，例如pandas等。若运行中出现错误，可尝试降低相关包的版本，
# 主要变动
## 1. 数据来源变动
基于原有的zipline 开发主要是为了适应美国市场，所以其数据的来源主要包括雅虎以及其他本地数据。所以本次我们将数据来源全部应用到本地，主要为mongodb数据库，读者可以根据自己本身数据情况，选择数据的来源，并进行改写。


*数据获取部分主要在zipline/data目录下，loader 和 mongodb,原zipline只存在loader文件夹，获取本地和从雅虎上获取的数据。mongodb.py
主要是对于mongodb的操作，loader 主要通过mongodb获取分钟和日线数据，并转换成相应的格式。*
## 2. 交易日历的改写
日历改写，由于中美交易时间不一致性，调整交易的日历序列，适用于国内

*1. 交易日历部分主要在zipline/utils 目录下，原交易日历为tradingcalendar.py从此文件中，get_no_trading_days,get_trading_days,get_early_closes,以及get_open_and_close四个重要的函数。由于交易如的不一致性，这里需要将其进行调整，由于本zipline 仅用于回测研究，不涉及交易。所以这里的trading_days我们使用指数‘000001’的交易日期作为交易日期。

*2. 由于开盘和收盘时间不一样，以及交易时间的不一致性，tradingcalendar均有改变，另外由于国内极少有提前收盘的情况，这里统一调整为15:00收盘。

*3. 对于分钟交易来说，由于国内的集合竞价制度和午间停盘制度（11:30--13:00）这里需要对时间的变化进行调整。调整部分在zipline/finance/trading目录下next_market_minute部分，将9：25的下一交易时间定为9:30，将11:30的下一个交易点定为13:00.


## 3. 涨停以及买空卖空规则的改写
涨跌停等规则的改写，买空卖空的改写，日内买卖限制t+1制度的改写。

*对于涨停板的限制，在国内是price*1.1 后四舍五入确定涨停价，这里我们在交易进行中进行判断。当命令买入的时候，判断股票是否为涨停，若涨停则买入失败。zipline/finance/blotter

*对于t+1 已经买空买空规则的确定，这里对order进行了一次包装，设置了order_list函数对order数据进行统一处理，对于融资融券等同样可以在此处进行增加，zipline/order_list

## 4. 交易单元的改写，以期适应多种策略。包括周期以及成交价情况（以开盘、收盘或者给定成交价成交）
交易单元的改写，原交易仅支持day 和minute 周期的回测，本次改写增加了tick 数据回测系列，不过鉴于zipline 是事件驱动型框架，跟随每个tick进行回测耗时会比价大。
*本处处理方法为在order 命令中增加give_price参数，用来控制成交价格。

*对于以开盘或者收盘成交价的状况，以及滑点等 请在zipline/finance/slippage进行调整。

## 5. 其他部分改写，包括为了加速回测速度，对部分运算进行cython 的改写等。


# 使用方法
按照原始的文件布局，下载所有的文件，对应安装所需要版本的包，参考demo进行使用
#联系
如果有哪些不明白的地方，欢迎联系qq：94006733。会抽空统一回复
