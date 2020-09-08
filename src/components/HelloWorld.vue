<template>
  <div >
    <el-table
      :data="tableData"
      border
      style="width: 100%">
      <el-table-column
        fixed
        prop="id"
        label="编号"
        width="150">
      </el-table-column>
      <el-table-column
        prop="username"
        label="姓名"
        width="120">
      </el-table-column>
      <el-table-column
        prop="nickname"
        label="公司名称"
        width="120">
      </el-table-column>
      <el-table-column
        prop="address"
        label="地址"
        width="120">
      </el-table-column>
      <el-table-column
        fixed="right"
        label="操作"
        width="100">
        <template slot-scope="scope">
          <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
          <el-button @click="handleUpdate(scope.row)" type="text" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-button @click="handleAdd()" type="success" size="small">添加</el-button>
    <!-- 分页插件-->
    <el-pagination
      layout="total,prev,pager,next,jumper"
      v-on:current-change="changePage"
      :total=this.total
      :current-page=this.params.page
      :page-sizes=this.pageSizes
      :page-size=this.params.size
      style="float:right;">
    </el-pagination>

    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="50%"
      :before-close="handleClose">
      <el-form ref="form" :model="form" label-width="100px">
        <el-form-item label="编号">
          <el-input v-model="form.id" :disabled="edit"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="公司名称">
          <el-input v-model="form.nickname"></el-input>
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="form.address"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">提交</el-button>
          <el-button @click="dialogVisible = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    methods: {
      handleClick(row) {
        var id = row.id
        axios.post("http://localhost:9002/springcloud-live-persion/findById",{"id":id}).then(res=>{
            this.form=res.data
        })
        this.dialogVisible=true;
      },
      handleUpdate(row){
        var id = row.id
        axios.post("http://localhost:9002/springcloud-live-persion/findById",{"id":id}).then(res=>{
          this.form=res.data
        })
        this.dialogVisible=true;
      },
      handleAdd(row){
        this.dialogVisible=true;
      },
      findAll(){
          axios.get("http://localhost:9002/springcloud-live-persion/findpersions/"+this.params.page+"/"+this.params.size).then(res=>{
              this.tableData=res.data.list
              this.total=res.data.total
          })
      },
      changePage:function (page) {
        this.params.page=page;
        this.findAll()
      },
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
      onSubmit() {
        axios.post("http://localhost:9002/springcloud-live-persion/savePersion",this.form).then(res=>{
            alert("--------------------")
          if(res.data==1){
            this.dialogVisible=false;
            alert("修改成功")
            this.findAll()
          }else{
              alert("修改失败")
          }
        })
      },
    },

    mounted(){
        this.findAll()
    },
    data() {
      return {
        tableData: [],
        params:{
            size:2,
            page:1
        },
        total:10,
        pageSizes:[2,3,4,5],
        dialogVisible:false,
        form: {
          id: '',
          username: '',
          nickname: '',
          address: ''
        },
        edit:true
      }
    }
  }
</script>
<style scoped>

</style>
