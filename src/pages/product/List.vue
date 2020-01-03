<template>
    <div>

        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
       <!-- 按钮结束 -->

       <!-- 表格 -->
       <!-- ！！！:（冒号） 绑定数据 -->
        <el-table :data="product">
            <el-table-column fixed="left" prop ="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop ="name" label="产品名称"></el-table-column>
            <el-table-column prop ="price" label="价格"></el-table-column>
            <el-table-column prop ="description" label="描述"></el-table-column>
            <el-table-column width="120" prop ="status" label="所属产品"></el-table-column>
            <!-- fixed 固定定位 -->
            <el-table-column fixed="right" label="操作">
                <!-- slot 接收 -->
                <template v-slot="slot">
                    <!--slot.row.id 当前行的ID  -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <!-- !!!!!@click 点击绑定事件-->
                    <!-- @click.prevent点击时阻止默认行为 （在这默认行为时跳转） -->
                    <a href="" @click.prevent="toUpdateHandler(slot.row.id)">修改</a>
                    
                </template>
            </el-table-column>
        </el-table>
<!-- 表格结束 -->

        <!-- 分页开始 -->
        <el-pagination
        layout="prev, pager, next"
         :total="50">
         </el-pagination>
        <!-- 分页结束 -->

        <!-- 模态框 -->
    <el-dialog
         :title="title"
         :visible.sync="visible"
         width="60%" >
     <el-form :model="form" label-width="80px">
         测试{{form}}
         <el-form-item label="产品名称">
             <el-input v-model="form.name" ></el-input>
         </el-form-item>

                  <el-form-item label="价格">
             <el-input v-model="form.price" ></el-input>
         </el-form-item>

                  <el-form-item label="描述">
             <el-input v-model="form.description" ></el-input>
         </el-form-item>


                  <el-form-item label="所属产品">
             <el-input v-model="form.status"></el-input>
         </el-form-item>

 

     </el-form>
        <span slot="footer" class="dialog-footer">
        <el-button @click="CloseModalHandler" size="small">取 消</el-button>
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
    </span>
     </el-dialog>
        <!-- 模态框 -->
        
    </div>
</template>





<script>
import request from '@/utils/request'
import querystring from "querystring" 
export default {
    //用于存放网页中需要调用的方法
    methods:{
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{//交互完毕
                //请求结束 模态框关闭
                this.CloseModalHandler();
                //刷新
                this.loadData()
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        loadData(){
            let url = "http://localhost:6677/product/findAll"
            //methods 有this值， 指向VUE实例访该实例中的数据
            //箭头函数中的this指向外部函数的this fuction指向widows的this
            request.get(url).then((response)=>{
                this.product=response.data
            })
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then((response) => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },

        toUpdateHandler(id){
            //！！调用 visible用this
            this.visible=true;
            this.title="编辑员工信息";

        },


        CloseModalHandler(){
            this.visible=false;
        },

        toAddHandler(){
            this.visible=true;
        }
    },
    created(){
        //在页面加载出来时加载数 
        this.loadData();
    },
    //用于存放要向网页中显示的数据 data一般用于定义数据，但与颁布不超过10个，本次data定义了两个
    data(){
return{
    title:"录入员工信息",
    visible:false,
    product:[],
    form:{
        type:"product"
    }
}

    }

    
}
</script>









<style scoped>

</style>
