<template>
  <div>
    <div class="container">
    <div id="titulo">
      <h1>Pesquisa de Satisfação Health</h1>
      <p>Avalie os nossos serviços:</p>
    </div>

    <form id="surveyForm" @submit.prevent="submitSurvey">
      <div v-for="(question, index) in questions" :key="index">
        <h5>{{ question.label }}</h5>
        <label class="radio-label" v-for="option in question.options" :key="option.value">
          <input type="radio" :name="question.name" :value="option.value" v-model="question.selectedOption">
          <span class="emoji">{{ option.emoji }}</span>
        </label>
        <div class="error-message" :id="'errorQ' + (index + 1)" v-if="!question.selectedOption">Por favor, selecione uma opção.</div>
      </div>

      <div>
        <h5>Descrição (limite de 250 caracteres)</h5>
        <textarea v-model.trim="description" name="description" maxlength="250"></textarea>
      </div>

      <button id="submitButton" type="submit">Enviar</button>
    </form>
  </div>
  <div class="thank-you" id="thankYouMessage" v-if="submitted">
      <h2>Obrigado pelo seu feedback!</h2>
      <p>Agradecemos por responder à pesquisa de satisfação.</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const questions = ref([
  { name: 'q1', label: '1. Como você avalia a qualidade do atendimento?', selectedOption: null, options: [
    { value: '1', emoji: '😡' },
    { value: '2', emoji: '😞' },
    { value: '3', emoji: '😐' },
    { value: '4', emoji: '😊' },
    { value: '5', emoji: '😄' }
  ]},
  { name: 'q2', label: '2. Como você avalia a qualidade do produto?', selectedOption: null, options: [
    { value: '1', emoji: '😡' },
    { value: '2', emoji: '😞' },
    { value: '3', emoji: '😐' },
    { value: '4', emoji: '😊' },
    { value: '5', emoji: '😄' }
  ]},
  { name: 'q3', label: '3. Como você avalia a qualidade do serviço?', selectedOption: null, options: [
    { value: '1', emoji: '😡' },
    { value: '2', emoji: '😞' },
    { value: '3', emoji: '😐' },
    { value: '4', emoji: '😊' },
    { value: '5', emoji: '😄' }
  ]}
])

const description = ref('')
const submitted = ref(false)

const submitSurvey = () => {
  if (validateForm()) {
    // Coletar os dados do formulário
    const form = document.getElementById('surveyForm');
    const formData = new FormData(form);
    console.log("🚀 ~ submitSurvey ~ formData:", formData)

    const adress = 'localhost'
    const port_adress = '3001'

    // Construir a URL com os dados do formulário como parâmetros de consulta
    let url = `http://${adress}:${port_adress}/email-messaging/insert-response`;
    // for (let [key, value] of formData.entries()) {
    //   url += encodeURIComponent(key) + '=' + encodeURIComponent(value) + '&';
    // }

    // Remover o último '&'
    // url = url.slice(0, -1);

    fetch(url)
      .then(response => {
        console.log("🚀 ~ document.getElementById ~ response:", response);

        // Exibir a mensagem de agradecimento
        submitted.value = true;
        form.reset();

        const container = document.querySelector('.container');
        container.style.display = 'none';
      })
      .catch(error => {
        console.error("🚀 ~ document.getElementById ~ error:", error);
      });
  }
}

const validateForm = () => {
  let valid = true
  questions.value.forEach((question, index) => {
    if (!question.selectedOption) {
      document.getElementById('errorQ' + (index + 1))?.classList.add('show')
      valid = false
    } else {
      document.getElementById('errorQ' + (index + 1))?.classList.remove('show')
    }
  })
  return valid
}
</script>

<style scoped>
.container {
  max-width: 600px;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

h1 {
  color: #007bff;
  margin-top: 0;
  text-align: center;
  word-break: break-word;
}

p {
  color: #666666;
  text-align: center;
}

form {
  margin-top: 20px;
  text-align: center;
}

h5 {
  margin-bottom: 10px;
  color: #333333;
}

.radio-label {
  display: inline-block;
  margin-right: 10px;
  cursor: pointer;
}

.emoji {
  font-size: 24px;
  vertical-align: middle;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #007bff;
  color: #ffffff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #0056b3;
}

/* Additional styling */
.container div {
  margin-bottom: 20px;
}

textarea {
  width: 100%;
  height: 100px;
  resize: vertical;
}

/* Custom styling */
.thank-you {
  width: 420px;
  padding: 70px;
  border-radius: 10px;
  background-color: white;
  text-align: center;
}

.thank-you h2 {
  color: #007bff;
  margin-top: 0;
  text-align: center;
  word-break: break-word;
}

.thank-you p {
  color: #666666;
  text-align: center;
  margin-top: 0;
  word-break: break-word;
}

.error-message {
  color: #dc3545;
  margin-top: 5px;
  display: none;
}

.error-message.show {
  display: block;
}
</style>
