<template>
  <v-container>
    <h2>Donors List</h2>
    <v-row>
      <v-col cols="6">
        <v-modal v-model="modal" centered>
          <v-card class="text-left">
            <v-card-title > <B>First Name: &nbsp;</B>{{ selectedDonor.Fname }} </v-card-title> 
            <v-card-title > <B>Last Name: &nbsp;</B>{{ selectedDonor.Lname }} </v-card-title> 
            <v-card-title > <B>Email: &nbsp;</B>{{ selectedDonor.Email }} </v-card-title> 
            <v-card-title > <B>Address: &nbsp;</B>{{ selectedDonor.Address }} </v-card-title> 
           <v-card-title > <B>Account Type: &nbsp;</B>{{ selectedDonor.AccType }} </v-card-title> 
           <v-card-title > <B>Business Name: &nbsp;</B>{{ selectedDonor.BusinessName }} </v-card-title> 
           <v-card-title > <B>Business Contact Number: &nbsp;</B>{{ selectedDonor.BusinessContact }} </v-card-title>           
           <v-card-title > <B>Business TIN: &nbsp;</B>{{ selectedDonor.BusinessTIN }} </v-card-title> 
           <!--<v-card-title > <B>Subscription: &nbsp;</B>{{ selectedDonor.Subscription }}</v-card-title>-->
           <!--<v-card-title > <B>Account Type: &nbsp;</B>{{ selectedDonor.AccType }} </v-card-title> 
           <v-card-title > <B>Account Type: &nbsp;</B>{{ selectedDonor.AccType }} </v-card-title> //Extra-->
            
          
          </v-card>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="red" @click="Deletedonor">Deactivated</v-btn>
          </v-card-actions>
        </v-modal>
        
        <!---End of modal-->
      </v-col>
      <v-col cols="6" style="height: 400px">
        <v-text-field v-model="search" label="Search"></v-text-field><!--Search bar-->
        <v-data-table :items="filteredDonor" :headers="headers" class="elevation-1" >
          <template v-slot:item.Email="{ item }">
            {{ item.Email}}
          </template>   
          <template v-slot:item.Edit="{ item }">
            <v-btn @click="openModal(item)">See More</v-btn>
          </template>
        </v-data-table>
        <template>
          <div>
            <nuxt-link to="/archiveDonor">

              <v-btn to="/archiveDonor" color="blue">Archive</v-btn>
            </nuxt-link>
          </div>
        </template>
      </v-col>
      
    </v-row>
  </v-container>
</template>

<script>
import firebase from '~/plugins/firebase'

export default {
  data () {
        return {
      Donor: [],
      headers: [
        { text: 'Email', value: 'Email' },
       // { text: 'Password', value: 'Password' },
        { text: '', value: 'Edit' },
      ],
      modal: false,
      selectedDonor: {},
      search:''

    }
  },
  computed: {
    filteredDonor() {
      return this.Donor.filter(Donor => {
        return Donor.Email.toLowerCase().includes(this.search.toLowerCase())&&Donor.Status === "Active"
      })
    }
  },
  created () {
    firebase.database().ref('Donor').on('value', snapshot => {
      this.Donor = Object.values(snapshot.val())
    })
  },
  methods: {
    openModal (item) {
      this.selectedDonor = item
      this.modal = true
    },
     Deletedonor () {
    const index = this.Donor.indexOf(this.selectedDonor.id)
    this.Donor.splice(index, 1)
    this.modal = false
    
    }
      
  }
  
}
</script>

