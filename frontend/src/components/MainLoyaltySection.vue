<script setup>
import { ref } from 'vue';

// Estado dos selos (simulando 1 selo preenchido conforme a imagem)
const stamps = ref([
  { id: 1, filled: true },
  { id: 2, filled: false },
  { id: 3, filled: false },
  { id: 4, filled: false }, 
  { id: 5, filled: false }
]);

const userCode = ref('');

const submitCode = () => {
  if(!userCode.value) return;
  alert(`Código digitado: ${userCode.value}`);
};
</script>

<template>
  <div class="container">
    <main class="content">
      <div class="logo-container">
        <div class="placeholder-logo">
          <span class="dragon-icon">🐉</span>
          <h1>DRAKON</h1>
          <h2>BURGER</h2>
        </div>
      </div>

      <h3 class="title">CARTÃO DE FIDELIDADE DIGITAL</h3>

      <div class="stamps-container">
        <div 
          v-for="stamp in stamps" 
          :key="stamp.id" 
          class="stamp-slot"
          :class="{ 'filled': stamp.filled }"
        >
          <div v-if="stamp.filled" class="check-mark">
            <svg viewBox="0 0 24 24" width="24" height="24" stroke="white" stroke-width="4" fill="none"><polyline points="20 6 9 17 4 12"></polyline></svg>
          </div>
        </div>
      </div>

      <p class="instruction">
        Junte *5* selos e ganhe *um hambúrguer* GRÁTIS!
      </p>

      <div class="input-wrapper">
        <input 
          type="text" 
          v-model="userCode" 
          placeholder="Enter your code" 
        />
        <button @click="submitCode" class="arrow-btn">
          <svg viewBox="0 0 24 24" width="24" height="24" stroke="white" stroke-width="2" fill="none"><polyline points="9 18 15 12 9 6"></polyline></svg>
        </button>
      </div>

      <button class="join-btn">JOIN NOW</button>
    </main>
  </div>
</template>

<style scoped>
.container {
  color: white;
  max-width: 600px;
  
}

.content {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 0 20px;
  text-align: center;
}

.placeholder-logo {
  margin-bottom: 20px;
}
.dragon-icon {
  font-size: 4rem;
  display: block;
  filter: drop-shadow(0 0 10px red);
}
.placeholder-logo h1 {
  font-size: 2.2rem;
  font-weight: 800;
  color: #fbbf24; /* Amarelo ouro */
  text-transform: uppercase;
  letter-spacing: 2px;
  margin: 0;
  line-height: 0.9;
  text-shadow: 2px 2px 0px #500;
}
.placeholder-logo h2 {
  font-size: 1.2rem;
  color: #fbbf24;
  margin: 0;
  letter-spacing: 4px;
  text-transform: uppercase;
}

.title {
  font-weight: 600;
  font-size: 1.1rem;
  margin-bottom: 25px;
  letter-spacing: 1px;
}

/* Slots de Selos */
.stamps-container {
  display: flex;
  gap: 12px;
  margin-bottom: 20px;
}

.stamp-slot {
  width: 50px;
  height: 50px;
  border: 2px solid #b91c1c; /* Vermelho escuro */
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0,0,0,0.3);
  box-shadow: inset 0 0 10px rgba(0,0,0,0.5);
}

.stamp-slot.filled {
  border-color: #f59e0b; /* Laranja/Ouro */
  background: radial-gradient(circle, #b45309, #78350f);
  box-shadow: 0 0 15px #f59e0b;
}

.check-mark {
  background: #f59e0b;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.instruction {
  font-size: 0.9rem;
  margin-bottom: 30px;
  max-width: 200px;
  line-height: 1.4;
}

/* Input */
.input-wrapper {
  display: flex;
  width: 100%;
  background: rgba(255,255,255,0.1);
  border: 1px solid #5a2626;
  border-radius: 12px;
  margin-bottom: 25px;
  overflow: hidden;
}

.input-wrapper input {
  flex: 1;
  background: transparent;
  border: none;
  padding: 15px;
  color: white;
  outline: none;
}

.input-wrapper input::placeholder {
  color: #a8a8a8;
}

.arrow-btn {
  background: linear-gradient(to bottom right, #ea580c, #9a3412);
  border: none;
  width: 50px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Botão Join */
.join-btn {
  width: 100%;
  padding: 15px;
  border-radius: 30px;
  border: none;
  background: linear-gradient(to bottom, #dc2626, #991b1b);
  color: white;
  font-weight: 800;
  font-size: 1.2rem;
  letter-spacing: 1px;
  box-shadow: 0 5px 0 #7f1d1d, 0 10px 10px rgba(0,0,0,0.4);
  cursor: pointer;
  margin-bottom: 20px;
  transition: transform 0.1s;
}

.join-btn:active {
  transform: translateY(4px);
  box-shadow: 0 1px 0 #7f1d1d;
}
</style>