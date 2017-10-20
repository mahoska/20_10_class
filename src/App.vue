<template>
  <div id="app">
    <img class="bg" src="/static/logo1.jpg"/>
    <div class="main-container">
    <!--img src="./assets/logo.png"-->
  <nav class="navbar navbar-default navbar-fixed-top transp clearfix " role="navigation">
    <div class="container-fluid">
      <span class="span-logo">BOOK</span>
      <router-link  :to="'/'" class="book_name" >
        <img class="img-logo" src="./assets/logo.png"/> 
      </router-link>
      <span class="span-logo">STORE</span>
    </div>
  </nav>
      <table class="mynav-table">
      <tr>
        <td>
          <form class="navbar-form navbar-right" role="search" style="margin-right:2px">
            <div class="form-group">
              <input type="text" class="form-control" placeholder="Search" v-model.trim="search_string">
              <button class="btn btn-default" @click="search">Search</button>
            </div>
          </form>
        </td>
      </tr>
      <tr>
        <td>
          <!--a class="reg" v-if="is_not_login_check"  @click="openLoginForm">Login</a>
          <!--a class="reg" v-if="is_not_login_check" @click="openRegistrationForm">Registration</a>
          <!--log form-->
      
          <div class="navbar-form navbar-righ" v-if="is_not_login_check">
            <div class="bg-danger" id="err" style="width: 100%">{{err_log}}</div>
            <div  class="form-group">
              <input type="text" class="form-control" id="login"  placeholder="login" v-model.trim="login" >
            </div>
            <div  class="form-group">
              <input type="password" class="form-control" id="password" placeholder="password" v-model.trim="password" >
            </div>
            <button class="btn btn-auth" @click="flogin" >login</button>
              <router-link  :to="'/registration'"   class="btn btn-auth">
                  Registration
              </router-link>
          </div>         
        <!--end log form-->
        <!--registration form>
        <div class="auth-form"  v-if="is_registr">
          <div class="bg-danger" id="err" style="width: 100%">{{err_reg}}</div>
          <div  class="form-group">
            <label for="name">First name</label>
            <input type="text" class="form-control" id="name"  placeholder="name" v-model.trim="name"  @blur="check_name">
          </div>
          <div  class="form-group">
            <label for="sname">Last name</label>
            <input type="text" class="form-control" id="sname" placeholder="last name" v-model.trim="surname"  @blur="check_name">
          </div>
          <div  class="form-group">
            <label for="email">Email</label>
            <input type="email" class="form-control" id="email"  placeholder="email" v-model.trim="email" @blur="check_email">
          </div>
          <div  class="form-group">
            <label for="phone">Phone</label>
            <input type="text" class="form-control" id="phone" placeholder="phone" v-model.trim="phone" @blur="check_phone">
          </div>
          <div  class="form-group">
            <label for="login">Login</label>
            <input type="text" class="form-control" id="login"  placeholder="login" v-model.trim="rlogin">
          </div>
          <div  class="form-group">
            <label for="password">Password</label>
            <input type="password" class="form-control" id="password" placeholder="password" v-model.trim="rpassword" >
          </div>
          <button class="btn btn-auth" @click="registration_1">registration</button> 
          <button class="btn btn-auth" @click="cancel" type="cancel">cancel</button>
        </div>
        <end registration-->
        <!--is_login_check form-->
        <div  class="auth-form" v-if="is_login_check">
          <table class="is_check">
            <tr>
              <td><span class="log_span">You login as:</span><br> {{login}}</td>
              <td>
                <button class="btn btn-auth" @click="logout" >
                   <span class="glyphicon glyphicon-log-out" aria-hidden="true" ></span>
                </button>
                <router-link  :to="'/cart'" class="btn btn-auth" >
                  <span class="glyphicon glyphicon-shopping-cart" aria-hidden="true" >{{bag_book_count}}</span>
                </router-link>
                <router-link  :to="'/order'" class="btn btn-auth" >
                   <span class="glyphicon glyphicon-folder-open" aria-hidden="true" ></span>
                </router-link>
              </td>
            </tr>
          </table>
        </div>
        <!--is_login_check form-->
        </td>
      </tr>
     </table>

      
  <div class = "fon clearfix" v-if="is_login || is_registr"></div>
  <div class="content">
    <router-view :filterstring="search_param"  
    @setlogout="logout" 
    @clearSearch="clearSearchString"  
    @changeCountTotal="changeCart" 
    @clearCountTotal="clearTotal"
    @client_reg="registration_check" />
  </div>
</div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'app',
  data () { 
    return{
      is_login: true,
      //is_registr: false,
      is_login_check: false,
      is_not_login_check: true,
      login: "",
      password: "",

     /* name: "",
      surname: "",
      rlogin: "",
      rpassword: "",
      email: "",
      phone: "",
      err_reg: "",*/
      err_log: "",
      search_string: "",
      search_param: "",

      bag_book_count: 0
    
    }
  },
  created(){
    var self = this
    if(localStorage['hash_log'] && localStorage['user']){
        //axios.get('http://localhost:88/BOOK_SHOP/client/api/auth/'+localStorage['hash_log']+"/"+localStorage['user'], this.config)
        axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/auth/'+localStorage['hash_log']+"/"+localStorage['user'], this.config)
            .then(function (response) {
              //console.log(response.data.data)
              var client_id = parseInt(response.data.data) 
              if(client_id>0){
                self.is_login_check = true
                self.is_not_login_check = false
                //self.is_login = false
                //self.is_registr = false
                self.login = localStorage['user']

                //axios.get('http://localhost:88/BOOK_SHOP/client/api/cart/'+ client_id, self.config)
                axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/cart/'+ client_id, self.config)
                .then(function (response) {
                  console.log(response.data)
                  if(response.data.data == null) 
                    self.bag_book_count = 0
                  else self.bag_book_count = parseInt(response.data.data)
              })
              .catch(function (error) {
                console.log(error);
              });
            } 
            })
            .catch(function (error) {
              console.log(error);
        });
    }
  },
  methods:{
    registration_check(client_login){
        this.is_login_check = true
        this.is_not_login_check = false
        this.login = client_login
    },


    //!!!!!---if user active---
    flogin(){
      if(this.login=="" || this.password=="" ){
				this.err_log = "not all fields are filled";
			}else{
         var self = this
        this.err_log = ""
        axios.put('http://192.168.0.15/~user15/BOOK_SHOP/client/api/auth/', 
        //axios.put('http://localhost:88/BOOK_SHOP/client/api/auth/', 
        {
          login: this.login,
          password: this.password
        }, this.config)
        .then(function (response) {
           if(response.data.err && response.data.err ==true){
              self.err_log = response.data.data
              setTimeout(function () {
                self.err_log = ""
              },2500);
            }else{
              self.is_login_check = true
              self.is_not_login_check = false
              //self.is_login = false
              //self.is_registr = false
              console.log(response.data['data'].status);
              console.log(response.data['data'].client_id);
              localStorage['hash_log'] = response.data['data'].status
              localStorage['user'] = self.login
              var client_id = response.data['data'].client_id;
              //axios.get('http://localhost:88/BOOK_SHOP/client/api/cart/'+ client_id, self.config)
              axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/cart/'+ client_id, self.config)
                .then(function (response) {
                  console.log(response.data)
                  if(response.data.data == null) 
                    self.bag_book_count = 0
                  else self.bag_book_count = parseInt(response.data.data)
              })
              .catch(function (error) {
                console.log(error);
              });
              
            }
        })
        .catch(function (error) {
            console.log(error);
        });
      }
    },

    logout(){
      localStorage['hash_log'] = ""
      localStorage['user'] = ""
      localStorage['cart'] = ""
      this.is_login_check = false
      this.is_not_login_check = true
      //this.is_login = true
      this.is_registr = false
      this.login = ""
      this.password = ""
      this.err_reg = ""
    },

    search(){
      this.search_param =  this.search_string
    },

    clearSearchString(){
      this.search_string = ""
      this.search_param = ""
    },

    changeCart(count, flag){
      if(flag)
        this.bag_book_count += parseInt(count)
      else
        this.bag_book_count -= parseInt(count)
    },

    clearTotal(){
      this.bag_book_count = 0
    }


   
  }
}
</script>

<style>
img.bg {
        /* Set rules to fill background */
        min-height: 100%;
        min-width: 1024px;
        /* Set up proportionate scaling */
        width: 100%;
        height: auto;
        /* Set up positioning */
        position: fixed;
        top: 0;
        left: 0;
}
@media screen and (max-width: 1024px) { /* Specific to this particular image */
        img.bg {
                left: 50%;
                margin-left: -512px;   /* 50% */
        }
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;
  color: #2c3e50;
  width: 1200px;
  margin: 10px auto;  
	-webkit-border-radius: 5px;
  border-radius: 5px;	

  background: -webkit-linear-gradient(#B4D2D8, #142427);
  background: -moz-linear-gradient(#B4D2D8, #142427);
  background: linear-gradient(#B4D2D8, #142427);
  padding-top: 10px;*/
}

.main-container{
  /*background: url('/static/logo1.jpg');
  background-size: 100%;
  background-repeat: no-repeat;*/
  /*background-position: 20px 10px 20px 10px;*/
 /* background-attachment: fixed;*/
}

.navbar {
  background-color:#2c3037;
  opacity: 0.7;
  z-index: 9;
}
 
.content  {
  padding-top: 100px;
} 

.clearfix:after{
	content: ".";
	display: block;
	height: 0;
	clear: both;
	visibility: hidden;
}

.btn-auth{
  background-color: #2C3E50;
  color: #D8EEFA;
}

/*--logo nav--*/
.img-logo{
  max-height: 90px;
  margin-top: -40px;
}

.span-logo{
  font-size: 60px;
  font-weight: bold;
  line-height: 100px;
  color: #2C3E50;
}

.mynav:first-child{
padding-left: 100px;
}
/*--end logo nav--*/

.log_span{
  font-weight: bold;
  color: #FFF;
  text-align:left;
}

.mynav-table{
position: absolute;
opacity: 1;
top: 0px;
right: 10px;
/*border: 1px solid red;*/
z-index: 999;
text-align: right;
}

/*.auth-form{
  color: #FFF;
}*/

.is_check td{
  line-height:17px;
  text-align:center;
  width: 135px;
  color: #FFF;
}

.is_check{
  margin-top:5px;
  margin-left: 17px;
}

/*
.auth-form label{
  font-size: 14px;
  font-weight: none;
}*/

/*
.fon{
  position:absolute;
  z-index: 5;
 width: 1200px;
  margin: -8px auto; 
  background-color: #1D2E31;
  opacity: 0.5;
  height: 177%; 
}

.mynav{
  height: 95px;
  background-color: #4A838C;
  margin: 10px;
  -webkit-border-radius: 5px;
  border-radius: 5px;	
  text-align:left;
}
*/

/*
a.reg{
  font-weight: bold;
  color: #2C3E50;
  margin-right: 60px;
  
}*/
/*
*/
/*
.nav-table tr{
  height: 40px;
  line-height: 40px;
  font-size: 16px;
  cursor: pointer;
border: 1px solid blue;
}
*/

/*
.auth-form{
position: relative;
text-align: right;
padding-right:10px;
z-index: 9998;
}*/
/*




#err{
font-weight:bold;
font-size:14px;
color:red;
line-height:16px;
padding: 0 5px;
text-align:center;
}

*/
</style>
