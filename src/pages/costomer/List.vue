<template>
    <div>

        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
       <!-- 按钮结束 -->

       <!-- 表格 -->
       <!-- ！！！:（冒号） 绑定数据 -->
        <el-table :data="customers">
            <el-table-column prop ="id" label="编号"></el-table-column>
            <el-table-column prop ="realname" label="姓名"></el-table-column>
            <el-table-column prop ="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <!-- slot 接收当前行的数据 -->
                <template v-slot="slot">
                    <!--slot.row.id 当前行的ID  -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <!-- !!!!!@click 点击绑定事件-->
                    <!-- @click.prevent点击时阻止默认行为 （在这默认行为时跳转） -->
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
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
         title="录入员工信息"
         :visible.sync="visible"
         width="60%" >
         <!-- 表单 -->
     <el-form :model="form" label-width="80px">

         <el-form-item label="用户名">
             <!-- 获取用户输入的username -->
             <!-- v-model 双向数据绑定 视图改变数据改变-->
             <el-input v-model="form.username"> 
             </el-input>
         </el-form-item>

            <el-form-item label="密码">
             <!-- 获取用户输入的password -->
             <el-input v-model="form.password" type="password">
             </el-input>
         </el-form-item>

            <el-form-item label="真实姓名">
             <!-- 获取用户输入的password -->
             <el-input v-model="form.realname" >
             </el-input>
         </el-form-item>

            <el-form-item label="手机号">
             <!-- 获取用户输入的password -->
             <el-input v-model="form.telephone">
             </el-input>
         </el-form-item>

     </el-form>
     <!-- 表单结束 -->

     <!-- 模态框 -->
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
        loadData(){
             let url="http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //this为当前vue实例
            //将查询结果设置到customers中this指向外部函数的this
        this.customers=response.data;
        })
        },
        submitHandler(){
            //通过request与后台进行交互，并且要携带数据
            let url="http://localhost:6677/customer/saveOrUpdate"//交互接口
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)//变成查询字符串   交互参数

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
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口(request)完成删除操作
            let url = "http://localhost:6677/customer/deleteById?id="+id

            request.get(url).then((response)=>{
                this.loadData();
                 this.$message({
                type: 'success',
                message:response.message
            
            });

            })
           
        })
        },

        toUpdateHandler(row){
            //！！调用 visible用this
            //显示出当前行的信息
            this.form=row;
            this.visible=true;
        },

        CloseModalHandler(){
            this.visible=false;
        },

        toAddHandler(){
            //将for设为初始值
            this.form={
                type:"costomer"
            }
            this.visible=true;
        }
    },
    //用于存放要向网页中显示的数据 data一般用于定义数据，但一般不超过10个
    data(){

return{
    title:"录入顾客信息",
    visible:false,
    customers:[],
    form:{
        type:"customer"
    }
}

    },
    
    created(){
        //vue实例创建完毕所要执行的操作
       this.loadData();//刷新
        }
    
}
</script>









<style scoped>

</style>
