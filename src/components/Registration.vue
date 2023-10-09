<template>
<div v-if="!success" class="container">
  <div v-if="loading" class="lds-ring">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
  </div>
  <h1 class="header__title-h1">Регистрация</h1>
  <form @submit="signup" method="post">
    <div class="container__checkbox-public">
      <label class="container__checkbox">
        <input type="checkbox" v-model="user.public_account" :checked="user.public_account">
        <span class="container__checkbox-switch"></span>
        <span class="title-span">Публичный профиль</span>
      </label>
      <div class="text_checkbox">Включает профиль для просмотра другими пользователями по ссылке</div>
    </div>
    <h2 class="title-h3">Данные</h2>
    <div class="container__infa-user">
      <div class="container__user-left">
        <div class="container__content-user">
          <input class="input" v-model="user.username" type="text" name="username" placeholder="Имя" />
          <div v-show="errors.username" class="error">{{ errors.username }}</div>
        </div>
        <div class="container__content-user">
          <input class="input" v-model="user.email" type="email" name="email" placeholder="Еmail" />
          <div v-show="errors.email" class="error">{{ errors.email }}</div>
        </div>
        <div class="container__content-user">
          <input class="input" v-model="user.password" type="password" name="password" placeholder="Пароль" />
          <div v-show="errors.password" class="error">{{errors.password}}</div>
        </div>
      </div>
      <div class="container__user-right">
        <select v-model="user.role" class="input select" name="role">
          <option value="" disabled selected hidden>Должность</option>
          <option :key="role.value" :value="role.value" v-for="role in roles">{{ role.name }}</option>
        </select>
        <div class="container__content-user">
          <input class="input" v-model="user.password_repeat" type="password" name="password_repeat" placeholder="Повторите пароль" />
          <div v-show="errors.password_repeat" class="error">{{errors.password_repeat}}</div>
        </div>
      </div>
    </div>
    <div class="container__personal-information">
      <label class="personal__information">
        <input id="cb2" type="checkbox" v-model="confidentiality" :checked="confidentiality">
        <div class="personal__information-text">Нажимая на кнопку “Регистрация”, я подтверждаю свое согласение с политикой конфиденциальности и обработки персональных данных</div>
        <div v-show="confidentialityError" class="personal__information_text-error">Согласитесь с политикой конфиденциальности и обработки персональных данных</div>
      </label>
    </div>
    <input class="button" type="submit" value="Регистрация">
  </form>
</div>
<div v-else-if="success">
  <h1>Регистрация успешно завершена</h1>
</div>
</template>

<script>
import axios from "axios";
import {
  roles
} from "../data/data"
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Registration',
  props: {
    defaultValues: {
      username: String,
      public_account: Number,
      role: String,
      email: String,
    },
  },
  data() {
    return {
      roles,
      success: false,
      confidentiality: true,
      confidentialityError: false,
      loading: false,
      user: {
        public_account: 0,
        username: null,
        role: null,
        email: null,
        password: null,
        password_repeat: null,
      },
      errors: {
        username: null,
        email: null,
        password: null,
        password_repeat: null,
      }
    }
  },
  methods: {
    signup(event) {
      event.preventDefault();
      this.loading = true;
      if (this.confidentiality) {
        axios.post('https://lmstestapi.reezonly.com/v1/user/signup', {
            public: this.user.public_account ? 1 : 0,
            username: this.user.username,
            role: this.user.role,
            email: this.user.email,
            password: this.user.password,
            password_repeat: this.user.password_repeat,
          })
          .then(response => {
            const data = response.data
            if (!data.success) {
              this.errors = {
                username: data.errors.username ? data.errors.username[0] : null,
                email: data.errors.email ? data.errors.email[0] : null,
                password: data.errors.password ? data.errors.password[0] : null,
                password_repeat: data.errors.password_repeat ? data.errors.password_repeat[0] : null,
              }
            } else if (data.success) {
              this.success = true
            }
          })
          .catch(error => {
            console.log('error', error);
          })
          .finally(() => {
            this.loading = false;

          })
      } else {
        this.confidentialityError = true;
      }
    }
  },
  mounted() {
    this.user = {
      ...this.user,
      ...this.defaultValues
    }
  },
  watch: {
    confidentiality(val) {
      if (val) this.confidentialityError = false
    },
    user: {
      handler(data) {
        if (data.email) this.errors.email = null
        if (data.username) this.errors.username = null
        if (data.password) this.errors.password = null
        if (data.password_repeat) this.errors.password_repeat = null
      },
      deep: true
    }
  },
}
</script>

<style scoped>
.container {
  background-color: #fff;
  padding: 40px 20px 40px 20px;
  border-radius: 15px;
  width: 958px;
  margin: 0 auto;
}

.container__checkbox-public {
  margin-bottom: 25px;
  padding-bottom: 20px;
  border-bottom: 1px solid rgb(233, 235, 240);
  text-align: left;
  line-height: 40px;
}

.header__title-h1 {
  text-align: left;
  padding-bottom: 25px;
  margin-bottom: 25px;
  border-bottom: 1px solid rgb(233, 235, 240);
}

.personal__information_text-error {
  position: absolute;
  top: 75px;
  color: red;
}

.error {
  position: absolute;
  top: 50px;
  color: red;
  font-size: 11px;
}

.container__content-user {
  position: relative;
}

.title-h3 {
  text-align: left;
  font-weight: 500;
  font-size: 16px;
}

.text_checkbox {
  font-size: 14px;
}

.title-span {
  margin-left: 15px;
  font-weight: 500;
  font-size: 16px;
  cursor: pointer;
}

.container__user-left {
  display: flex;
  align-items: flex-start;
  flex-wrap: wrap;
  align-content: space-between;
}

.container__user-right {
  display: flex;
  align-items: flex-start;
  flex-wrap: wrap;
  align-content: space-between;
}

.input {
  width: 450px;
  color: #9292A0;
  border-radius: 11px;
  padding: 14px 11px 14px 11px;
  border: 1px solid rgb(233, 235, 240);
  margin-bottom: 25px;
}

.input:last-child {
  margin-bottom: 0
}

.input::placeholder {
  color: #9292A0;
}

.select {
  appearance: none;
  background: url(../assets/Carret.svg) no-repeat right;
  background-position-x: calc(100% - 16px);
  width: 474px;
  height: 45px;
}

select {
  color: #9292A0;
}

.button {
  width: 234px;
  height: 39px;
  padding: 10px 12px 10px 12px;
  color: #07A86E;
  border: 1px solid #07A86E;
  border-radius: 9px;
  background: transparent;
  cursor: pointer;
}

.button:active {
  background-color: antiquewhite;
  color: #000;
  border: 1px solid #000;

}

.container__infa-user {
  display: flex;
  padding-bottom: 25px;
  border-bottom: 1px solid rgb(233, 235, 240);
}

#cb2 {
  accent-color: rgb(254, 204, 85);
  cursor: pointer;
}

.container__personal-information {
  position: relative;
  max-width: 800px;
}

.personal__information-text {
  padding: 30px 20px 20px 10px;
}

.personal__information {
  display: flex;
  text-align: left;
  margin-bottom: 20px;
}

.container__checkbox {
  display: inline-block;
  height: 28px;
  line-height: 28px;
  margin-right: 10px;
  position: relative;
  vertical-align: middle;
  font-size: 14px;
  user-select: none;
}

.container__checkbox .container__checkbox-switch {
  position: relative;
  display: inline-block;
  box-sizing: border-box;
  width: 56px;
  height: 28px;
  border: 1px solid rgba(0, 0, 0, .1);
  border-radius: 25%/50%;
  vertical-align: top;
  background: #eee;
  transition: .2s;
  cursor: pointer;
}

.container__checkbox .container__checkbox-switch:before {
  content: '';
  position: absolute;
  top: 1px;
  left: 1px;
  display: inline-block;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: white;
  box-shadow: 0 3px 5px rgba(0, 0, 0, .3);
  transition: .15s;
}

.container__checkbox input[type=checkbox] {
  display: block;
  width: 0;
  height: 0;
  position: absolute;
  z-index: -1;
  opacity: 0;
}

.container__checkbox input[type=checkbox]:not(:disabled):active+.container__checkbox-switch:before {
  box-shadow: inset 0 0 2px rgba(0, 0, 0, .3);
}

.container__checkbox input[type=checkbox]:checked+.container__checkbox-switch {
  background: #7A97FF;
}

.container__checkbox input[type=checkbox]:checked+.container__checkbox-switch:before {
  transform: translateX(28px);
}

/* Hover */
.container__checkbox input[type="checkbox"]:not(:disabled)+.checkbox-switch {
  cursor: pointer;
  border-color: rgba(0, 0, 0, .3);
}

.container__checkbox input[type=checkbox]:disabled+.container__checkbox-switch:before {
  background: #eee;
}

.lds-ring {
  display: inline-block;
  position: fixed;
  top: 75%;
  width: 80px;
  height: 80px;
  left: 47%;
  right: 47%;
}

.lds-ring div {
  box-sizing: border-box;
  display: block;
  position: absolute;
  width: 64px;
  height: 64px;
  margin: 8px;
  border: 2px solid #07A86E;
  border-radius: 50%;
  animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
  border-color: #07A86E transparent transparent transparent;
}

.lds-ring div:nth-child(1) {
  animation-delay: -0.45s;
}

.lds-ring div:nth-child(2) {
  animation-delay: -0.3s;
}

.lds-ring div:nth-child(3) {
  animation-delay: -0.15s;
}

@keyframes lds-ring {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>
