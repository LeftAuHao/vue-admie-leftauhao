<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--按钮结束-->
        <!--表格-->
         <el-table :data="product">
             <el-table-column prop="id" label="编号"></el-table-column>
             <el-table-column prop="name" label="产品名称"></el-table-column>
             <el-table-column prop="price" label="价格"></el-table-column>
             <el-table-column prop="description" label="描述"></el-table-column>
             <el-table-column prop="categoryId" label="所属产品"></el-table-column>
             <el-table-column label="操作">
                 <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                    <a href="" @click.prevent="toUpdateHandler">详情</a>
                 </template>
             </el-table-column>
        </el-table>
        <!--表格结束-->
        <!--分页-->
        <el-pagination
            layout="prev, pager, next" :total="50">
         </el-pagination>
        <!--分页结束-->
        <!--模态框-->
        <el-dialog title="录入产品信息" :visible.sync="visible" width="60%" >
            <el-form :model="form" label-width="80px"> 
                    <el-form-item label="名称">
                        <el-input type="name" v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="价格">
                        <el-input v-model="form.price"></el-input>
                    </el-form-item>
                    <el-form-item label="所属栏目">
                    <el-select v-model="form.status" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" :label="item.name"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                    <el-form-item label="介绍">
                        <el-input  v-model="form.description"></el-input>
                    </el-form-item>
                </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!--模态框结束-->
    </div>   
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url ="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
            this.product=response.data;
           //将查询结果设置到customers中,this指向外部函数的对象
            })
        },
        submitHandler(){
            //通过request与后台进行交互，并且要携带参数
            let url = "http://localhost:6677/product/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)//转化为查询字符串
            }).then((response)=>{
                //请求关闭
                this.closeModalHandler();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
                this.loadData();
            })
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
            }).then(() => {
                this.$message({
                        type: 'success',
                        message: '删除成功!'
                 });
            })
        },
        toUpdateHandler(row){
            this.form=row;
            this.title="修改产品信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
            this.visible=true;
        }
    },
   //用于存放要向网页中显示的数据
   data(){
       return{
            visible:false,
            product:[],
            form:{}
       }
   },
   created(){
       //vue实例创建完毕
       this.loadData();
   } 
}
</script>

<style scoped>

</style>