<template>
  <div class="home">

    <h2>New Contact</h2>
    <div>
      First Name: <input type="text" v-model="newContactFirstName"><br>
      Middle Name: <input type="text" v-model="newContactMiddleName"><br>
      Last Name: <input type="text" v-model="newContactLastName"><br>
      Phone: <input type="text" v-model="newContactPhone"><br>
      Email: <input type="text" v-model="newContactEmail"><br>
    </div>
    <!-- <div>{{ newContactName }}</div> -->
    <div>
      <button v-on:click="createContact()">Create</button>
    </div>


    <h2>All Contacts</h2>
    <div> {{ currentContact }} </div>

    <div v-for="contact in contacts">
      <h2> {{ contact.full_name }} </h2><br>
      
      <div>
        <button v-on:click="showContact(contact)">More Info</button>
      </div>
      
      
      <div v-if="currentContact === contact">
        <p>Group: {{ contact.group_names }}</p>
        <p>Full Name: {{ contact.full_name }}</p>
        <p>First Name: {{ contact.first_name }}</p>
        <p>Middle Name: {{ contact.middle_name }}</p>
        <p>Last Name: {{ contact.last_name }}</p>
        <p>Phone: {{ contact.phone }}</p>
        <p>Email: {{ contact.email }}</p>
        <h3>Edit Contact</h3>
        <div>
          First Name: <input type="text" v-model="contact.first_name"><br>
          Middle Name: <input type="text" v-model="contact.middle_name"><br>
          Last Name: <input type="text" v-model="contact.last_name"><br>
          Phone: <input type="text" v-model="contact.phone"><br>
          Email: <input type="text" v-model="contact.email"><br>
          <button v-on:click="updateContact(contact)">Update</button>
          <button v-on:click="destroyContact(contact)">Destroy</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      contacts: [],
      currentContact: {},
      newContactFirstName: "",
      newContactMiddleName: "",
      newContactLastName: "",
      newContactPhone: "",
      newContactEmail: ""
    };
  },
  created: function() {
    axios.get("/api/contacts").then(response => {
      this.contacts = response.data;
    });
  },
  methods: {
    createContact: function(contact) {
      var contactParams = {
        first_name: this.newContactFirstName,
        middle_name: this.newContactMiddleName,
        last_name: this.newContactLastName,
        phone: this.newContactPhone,
        email: this.newContactEmail
      };
      axios.post("/api/contacts", contactParams).then(response => {
        console.log("Success!", response.data);
        this.contacts.push(response.data);
      });
    },
    showContact: function(contact) {
      if (this.currentContact === contact) {
        this.currentContact = {};
      } else {
        this.currentContact = contact;
      }
    },
    updateContact: function(contact) {
      var contactParams = {
        first_name: contact.first_name,
        middle_name: contact.middle_name,
        last_name: contact.last_name,
        phone: contact.phone,
        email: contact.email
      };
      axios.patch("/api/contacts/" + contact.id, contactParams).then(response => {
        console.log("Success!", response.data);
        contact = response.data;
      });
    },
    destroyContact: function(contact) {
      axios.delete("/api/contacts/" + contact.id).then(response => {
        console.log("BOOM! It's gone.", response.data);
        var index = this.contacts.indexOf(contact);
        this.contacts.splice(index, 1);
      });
    }
  }
};
</script>