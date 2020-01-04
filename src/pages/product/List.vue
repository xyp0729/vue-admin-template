<template>
  <div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="products">
          <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
          <el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
          <el-table-column prop="realname" label=""></el-table-column>
           <el-table-column prop="price" label="价格"></el-table-column>
           <el-table-column prop="status" label="状态"></el-table-column>       
           <el-table-column width=120px prop="categoryId" label="所属产品"></el-table-column>
           <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                  <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                  <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                  <a href="" @click.prevent>详情</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <!-- <el-pagination layout="prev, pager, next" :total="50"></el-pagination> -->
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      title="录入产品信息"
      :visible.sync="visible"
      width="60%">
        ---{{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="名称">
            <el-input v-model="form.name"/>
        </el-form-item>
        <el-form-item label="价格">
            <el-input v-model="form.price"/>
        </el-form-item>
        <el-form-item label="所属栏目">
            <el-select v-model="form.categoryId" placeholder="请选择">
                <el-option
                     v-for="item in options"
                     :key="item.id"
                     :label="item.name"
                     :value="item.id">
                </el-option>
            </el-select>
                    <!-- <el-dropdown>
                        <el-button  v-model="form.categoryId">
                            请选择<i class="el-icon-arrow-down el-icon--right"></i>
                        </el-button>
                    <el-dropdown-menu slot="dropdown"> 
                        <el-dropdown-item>12</el-dropdown-item>
                          <el-dropdown-item>家居洗护</el-dropdown-item>
                          <el-dropdown-item>9357</el-dropdown-item>
                          <el-dropdown-item>洗护服务</el-dropdown-item>
                          <el-dropdown-item>生活急救箱</el-dropdown-item>
                          <el-dropdown-item>yyy</el-dropdown-item>
                          <el-dropdown-item>水果慢羊羊</el-dropdown-item>
                          <el-dropdown-item>wxj</el-dropdown-item>
                          <el-dropdown-item>www</el-dropdown-item>
                          <el-dropdown-item>123</el-dropdown-item>
                          <el-dropdown-item>嗷嗷嗷啊</el-dropdown-item>
                     </el-dropdown-menu>
                    </el-dropdown> -->
                </el-form-item>
                  <el-form-item label="介绍">
                    <el-input  type="textarea" rows="2" placeholder="请输入内容" v-model="form.description"/>
                </el-form-item>
                 <el-form-item label="产品主图">
                     <el-button type="primary" v-model="form.photo">
                         点击上传<i class="el-icon-upload el-icon--right"></i>
                     </el-button>
                </el-form-item>
                只能上传jpg/png文件，且不超过500kb
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->

  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  methods:{
    loadData(){
      let url ="http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.products = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'product',age:12}
      // json字符串 '{"type":"product","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=product&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/product/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {

        let url ="http://localhost:6677/product/deleteById?id="+id;
        request.get(url).then((response)=>{
          this.loadData();
          this.$message({
                  type: 'success',
                  message: response.message
        });
          
        })
       
      })
      
    },
    toUpdateHandler(row){
      this.form=row;
      this.visible =true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.form = {
        type:"product"
      }
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      products:[],
      options:[],
      form:{
        type:"product"
      }
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  }
}
</script>

<style scoped>
 
</style>