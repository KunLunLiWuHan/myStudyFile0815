# 1 介绍

数据可视化：借助图形化手段，把数据由冰冷的数字转换成图形，揭示数据中蕴含的规律和道理。

官网地址：https://echarts.apache.org/zh/index.html。

下载地址:https://www.runoob.com/echarts/echarts-install.html。

## 1.1 入门案例

**项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125627.png" alt="image-20200707230149211" style="zoom:80%;" />

**index.html代码**

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!-- 引入 ECharts 文件
        js引入加入 type="text/javascript"
        css引入加入rel="stylesheet"
     -->
    <script type="text/javascript" src="js/echarts.js"></script>
</head>
</head>
<body>
    <h1>Echart案例</h1>
    <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM
    定义图表显示区域-->
    <div id="main" style="width: 600px;height:400px;"></div>
    <script type="text/javascript">
        // 基于准备好的dom，初始化echarts实例
        var myChart = echarts.init(document.getElementById('main'));

        // 指定图表的配置项和数据
        // option 表示使用 json 数据格式的配置来绘制图表
        var option = {
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            legend: {  //图例
                data:['销量','成本'] //和series中的name中属性一致
            },
            xAxis: { //x轴数据
                data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"] //是一个数组
            },
            yAxis: {}, //y轴数据
            series: [
                {
                    name: '销量',
                    type: 'bar', //显示的类型，柱形
                    data: [5, 20, 36, 10, 10, 20] //数据
                },
                {
                    name: '成本',
                    type: 'bar',
                    data: [0.5, 2, 3.6, 1, 1, 2]
                }
            ]
        };

        // 使用刚指定的配置项和数据显示图表（渲染图标）。
        myChart.setOption(option);
    </script>
</body>
```

+ 控制台输出访问：http://localhost:8080/index.html，将会显示下面的界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125628.png" alt="image-20200707230326448" style="zoom:80%;" />

# 2 线性图

1. 对于折线图中的smooth属性的查询过程如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125629.png" alt="image-20200708074552331" style="zoom:80%;" />

​		我们了解到：该属性表示曲线是否平滑曲线显示。

​		如果是 `boolean` 类型，则表示是否开启平滑处理。如果是 `number` 类型（取值范围 0 到 1），表示平滑程度，越小表示越接近折线段，反之则反。设为 `true` 时相当于设为 `0.5`。

2. 数据堆叠

stack属性默认值为null，使用时通过系列名称指定（serial中的name属性值）。

# 3 柱状图

1. 测试

**项目结构**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125630.png" alt="image-20200708083901866" style="zoom:80%;" />

**前端代码**

```html
 <script type="text/javascript" src="js/echarts.js"></script>
    <script type="text/javascript" src="js/jquery-3.4.1.min.js"></script>
</head>
</head>
<body>
    <div id="main" style="width: 600px;height:400px;"></div>
    <button type="button" id="change">更新数据</button>
    <script type="text/javascript">
        var myChart = echarts.init(document.getElementById('main'));
        var xAxisData = [];
        var data1 = [];
        var data2 = [];
        for (var i = 0; i < 100; i++) {
            xAxisData.push('类目' + i);
            data1.push((Math.sin(i / 5) * (i / 5 -10) + i / 6) * 5);
            data2.push((Math.cos(i / 5) * (i / 5 -10) + i / 6) * 5);
        }

        option = {
            title: {
                text: '柱状图动画延迟'
            },
            legend: {
                data: ['bar', 'bar2']
            },
            toolbox: {
                // y: 'bottom',
                feature: {
                    magicType: {
                        type: [ 'stack','tiled']
                    },
                    dataView: {},
                    saveAsImage: {
                        pixelRatio: 2
                    }
                }
            },
            tooltip: {},
            xAxis: {
                data: xAxisData,
                splitLine: {
                    show: false
                }
            },
            yAxis: {
            },
            series: [{
                name: 'bar',
                type: 'bar',
                data: data1,
                animationDelay: function (idx) {
                    return idx * 10;
                }
            }, {
                name: 'bar2',
                type: 'bar',
                data: data2,
                animationDelay: function (idx) {
                    return idx * 10 + 100;
                }
            }],
            animationEasing: 'elasticOut',
            animationDelayUpdate: function (idx) {
                return idx * 5;
            }
        };
        myChart.setOption(option);
    //  在这里更新柱状图的数据
        $('#change').click(function () {
            //这个数组声明不能省略，否则在一个图形中会出现两个柱形图
            xAxisData = [];
            data1 = [];
            data2 = [];
            for (var i = 0; i < 100; i++) {
                xAxisData.push('类目' + i);
                data1.push((Math.sin(i / 5) * (i / 5 -10) + i / 6) * 10);
                data2.push((Math.cos(i / 5) * (i / 5 -10) + i / 6) * 10);
            }
            //将数据替换
            option.series[0].data = data1;
            option.series[1].data = data2;
            myChart.setOption(option);
        });

    </script>
</body>
```

前端界面显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831125631.png" alt="image-20200708084016483" style="zoom:80%;" />

