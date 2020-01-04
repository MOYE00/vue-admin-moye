<template>
    <div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>

    <el-table :data="waiters">
        <el-table-column prop="id" fixed="left" label="编号"></el-table-column>
            <el-table-column prop="username" fixed="left"  label="用户名"></el-table-column>
            <el-table-column prop="realname"  label="姓名"></el-table-column>
            <el-table-column prop="telephone" width="120" label="手机号"></el-table-column>
            <el-table-column prop="idCard" width="200" label="身份证号"></el-table-column>
            <el-table-column prop="bankCard" width="200" label="银行卡号"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>
    <!--分页开始-->
    <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!--分页结束-->
  <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
  <el-form :model="form" label-width="80px">
    <el-form-item label="用户名">
      <el-input v-model="form.username"/>
    </el-form-item>
    <el-form-item label="密码">
      <el-input type="password" v-model="form.password"></el-input>
    </el-form-item>
    <el-form-item label="姓名">
      <el-input v-model="form.realname"></el-input>
    </el-form-item>
    <el-form-item label="性别">
      <el-radio-group v-model="form.gender">
      <el-radio label="男">男</el-radio>
      <el-radio label="女">女</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="手机号">
      <el-input v-model="form.telephone"></el-input>
    </el-form-item>
    <el-form-item label="身份证号">
      <el-input v-model="form.idCard"></el-input>
    </el-form-item>
    <el-form-item label="银行卡号">
      <el-input v-model="form.bankCard"></el-input>
    </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>


<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放要向网页中显示的数据
    methods:{
      loadData(){
        //this是当前实例实例
        // VUE实例创建完毕
        let url ="http://localhost:6677/waiter/findAll"
        request.get(url).then((response)=>{
          //将查询结果设置到waiters中
          this.waiters = response.data;
        })
      },
      submitHandler(){
            // 通过request与后台进行交互
            let url="http://localhost:6677/waiter/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
              //模态框关闭
              this.closeModalHandler();
              this.loadData();
              this.$message({
                type:"success",
                message:response.message
              })
            })
        },
        toAddHandler(){
          this.title="添加顾客信息"
          this.form = {
            type:"waiter"
          }
          this.visible=true;
        },
        closeModalHandler(){
          this.visible=false;
        },
        toUpdateHandler(row){
          this.title="修改顾客信息"
          this.form = row;
          this.visible=true;
        },
        toDeleteHandler(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口，完成删除操作
          let url = "http://localhost:6677/waiter/deleteById?id="+id;
          request.get(url).then((request)=>{
            //刷新数据
            this.loadData();
            //提示消息
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        });
      }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      }
    },
    data(){
        
        return{
            visible:false,
            waiters:[],
            form:{
              type:"waiter"
            }
        }
    },
    created (){
        this.loadData()
      }
}
</script>

<style scoped>
</style>