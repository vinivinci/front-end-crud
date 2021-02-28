<template>
    <div class="divBackground">
        <!-- Material form login -->
        <form v-on:submit.prevent="loginSubmit" class="formStyle">
            <div
                style="border:1px solid #dadada;
                      padding:15px;
                      background:#AfaDFb;
                      color:white;
                      border-radius:20px"
            >
                <p
                    style="margin-bottom:0px !important"
                    class="h4 text-center mb-4"
                >
                    Sign in
                </p>
            </div>

            <div style="margin-top:60px" class="grey-text">
                <mdb-input
                    v-model="email"
                    label="Your email"
                    icon="envelope"
                    type="email"
                />
                <mdb-input
                    v-model="password"
                    label="Your password"
                    icon="lock"
                    type="password"
                />
            </div>
            <div class="custom-control custom-checkbox">
                <input
                    type="checkbox"
                    class="custom-control-input"
                    id="defaultChecked2"
                    v-model="remember"
                />
                <label class="custom-control-label" for="defaultChecked2"
                    >Remember me
                  </label
                >
            </div>
            <div class="text-center">
                <mdb-btn
                    type="submit"
                    :class="{ disabled: email == '' || password == '' }"
                    >Login</mdb-btn
                >
            </div>
            <div class="row justify-content-center">
              <span
                style="color:red;font-size:12px;"
               :v-if="authError"
              >
                {{authError}}
              </span>
            </div>

        </form>
        <!-- Material form login -->
    </div>
</template>
<script>
import { mdbInput, mdbBtn } from 'mdbvue';
import axios from 'axios';

export default {
  name: 'login',
  data() {
    return {
      email: '',
      password: '',
      remember: false,
      authError: '',
    };
  },
  components: {
    mdbInput,
    mdbBtn,
  },
  methods: {
    loginSubmit() {
      axios
        .post('http://localhost:8080/auth/authenticate', {
          email: this.email,
          password: this.password,
        })
        .then((response) => {
          if (response.data) {
            console.log(response.data.token);
            if (this.remember) {
              localStorage.setItem('token', response.data.token);
              localStorage.setItem('user', JSON.stringify(response.data.user));
            } else {
              sessionStorage.setItem('token', response.data.token);
              sessionStorage.setItem('user', JSON.stringify(response.data.user));
            }
            window.location.href = 'home';
          }
        })
        .catch((err) => {
          this.authError = err.request.response;
        });
    },
  },
};
</script>
<style>
.divBackground {
    display: flex;
    justify-content: center;
    height: 100vh;
    align-items: center;
    background-image: linear-gradient(180deg, #afadfb, #30cfc0);
}
.formStyle {
    width: 30%;
    border: 2px solid #dadada;
    border-radius: 10px;
    padding: 30px 40px;
    background: white;
    height: 40%;
}
</style>
