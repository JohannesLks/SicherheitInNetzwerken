<template>
    <div>
      <form @submit.prevent="submitNumber">
        <input v-model.number="number"
               type="number"
               placeholder="Geben Sie eine Integer-Zahl ein"
               required
               pattern="\d*"
               @input="validateInput"
               :class="{ 'is-invalid': errorMessage }">
        <span v-if="errorMessage" class="error-tooltip">{{ errorMessage }}</span>
        <button type="submit">Senden</button>
      </form>
      <div v-if="responseMessage">
        Antwort vom Server: {{ responseMessage }}
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        number: null,
        responseMessage: null,
        errorMessage: ''
      };
    },
    methods: {
      validateInput() {
        const input = this.$el.querySelector('input[type="number"]');
        if (!input.checkValidity()) {
          this.errorMessage = input.validationMessage;
        } else {
          this.errorMessage = '';
        }
      },
      async submitNumber() {
        this.validateInput();
        if (this.errorMessage) {
          return;
        }
        try {
          const response = await axios.post('URL_DES_SERVERS', { number: this.number });
          this.responseMessage = response.data.message;
          this.number = null; // Feld leeren nach erfolgreichem Senden
        } catch (error) {
          console.error('Fehler beim Senden der Daten: ', error);
          this.responseMessage = 'Fehler beim Senden der Daten';
        }
      }
    }
  };
  </script>
  
  <style scoped>
  .is-invalid {
    border-color: red;
  }
  .error-tooltip {
    position: absolute;
    background-color: red;
    color: white;
    padding: 2px 8px;
    border-radius: 4px;
    top: 2em;
  }
  </style>
  