<template>
  <div class="login row vertical" data-width="20%">
    <p>使用者帳號</p>
    <el-input v-model="loginForm.account" type="text" placeholder="請輸入帳號"  data-space-bottom="0.5rem"/>
    <p>密碼</p>
    <el-input v-model="loginForm.password" type="password" placeholder="請輸入密碼" data-space-bottom="0.5rem"/>
    <p>驗證碼</p>
    <div class="row horizontal">
      <el-input v-model="loginForm.otp" type="text"  data-space-bottom="1rem"/>
      <div class="otp">{{ otp.OTP }}</div>
    </div>
    <div class="row horizontal center">
      <el-button type="primary" @click="hanleLogin()" >登入</el-button>
      <el-button @click="removeLogin()">清除</el-button>
    </div>
  </div>
</template>

<script>
import { ref, reactive, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { getOtp, login } from '@/service/api'
import { useStore } from 'vuex'


export default {
  name:'Login',
  setup(){
    const store = useStore()
    const router = useRouter()
    let otp = reactive({ OTP:'' })
    const loginForm = reactive({
      account:'',
      password:'',
      otp:''
    })
    const callOtp = onMounted( async () => {
      const data =''
      const res = await getOtp(data)
      if (res.data.Code === 200) {
        otp.OTP = res.data.Data.OTP
      } else {
        alert('Get OTP ERROR!!')
      }
    })
    const hanleLogin = async() =>{
      if ( loginForm.account !=='' && loginForm.password !== '' && loginForm.otp !=='' ){
        const res = await login(JSON.stringify(loginForm)).then(
          res => {
            if(res.data.Code === 200){
              store.commit('userModules/SET_TOKEN', res.data.Data.Token)
              router.push({ name: 'Dashboard' })
            }else{}
          }
        ).catch(
          error =>{
            if(loginForm.otp !== otp.OTP){
              alert('驗證碼有誤請重新輸入')
              callOtp()
              Object.keys(loginForm).forEach( item => {
                loginForm [item] = ''
              })
            }else{
              alert('輸入資料有誤，請重新輸入')
              callOtp()
              Object.keys(loginForm).forEach( item => {
                loginForm [item] = ''
              })
            }
          }
        )
      }else if( loginForm.account =='' ){
        alert('帳號不能為空')
      }else if( loginForm.password =='' ){
        alert('密碼不能為空')
      }else if( loginForm.otp =='' ){
        alert('驗證碼不能為空')
      }
    }
    const removeLogin = () => {
      loginForm.account='',
      loginForm.password='',
      loginForm.otp=''
    }
    
    return{
      loginForm,
      hanleLogin,
      removeLogin,
      callOtp,
      otp
    }
  }
}
</script>