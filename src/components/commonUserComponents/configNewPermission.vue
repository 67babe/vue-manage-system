
<template>
  <div class="modal-backdrop">
     <div class="modal" >
        <div class="modal-header">
          <h3>配置新权限</h3>
        </div>
        <div class="modal-body">
            <div>
                权限名：<el-input v-model="permission_name" placeholder="请输入内容"></el-input>
            </div>
            <div>
                权限同步接口：<el-input v-model="url" placeholder="请输入内容"></el-input>
            </div>
        </div>
        <div class="modal-footer">
            <button type="button" class="btn-close" @click="closeSelf">关闭</button>
            <button type="button" class="btn-confirm" @click="confirm">确认</button>
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
export default {
  name: 'SelectPermissionModel',
  data() {
    return {
        permission_name:"",
        url:""
    }
  }, 
  methods: {    
    closeSelf() {
        this.$emit("closeme");      
    },
    confirm(){
        this.$emit("confirm",this.permission_name,this.url);
    }
  }
}
</script>