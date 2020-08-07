<template>
  <div class="home">
    <h1>{{ message }}</h1>

    <p>First Name: <input v-model="newContactFirstName"></p>
    <p>Last Name: <input v-model="newContactLastName"></p>
    <p>Email: <input v-model="newContactEmail"></p>
    <p>Phone Number: <input v-model="newContactPhoneNumber"></p>
    <button v-on:click="newContact">Create New Contact</button>

    <div v-for="contact in contacts">
      <hr>
      <!-- <p>ID: {{ contact.id }}</p> -->
      <p>First Name: {{ contact.first_name }}</p>
      <p>Last Name: {{ contact.last_name }}</p>
      <!-- <p>Email: {{ contact.email }}</p>
      <p>Phone Number: {{ contact.phone_number }}</p> -->
      <button v-on:click="showContact(contact)">More Info</button>
    </div>

    <dialog id="contact-details">
      <form method="dialog">
        <h1> {{currentContact.first_name}} {{currentContact.last_name}} </h1>
        <p>First Name: <input v-model="currentContact.first_name"></p>
        <p>Last Name: <input v-model="currentContact.last_name"></p>
        <p>Email: <input v-model="currentContact.email"></p>
        <p>Phone Number: <input v-model="currentContact.phone_number"></p>
        <button v-on:click="updateContact">Update</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to the Contacts App!",
      contacts: [],
      newContactFirstName: "",
      newContactLastName: "",
      newContactEmail: "",
      newContactPhoneNumber: "",
      currentContact: {},
    };
  },
  created: function () {
    this.indexContacts();
  },
  methods: {
    indexContacts: function () {
      console.log("all the contacts...");
      axios.get("api/contacts").then((response) => {
        console.log(response);
        this.contacts = response.data;
      });
    },

    newContact: function () {
      console.log("creating new contact...");
      var params = {
        first_name: this.newContactFirstName,
        last_name: this.newContactLastName,
        email: this.newContactEmail,
        phone_number: this.newContactPhoneNumber,
      };
      axios
        .post("api/contacts", params)
        .then((response) => {
          console.log(response.data);
          this.contacts.push(response.data);
        })
        .catch((errors) => {
          this.errors = errors.response;
        });
    },

    showContact: function (contact) {
      console.log("showing info...");
      console.log(contact);
      this.currentContact = contact;
      document.querySelector("#contact-details").showModal();
    },

    updateContact: function (contact) {
      console.log("updating contact...");
      var params = {
        first_name: this.currentContact.first_name,
        last_name: this.currentContact.last_name,
        email: this.currentContact.email,
        phone_number: this.currentContact.phone_number,
      };

      axios.patch("api/contacts/" + this.currentContact.id, params).then((response) => {
        console.log(response.data);
        this.currentContact = response.data;
      });
    },
  },
};
</script>