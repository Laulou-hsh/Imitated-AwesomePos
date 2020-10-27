<template>
    <!----------------------------------点餐栏------------------------------>
    <div class="pos">
        <el-row :gutter="10" style="margin:0">
            <el-col :span="7" class="pos-order" id="order-list">
                <el-tabs style="padding: 0px 8px">
                    <el-tab-pane label="点餐">
                        <el-table :data="tableData" boder style="width:100%">
                            <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                            <el-table-column prop="count" label="数量" width="50"></el-table-column>
                            <el-table-column prop="price" label="价格" width="70"></el-table-column>
                            <el-table-column label="操作" width="100" fixed="right">
                                <template slot-scope="scope">
                                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="totalDiv">
                            <small>数量：</small>
                            {{ totalCount }}   &nbsp;&nbsp;&nbsp;&nbsp;  <small>金额：</small>{{ totalMoney }}
                        </div>
                        <div class="div-btn">
                            <el-button type="warning">挂单</el-button>
                            <el-button type="danger" icon="el-icon-delete" @click="delAllGoods()">清空</el-button>
                            <el-button type="success" icon="el-icon-check" @click="checkout()">结账</el-button>
                        </div>
                    </el-tab-pane>
                    <!-- <el-tab-pane label="挂单">
                        挂单
                    </el-tab-pane>
                    <el-tab-pane label="外卖">
                        外卖
                    </el-tab-pane> -->
                </el-tabs>
            </el-col>
            <!----------------------------------- 商品栏 -------------------------->
            <el-col :span="17" style="padding: 0px">
                <div class="often-goods">
                    <div class="title">常用商品</div>
                    <div class="often-goods-list">
                        <ul>
                            <li v-for="goods in oftenGoods" :key="goods.goodsId" @click="addOrderList(goods)">
                                <span>{{ goods.goodsName }}</span>
                                <span class="o-price">￥{{ goods.price }}元</span>
                            </li>
                        </ul>
                    </div>
                </div>

                <div class="goods-type">
                    <el-tabs>
                        <el-tab-pane label="汉堡">
                            <div>
                                <ul class="cookList">
                                    <li v-for="goods in type0Goods" :key="goods.goodsId" @click="addOrderList(goods)">
                                        <span class="foodImg"><img :src= "goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{ goods.goodsName }}</span>
                                        <span class="foodPrice">￥{{ goods.price }}</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="小食">
                            <div>
                                <ul class="cookList">
                                    <li v-for="goods in type1Goods" :key="goods.goodsId">
                                        <span class="foodImg"><img :src= "goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{ goods.goodsName }}</span>
                                        <span class="foodPrice">￥{{ goods.price }}</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="饮料">
                            <div>
                                <ul class="cookList">
                                    <li v-for="goods in type2Goods" :key="goods.goodsId">
                                        <span class="foodImg"><img :src= "goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{ goods.goodsName }}</span>
                                        <span class="foodPrice">￥{{ goods.price }}</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                        <el-tab-pane label="套餐">
                            <div>
                                <ul class="cookList">
                                    <li v-for="goods in type3Goods" :key="goods.goodsId">
                                        <span class="foodImg"><img :src= "goods.goodsImg" width="100%"></span>
                                        <span class="foodName">{{ goods.goodsName }}</span>
                                        <span class="foodPrice">￥{{ goods.price }}</span>
                                    </li>
                                </ul>
                            </div>
                        </el-tab-pane>
                    </el-tabs>
                </div>
            </el-col>
        </el-row>
        
    </div>
</template>

<!---------------------------- JavaScript部分 ------------------------->
<script>
import axios from 'axios';
export default {
    name: 'pos',
    data(){
        return {
            //点餐数据
            tableData: [],
            //常用商品数据
            oftenGoods: [],
            //分类点餐数据
            type0Goods: [],
            type1Goods: [],
            type2Goods: [],
            type3Goods: [],
            totalMoney: 0,
            totalCount: 0,
        };
    },
    //利用fastmock模拟在线远程后台数据
    created: function(){
        axios.get('https://www.fastmock.site/mock/9bfb2715faba0c59062c06526d4e6727/kuaican/oftenGoods')
        .then(response =>{
            console.log(response);
            this.oftenGoods = response.data.oftenGoods;
        })
        .catch(error =>{
            // console.log(error);
            alert('网络错误，无法访问！');
        });
        //response返回来的值

        axios.get('https://www.fastmock.site/mock/9bfb2715faba0c59062c06526d4e6727/kuaican/typeGoods')
          .then(response=>{
             console.log(response);
             //this.oftenGoods=response.data;
             this.type0Goods=response.data.data[0];
             this.type1Goods=response.data.data[1];
             this.type2Goods=response.data.data[2];
             this.type3Goods=response.data.data[3];
          })
          .catch(error=>{
              console.log(error);
              alert('网络错误，不能访问');
          })
    },
    mounted: function(){
        //原生JS改变高度
        var orderHeight = document.body.clientHeight;
        document.getElementById('order-list').style.height = orderHeight + 'px';
    },
    //将商品数据传递给订餐栏
    methods: {
        //增加
        addOrderList(goods){
            //初始化
            this.totalMoney = 0;
            this.totalCount = 0;

            //商品是否存在于订单列表
            let isHave = false;
            for(let i = 0; i < this.tableData.length; i++){
                if(this.tableData[i].goodsId == goods.goodsId){
                    isHave = true;
                }
            }
            //根据判断的值编写业务逻辑
            if(isHave){
                //改变列表中商品的数量
                let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
                arr[0].count++;
            }else{
                //不存在就推入数组
                let newGoods = {
                    goodsId: goods.goodsId, 
                    goodsName: goods.goodsName,
                    price: goods.price,
                    count: 1,
                }
                this.tableData.push(newGoods);
            }
            this.getAllMoney();
        },
        //删除单个商品
        delSingleGoods(goods){
            this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
            this.getAllMoney();
        },
        //删除所有商品
        delAllGoods(){
            this.tableData = [];
            this.totalCount = 0;
            this.totalMoney = 0;
        },
        checkout(){
            if(this.totalCount != 0){
                this.tableData = [];
                this.totalCount = 0;
                this.totalMoney = 0;
                this.$message({
                    message: '结账成功！谢谢惠顾！',
                    type: 'success',
                });
            }else{
                this.$message.error('请选择商品！');
            }
        },
        //汇总数量和金额
        getAllMoney(){
            //每次都初始化
            this.totalCount = 0;
            this.totalMoney = 0;
            if(this.tableData){
                //进行数量和价格的汇总计算
                this.tableData.forEach((element) =>{
                //取得数量
                    this.totalCount += element.count;
                    this.totalMoney = this.totalMoney + (element.price * element.count);
                });
            }
        },
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<!--样式-->
<style scoped>
    .pos-order{
        background-color: rgba(239,242,247,1);
        border-right: 1px solid #cbccda;
    }
    .div-btn{
        margin: 10px 0px;
    }
    .title{
        height: 20px;
        border-bottom: 1px solid #d3dce6;
        background-color: #f9fafc;
        padding: 10px;
        margin: 0px auto;
        text-align: left;
    }
    .often-goods-list ul{
        width: 90%;
        display: grid;
        grid-template-columns: 1fr 1fr 1fr 1fr;
        align-items: center;
        justify-content: center;
    }
    .often-goods-list ul li{
        width: 90%;
        height: 50px;
        list-style: none;
        /* float: left; */
        border: 1px solid #e5e9f2;
        padding: 5px;
        margin: 5px;
        background-color: #fff;
        text-align: center;
        line-height: 50px;
        column-gap: 5px;
        row-gap: 5px;
        cursor: pointer;
    }
    /* 价格 */
    .o-price{
        color: #58b7ff;
    }
    .goods-type{
        height: 100%;
        clear: both;
        padding: 0px 10px;
    }
    .cookList li{
       list-style: none;
       width: 18%;
       border: 1px solid #E5E9F2;
       height: 150px;
       overflow: auto;
       background-color: #fff;
       padding: 2px;
       float: left;
       margin: 5px;
       cursor: pointer;
    }
    .cookList li span{
        display: inline-block;
    }
    /*食品图片*/
    .foodImg{
        width: 80%;
        margin: 0px auto;
    }
    .foodImg img{
        height: 100px;
        width: 100px;
    }
    .foodName{
        font-size: 18px;
        text-align: center;
        color:brown;
    }
    .foodPrice{
        font-size: 16px;
        padding-top:10px;
    }
    .totalDiv{
        background-color: #fff;
        padding: 10px;
        border-bottom: 1px solid #d3dce6;
        line-height: normal;
    }
    .etn-tabs__nav-scroll{
        background-color: #fff;
    }
</style>
