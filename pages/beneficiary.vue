<template>
  <v-container>
    <h2>Beneficiaries List</h2>
    <v-row>
      <v-col cols="6">
        <v-modal v-model="modal" centered>
          <v-card class="text-left">
            <v-card-title > <B>First Name: &nbsp;</B>{{ selectedPerson.Fname }} </v-card-title> 
            <v-card-title > <B>Last Name: &nbsp;</B>{{ selectedPerson.Lname }} </v-card-title> 
            <v-card-title > <B>Email: &nbsp;</B>{{ selectedPerson.Email }} </v-card-title> 
            <v-card-title > <B>Address: &nbsp;</B>{{ selectedPerson.Address }} </v-card-title> 
           <!-- <v-card-title > <B>Address: &nbsp;</B>{{ selectedPerson.Address }} </v-card-title> //huwat kay Wala sa db -->
           <v-card-title > <B>Account Type: &nbsp;</B>{{ selectedPerson.AccType }} </v-card-title> 
           <v-card-title > <B>Business Name: &nbsp;</B>{{ selectedPerson.BusinessName }} </v-card-title> 
           <v-card-title > <B>Business Contact Number: &nbsp;</B>{{ selectedPerson.BusinessContact }} </v-card-title>           
           <v-card-title > <B>Business TIN: &nbsp;</B>{{ selectedPerson.BusinessTIN }} </v-card-title> 
           <v-card-title > <B>Subscription: &nbsp;</B>{{ selectedPerson.Subscription }}
           <v-btn @click="updateStatus(item)">Set Status</v-btn> </v-card-title> 
           <!--<v-card-title > <B>Account Type: &nbsp;</B>{{ selectedPerson.AccType }} </v-card-title> 
           <v-card-title > <B>Account Type: &nbsp;</B>{{ selectedPerson.AccType }} </v-card-title> //Extra-->
          </v-card>
            <v-spacer></v-spacer>
        <v-btn color="red" @click="deletePerson">Deactived</v-btn>
      </v-card-actions>

        </v-modal>
      </v-col><!--End Of Modal-->
      <v-col cols="6" style="height: 400px">
        <v-text-field v-model="search" label="Search"></v-text-field><!--Search bar-->
        <v-data-table :items="filteredPersons" :headers="headers" class="elevation-1" >
          <template v-slot:item.Email="{ item }">
            {{ item.Email}}
          </template>   
          <template v-slot:item.Edit="{ item }">
            <v-btn @click="openModal(item)">See More</v-btn>
          </template> 
        </v-data-table>
        <template>
          <div>
            <nuxt-link to="/archivePer">

              <v-btn to="/archivePer" color="blue">Archive</v-btn>
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
      Persons: [],
      headers: [
        { text: 'Email', value: 'Email' },
       // { text: 'Password', value: 'Password' },
        { text: '', value: 'Edit' },
      ],
      modal: false,
      selectedPerson: {},
      search:''
    }
  },
  computed: {
    filteredPersons() {
      return this.Persons.filter(person => {
        return person.Email.toLowerCase().includes(this.search.toLowerCase())&&person.Status === "Active"
      })
    }
  },
  created () {
    firebase.database().ref('Persons').on('value', snapshot => {
      this.Persons = Object.values(snapshot.val())
    })
  },
  methods: {
    openModal (item) {
      this.selectedPerson = item
      this.modal = true
    },
    deletePerson () {
    const index = this.Persons.indexOf(this.selectedPersons)
    this.Persons.splice(index, 1)
    this.modal = false
    },
    updateStatus() {
      if (!this.selectedPerson || !this.selectedPerson.id) {
      return
  }
      const ref = firebase.database().ref(`Persons/${this.selectedPerson.id}`)
      this.selectedPerson.Subscription = this.selectedPerson.Subscription === 'Basic User' ? 'Subscribed' : 'Basic User'
      ref.update({ Subscription: this.selectedPerson.Subscription }, error => {
        if (error) {
          console.log(error)
        } else {
          console.log('Update successful')
        }
     })
    }
  }
}
</script>

<style>
  /* make the v-container overflow-y scrollable */
  .v-container1 {
    overflow-y: scroll;
  }
</style>

