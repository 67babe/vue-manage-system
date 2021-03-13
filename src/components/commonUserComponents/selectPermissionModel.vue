
<template>
  <div class="modal-backdrop">
     <div class="modal" >
        <div class="modal-header">
          <h3>编辑权限</h3>
        </div>
        <div class="modal-body">
            <el-table :data="items" stripe :header-cell-style="{background:'#eef1f6',color:'#606266'}">
                <el-table-column prop="permissionName" label="权限名">
                </el-table-column>

                <el-table-column label="编辑权限">
                    <template slot-scope="scope">
                        <el-button  type="text" size="small" v-if="!scope.row.checked" @click="open(scope.row)">开启</el-button>
                        <el-button  type="text" size="small" v-if="scope.row.checked" @click="close(scope.row)">关闭</el-button>
                    </template>
                </el-table-column>
                
            </el-table>
            <!-- <el-checkbox v-model="item.checked" v-for="(item,i) in items" :key="i">{{item.permissionName}}</el-checkbox> -->
        </div>
        <div class="modal-footer">
            <button type="button" class="btn-close" @click="closeSelf">关闭</button>
            <!-- <button type="button" class="btn-confirm" @click="confirm">确认</button> -->
        </div>
    </div>
 
  </div>
</template>
 
<style>
.modal-backdrop {
    position: fixed; 
    top: 0; 
    right: 0; 
    bottom: 0; 
    left: 0;
    z-index: 999;
    background-color: rgba(0,0,0,.3); 
    display: flex; 
    justify-content: center; 
    align-items: center; 
}
.modal { 
    background-color: #fff; 
    box-shadow: 2px 2px 20px 1px; 
    overflow-x:auto; 
    display: flex; 
    flex-direction: column;
    border-radius: 16px;
    width: 700px;
} 
.modal-header { 
    border-bottom: 1px solid #eee; 
    color: #313131; 
    justify-content: space-between;
    padding: 15px; 
    display: flex; 
} 
.modal-footer { 
    border-top: 1px solid #eee; 
    justify-content: flex-end;
    padding: 15px; 
    display: flex; 
} 
.modal-body { 
    position: relative; 
    padding: 20px 10px; 
}
.btn-close, .btn-confirm {    
    border-radius: 8px;
    margin-left:16px;
    width:56px;
    height: 36px;
    border:none;
    cursor: pointer;
}
.btn-close {
    color: #313131;
    background-color:transparent;
}
.btn-confirm {
    color: #fff; 
    background-color: #2d8cf0;
}
 
 
</style>
 
<script>
import {showLoading, hideLoading } from './loading.js';
export default {
  name: 'SelectPermissionModel',
  props: {  
      items:{
          type:[Object, Array, Number, String],
          default:null
      },
      user_id:{
           type:[Object, Array, Number, String],
           default:null         
      }
  },
  mounted(){
      console.log(this.items)
  },
  data() {
    return {
    
    }
  }, 
  methods: {    
    closeSelf() {
        this.$emit("closeme");      
    },
    open(row){
        
        let permission_id=row.permissionId;
        this.updateItems(permission_id,true);
        showLoading()
        this.$ajax({
            method: 'post',
            url: 'userPermission/add_user_permission',
            data: {
                "user_id":this.user_id,
                "permission_id":permission_id
            }
        }).
        then(res => {
            console.log(res);
             
             hideLoading();
              this.$message.success("权限开启成功！");
        });          
       
    },
    close(row){
        
        let permission_id=row.permissionId;
        this.updateItems(permission_id,false);
        showLoading()
        this.$ajax({
            method: 'post',
            url: 'userPermission/remove_user_permission',
            data: {
                "user_id":this.user_id,
                "permission_id":permission_id
            }
        }).
        then(res => {
             console.log(res);
             
             hideLoading();
             if(res.data.code!=0){
                 this.$message.success("权限关闭成功！");
             }else{
                 this.$message.error("权限关闭失败！");
             }
             
        });   
    },
    updateItems(id,bool){
        for(let i=0; i<this.items.length; i++){
            if(this.items[i].permissionId==id){
                this.items[i].checked=bool;
                
            }
        }
    }
    // confirm(){
    //     console.log(this.user_id);
    //     console.log(this.items)
    //     //this.$emit("confirm",this.items);
    // }
  }
}
</script>