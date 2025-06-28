Hello everyone, welcome back to the special session of HarmonyOS 5 Meichuang chart components. In this episode, we will explain the practical application scenarios and case usage of yAxis in McLineChart line charts.  


## Basic Introduction to yAxis  
The yAxis is the configuration item for the Y-axis in line charts, used to control the display style, scale, labels, etc., of the Y-axis. In Meichuang charts, yAxis supports the following core functions:  

1. Single Y-axis and multi-Y-axis configuration  
2. Customization of axis names and styles  
3. Configuration of scale lines and split lines  
4. Numeric range limitation  
5. Label formatting  


## Practical Scenario Cases  

### Case 1: Dual Y-axis Chart for Temperature Monitoring  
```typescript
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
            text: 'Indoor/Outdoor Temperature & Humidity Monitoring',
            top: 10
        },
        xAxis: {
            data: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
        },
        yAxis: [
            {
                name: 'Temperature (°C)',
                nameTextStyle: {
                    color: '#ff5500',
                    fontSize: 14
                },
                axisLabel: {
                    color: '#ff5500'
                }
            },
            {
                name: 'Humidity (%)',
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
                name: 'Indoor Temperature',
                data: this.indoorData,
                color: '#ff7d4f'
            },
            {
                name: 'Outdoor Temperature',
                data: this.outdoorData,
                color: '#ffbb96'
            },
            {
                name: 'Indoor Humidity',
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

**Case Analysis**: This case demonstrates how to display temperature (left Y-axis) and humidity (right Y-axis) data simultaneously in one chart, distinguishing different Y-axes and corresponding data series through different colors.  


### Case 2: Stock Price Range Limitation Chart  
```typescript
import { McLineChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct StockChart {
    @State priceData: number[] = [45.2, 46.5, 47.8, 48.3, 47.5, 46.8, 45.9]
    @State volumeData: number[] = [1200000, 1500000, 1800000, 2000000, 1700000, 1400000, 1300000]
    
    @State stockOption: Options = new Options({
        title: {
            show: true,
            text: 'Stock Price & Trading Volume',
            top: 10
        },
        xAxis: {
            data: ['9:30', '10:00', '10:30', '11:00', '11:30', '13:00', '13:30']
        },
        yAxis: [
            {
                name: 'Price ($)',
                min: 44,
                max: 50,
                axisLabel: {
                    formatter: (value) => `$${value}`
                }
            },
            {
                name: 'Trading Volume',
                position: 'right',
                axisLabel: {
                    formatter: (value) => `${value/1000000}M`
                },
                splitLine: { show: false }
            }
        ],
        series: [
            {
                name: 'Stock Price',
                data: this.priceData,
                smooth: true,
                lineStyle: {
                    width: 3
                }
            },
            {
                name: 'Trading Volume',
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

**Case Analysis**: This case demonstrates how to limit the Y-axis range (via `min/max`) and format Y-axis labels to display currency units and million units.  


## Extended Case: Sales Dashboard with Dynamic Data Update  
```typescript
import { McLineChart, Options } from '@mcui/mccharts'

@Entry
@Component
struct SalesDashboard {
    @State salesData: number[] = [12000, 15000, 18000, 20000, 17000, 14000, 13000]
    @State targetData: number[] = [15000, 15000, 15000, 15000, 15000, 15000, 15000]
    
    @State salesOption: Options = new Options({
        title: {
            show: true,
            text: 'Weekly Sales vs Target',
            top: 10
        },
        xAxis: {
            data: ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'],
            axisLabel: {
                fontWeight: 'bold'
            }
        },
        yAxis: {
            name: 'Sales (CNY)',
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
                name: 'Actual Sales',
                data: this.salesData,
                color: '#52c41a',
                label: {
                    show: true,
                    formatter: (params) => `${params.value/1000}K`
                }
            },
            {
                name: 'Sales Target',
                data: this.targetData,
                color: '#faad14',
                lineStyle: {
                    type: 'dashed'
                }
            }
        ]
    })
    
    aboutToAppear() {
        // Simulate data updates
        setInterval(() => {
            const newData = this.salesData.map(value => {
                const fluctuation = Math.random() > 0.5 ? 1 : -1
                return value + Math.floor(Math.random() * 2000) * fluctuation
            })
            this.salesData = newData
            
            this.salesOption.setVal({
                series: [
                    {
                        name: 'Actual Sales',
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

**Case Analysis**: This extended case demonstrates:  
1. Dynamic update of Y-axis data  
2. Formatting Y-axis labels to display thousand units (`K`)  
3. Displaying labels on data points  
4. Using dashed lines to represent target lines  
5. Automatically updating data at regular intervals  


## Summary  
Through the above cases, we can see the powerful functions of yAxis in Meichuang charts:  

1. **Multi-Y-axis support**: Simultaneously display data with different units and magnitudes.  
2. **Label formatting**: Customize display formats such as currency and percentages.  
3. **Range limitation**: Control the Y-axis display range via `min/max`.  
4. **Style customization**: Fully customize the styles of axis lines, scales, and labels.  
5. **Dynamic update**: Combine with the `setVal` method to achieve dynamic data updates.  

This concludes this episode. We hope these practical cases help you better understand and use the yAxis function in Meichuang charts, enabling you to create more professional and visually appealing data visualizations in your HarmonyOS app development. If you have any questions, feel free to leave a comment!
