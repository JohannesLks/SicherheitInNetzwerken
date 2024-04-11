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
        return; // Beendet die Methode frühzeitig, falls Validierungsfehler vorhanden sind
    }
    try {
        const response = await axios.post('http://fh-kiel.com:81/backend.php', { number: this.number });
        
        // Aktualisiert die responseMessage mit der Nachricht vom Server
        this.responseMessage = response.data.message;
        
        // Setzt die eingegebene Zahl zurück, wenn die Anfrage erfolgreich war
        this.number = null;
    } catch (error) {
        // Fängt Netzwerkfehler und zeigt eine Fehlermeldung an
        if (error.response) {
            // Der Server antwortete mit einem Statuscode außerhalb des Bereichs von 2xx
            console.error('Fehler beim Senden der Daten: ', error.response.data);
            this.responseMessage = error.response.data.message || 'Ein unbekannter Fehler ist aufgetreten';
        } else if (error.request) {
            // Die Anfrage wurde gesendet, aber keine Antwort wurde empfangen
            console.error('Keine Antwort vom Server', error.request);
            this.responseMessage = 'Keine Antwort vom Server';
        } else {
            // Etwas anderes hat den Fehler ausgelöst
            console.error('Fehler: ', error.message);
            this.responseMessage = 'Fehler beim Senden der Daten';
        }
    }
}}};
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
  