<template lang="pug">
  #login
    b-container
      b-row
        b-col(cols="12")
          b-form(@submit.prevent="onSubmit" @reset="onReset")
            b-form-group#input-group-1(
              label="帳號"
              label-for="input-1"
              :state="accountState"
              description="帳號長度為 4 ~ 20 個字"
              invalid-feedback="帳號格式不符"
            )
              b-form-input#input-1(
                v-model="account"
                type="text"
                required
                placeholder="請輸入帳號..."
                :state="accountState"
              )
            b-form-group#input-group-2(
              label="密碼"
              label-for="input-2"
              :state="passwordState"
              description="密碼長度為 4 ~ 20 個字"
              invalid-feedback="密碼格式不符"
            )
              b-form-input#input-2(
                v-model="password"
                type="password"
                required
                placeholder="請輸入密碼..."
                :state="passwordState"
              )
            div.text-center
              b-btn.mx-1(variant="success" type="submit") 登入
              b-btn.mx-1(variant="danger" type="reset") 重置
</template>

<script>
export default {
  name: 'Reg',
  data () {
    return {
      account: '',
      password: ''
    }
  },
  computed: {
    accountState () {
      if (this.account.length === 0) {
        return null
      } else if (this.account.length >= 4 && this.account.length <= 20) {
        return true
      } else {
        return false
      }
    },
    passwordState () {
      if (this.password.length === 0) {
        return null
      } else if (this.password.length >= 4 && this.password.length <= 20) {
        return true
      } else {
        return false
      }
    }
  },
  methods: {
    onSubmit () {
      // 如果帳號密碼驗證通過
      if (this.accountState && this.passwordState) {
        this.axios.post(process.env.VUE_APP_API + '/users/login', this.$data)
          .then(res => {
            if (res.data.success) {
              this.$store.commit('login', res.data.result)
              this.$swal({
                icon: 'success',
                title: '登入成功'
              }).then(() => {
                // 導入到相簿頁面
                this.$router.push('/album')
              })
            } else {
              this.$swal({
                icon: 'error',
                title: '發生錯誤',
                text: res.data.message
              })
            }
          })
          .catch(err => {
            this.$swal({
              icon: 'error',
              title: '發生錯誤',
              text: err.response.data.message
            })
          })
      }
    },
    onReset () {
      this.account = ''
      this.password = ''
    }
  }
}
</script>
