[合集 \- 玩转Streamlit(16\)](https://github.com)[1\.什么是Streamlit10\-11](https://github.com/wang_yb/p/18458062)[2\.『玩转Streamlit』\-\-环境配置10\-17](https://github.com/wang_yb/p/18471660)[3\.『玩转Streamlit』\-\-架构和运行机制10\-22](https://github.com/wang_yb/p/18492213)[4\.『玩转Streamlit』\-\-多页应用10\-25](https://github.com/wang_yb/p/18502232)[5\.『玩转Streamlit』\-\-页面布局10\-31](https://github.com/wang_yb/p/18516928)[6\.『玩转Streamlit』\-\-登录认证机制11\-05](https://github.com/wang_yb/p/18527320)[7\.『玩转Streamlit』\-\-文本与标题组件11\-07](https://github.com/wang_yb/p/18531821)[8\.『玩转Streamlit』\-\-数据展示组件11\-13](https://github.com/wang_yb/p/18543687):[slower加速器](https://jisuanqi.org)[9\.『玩转Streamlit』\-\-图像与媒体组件11\-16](https://github.com/wang_yb/p/18549720)[10\.『玩转Streamlit』\-\-交互类组件11\-19](https://github.com/wang_yb/p/18554142)[11\.『玩转Streamlit』\-\-布局与容器组件11\-23](https://github.com/wang_yb/p/18564468)[12\.『玩转Streamlit』\-\-可编辑表格11\-29](https://github.com/wang_yb/p/18576592)[13\.『玩转Streamlit』\-\-表单Form12\-04](https://github.com/wang_yb/p/18585991)[14\.『玩转Streamlit』\-\-片段Fragments12\-10](https://github.com/wang_yb/p/18597017)[15\.『玩转Streamlit』\-\-集成Matplotlib12\-17](https://github.com/wang_yb/p/18613506)16\.『玩转Streamlit』\-\-集成Plotly12\-24收起
之前介绍了如何在`Streamlit App`中使用`Matplotlib`库来绘图。


本篇介绍 `Steamlit`结合`Poltly`的方法，相比于`Matplotlib`，`Poltly`的交互性更强，


更适合在`Web`应用中做为可视化的工具。


# 1\. st.plotly\_chart函数


`st.plotly_chart`函数专门用于在`Steamlit`应用中显示 `Plotly` 绘制的图形。


这个函数能够直接将**Plotly Figure对象**或者**Poltly支持的数据对象**直接渲染到页面的指定位置上。


`st.plotly_chart`的参数不多，与`st.pyplot`比，多了一些交互用的参数：




| **名称** | **类型** | **说明** |
| --- | --- | --- |
| figure\_or\_data | Figure或Data对象 |  |
| theme | str | 指定图表的主题 |
| use\_container\_width | bool | 决定是否使用父容器的宽度覆盖图形的原始宽度 |
| key | str | 为元素提供标识 |
| on\_select | str | 控制图表如何响应用户选择事件 |
| selection\_mode | str | 图表的选择模式 |


因为`Plotly`绘制的图形可交互，通过`key`参数，在交互的过程中，我们可以精确地定位到交互的图表。


`on_select`参数有以下几种取值：


1. `ignore`：不对图表中的任何选择事件做出反应
2. `rerun`：在图表中选择数据时，会重新运行应用程序
3. `可调用对象****`：会重新运行应用程序，并在应用程序的其余部分之前执行该可调用对象作为回调函数


`selection_mode`参数定义图表的选择模式，包括：


1. `points`：允许基于单个数据点进行选择
2. `box`：允许基于矩形区域进行选择
3. `lasso`：允许基于自由绘制区域进行选择


`on_select`不同时，页面的效果如下：


## 1\.1\. on\_select\=ignore


`ignore`是`on_select`的默认值，此时`Plotly`图形上无法选择对象。



```
import streamlit as st
import plotly.express as px

df = px.data.iris()
fig = px.scatter(df, x="sepal_width", y="sepal_length")


st.plotly_chart(fig, key="iris")
# 或者
# st.plotly_chart(fig, key="iris", on_select="ignore")

```

![](https://img2024.cnblogs.com/blog/83005/202412/83005-20241224094257606-1732212876.png)


此时，工具栏上没有选择数据的小工具。


## 1\.2\. on\_select\=rerun


此时，`st.plotly_chart`会将选择的数据点返回。


选择数据点时，可以切换成**矩形选择**和**自由区域**选择。



```
event = st.plotly_chart(fig, key="iris", on_select="rerun")
event

```

![](https://img2024.cnblogs.com/blog/83005/202412/83005-20241224094257671-870221187.gif)


## 1\.3\. on\_select\=callable


`on_select=callable`的效果`on_select=rerun`差不多，也能对数据点选择并得到选择的数据点。


不同之处在于，可以在选择数据点之后，调用`callable`函数进行额外的处理。



```
def handle_selection():
    from datetime import datetime

    st.write(f"Selected data at {datetime.now()}")


event = st.plotly_chart(fig, key="iris", on_select=handle_selection)
event

```

![](https://img2024.cnblogs.com/blog/83005/202412/83005-20241224094257640-1551295950.gif)


每次选择数据之后，上面的时间都会变化，说明`handle_selection`函数在每次选择数据之后都被回调。


# 2\. 使用示例


下面通过示例演示实际场景中如何使用`streamlit`和`Poltly`图表。


## 2\.1\. 销售数据时间序列分析


在这个示例中，首先创建了一个模拟的销售数据时间序列，然后通过st.plotly\_chart展示图表，并设置`on_select`回调函数来处理用户在图表上的选择操作。


当用户选择图上的点时，会在 `Streamlit` 应用中显示所选数据点对应的日期和销售额信息。



```
import streamlit as st
import plotly.express as px
import pandas as pd

# 模拟销售数据
data = {
    "Date": pd.date_range(start="2024-01-01", periods=100),
    "Sales": [i**2 + 50 + 10 * (i % 10) for i in range(100)],
}
df = pd.DataFrame(data)

# 创建时间序列折线图
fig = px.scatter(df, x="Date", y="Sales")


# 显示图表并处理选择事件
def handle_selection():
    selected_points = st.session_state["sales_chart"].selection.points
    st.write("已选择的数据点:")
    df = pd.DataFrame(columns=["日期", "销售额"])

    for idx, p in enumerate(selected_points):
        df.loc[idx, "日期"] = p["x"]
        df.loc[idx, "销售额"] = p["y"]

    st.dataframe(df)


st.plotly_chart(fig, key="sales_chart", on_select=handle_selection)

```

![](https://img2024.cnblogs.com/blog/83005/202412/83005-20241224094257660-1429341930.gif)


## 2\.2\. 模拟股票分析


使用`generate_stock_data`函数生成模拟的股票数据，再使用`plotly.graph_objects`创建一个烛台图，将模拟数据绘制到图表中。


编写一个回调函数，当用户在图表上选择某个点时，它会获取所选点的详细信息并在 `Streamlit` 应用中展示出来。



```
import streamlit as st
import plotly.graph_objects as go
import pandas as pd
import numpy as np


# 生成随机模拟的股票数据
def generate_stock_data(days=300):
    dates = pd.date_range(start="2024-01-01", periods=days)
    open_prices = np.random.rand(days) * 100 + 50
    high_prices = open_prices + np.random.rand(days) * 10
    low_prices = open_prices - np.random.rand(days) * 10
    close_prices = open_prices + np.random.randn(days) * 5

    data = {
        "Date": dates,
        "Open": open_prices,
        "High": high_prices,
        "Low": low_prices,
        "Close": close_prices,
    }
    return pd.DataFrame(data)


# 生成模拟股票数据
df = generate_stock_data()

# 创建交互式图表
fig = go.Figure(
    data=[
        go.Candlestick(
            x=df["Date"],
            open=df["Open"],
            high=df["High"],
            low=df["Low"],
            close=df["Close"],
        )
    ]
)

fig.update_layout(title="模拟股票价格", xaxis_title="Date", yaxis_title="Price")


# onselect 回调函数
def handle_selection():
    selected_points = st.session_state.stock_chart.selection.points
    st.write("Selected Stock Data Points:")

    df = pd.DataFrame(columns=["日期", "开盘价", "收盘价", "最高价", "最低价"])

    for idx, p in enumerate(selected_points):
        print(idx, p)
        df.loc[idx, "日期"] = p["x"]
        df.loc[idx, "开盘价"] = p["open"]
        df.loc[idx, "收盘价"] = p["close"]
        df.loc[idx, "最高价"] = p["high"]
        df.loc[idx, "最低价"] = p["low"]

    st.dataframe(df)


# 显示图表
st.plotly_chart(fig, key="stock_chart", on_select=handle_selection)

```

![](https://img2024.cnblogs.com/blog/83005/202412/83005-20241224094257635-501595836.gif)


# 3\. 总结


`Streamlit` 可以简化 `Web` 应用构建流程，`Plotly` 提供丰富图表类型，二者结合能快速将数据转化为交互式可视化应用，节省开发时间。


此外，`Plotly` 图表交互性高，在 `Streamlit` 应用中可实现数据探索、筛选等操作，增强用户体验。


