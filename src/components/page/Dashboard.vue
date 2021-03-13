<template>
    <div>
<!--        67babe's gitee上传测试-->
        <!--        第一栏三个状态 span属性就是把24拆分成几个部分，gutter指的是一栏之间的间隔-->
        <el-row :gutter='20' class='mgb20'>
            <el-col :span='8'>
                <el-card shadow='hover' :body-style="{padding: '0px'}">
                    <div class='grid-content grid-con-2'>
                        <i class='el-icon-lx-roundcheckfill grid-con-icon'></i>
                        <div class='grid-cont-right'>
                            <div class='grid-num'>321</div>
                            <div>成功</div>
                        </div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span='8'>
                <el-card shadow='hover' :body-style="{padding: '0px'}">
                    <div class='grid-content grid-con-3'>
                        <i class='el-icon-lx-roundclosefill grid-con-icon'></i>
                        <div class='grid-cont-right'>
                            <div class='grid-num'>5000</div>
                            <div>失败</div>
                        </div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span='8'>
                <el-card shadow='hover' :body-style="{padding: '0px'}">
                    <div class='grid-content grid-con-1'>
                        <i class='el-icon-refresh grid-con-icon'></i>
                        <div class='grid-cont-right'>
                            <div class='grid-num'>1234</div>
                            <div>执行中</div>
                        </div>
                    </div>
                </el-card>
            </el-col>
        </el-row>

        <!--        第二栏两个图-->
        <el-row :gutter='20'>
            <el-col :span='12'>
                <el-card shadow='hover'>
                    <schart ref='ring' class='schart' canvasId='ring' :options='options3'></schart>
                </el-card>
            </el-col>
            <el-col :span='12'>
                <el-card shadow='hover'>
                    <schart ref='pie' class='schart' canvasId='pie' :options='options4'></schart>
                </el-card>
            </el-col>
        </el-row>
        <!--        第三栏折线图-->
        <el-row :gutter='20'>
            <el-col :span='24'>
                <el-card shadow='hover'>
                    <schart ref='line' class='schart' canvasId='line' :options='options2'></schart>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
import Schart from 'vue-schart';
import bus from '../common/bus';

export default {
    name: 'dashboard',
    data() {
        return {
            name: localStorage.getItem('ms_username'),
            todoList: [
                {
                    title: '今天要修复100个bug',
                    status: false
                },
                {
                    title: '今天要修复100个bug',
                    status: false
                },
                {
                    title: '今天要写100行代码加几个bug吧',
                    status: false
                },
                {
                    title: '今天要修复100个bug',
                    status: false
                },
                {
                    title: '今天要修复100个bug',
                    status: true
                },
                {
                    title: '今天要写100行代码加几个bug吧',
                    status: true
                }
            ],
            data: [
                {
                    name: '2018/09/04',
                    value: 1083
                },
                {
                    name: '2018/09/05',
                    value: 941
                },
                {
                    name: '2018/09/06',
                    value: 1139
                },
                {
                    name: '2018/09/07',
                    value: 816
                },
                {
                    name: '2018/09/08',
                    value: 327
                },
                {
                    name: '2018/09/09',
                    value: 228
                },
                {
                    name: '2018/09/10',
                    value: 1065
                }
            ],
            options: {
                type: 'bar',
                title: {
                    text: '最近一周各品类销售图'
                },
                xRorate: 25,
                labels: ['周一', '周二', '周三', '周四', '周五'],
                datasets: [
                    {
                        label: '家电',
                        data: [234, 278, 270, 190, 230]
                    },
                    {
                        label: '百货',
                        data: [164, 178, 190, 135, 160]
                    },
                    {
                        label: '食品',
                        data: [144, 198, 150, 235, 120]
                    }
                ]
            },
            options2: {
                type: 'line',
                title: {
                    text: '近一年数据交换折线图'
                },
                labels: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'],
                datasets: [
                    {
                        label: '成功',
                        data: [234, 278, 270, 190, 230, 111, 164, 178, 150, 135, 160, 239]
                    },
                    {
                        label: '执行中',
                        data: [164, 178, 150, 135, 160, 180, 74, 118, 200, 235, 90, 120]
                    },
                    {
                        label: '失败',
                        data: [74, 118, 200, 235, 90, 10, 118, 150, 135, 160, 180, 88]
                    }
                ]
            },
            options3: {
                type: 'ring',
                title: {
                    text: '近一年数据交换环形图'
                },
                showValue: false,
                legend: {
                    position: 'bottom',
                    bottom: 40
                },
                bgColor: '#fbfbfb',
                labels: ['成功', '失败', '执行中'],
                datasets: [
                    {
                        data: [300, 600, 500]
                    }
                ]
            },
            options4: {
                type: 'pie',
                title: {
                    text: '近一年数据交换饼状图'
                },
                legend: {
                    position: 'left'
                },
                bgColor: '#fbfbfb',
                labels: ['成功', '失败', '执行中'],
                datasets: [
                    {
                        data: [300, 600, 500]
                    }
                ]
            }
        };
    },
    components: {
        Schart
    },
    computed: {
        role() {
            return this.name === 'admin' ? '超级管理员' : '普通用户';
        }
    },
    // created() {
    //     this.handleListener();
    //     this.changeDate();
    // },
    // activated() {
    //     this.handleListener();
    // },
    // deactivated() {
    //     window.removeEventListener('resize', this.renderChart);
    //     bus.$off('collapse', this.handleBus);
    // },
    methods: {
        changeDate() {
            const now = new Date().getTime();
            this.data.forEach((item, index) => {
                const date = new Date(now - (6 - index) * 86400000);
                item.name = `${date.getFullYear()}/${date.getMonth() + 1}/${date.getDate()}`;
            });
        }
        // handleListener() {
        //     bus.$on('collapse', this.handleBus);
        //     // 调用renderChart方法对图表进行重新渲染
        //     window.addEventListener('resize', this.renderChart);
        // },
        // handleBus(msg) {
        //     setTimeout(() => {
        //         this.renderChart();
        //     }, 200);
        // },
        // renderChart() {
        //     this.$refs.bar.renderChart();
        //     this.$refs.line.renderChart();
        // }
    }
};
</script>


<style scoped>
.el-row {
    margin-bottom: 20px;
    margin-top: 10px;
}

.grid-content {
    display: flex;
    align-items: center;
    height: 100px;
}

.grid-cont-right {
    flex: 1;
    text-align: center;
    font-size: 14px;
    color: #999;
}

.grid-num {
    font-size: 30px;
    font-weight: bold;
}

.grid-con-icon {
    font-size: 50px;
    width: 100px;
    height: 100px;
    text-align: center;
    line-height: 100px;
    color: #fff;
}

.grid-con-1 .grid-con-icon {
    background: rgb(45, 140, 240);
}

.grid-con-1 .grid-num {
    color: rgb(45, 140, 240);
}

.grid-con-2 .grid-con-icon {
    background: rgb(100, 213, 114);
}

.grid-con-2 .grid-num {
    color: rgb(45, 140, 240);
}

.grid-con-3 .grid-con-icon {
    background: rgb(242, 94, 67);
}

.grid-con-3 .grid-num {
    color: rgb(242, 94, 67);
}

.user-info {
    display: flex;
    align-items: center;
    padding-bottom: 20px;
    border-bottom: 2px solid #ccc;
    margin-bottom: 20px;
}

.user-avator {
    width: 120px;
    height: 120px;
    border-radius: 50%;
}

.user-info-cont {
    padding-left: 50px;
    flex: 1;
    font-size: 14px;
    color: #999;
}

.user-info-cont div:first-child {
    font-size: 30px;
    color: #222;
}

.user-info-list {
    font-size: 14px;
    color: #999;
    line-height: 25px;
}

.user-info-list span {
    margin-left: 70px;
}

/*.mgb20 {*/
/*    margin-bottom: 20px;*/
/*}*/

.todo-item {
    font-size: 14px;
}

.todo-item-del {
    text-decoration: line-through;
    color: #999;
}

.schart {
    width: 100%;
    height: 300px;
}
</style>
