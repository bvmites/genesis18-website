<template>
  <transition name="puff-in">
    <div>
      <navbarComponent></navbarComponent>
      <div class="container">
        <div class="heading">{{department}}</div>
        <div v-for="(event, index) in events" v-if="event.description">
          <b-card no-body class="mb-1">
            <b-card-header header-tag="header" class="p-1" role="tab">
              <b-btn block href="#" v-b-toggle="event._id">{{event.eventName}}</b-btn>
            </b-card-header>
            <b-collapse :id="event._id" accordion="my-accordion" role="tabpanel">
              <b-card-body>
                <div v-for="(round, index) in event.rounds" class="card-text">
                  <div class="sub-heading">Round {{index+1}}</div>
                  <p class="">{{round}}</p>
                </div>
                <div class="sub-heading">Managers</div>
                <div v-for="manager in event.managers" class="row managers">
                  <div class="col-sm-3">
                    <div>{{manager.name}}</div>
                  </div>
                  <div class="col-sm-2">{{manager.phone}}</div>
                </div>
                <div class="sub-heading">Entry Fee: Rs. {{event.entryFee}}/team</div>
                <div class="sub-heading">Team of : {{event.teamSize}}</div>
                <button class="cart-btn btn ml-auto" :disabled="events[index].checked"
                        @click="addToCart(event, index)">Add to cart</button>
              </b-card-body>
            </b-collapse>
          </b-card>
        </div>
        <div class="checkout-btn btn ml-auto" v-if="user.pendingOrder.sum" @click="goToCart">Proceed to
          Checkout</div>
        <div class="workshops">
          <div v-for="(event, index) in events" v-if="!event.description" class="workshop">
            <div class="card">
              <div class="card-header workshop-header">{{event.eventName}}</div>
              <div class="card-body workshop-body" v-for="round in event.rounds">{{round}}</div>
            </div>
          </div>

        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import navbarComponent from '../components/navbarComponent'
import {mapState} from 'vuex'
export default {
  name: 'department',
  components: {
    navbarComponent
  },
  data() {
    return {
      department: null,
      events: null,
    }
  },
  methods: {
    goToCart() {
      this.$router.push('/cart')
    },
    addToCart(event, index) {
      if(this.user.authToken){
        this.$store.commit('addToCart', event)
      }
      else
        this.$router.push('/login')

    }
  },
  computed: {
     ...mapState({
       user: state => state.user
     })
  },
  created() {
    let id = this.$route.params.id
    this.$store.state.departments.forEach((department) => {
      if(department.id === id){
        this.department = department.name
        this.events = department.events
      }
    })
  },
  mounted() {
    window.onscroll = () => {
      document.querySelector('.navbar-dark').classList.remove('pull')
    }
  }
}
</script>

<style scoped lang="sass">
@import '../sass/variables'

.container
  position: absolute
  margin-top: 100px
  margin-left: 50%
  transform: translate(-50%, 0)
  padding-bottom: 20px

.card
  box-shadow: 5px 5px 5px #333333
  margin-top: 20px
  a.btn.btn-secondary.btn-block
    text-transform: uppercase
    font-weight: bolder

.cart-btn, .checkout-btn
  background: #EF8354
  color: white
  display: block
  max-width: 150px
  box-shadow: 2px 2px 2px #333333
  &:active
    box-shadow: none

.checkout-btn
  margin-top: 5%
  max-width: 200px

.sub-heading
  font-size: 20px
  color: #EF8354
  text-transform: uppercase
  font-weight: bold

.managers
  margin-top: 5px
  margin-bottom: 5px
  text-transform: uppercase
  font-weight: bolder

.workshops
  text-align: center
  .workshop
    display: inline-block
    .card
      position: relative
      left: 50%
      transform: translateX(-50%)
      max-width: 300px
      margin: 10px
      height: 200px
      .card-header
        height: 150px
    .workshop-header
      text-transform: uppercase
      background: #5A6268
      font-weight: bolder
      color: white


</style>