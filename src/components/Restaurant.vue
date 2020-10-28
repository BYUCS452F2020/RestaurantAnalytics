<template>
  <v-container class="mx-auto">
    <v-row>
      <v-col>
        <span>
          Restaurant Name: {{restaurant.name || "restaurant.name"}}
        </span>
      </v-col>
      <v-col>
        <span>
          Chef On Duty: {{chef.name || "chef.name"}}
        </span>
      </v-col>
      <v-col>
        <span>
          Your Waiter: {{waiter.name || "waiter.name"}}
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
        :items="menu"
        label="Select Items to Order"
        outlined
        multiple
      />
    </v-row>
    <v-row>
      <span>
        SubTotal: {{getPriceSelectedItems()}}
      </span>
    </v-row>
    <v-row>
      <v-select
        :items="tipAmounts"
        label="Tip Amount"
        outlined
      />
    </v-row>
    <v-row>
      <span>
        Total: {{getTotalPrice()}}
      </span>
    </v-row>
    <v-row>
      <v-btn
        width="100%"
        elevation="2"
        x-large
      >Submit Order</v-btn>
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
    // no params passed
    // expecting to get an object named restaurant with all cols named appropriately 
    // restaurant: {
    //   BusinessID,
    //   Name, 
    //   Phone,
    //   Website,
    //   Address,
    //   NumEmployees,
    //   Specialty
    // }
    axios.get(`http://our-url-goes-here/get-restaurant`)
      .then(response => {
        // JSON responses are automatically parsed.
        console.log(response)
        this.restaurant = response.data.restaurant
      })
      .catch(e => {
        this.errors.push(e)
      })

    // no params passed
    // expecting to get an object named staff containing two objects-- chef and waiter.
    // Chef and waiter should have all cols named appropriately 
    // staff: {
    //   chef: {
    //     EmployeeID,
    //     BusinessID,
    //     EmployeeFirstName,
    //     EmployeeLastName,
    //     EmployeePhone,
    //     Position
    //   }
    //   waiter: {
    //     EmployeeID,
    //     BusinessID,
    //     EmployeeFirstName,
    //     EmployeeLastName,
    //     EmployeePhone,
    //     Position
    //   }
    // }
    axios.get(`http://our-url-goes-here/get-staff`) // gets chef and waiter
      .then(response => {
        // JSON responses are automatically parsed.
        console.log(response)
        this.chef = response.data.staff.chef
        this.waiter = response.data.staff.waiter
      })
      .catch(e => {
        this.errors.push(e)
      })

    // no params passed
    // expecting to get an object named menu that is a list of menuItems
    // Each MenuItem should have all cols named appropriately 
    // menu: [
      // {
      //   MenuID,
      //   BusinessID,
      //   Name, 
      //   Cost, 
      //   ProductionCost, 
      //   IsASpecial, 
      //   IsGlutenFree, 
      //   IsDairyFree, 
      //   IsVegan, 
      //   IsAlcoholic, 
      //   Type
      // },
      // {
      //   MenuID,
      //   BusinessID,
      //   Name, 
      //   Cost, 
      //   ProductionCost, 
      //   IsASpecial, 
      //   IsGlutenFree, 
      //   IsDairyFree, 
      //   IsVegan, 
      //   IsAlcoholic, 
      //   Type
      // },
      // ...
    // ]
    axios.get(`http://our-url-goes-here/get-menu`)
      .then(response => {
        // JSON responses are automatically parsed.
        console.log(response)
        this.menu = response.data.menu
      })
      .catch(e => {
        this.errors.push(e)
      })

    // passed two params: party and menuItems

    // (NOTE that I am expecting the party id to be generated when you add a new row to the table)
    // (NOTE that I am expecting the datetime to be generated on the backend)
    // party: {
    //   ChefID,
    //   WaiterID,
    //   PartyName,
    //   BusinessID,
    //   TotalCost,
    //   TipAmount,
    // }

    // menuItems: [
    //   MenuID,
    //   MenuID,
    //   MenuID,
    //   ...
    // ]
    // expecting to get an object named order
    // order: {
    //   OrderID,
    //   PartyID,
    //   MenuID
    // }
    axios.post(`http://our-url-goes-here/submit-order`, { params: { party: this.getParty(), menuItems: this.getSelectedItems()}})
      .then(response => {
        // JSON responses are automatically parsed.
        console.log(response)
        this.order = response.order
      })
      .catch(e => {
        this.errors.push(e)
      })
  },
  data () {
    return {
      restaurant: {},
      chef: {},
      waiter: {},
      menu: [],
      subTotal: 0.00,
      total: 0.00,
      partyName: "",
      tipAmounts: [
        "15%",
        "20%",
        "25%"
      ]
    }
  },
  computed: {
    getRestaurant () {
      return "PlaceHolder Restaurant"
    },
    getChef () {
      return "PlaceHolder Chef"
    },
    getWaiter () {
      return "PlaceHolder Waiter"
    },
  },
  methods: {
    getPriceSelectedItems () {
      return "$36.86"
    },
    getTotalPrice () {
      return "$40.00"
    },
    getParty () {

    },
    getSelectedItems () {

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
