<!--一个vue组件的三部分，template，script，style-->
<template>
  <div class="post">
<el-row>
<el-col :span='7' class='pos-order' id='order-list'><!--设置订单栏，占7栏-->
<!--整体table表格-->
<el-tabs><!--类似tabel-->
<!--顶部点餐-->
<el-tab-pane label='点餐'>

<!--制作具体的清单table内容-->
<!--里面加show-summary则会自动计算，但是不准确-->
<el-table :data='tableData' border  style='width:100%'>

<el-table-column prop='goodsName'label='商品名称' ></el-table-column><!--prop为要操作的属性-->
<el-table-column prop='count'label='数量' width='50'></el-table-column>
<el-table-column prop='price'label='金额' width='70'></el-table-column>
<el-table-column  label='操作' fixed='right' width='100'><!--将操作区fixed固定在右边-->
<!--为操作区添加删除，增加-->
<template scope='scope'>
<el-button type='text' size='small' @click="del(scope.row)">删除</el-button>
<el-button type='text' size='small' @click="addOrderList(scope.row)">赠加</el-button>
</template>
</el-table-column>


</el-table>
<div class='computed'>
<small>数量</small>:{{totalCount}}&nbsp;&nbsp;<small>金额</small>:{{totalMoney}}元

</div>

<!--增加三个按钮-->
<div class='div-btn'>
<!--通过设置type值，设置按钮颜色-->
<el-button type='warning'>挂单</el-button>
<el-button type='danger' @click="delAll()">删除</el-button>
<el-button type='success' @click="checkout()">结账</el-button>
</div>

</el-tab-pane>
<!--顶部挂单-->
<el-tab-pane label='挂单'>
loading...
</el-tab-pane>
<!--顶部外卖-->
<el-tab-pane label='外卖'>
loading...

</el-tab-pane>


</el-tabs>
</el-col>
<!--采用24栏布局-->
<el-col :span='17'>
<div class='often-goods'>
<div class='title'>常用商品</div>
<div class='often-goods-list'>
<ul>
<li v-for='goods in oftenGoods' @click='addOrderList(goods)'>
<span>{{goods.goodsName}}</span>
<span class='o-price'>￥{{goods.price}}元</span>
</li>
</ul>
</div>
</div>

<div class='goods-type'>
<el-tabs>

<el-tab-pane label='汉堡'>
<!--制作汉堡详情页-->
<div>
<ul class='cookList'>
<li v-for='goods in type0Goods'>
<span class='foodImg'><img :src='goods.goodsImg' width='100%'/></span>
<span class='foodName'>{{goods.goodsName}}</span>
<span class='foodPrice'>￥{{goods.price}}元</span>
</li>
</ul>
</div>
</el-tab-pane>

<el-tab-pane label='零食'>
<!--制作零食详情页-->
<div>
<ul class='cookList'>
<li v-for='goods in type1Goods'>
<span class='foodImg'><img :src='goods.goodsImg' width='100%'/></span>
<span class='foodName'>{{goods.goodsName}}</span>
<span class='foodPrice'>￥{{goods.price}}元</span>
</li>
</ul>
</div>
</el-tab-pane>
<el-tab-pane label='饮料'>
<!--制作饮料详情页-->
<div>
<ul class='cookList'>
<li v-for='goods in type2Goods'>
<span class='foodImg'><img :src='goods.goodsImg' width='100%'/></span>
<span class='foodName'>{{goods.goodsName}}</span>
<span class='foodPrice'>￥{{goods.price}}元</span>
</li>
</ul>
</div>
</el-tab-pane>
<el-tab-pane label='套餐'>
<!--制作套餐详情页-->
<div>
<ul class='cookList'>
<li v-for='goods in type0Goods'>
<span class='foodImg'><img :src='goods.goodsImg' width='100%'/></span>
<span class='foodName'>{{goods.goodsName}}</span>
<span class='foodPrice'>￥{{goods.price}}元</span>
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

<script>
//那个组件需要axios，就在那个组件引入
import axios from 'axios';
export default {

  name: 'post',
  data(){
  return {
  tableData:[

  ],
  oftenGoods:[],


  type0Goods:[],
  type1Goods:[],
  type2Goods:[],
  type3Goods:[],
  totalMoney:0,
  totalCount:0
  }
  },
  //使用created钩子函数
  created:function(){
  axios.get('http://jspang.com/DemoApi/oftenGoods.php').then(response=>{
      //console.log(response)
      this.oftenGoods=response.data;
  }).catch(error=>{
  alert('网络错误，不能访问');
  });
  axios.get('http://jspang.com/DemoApi/typeGoods.php').then(response=>{
        //console.log(response)
        this.type0Goods=response.data[0];
        this.type1Goods=response.data[1];
        this.type2Goods=response.data[2];
        this.type3Goods=response.data[3];

}).catch(error=>{
    alert('网络错误，不能访问');
    })
  },
  //虚拟dom加载完执行
  mounted:function(){
  var orderHeight=document.body.clientHeight;
  document.getElementById('order-list').style.height=orderHeight+'px';
  },
  methods:{
  addOrderList(goods){
  this.totalMoney=0;
  this.totalCount=0;
  //判断商品是否已经存在于订单列表中
  let isHave=false;
  for(let i=0;i<this.tableData.length;i++){
  if(this.tableData[i].goodsId==goods.goodsId){
  isHave=true;
  }
  }


  //根据判断进行订单操作
  if(isHave){
  //改变列表中商品数量
  let arr=this.tableData.filter(o=>o.goodsId==goods.goodsId)
  arr[0].count++;
  }else{
  let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1}
  this.tableData.push(newGoods)
  }

  //进行价格计算
  this.tableData.forEach((canshu)=>{
  this.totalCount+=canshu.count;
  this.totalMoney=this.totalMoney+(canshu.price*canshu.count)
  })
  this.getAll()

},
//删除单个商品
del(goods){
this.tableData=this.tableData.filter(o=>o.goodsId !=goods.goodsId)
this.getAll()
},
//删除后重新计算数量金额
getAll(){
this.totalCount=0;
this.totalMoney=0;
if(this.tableData){
 this.tableData.forEach((canshu)=>{
  this.totalCount+=canshu.count;
  this.totalMoney=this.totalMoney+(canshu.price*canshu.count)
  })
}
},
//删除所有商品
delAll(){
this.totalCount=0;
this.totalMoney=0;
this.tableData=[];
},
//结账
checkout() {
    if (this.totalCount!=0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        //element提供的message组件
        this.$message({
            message: '您好，结账成功！',
            type: 'success'
        });

    }else{
        this.$message.error('您好，暂无订单！');
    }

}
},


}
</script>

<!--scoped表示这个style是局部的，只在这个组件中有用-->
<style scoped>
.post{
font-size:12px;}
.pos-order{
background-color:#F9FAFC;
border-right:1px solid #C0CCDA;
}
.div-btn{
margin-top:10px;
text-align:center;
}
.title{
height:20px;
border-bottom:1px solid #D3dce6;
background-color:#F9FAFC;
padding:10px;
text-align:left;
}
.often-goods-list ul li{
list-style:none;
float:left;
border:1px solid #E5E9F2;
padding:8px;
margin:10px;
background-color:#FFF;
}
.o-price{
color:#58B7FF;
}
.goods-type{
clear:both;
}
/*详情页样式*/
.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auto;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;

   }
    li:hover{
   cursor:pointer;
   }
   .cookList li span{

        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;

   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
       }
   .computed{

       height: auto;
       overflow: hidden;
       text-align: center;
       font-size: 16px;
       backround-color:#fff;
       padding:10px;
       border-bottome:1px solid #D3dce6;}
.is-leaf{display:none;}
</style>
