<template>
<div class="order">
  <table class="table table-bordered cart-table">
    <tr>
      <th>Date create</th>
      <th>Status</th>
      <th>Client discount</th>
      <th>Payment</th>
      <th>Total sum</th>
    </tr>
<tbody  v-for="order in orders" :key="order.id" >
    <tr  @click="showOrder(order.id)" data-toggle="collapse" data-parent="#accordion" :href="'#'+order.id" class="collapsed" aria-expanded="false" :aria-controls="order.id">
        <td>{{order.date_order_create}}</td>
        <td>{{order.status}}</td>
        <td>{{order.discount_client}}</td>
        <td>{{order.payment}}</td>
        <td>{{order.total_price}}</td>
    </tr>
    <tr :id="order.id" class="collapse"   role="tabpanel" :aria-labelledby="order.id"> 
      <td colspan="6">
        <table class="table table-bordered cart-table">
              <tr>
                <th>Book name</th>
                <th>Count</th>
                <th>Price</th>
                <th>Discount</th>
              </tr>
              <tr v-for="record in orderItem" :key="record.book_id">
                <td>{{record.name}}</td>
                <td>{{record.count}}</td>
                <td>{{record.book_price}}</td>
                <td>{{record.discount_book}}</td>
              </tr>
            </table>
      </td>
    </tr>
</tbody>
  </table>  

 
</div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'order',
  data () {
    return {
    orders: [],
    isOrd: "",
    showInfoOrder: false,
    orderItem: []
    }
  },
  created(){
   
     var self = this
         if(localStorage['hash_log'] && localStorage['user']){
            //axios.get('http://localhost:88/BOOK_SHOP/client/api/auth/'+localStorage['hash_log']+"/"+localStorage['user'], this.config)
            axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/auth/'+localStorage['hash_log']+"/"+localStorage['user'], this.config)
              .then(function (response) {
                var client_id = parseInt(response.data.data)
                if(client_id>0){ 

                    //axios.get('http://localhost:88/BOOK_SHOP/client/api/order/'+ client_id, self.config)
                    axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/order/'+ client_id, self.config)
                          .then(function (response) {
                            self.orders = response.data.data 
                            //console.log(response.data.data)
                            self.orders.forEach(function(element) {
                               //axios.get('http://localhost:88/BOOK_SHOP/client/api/fullinfoorder/'+ element.id, self.config)
                               axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/fullinfoorder/'+  element.id, self.config)
                              .then(function (response) {
                                  element.info = response.data.data
                                  //console.log(response.data.data)

                               })
                              .catch(function (error) {
                                //console.log(error)
                              })
                            }, this);
                            console.log(self.orders)
                    })
                    .catch(function (error) {
                      //console.log(error)
                    })
                }    
              }).catch(function (error) {
                // console.log(error);
                self.isOrd = "for ordering you need to register"
                setTimeout(function () {
                      self.isOrd = ""   
                },1500); 
                return;   
              })
          } else{
            this.isOrd = "for this action you need to register"
              setTimeout(function () {
                this.isOrd= ""
              },1500);
            this.$emit('setlogout')  
          }
  },
  methods:{
    showOrder(order_id){
  // if(!this.showInfoOrder) 
     // {
        var self = this
        //axios.get('http://localhost:88/BOOK_SHOP/client/api/fullinfoorder/'+ order_id, this.config)
        axios.get('http://192.168.0.15/~user15/BOOK_SHOP/client/api/fullinfoorder/'+  order_id, this.config)
            .then(function (response) {
              self.orderItem = response.data.data
              //console.log(response.data.data)

              })
              .catch(function (error) {
                //console.log(error)
              })
       // this.showInfoOrder = true
     // }else{
       // this.showInfoOrder = false
       // this.orderItem = {}
     // }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.order{
  margin: 50px auto;
  min-height: 500px;
}
table {
			font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
      margin: 0px auto;
			font-size: 14px;
			background: white;
      color: black;
			max-width: 70%;
			width: 70%;
			border-collapse: collapse;
			text-align: left;
			/*background: url("../assets/blurry.jpg") no-repeat;*/
			/*background-position: 100% 55px;*/
		}
    th {
		  font-weight: normal;
		  color: #339;
		  padding: 10px 12px;
		}
		td {
		 background-image: url("../assets/back.png");
		 filter:alpha(opacity=60);
		 opacity:0.6;
		  color: #669;
		  border-top: 1px solid white;
		  padding: 10px 12px;
		}


</style>
