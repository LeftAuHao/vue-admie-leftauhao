<template>
    <div>
        <!--按钮-->
        <div>
            <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
            <el-button type="danger" size="small">批量删除</el-button>
        </div>
        <!--按钮结束-->
        <!--表格-->
         <el-table :data="category">
             <el-table-column prop="num" label="序号"></el-table-column>
             <el-table-column prop="id" label="编号"></el-table-column>
             <el-table-column prop="name" label="栏目名称"></el-table-column>
             <el-table-column width="120" prop="parentId" label="父栏目"></el-table-column>
             <el-table-column fixed="right" label="操作">
                 <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
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
        <el-dialog title="录入栏目信息" :visible.sync="visible" width="60%" >

            <el-form :model="form" label-width="80px">
                <el-form-item label="栏目名称">
                    <el-input v-model="form.name"/>
                </el-form-item>
                <el-form-item label="序号">
                    <el-input v-model="form.num"/>
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
    created(){
        this.loadDate();
    },
    data(){
        return{
            title:'修改栏目信息',
            visible:false,
            category:[],
            form:{
                type:"category"
            }
        }
    },
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/category/saveOrUpdate";
            request({
                url,
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求关闭
                this.closeModalHandler();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
                this.loadDate();
            })
        },
        loadDate(){
            let url ="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                //箭头函数的this 指向外部函数中的this
                this.category=response.data;
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
            title:'修改栏目信息';
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            title:'添加栏目信息';
            this.visible=true;
        }
    }
}
</script>

<style scoped>
    
</style>