大家好，欢迎回来鸿蒙5莓创图表组件的专场，我们这一期来讲解McLineChart折线图中yAxis的实际应用场景和案例用法。

## yAxis基础介绍
yAxis是折线图中Y轴的配置项，用于控制Y轴的显示样式、刻度、标签等。在莓创图表中，yAxis支持以下核心功能：

1. 单Y轴和多Y轴配置
2. 轴名称和样式自定义
3. 刻度线和分割线配置
4. 数值范围限定
5. 标签格式化

## 实际场景案例
### 案例1：温度监控双Y轴图表
```plain
import { McLineChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct TemperatureChart {
    @State indoorData: number[] = [22, 23, 24, 23, 25, 24, 23]
    @State outdoorData: number[] = [15, 16, 18, 20, 19, 17, 16]
    @State humidityData: number[] = [45, 50, 55, 60, 58, 52, 48]
    
    @State tempOption: Options = new Options({
        title: {
            show: true,
            text: '室内外温度与湿度监控',
            top: 10
        },
        xAxis: {
            data: ['周一','周二','周三','周四','周五','周六','周日']
        },
        yAxis: [
            {
                name: '温度(℃)',
                nameTextStyle: {
                    color: '#ff5500',
                    fontSize: 14
                },
                axisLabel: {
                    color: '#ff5500'
                }
            },
            {
                name: '湿度(%)',
                position: 'right',
                nameTextStyle: {
                    color: '#1890ff',
                    fontSize: 14
                },
                axisLabel: {
                    color: '#1890ff',
                    formatter: (value) => `${value}%`
                },
                splitLine: { show: false }
            }
        ],
        series: [
            {
                name: '室内温度',
                data: this.indoorData,
                color: '#ff7d4f'
            },
            {
                name: '室外温度',
                data: this.outdoorData,
                color: '#ffbb96'
            },
            {
                name: '室内湿度',
                data: this.humidityData,
                yAxisIndex: 1,
                color: '#69c0ff'
            }
        ]
    })
    
    build() {
        Column() {
            McLineChart({
                options: this.tempOption
            })
        }
        .height('50%')
        .width('100%')
    }
}
```

这个案例展示了如何在一个图表中同时显示温度(左Y轴)和湿度(右Y轴)数据，通过不同的颜色区分不同的Y轴和对应的数据系列。

### 案例2：股票价格区间限定图表
```plain
import { McLineChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct StockChart {
    @State priceData: number[] = [45.2, 46.5, 47.8, 48.3, 47.5, 46.8, 45.9]
    @State volumeData: number[] = [1200000, 1500000, 1800000, 2000000, 1700000, 1400000, 1300000]
    
    @State stockOption: Options = new Options({
        title: {
            show: true,
            text: '股票价格与成交量',
            top: 10
        },
        xAxis: {
            data: ['9:30','10:00','10:30','11:00','11:30','13:00','13:30']
        },
        yAxis: [
            {
                name: '价格($)',
                min: 44,
                max: 50,
                axisLabel: {
                    formatter: (value) => `$${value}`
                }
            },
            {
                name: '成交量',
                position: 'right',
                axisLabel: {
                    formatter: (value) => `${value/1000000}M`
                },
                splitLine: { show: false }
            }
        ],
        series: [
            {
                name: '股价',
                data: this.priceData,
                smooth: true,
                lineStyle: {
                    width: 3
                }
            },
            {
                name: '成交量',
                data: this.volumeData,
                yAxisIndex: 1,
                areaStyle: {
                    show: true,
                    gradient: ['#e6f7ff', '#bae7ff']
                }
            }
        ]
    })
    
    build() {
        Column() {
            McLineChart({
                options: this.stockOption
            })
        }
        .height('50%')
        .width('100%')
    }
}
```

这个案例展示了如何限定Y轴的范围(min/max)，以及如何格式化Y轴标签显示货币单位和百万单位。

## 拓展案例：动态数据更新的销售仪表盘
```plain
import { McLineChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct SalesDashboard {
    @State salesData: number[] = [12000, 15000, 18000, 20000, 17000, 14000, 13000]
    @State targetData: number[] = [15000, 15000, 15000, 15000, 15000, 15000, 15000]
    
    @State salesOption: Options = new Options({
        title: {
            show: true,
            text: '周销售业绩 vs 目标',
            top: 10
        },
        xAxis: {
            data: ['周一','周二','周三','周四','周五','周六','周日'],
            axisLabel: {
                fontWeight: 'bold'
            }
        },
        yAxis: {
            name: '销售额(元)',
            nameTextStyle: {
                color: '#333',
                fontSize: 14
            },
            axisLabel: {
                formatter: (value) => `${value/1000}K`
            },
            splitLine: {
                lineStyle: {
                    color: '#f0f0f0'
                }
            }
        },
        series: [
            {
                name: '实际销售额',
                data: this.salesData,
                color: '#52c41a',
                label: {
                    show: true,
                    formatter: (params) => `${params.value/1000}K`
                }
            },
            {
                name: '销售目标',
                data: this.targetData,
                color: '#faad14',
                lineStyle: {
                    type: 'dashed'
                }
            }
        ]
    })
    
    aboutToAppear() {
        // 模拟数据更新
        setInterval(() => {
            const newData = this.salesData.map(value => {
                const fluctuation = Math.random() > 0.5 ? 1 : -1
                return value + Math.floor(Math.random() * 2000) * fluctuation
            })
            this.salesData = newData
            
            this.salesOption.setVal({
                series: [
                    {
                        name: '实际销售额',
                        data: this.salesData
                    }
                ]
            })
        }, 3000)
    }
    
    build() {
        Column() {
            McLineChart({
                options: this.salesOption
            })
        }
        .height('50%')
        .width('100%')
    }
}
```

这个拓展案例展示了：

1. 动态更新Y轴数据
2. 格式化Y轴标签显示千单位
3. 在数据点上显示标签
4. 使用虚线表示目标线
5. 定时自动更新数据

## 总结
通过以上案例，我们可以看到yAxis在莓创图表中的强大功能：

1. **多Y轴支持**：可以同时显示不同单位和量级的数据
2. **标签格式化**：可以自定义显示格式，如货币、百分比等
3. **范围限定**：通过min/max控制Y轴显示范围
4. **样式定制**：可以完全自定义轴线、刻度、标签的样式
5. **动态更新**：结合setVal方法实现数据动态更新

好，这期讲到这里就结束了，希望大家能够通过这些实际案例更好地理解和使用莓创图表中的yAxis功能，在你们的鸿蒙应用开发中创建出更专业、更美观的数据可视化图表。如果有任何问题，欢迎在评论区留言讨论！