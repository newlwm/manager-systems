<template>
        <el-row>
            <el-col :span="8" style="padding-right:10px ">
                <el-card class="box-card">
                    <div class="user">
                        <img src="../assets/images/user.png" alt="">
                        <div class="userinfo">
                            <p class="name">Admin</p>
                            <p class="access">超级管理员</p>
                        </div>
                    </div>
                    <div class="login-info">
                        <p>上次登录时间: <span>2021-7-19</span></p>
                        <p>上次登录地点: <span>武汉</span></p>
                    </div>
                </el-card>
            <el-card style="margin-top: 20px;height: 460px;">
                <el-table
                :data="tableData"
                style="width: 100%">
                    <el-table-column v-for="(val,key) in tabelLabel" :prop="key" :label="val" />
                    <!-- <el-table-column
                        prop="todayBuy"
                        label="今日购买">
                    </el-table-column>
                    <el-table-column
                        prop="monthBuy"
                        label="本月购买">
                    </el-table-column>
                    <el-table-column
                        prop="totalBuy"
                        label="总共购买">
                    </el-table-column> -->
                </el-table>
            </el-card>
            </el-col>
            <el-col :span="16" style="padding-left:10px ">
                <div class="num">
                    <el-card v-for="item in countData" :key="item.name" :body-style="{ display:'flex',padding:0}">
                        <i class="icon" :class="`el-icon-${item.icon}`" :style="{background:item.color}"></i>
                        <div class="detail">
                            <p class="price">￥{{ item.value }}</p>
                            <p class="desc">{{ item.name }}</p>
                        </div>
                    </el-card>
                        </div>
                        <el-card style="height:280px">
                             <div ref="echarts1" style="height: 280px"></div>
                        </el-card>
                        <div class="graph">
                            <el-card style="height:260px">
                                <div ref="echarts2" style="height:260px"></div>
                            </el-card>
                            <el-card style="height:260px">
                                 <div ref="echarts3" style="height:240px"></div>
                            </el-card>
                        </div>
                        </el-col>
        </el-row>
</template>

<script>
    import { getData }  from '../api'
    import * as echarts from 'echarts'
export default {
    data () {
        return {
        tableData:[],
        tabelLabel:{
            name:'课程',
            todayBuy:'今日购买',
            monthBuy:'本月购买',
            totalBuy:'总购买'
        },
        countData: [
        {
          name: "今日支付订单",
          value: 1234,
          icon: "success",
          color: "#2ec7c9",
        },
        {
          name: "今日收藏订单",
          value: 210,
          icon: "star-on",
          color: "#ffb980",
        },
        {
          name: "今日未支付订单",
          value: 1234,
          icon: "s-goods",
          color: "#5ab1ef",
        },
        {
          name: "本月支付订单",
          value: 1234,
          icon: "success",
          color: "#2ec7c9",
        },
        {
          name: "本月收藏订单",
          value: 210,
          icon: "star-on",
          color: "#ffb980",
        },
        {
          name: "本月未支付订单",
          value: 1234,
          icon: "s-goods",
          color: "#5ab1ef",
        },
      ],
        }
    },
    mounted(){
        getData().then(({ data }) => {
            const { tableData } = data.data
            this.tableData = tableData
            // console.log(tableData);
            const echarts1 = echarts.init(this.$refs.echarts1)
            // 指定图表的配置项和数据
            var echarts1Option = {}
            // 处理数据xAxi
            const { orderData , userData , videoData} = data.data
            const xAxis = Object.keys(orderData.data[0])   
            const xAxisData = {
                data : xAxis
            }
            echarts1Option.xAxis = xAxisData
            echarts1Option.yAxis = {}
            echarts1Option.legend = xAxisData
            echarts1Option.series = []
            
            xAxis.forEach(key => {
                echarts1Option.series.push({
                    name:key,
                    data:orderData.data.map(item => item[key]),
                    type:'line'
                })
            })
            // console.log(echarts1Option)
            // 使用刚指定的配置项和数据显示图表。
            echarts1.setOption(echarts1Option)

            //柱状图
            const echarts2 = echarts.init(this.$refs.echarts2)
            const echart2Option = {
                legend: {
                // 图例文字颜色
                textStyle: {
                    color: "#333",
                },
                },
                grid: {
                left: "20%",
                },
                // 提示框
                tooltip: {
                trigger: "axis",
                },
                xAxis: {
                type: "category", // 类目轴
                data: userData.map(item => item.date),
                axisLine: {
                    lineStyle: {
                    color: "#17b3a3",
                    },
                },
                axisLabel: {
                    interval: 0,
                    color: "#333",
                },
                },
                yAxis: [
                {
                    type: "value",
                    axisLine: {
                    lineStyle: {
                        color: "#17b3a3",
                    },
                    },
                },
                ],
                color: ["#2ec7c9", "#b6a2de", "#5ab1ef", "#ffb980", "#d87a80", "#8d98b3"],
                series: [
                    {
                        name:'新增用户',
                        data:userData.map(item => item.new),
                        type: 'bar'
                    },
                    {
                        name:'活跃用户',
                        data:userData.map(item => item.active),
                        type: 'bar'
                    }
                ],
                        }
            echarts2.setOption(echart2Option)           
            
            //饼状图
            const echarts3 = echarts.init(this.$refs.echarts3)
            const echart3Option = {
                 tooltip: {
                    trigger: "item",
                },
                color: [
                    "#0f78f4",
                    "#dd536b",
                    "#9462e5",
                    "#a6a6a6",
                    "#e1bb22",
                    "#39c362",
                    "#3ed1cf",
                ],
                series: [
                    {
                        data:videoData,
                        type:'pie'
                    }
                ],
            }
            echarts3.setOption(echart3Option)
        })
    }

    
}
</script>
<style lang="less" scoped>
    .user {
        margin-bottom: 20px;
        padding-bottom: 20px;
        display: flex;
        align-items: center;
        border-bottom: 1px solid #ccc;
        img {
            margin-right: 40px;
            width: 140px;
            height: 140px;
            border-radius: 50%;
        }
        .userinfo {
            .name {
                font-size: 32px;
                margin-bottom: 10px;
            }
            .access {
                color: #999;
            }
        }
    }
    .login-info {
        p {
            line-height: 28px;
            font-size: 14px;
            color: #999;
            span {
                color: #666;
                margin-left: 60px;
            }
        }
    }
    .num {
        display: flex;
        justify-content: space-between;
        flex-wrap: wrap;
        .icon {
            width: 80px;
            height: 80px;
            line-height: 80px;
            text-align: center;
            color: #fff;
        }
        .detail {
            margin-left: 15px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            .price {
                font-size: 30px;
                margin-bottom: 10px;
                line-height: 30px;
                height: 30px;
        }
            .desc {
                font-size: 14px;
                color: #999;
                text-align: center;
            }
        }
        .el-card {
            width:32% ;
            margin-bottom: 20px;
        }
        
    }
    .graph {
        margin-top: 20px;
        display: flex;
        justify-content: space-between;
        .el-card {
            width: 48%;
        }
    }
</style>
