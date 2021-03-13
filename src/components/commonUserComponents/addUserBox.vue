<template>
  <div>
      <h3>用户名:</h3>
      <el-input class="user_name" v-model="user_name"></el-input>
      <h3>密码:</h3>
      <el-input show-password v-model="password"></el-input>
  </div>
</template>

<script>
  export default{
      data(){
          return {
              user_name:"",
              password:""
          }
      },
      mounted:function(){

      },
      methods:{

      },

  }
</script>


<style lang="scss">
    .user_name{
        margin-bottom: 5%;
    }

</style>
