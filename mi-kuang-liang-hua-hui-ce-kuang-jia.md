# 米筐量化回测框架

## 1. 策略主体运行流程

* Init方法实现策略的初始化逻辑
* before\_trading实现开盘前操作，每天只调用一次
* handle\_bar实现策略具体逻辑

其中context作为对象在三个方法中进行传递



## 2. 数据获取接口

* 米筐使用rqdatc来获取金融数据
* 获取指定行业、板块的股票列表

```python
instruments(order_book_ids, market='cn')
```

中国市场的 order\_book\_id 通常类似'000001.XSHE'。需要注意，国内股票、ETF、指数合约代码分别应当以'.XSHG'或'.XSHE'结尾，前者代表上证，后者代表深证。 比如查询平安银行这个股票合约，则键入'000001.XSHE'，前面的数字部分为交易所内这个股票的合约代码，后半部分为对应的交易所代码。

* 获取股票历史数据

```python
get_price(order_book_ids, start_date='2013-01-04', end_date='2014-01-04', frequency='1d', fields=None, adjust_type='pre', skip_suspended =False, market='cn', expect_df=True,time_slice=None)
```

frequency为频率1m/d/w

skip\_suspended为是否跳过停盘数据

* 查询财务数据

```python
get_factor(order_book_ids, factor, start_date, end_date, universe=None,expect_df=True)
```

米筐将财务数据作为因子的一组类型，使用get\_all\_factor\_names() 可以查询所有有效因子字段



## 3. 过滤指标条件

* query().filter() 根据filter条件进行筛线
* query().order\_by() 通过什么进行排律
* query().limit() 限制在多少以内



## 4. 股票的交易函数

rqalpha-plus是本地回测框架

对于股票，RQAlphaPlus 提供了六个下单接口，均可以用于开仓或平仓：

* order\_shares: 用于按股数下单
* order\_lots: 用于按手数下单
* order\_value: 用于按价值下单
* order\_percent: 用于按价值占当前账户总权益的比例下单
* order\_target\_value: 用于按目标仓位价值下单
* order\_target\_percent: 用于按目标仓位价值占账户总权益的比例下单



## 5. 本地数据更新

样例数据包含完整的日线和基础数据，可供运行（RQAlphaPlus 支持的）任意合约几乎全时间段的日级别回测。样例数据另外包含有限的分钟和 tick 数据。

示例，运行如下命令以更新日线、平安银行的分钟线数据、所有螺纹钢期货的分钟线数据和 IO2002C3900 的 tick 数据：

```python
rqsdk update-data --minbar 000001.XSHE --minbar RB --tick IO2002C3900
```

默认情况下，数据包存储于用户目录下的 .rqalpha-plus/bundle 目录下。



## 6. 本地运行策略

```python
rqalpha-plus run -f macd_000001.py -a stock 100000 -s 20190101 -e 20191231 -bm 000300.XSHG -p
```

* `rqalpha-plus`：RQAlphaPlus 所有命令行工具的总入口，执行 `rqalpha-plus --help` 以查看所有可用的功能
* `run`: 用于运行策略的子命令，
* 参数：策略运行的各种选项，顺序不限，部分参数需要传入参数值
  * `-f macd_000001.py`：指定运行的策略文件，支持绝对路径或相对路径
  * `-a stock 100000`：指定股票账户的初始资金为十万元，RQAlphaPlus 支持股票（`stock`）、期货（`future`）两种账户，策略交易不同品种的标的需要配置对应账户的初始资金
  * `-s 20190101`：回测运行的初始时间为 2019 年 1 月 1 日，回测实际上会从不早于该日期的第一个交易日开始运行
  * `-e 20191231`：回测运行的终止时间为 2019 年 12 月 31 日，回测实际上会运行至不晚于改日期的最后一个交易日
  * `-bm 000300.XSHG` 使用沪深三百指数（000300.XSHG）作为回测运行的基准，该基准用于计算 Alpha、Beta 等基于超额收益的指标，基准也会和策略收益一起展示在收益曲线图上
  * `-p`：策略运行结束后展示收益曲线图

## 7. 评价指标

* 收益指标
  * 回测总收益率
  * 基准收益率
  * 年化收益率
* 风险指标
  * 最大回撤
  * 夏普比率 \[投资组合预期报酬率-无风险利率]/投资组合标准差



## 8. 例子



\
\
