<template>
  <div class="pos">
    <div>
      <el-row>
        <el-col :span='7' class='pos-order' id='order-list'>
          <el-tabs v-model="activeName">
            <el-tab-pane label="点餐" name="first">
              <el-table :data="tableData" border show-summary style="width: 100%">
                <el-table-column prop="goodsName" label="商品名称">
                </el-table-column>
                <el-table-column prop="count" label="数量" width="70">
                </el-table-column>
                <el-table-column prop="totalPrice" label="金额" width="70">
                </el-table-column>
                <el-table-column label="操作" width="100" fixed="right">
                  <template scope="scope">
                    <el-button type="text" size="small" @click="delGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addGoods(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="totalDiv">
                <small>数量: </small>{{calTotalCount}} &nbsp;
                <small>金额: </small>{{calTotalMoney | formatMoney('元')}}(注:仅测试'computed')
              </div>
              <div class="div-btn">
                <el-button type="warning">挂单</el-button>
                <el-button type="danger" @click="delAllGoods">删除</el-button>
                <el-button type="success" @click="checkout">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单" name="second">
              挂单
            </el-tab-pane>
            <el-tab-pane label="外卖" name="third">
              外卖
            </el-tab-pane>
          </el-tabs>
        </el-col>
        <!--商品展示-->
        <el-col :span="17">
          <div class="offen-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="goods in oftenGoods" :key="goods.goodsId" @click="addGoods(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-price">{{goods.price}}</span>
                </li>
              </ul>
            </div>
            <div class="goods-type">
  
              <el-tabs>
                <el-tab-pane label="汉堡">
                  <div>
                    <ul class='cookList'>
                      <li v-for="goods in type0Goods" :key="goods.goodsId" @click="addGoods(goods)">
                        <span class="foodImg">
                          <img :src="goods.goodsImg" width="100%">
                        </span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="小食">
                  <div>
                    <ul class='cookList'>
                      <li v-for="goods in type1Goods" :key="goods.goodsId" @click="addGoods(goods)">
                        <span class="foodImg">
                          <img :src="goods.goodsImg" width="100%">
                        </span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="饮料">
                  <div>
                    <ul class='cookList'>
                      <li v-for="goods in type2Goods" :key="goods.goodsId" @click="addGoods(goods)">
                        <span class="foodImg">
                          <img :src="goods.goodsImg" width="100%">
                        </span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="套餐">
                  <div>
                    <ul class='cookList'>
                      <li v-for="goods in type3Goods" :key="goods.goodsId" @click="addGoods(goods)">
                        <span class="foodImg">
                          <img :src="goods.goodsImg" width="100%">
                        </span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                      </li>
                    </ul>
                  </div>
                </el-tab-pane>
  
              </el-tabs>
            </div>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
 
<script>
import axios from "axios";

export default {
  name: 'Pos',
  data() {
    return {
      activeName: 'first',
      totalCount: 0,
      totalMoney: 0,
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
    }
  },
  filters: {
    formatMoney: function (value, type) {
      return "￥" + value + type
    }
  },
  methods: {
    addGoods: function (goods) {
      let flag = true;
      this.tableData.forEach(item => {
        if (item.goodsId == goods.goodsId) {
          item.totalPrice = item.price * ++item.count;
          flag = false;
          return false;
        }
      });
      if (flag) {
        this.tableData.push({
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          count: 1,
          price: goods.price,
          totalPrice: goods.price
        })
      }
    },
    delGoods: function (goods) {
      let index = this.tableData.indexOf(goods);
      this.tableData.splice(index, 1);
    },
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    checkout() {
      if (this.totalCount != 0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        this.$message.success({
          message: '结账成功，感谢你又为店里出了一份力!',
          duration: 1000
        });
      } else {
        this.$message.error({ message: '不能空结。老板了解你急切的心情！', duration: 1000 });
      }

    }
  },
  computed: {
    calTotalCount: function () {
      this.totalCount = 0;
      this.tableData.forEach(item => {
        this.totalCount += item.count;
      });
      return this.totalCount;
    },
    calTotalMoney: function () {
      this.totalMoney = 0;
      this.tableData.forEach(item => {
        this.totalMoney += item.totalPrice;
      });
      return this.totalMoney;
    },
  },
  created() {
    axios.get('http://jspang.com/DemoApi/oftenGoods.php').then(response => {
      this.oftenGoods = response.data;
    }).catch(error => {
      console.log(error);
      alert('网络错误，不能访问');
    });
    axios.get('http://jspang.com/DemoApi/typeGoods.php').then(response => {
      this.type0Goods = response.data[0];
      this.type1Goods = response.data[1];
      this.type2Goods = response.data[2];
      this.type3Goods = response.data[3];

    }).catch(error => {
      console.log(error);
      alert('网络错误，不能访问');
    })
  },
  mounted: function () {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + 'px';
  },
}
</script>
<style scoped>
.pos-order {
  background-color: #F9FAFC;
  border-right: 1px solid #C0CCDA;
  height: 100%;
}

.div-btn {
  margin-top: 10px;
}

.title {
  height: 20px;
  border-bottom: 1px solid #D3DCE6;
  background-color: #F9FAFC;
  padding: 10px;
  text-align: left;
}

.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #E5E9F2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
  cursor: pointer;
}

.o-price {
  color: #58B7FF;
}

.goods-type {
  clear: both;
}

.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #E5E9F2;
  height: auto;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}

.cookList li span {

  display: block;
  float: left;
}

.foodImg {
  width: 40%;
}

.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}

.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}

.totalDiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #D3DCE6;
}
</style>