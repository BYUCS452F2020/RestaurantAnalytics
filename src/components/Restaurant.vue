<template>
  <v-container class="mx-auto">
    <v-row>
      <v-col>
        <span>
          Restaurant Name: {{restaurant.business_name || "restaurant.name"}}
        </span>
      </v-col>
      <v-col>
        <span>
          Chef On Duty: {{chef.first_name + " " + chef.last_name || "chef.name"}}
        </span>
      </v-col>
      <v-col>
        <span>
          Your Waiter: {{waiter.first_name + " " + waiter.last_name || "waiter.name"}}
        </span>
      </v-col>
    </v-row>
    <v-row>
      <v-text-field
        v-model="partyName"
        label="Enter Party Name"
        outlined
      />
    </v-row>
    <v-row>
      <v-select
        :items="[1,2,3,4,5,6,7,8,9,10]"
        label="Number In Group"
        outlined
        @input="setNumPeople($event)"
      />
    </v-row>
    <v-row
      v-for="person in numPeople"
      :key="person"
    >
      <v-select
        :items="menu"
        label="Select Items to Order"
        outlined
        multiple
        item-text="item_name"
        return-object
        @change="selectItem($event)"
      />
    </v-row>
    <v-row>
      <span>
        Sub Total: {{'$' + getPriceSelectedItems().toFixed(2)}}
      </span>
    </v-row>
    <v-row>
      <v-select
        :items="tipAmounts"
        label="Tip Amount"
        outlined
        @input="setTipAmount($event)"
      />
    </v-row>
    <v-row>
      <span>
        Total: {{'$' + getGrandTotal().toFixed(2)}}
      </span>
    </v-row>
    <v-row>
      <v-btn
        width="100%"
        elevation="2"
        x-large
        @click="submitOrder()"
      >
        Pay
      </v-btn>
    </v-row>
    <v-row v-if="paid">
      <span>Payment Successful!</span>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Restaurant',
  props: {
  },
  mounted() {
    axios.get(`https://jyev82mlki.execute-api.us-west-2.amazonaws.com/devTest1/restaurant/1`)
      .then(response => {
        this.restaurant = response.data[0]
      })
      .catch(e => {
        console.log(e)
      })
    // https://cors-anywhere.herokuapp.com/
    axios.get(`https://jyev82mlki.execute-api.us-west-2.amazonaws.com/devTest1/staff/1`) // gets chef and waiter
      .then(response => {
        this.chef = response.data.chef[0]
        this.waiter = response.data.waiter[0]
      })
      .catch(e => {
        console.log(e)
      })

    axios.get(`https://jyev82mlki.execute-api.us-west-2.amazonaws.com/devTest1/menu/1`)
      .then(response => {
        this.menu = response.data
      })
      .catch(e => {
        console.log(e)
      })

  },
  data () {
    return {
      restaurant: {},
      chef: {},
      waiter: {},
      menu: [],
      selectedItems: [],
      subTotal: 0.00,
      total: 0.00,
      paid: false,
      partyName: "",
      tipAmounts: [
        "15%",
        "20%",
        "25%"
      ],
      tipAmount: 0,
      numPeople: 0,
    }
  },
  
  methods: {
    getGrandTotal () {
      // console.log(this.getPriceSelectedItems())
      // console.log(this.tipAmount)
      this.total = (this.getPriceSelectedItems() + (this.getPriceSelectedItems() * this.tipAmount))
    
      return this.total
    },
    setNumPeople (e) {
      this.numPeople = e
    },
    setTipAmount (e) {
      let percentage = parseInt(e, 10)/100
      this.tipAmount = percentage
    },
    submitOrder () {
      axios.post(`https://jyev82mlki.execute-api.us-west-2.amazonaws.com/devTest1/submit-order`, { party: this.getParty(), selected_items: this.getSelectedMenuItems()})
        .then(response => {
          console.log(response)
          this.total = response.data
          this.paid = true
        })
        .catch(e => {
          console.log(e)
        })
    },
    getSelectedMenuItems () {
      let list = []
      for (let i = 0; i < this.selectedItems.length; i++) {
        list.push(this.selectedItems[i].menu_id)
      }
      return list
    },
    selectItem (e) {
      this.selectedItems.push(...e)
    },
    getPriceSelectedItems () {
      let total = 0
      for (let i = 0; i < this.selectedItems.length; i++) {

        total += parseInt(this.selectedItems[i].customer_cost.toFixed(2))
      }
      return total
    },
    getParty () {
      return {
        chef_id: this.chef.employee_id,
        waiter_id: this.waiter.employee_id,
        party_name: this.partyName,
        business_id: this.restaurant.business_id,
        tip_amount: this.tipAmount
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
