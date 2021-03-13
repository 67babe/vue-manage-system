<template>
  <div>
      <el-button class="add-user" type="success" @click="addUser">添加用户</el-button>
    <el-button class="add-permission" type="success" @click="addPermission">添加新权限</el-button>
  <div class="main_menu">
    <el-select v-model="search_value" placeholder="请选择">
        <el-option
        v-for="item in new_permissions_list"
        :key="item.permissionId"
        :label="item.permissionName"
        :value="item.permissionName">
        </el-option>
    </el-select> 
    <el-button class="search" type="primary">搜索</el-button>
        <div class="page">
        <el-pagination
            layout="prev, pager, next" @current-change="handleCurPage"
            :total="50">
        </el-pagination>
        </div> 
  </div>
 
    <el-table :data="users" stripe :header-cell-style="{background:'#eef1f6',color:'#606266'}">
        <el-table-column prop="userName" label="用户名">
        </el-table-column>
        <el-table-column  label="已有权限">
            <template slot-scope="scope">
                <span v-for="(item,i) in scope.row.permissions" :key="i">
                    {{item.permissionName}}&nbsp;
                </span>
            </template>            
        </el-table-column>
        <el-table-column label="编辑权限">
            <template slot-scope="scope">
            <el-button  type="text" size="small" @click="edit(scope.row)">编辑</el-button>
        </template>
        </el-table-column>
        <el-table-column
        fixed="right"
        label="操作"
        width="100">
        <template slot-scope="scope">
            <el-button @click="update(scope.row)" type="text" size="small">修改密码</el-button>
            <el-button type="text" size="small" @click="remove(scope.row)">删除</el-button>
        </template>
        </el-table-column>        
    </el-table>
    <EditPermissionModel :items="permissions_list" :user_id="cur_user_id" v-show="show_edit_permission" v-on:closeme="closeEdit" v-on:confirm="confirmEdit"></EditPermissionModel>
    <AddPermissionModel  v-show="show_add_permission" v-on:closeme="closeAdd" v-on:confirm="confirmAdd"></AddPermissionModel>
    <common-modal v-show="is_show" v-on:closeme="close" v-on:confirm="confirm">
            <div slot="body">
                用户名：<el-input v-model="user_name" placeholder="请输入内容"></el-input>
            </div>
            <div slot="body">
                邮箱：<el-input v-model="email" placeholder="请输入内容"></el-input>
            </div>            
            <div slot="body" style="margin-top:2%;">
                密码：<el-input v-model="password" placeholder="请输入内容" show-password></el-input>
            </div>
            <el-checkbox slot="body" v-model="item.checked" v-for="(item,i) in new_permissions_list" :key="i">{{item.permissionName}}</el-checkbox>   
    </common-modal>
    <common-modal v-show="is_show_update_pwd" v-on:closeme="closeUpdate" v-on:confirm="confirmUpdate">
            <div slot="body">
                旧密码：<el-input v-model="old_pwd" placeholder="请输入内容" show-password></el-input>
            </div>           
            <div slot="body" style="margin-top:2%;">
                新密码：<el-input v-model="new_pwd" placeholder="请输入内容" show-password></el-input>
            </div> 
    </common-modal>      
  </div>
</template>

<script>
//   import test_data from '../../json/test_data.json'
  import EditPermissionModel from '../commonUserComponents/selectPermissionModel.vue'
  import AddPermissionModel from '../commonUserComponents/configNewPermission.vue';
  import commonModal from '../commonUserComponents/commonModal.vue'
  import {showLoading, hideLoading } from '../commonUserComponents/loading.js';
  export default{
      data(){
          return {
            search_value: '',
			 base_url:"http://zprwh9.natappfree.cc/api/v1/pub/",
              new_permissions_list: [],
              users:[],
              user_name:"",
              email:"",
              password:"",
              is_show:false, 
              is_show_update_pwd:false,             
              show_edit_permission:false,
              show_add_permission:false,
              cur_edit_user:"",
              permissions_list:[],
              tmp_pre_permissions_list:[],
              cur_user_id:"" ,
              old_pwd:"",
              new_pwd:"",
              row:""   
          }
      },
      components:{
          EditPermissionModel,
          AddPermissionModel,
          'common-modal':commonModal
      },
      
    created(){
        this.getPermissions();
      },
      mounted:function(){
        
        
        // this.users=test_data.users_new;
        // this.permissions_list=test_data.permissions_list;
      },
      methods:{
           addUser(){
               for(let i=0; i<this.new_permissions_list.length; i++){
                   this.new_permissions_list[i].checked=false;
               }
              this.is_show=true;
          },
          close(){
              this.is_show=false;
          },
          handleCurPage(page){
              this.getUsers(page);

          },
          confirm(){
              //这里调接口
              
              console.log(this.user_name,this.password)
              this.is_show=false;
              var reg = new RegExp("^[0-9]*$");
              var reg_mail = /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/;
              var msg;
              
             if(this.user_name.length!=11||!reg.test(this.user_name)){
                 this.$message.error("用户名不是电话！");
             }else if(this.password.length<6){
                 this.$message.error("密码至少六位！");
             }else if(!reg_mail.test(this.email)){
                this.$message.error("邮箱格式错误！");
             }else{
                 console.log(this.new_permissions_list)
                 let list=[];
               for(let i=0; i<this.new_permissions_list.length; i++){
                   if(this.new_permissions_list[i].checked==true){
                        list.push(this.new_permissions_list[i].permissionId);
                   }
                   
               } 
               msg={
                "user_name":this.user_name,
                "password":this.password,
                "permissions":list,
                "type":"user"                
               };
               console.log(msg);
                this.$ajax({
                    method: 'post',
                    url: this.base_url+'user/register',
                    data: msg
                }).
                then(res => {
                    console.log(res)
                    let code=res.data.code;
                this.user_name='';
                    this.password='';
                    this.email='';           
                    if(code==2){
                        this.getUsers();
                        this.$message.success("新用户添加成功！");
                        
                    }else if(code==1){
                        this.$message.error("添加失败，该用户名已被占用！");
                    }else{
                        this.$message.error("未知错误！");
                    }
                    
                });                 
             }

              
              
          },
          closeUpdate(){
           this.$message({
                type: 'info',
                message: '取消修改'
            });  
              this.is_show_update_pwd=false;
          },
          confirmUpdate(){
              let row=this.row;
              this.is_show_update_pwd=false;
              if(this.old_pwd!=row.password){
                this.$message({
                        type: 'error',
                        message: '原密码验证错误'
                    });  
                  return;
              }
            this.$ajax({
                method: 'post',
                url: this.base_url+'user/update_password',
                data: {
                "user_name":row.userName,
                "password":this.new_pwd
                }
            }).
            then(res => {
                console.log(res)
                this.$message({
                    type: 'success',
                    message: '密码修改成功！'
                });
                this.getUsers();
                
            });  
          },
          update(row){
              this.is_show_update_pwd=true;
              this.row=row;
              console.log(row)
            // this.$prompt('请输入新密码', '提示', {
            // confirmButtonText: '确定',
            // cancelButtonText: '取消',
            // }).then(({ value }) => {
            // //这里连后端
            
            // this.$ajax({
            //     method: 'post',
            //     url: 'user/update_password',
            //     data: {
            //     "user_name":row.userName,
            //     "password":value
            //     }
            // }).
            // then(res => {
            //     console.log(res)
            //     this.$message({
            //         type: 'success',
            //         message: '密码修改成功！'
            //     });
            //     this.getUsers();
                
            // });             


            // }).catch(() => {
            // this.$message({
            //     type: 'info',
            //     message: '取消输入'
            // });       
            // });
          },
          remove(row){
            this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                //在这里调用删除接口
                console.log(row.userName);
                this.$ajax({
                    method: 'post',
                    url: this.base_url+'user/remove_user',
                    data: {
                    "user_name":row.userName,

                    }
                }).
                then(res => {
                    console.log(res)
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
                     
          },         
          getUsers(page){
            this.$ajax({
                method: 'post',
                url: this.base_url+'user/show_users',
                data: {
                "page":page,
                "page_size":"4"
                }
            }).
            then(res => {
                let users=res.data.data;
                for(let i=0; i<users.length; i++){
                    let tmp=users[i];
                    try {
                        tmp.permissions=tmp.userPermissionList[0].permissionList
                    } catch (error) {
                        tmp.permissions=[];
                    }
                        
                }
                this.users=users;
                
            });               
          },
          getPermissions(){
            this.$ajax({
                method: 'post',
                url: this.base_url+'permission/show_permissions',
                data: {
                "page":1,
                "page_size":5
                }
            }).
            then(res => {
                let permissions=res.data.data;
                // console.log(permissions)
                for(let i=0; i<permissions.length; i++){
                    permissions[i].checked=false;
                }
                this.permissions_list=permissions;
                this.new_permissions_list=permissions;
                this.getUsers(1);
            });               
          },
          edit(row){
              this.tmp_pre_permissions_list=[];
              this.cur_user_id=row.userId;
              let user_permissions=row.permissions;
              for(let i=0; i<user_permissions.length; i++){
                  let p=user_permissions[i].permissionName;
                  
                  for(let j=0; j< this.permissions_list.length; j++){
                      if(p==this.permissions_list[j].permissionName){
                          
                          this.permissions_list[j].checked=true;
                      }
                  }
              }
             this.tmp_pre_permissions_list=this.permissions_list;
              this.show_edit_permission=true;
          },          
          closeEdit(){
              this.show_edit_permission=false;
              this.getPermissions();
            // for(let j=0; j< this.permissions_list.length; j++){
                    
            //     this.permissions_list[j].checked=false;
            // }              
          },
          confirmEdit(items){
              console.log(22)
              console.log(items);
              console.log(this.tmp_pre_permissions_list);
            //   for(let i=0; i<items.length; i++){
            //       if(items[i].checked==true){

            //       }
            //   }
              //在这里调用改变权限的接口
              showLoading()
                // for(let j=0; j< this.permissions_list.length; j++){
                        
                //     this.permissions_list[j].checked=false;
                // }               
              setTimeout(()=>{hideLoading()},1000);
          },
          addPermission(){
              this.show_add_permission=true;
          },          
          closeAdd(){
              this.show_add_permission=false;
          },          
          confirmAdd(permission_name,url){
              //在这里调用添加新权限的接口
              console.log("权限名："+permission_name+";url："+url);
                this.$ajax({
                    method: 'post',
                    url: this.base_url+'/add_permission',
                    data: {
                    "permission_name":permission_name,
                    "url":url
                    }
                }).
                then(res => {
                    console.log(11)

                    
                }); 
              this.show_add_permission=false;
          },


        //   open(){
        //      this.$confirm('是否开启?', '提示', {
        //         confirmButtonText: '确定',
        //         cancelButtonText: '取消',
        //         type: 'warning'
        //     }).then(() => {
        //         this.$message({
        //             type: 'success',
        //             message: '开启成功!'
        //         });
        //     }).catch(() => {
        //         this.$message({
        //             type: 'info',
        //             message: '已取消关闭'
        //         });          
        //     }); 
        //   },
        //   close(){
        //      this.$confirm('是否关闭?', '提示', {
        //         confirmButtonText: '确定',
        //         cancelButtonText: '取消',
        //         type: 'warning'
        //     }).then(() => {
        //         this.$message({
        //             type: 'success',
        //             message: '关闭成功!'
        //         });
        //     }).catch(() => {
        //         this.$message({
        //             type: 'info',
        //             message: '已取消关闭'
        //         });          
        //     });             
        //   }
      },

  }
</script>


<style scoped>
    .add-permission{
        margin: 2% 0 2% 85% ;
    }
    .add-user{
         margin: 2% 0 2% 0% ;
    }
    .el-table{
        margin-top: 2%;
    }
    .main_menu{
        display: flex;
    }
    .main_menu .search{
        margin-left: 1%;
    }
    .main_menu .page{
        margin-left: 55%;
    }

</style>
