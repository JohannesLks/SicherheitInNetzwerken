<template>
    <div>
      <div v-if="!isLoggedIn">
        <input v-model="username" placeholder="Benutzername" />
        <input v-model="password" type="password" placeholder="Passwort" />
        <button @click="login">Login</button>
      </div>
  
      <form v-else @submit.prevent="submitNumber">
        <input v-model.number="number" type="number" placeholder="Geben Sie eine Integer-Zahl ein" required pattern="\d*"/>
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
        isLoggedIn: false,
        username: '',
        password: '',
        number: null,
        responseMessage: null,
        errorMessage: ''
      };
    },
    methods: {
      async login() {
        try {
          const response = await axios.post('http://fh-kiel.com:81/login.php', {
            username: this.username,
            password: this.password
          });
  
          if (response.data.message === "Login erfolgreich") {
            this.isLoggedIn = true;
            this.responseMessage = response.data.message;
          } else {
            alert("Login fehlgeschlagen");
          }
        } catch (error) {
          console.error('Login Fehler: ', error);
        }
      },
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
        // Verwendung von 'params' für GET-Parameter
        const response = await axios.post('http://fh-kiel.com:81/backend.php?secret=letmein', { number: this.number });
        this.responseMessage = response.data.message;
        this.number = null; // Feld leeren nach erfolgreichem Senden
    } catch (error) {
        if (error.response) {
            console.error('Fehler beim Senden der Daten: ', error.response.data);
            this.responseMessage = error.response.data.message || 'Ein unbekannter Fehler ist aufgetreten';
        } else if (error.request) {
            console.error('Keine Antwort vom Server', error.request);
            this.responseMessage = 'Keine Antwort vom Server';
        } else {
            console.error('Fehler: ', error.message);
            this.responseMessage = 'Fehler beim Senden der Daten';
        }
    }
}

}};
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
  