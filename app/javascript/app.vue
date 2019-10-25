<template>
  <div id="app">
    <div class="container">
      <br>
      <nav id="top-navigation" class="well well-sm flex flex-row align-center">
        <a href="#" @click.prevent="isShowOnCart = false"><strong>E-commerce Inc.</strong></a>
        <div class="text-right pull-right cart-info">
          <span class="stats">{{ cartTotalItem }} items in cart, totalling {{ cartTotalMoney | currency }}</span>
          <button @click="isShowOnCart = true" class="btn btn-primary">View Cart</button>
        </div>
      </nav>
      <div v-if="!isShowOnCart" id="products" class="row list-group">
        <div v-for="product in products" class="item col-xs-4">
          <div class="thumbnail">
            <img class="group list-group-image" src="http://placehold.it/400x250/000/fff">
            <div class="caption">
              <h4 class="group inner list-group-item-heading">{{ product.name }}</h4>
              <p class="group inner list-group-item-text">{{ product.description }}</p>
              <br>
              <div class="row flex flex-row align-center">
                <div class="col-xs-4">
                  <p class="lead">{{ product.price | currency }}</p>
                </div>
                <div class="col-xs-8 flex flex-row align-center justify-right">
                  <div v-bind:class="{ few: product.inStock < 10 && product.inStock > 0, none: product.inStock == 0 }" class="number-in-stock">
                    {{ product.inStock }} in stock
                  </div>
                  <button v-bind:disabled="product.inStock == 0" v-on:click="addItemToCart(product)" class="btn btn-success">Add to cart</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else="isShowOnCart">
        <h1>Cart</h1>
        <table v-if="cart.items.length > 0" class="table">
          <thead>
            <tr>
              <th>Product Name</th>
              <th>Quantities</th>
              <th>Price</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in cart.items"  class="success">
              <td>{{ item.product.name }}</td>
              <td>{{ item.quantity }}
                <button v-on:click="addMoreProduct(item)" class="btn btn-success">+</button>
                <button v-on:click="removeProduct(item)" class="btn btn-danger" >-</button>
              </td>
              <td>{{ item.product.price * item.quantity | currency }}</td>
            </tr>
            <tr class="info">
              <td>
                <input type="asdas"  name="">                
              </td>
              <td>Total</td>
              <td>{{ cartTotalMoney | currency }}</td>
            </tr>
            <tr class="danger">
              <td></td>
              <td>Tax(10%)</td>
              <td>{{ taxAmount | currency }}</td>
            </tr> 
            <tr class="info">
              <td></td>
              <td>Sub</td>
              <td>{{ cartTotalMoney + taxAmount | currency }}</td>
            </tr>
            <tr class="warning">
              <td></td>
              <td></td>
              <td>
                <button v-on:click="checkout" class="btn btn-warning" >Checkout</button>
              </td>
            </tr>
          </tbody>
        </table>
        <h3 v-else>Your cart was empty! Please add some products to checkout</h3>
      </div>
    </div>  
  </div>
</template>

<script>
  export default {
    data: function () {
      return {
        isShowOnCart: false,
        cart: {
          items: []
        },

        products: [
          {
            id: 1,
            name: 'MacBook Pro (15 inch)',
            description: 'This laptop has a super crisp Retina display. Yes, we know that it\'s overpriced...',
            price: 2999,
            inStock: 50
          },
          {
            id: 2,
            name: 'Samsung Galaxy Note 7',
            description: 'Unlike the overpriced MacBook Pro, we\'re selling this one a bit cheap, as we heard it might explode...',
            price: 299,
            inStock: 755
          },
          {
            id: 3,
            name: 'HP Officejet 5740 e-All-in-One-printer',
            description: 'This one might not last for so long, but hey, printers never work anyways, right?',
            price: 149,
            inStock: 5
          },
          {
            id: 4,
            name: 'iPhone 7 cover',
            description: 'Having problems keeping a hold of that phone, huh? Ever considered not dropping it in the first place?',
            price: 49,
            inStock: 42
          },
          {
            id: 5,
            name: 'iPad Pro (9.7 inch)',
            description: 'We heard it\'s supposed to be pretty good. At least that\'s what people say.',
            price: 599,
            inStock: 0
          },
          {
            id: 6,
            name: 'OnePlus 3 cover',
            description: 'Does your phone spend most of its time on the ground? This cheap piece of plastic is the solution!',
            price: 19,
            inStock: 81
          }
        ],
        code: [
          {
            id: 1,
            name: 'STUDENT-CODE10',
            percent: 10 
          },

          {
            id: 2,
            name: 'STUDENT-CODE20',
            percent: 20
          },

          {
            id: 3,
            name: 'STUDENT-CODE30',
            percent: 30 
          },

          {
            id: 4,
            name: 'STUDENT-CODE40',
            percent: 40 
          },

          {
            id: 5,
            name: 'STUDENT-CODE50',
            percent: 50 
          },
        ]
      }
    },
    methods: {
      addItemToCart: function (product) {
        var itemCart = this.getItemFromCart(product)
        if (itemCart != null) {
          itemCart.quantity++;
        } else {
          this.cart.items.push({
            product: product,
            quantity: 1
          });
        }
        product.inStock--;
      },

      getItemFromCart: function(product){
        
        for( var i = 0; i < this.cart.items.length; i++){
          if (this.cart.items[i].product.id === product.id) {
            return this.cart.items[i];
          }
        }
        return null;
      },

      addMoreProduct: function(itemCart){
        console.log(itemCart)

        if (itemCart.product.inStock == 0) {
          alert("These product was out of stock")
        } else {
          itemCart.quantity++
          itemCart.product.inStock--
        }
      },

      removeProduct: function(itemCart){
        itemCart.quantity--
        itemCart.product.inStock++

        if (itemCart.quantity == 0) {
          var r = confirm('Are you sure that you want to remove these products?');
          if (r == true) {
            this.removeItemFromCart(itemCart)
          } else {
            itemCart.quantity++
            itemCart.product.inStock--
          }
        }
      },

      removeItemFromCart: function(cartItem) {
        var index = this.cart.items.indexOf(cartItem);
    
        if (index !== -1) {
            this.cart.items.splice(index, 1);
        }
      },

      checkout: function(){
        if (confirm('Are you sure that you want to purchase these products?')) {
          this.cart.items = [];
        }
      }
    },

    computed: {
      cartTotalItem: function() {
        var total = 0;
        this.cart.items.forEach(function(item) {
          total += item.quantity;
        });
        return total;
      },
      cartTotalMoney: function() {
        var total = 0;

        this.cart.items.forEach(function(item) {
          total += item.quantity * item.product.price;
        });

        return total;

      },
      taxAmount: function() {
        return ((this.cartTotalMoney * 10) / 100);
      }
    },
    filters: {
      currency: function(value){
        var formatter = new Intl.NumberFormat('us', {
          style: 'currency',
          currency: 'USD',
          minimumFractionDigits: 0
        });
        return formatter.format(value);
      }
    }
  }
</script>

<style scoped>
  .flex { display:flex }
  .flex.flex-column { flex-direction: column; }
  .flex.flex-row { flex-direction: row; }
  .flex.justify-center { justify-content: center; }
  .flex.justify-left { justify-content: flex-start; }
  .flex.justify-right { justify-content: flex-end; }
  .flex.align-center { align-items: center; }
  .flex.align-left { align-items: flex-start; }
  .flex.align-right { align-items: flex-end; }
  /* Navigation */
  #top-navigation a,
  #top-navigation .cart-info {
    width: 100%;
  }
  #top-navigation .cart-info .stats { margin-right: 5px; }
  /* Products */
  #products .item img { background-color: #000; }
  #products .item p.lead { margin-bottom: 0; }
  #products .item .number-in-stock { margin-right: 10px; }
  #products .item .number-in-stock.few { color: #E0A14F; }
  #products .item .number-in-stock.none { color: #C45E5E; }
</style>