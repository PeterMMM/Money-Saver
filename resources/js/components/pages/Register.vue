<template>
    <div class="container" style="min-width:200px;max-width: 400px;">
        <h4 v-if="success">Welcome {{ userauth.name }}! Register successfully.</h4>
        <h4 v-if="!success">Register to access Money Saver</h4>
        <form autocomplete="off" @submit="registerForm" v-if="!success" method="post">
          <div class="form-group" v-bind:class="{ 'has-error': error && errors.name }">
            <label for="name">Name</label>
            <input type="text" class="form-control" id="name" aria-describedby="name_help" placeholder="Enter name" v-model="name" required>
            <span class="text-danger" v-if="error && errors.name">{{ errors.name }}</span>
          </div>
          <div class="form-group" v-bind:class="{ 'has-error': error && errors.email}">
            <label for="email">Email address</label>
            <input type="email" class="form-control" id="email" aria-describedby="email_help" placeholder="user@example.com" v-model="email" required>
            <span class="text-danger" v-if="error && errors.email" >{{ errors.email }}</span>
          </div>
          <div class="form-group" v-bind:class="{ 'has-error': error && errors.password}">
            <label for="password1">New Password</label>
            <input type="password" class="form-control" id="password" placeholder="Enter New Password" v-model="password" required>
            <span class="text-danger" v-if="error && errors.password">{{ errors.password }}</span>
          </div>
          <div class="form-group" v-bind:class="{ 'has-error': error && errors.confirm_password}">
            <label for="password1">Again New Password</label>
            <input type="password" class="form-control" id="confirm_password" placeholder="Enter Again New Password" v-model="confirm_password" required>
            <span class="text-danger" v-if="error && errors.confirm_password">{{ errors.confirm_password }}</span>
          </div>
          <div class="form-check">
            <input type="checkbox" class="form-check-input" id="agreed" required>
            <label class="form-check-label" for="exampleCheck1">Agreed <a href="">Terms and Conditions</a></label>
          </div>
          <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </div>
</template>

<script>
    export default {
        mounted() {
            console.log('Register Component mounted.')
            this.$parent.checkLoginForRegisterationPage();
        },
        data(){
          return {
            name: '',
            email: '',
            password: '',
            confirm_password: '',
            error: false,
            errors: {},
            success: false,
            userauth: {},
          }
        },
        methods: {
          registerForm(e){
            e.preventDefault();
            let v = this;
            this.axios.post('http://127.0.0.1:8000/api/auth/register', {
              name : this.name,
              email : this.email,
              password : this.password,
              confirm_password : this.confirm_password
            })
            .then(function (response) {
              console.log("Status - "+ response.status);
              console.log("data - "+ JSON.stringify(response.data));
              if (response.data.status == 'success') {
                v.success = true;
                v.error = false;
                v.userauth = response.data.userauth;
                localStorage.setItem('token', response.data.userauth.token)
                localStorage.setItem('userauth', response.data.userauth)
                localStorage.setItem('user_id', response.data.userauth.id)
                localStorage.setItem('user_name', response.data.userauth.name)
                localStorage.setItem('user_token', response.data.userauth.token)
                v.$forceUpdate();
                v.$router.push({ name: 'home'});
              }else {
                v.error = true;
                v.errors = response.data.errors;
                console.log("Errors -"+ JSON.stringify(response.data.errors));
              }
              console.log("userauth - "+ JSON.stringify(response.data));
            })
            .catch(function (error) {
              v.errors = error;
              v.error = true;
            });
          },
          register(){
            var app = this
            this.$auth.register({
              data:{
                name: app.name,
                email: app.email,
                password: app.password
              },
              success: function(){
                app.success = true
              },
              error: function(rep){
                app.error = true;
                app.errors = resp.response.data.errors;
              },
              redirect: null
            });
          }
        }
    }
</script>
