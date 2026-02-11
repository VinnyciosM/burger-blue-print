<script setup>
import { ref, watch } from 'vue';

const emit = defineEmits(['login-success']);

const phoneNumber = ref('');
const isLoading = ref(false);

const formatPhone = (value) => {
  if (!value) return '';
  value = value.replace(/\D/g, '');
  value = value.substring(0, 11);
  
  if (value.length > 10) {
    return value.replace(/^(\d{2})(\d{5})(\d{4}).*/, '($1) $2-$3');
  } else if (value.length > 5) {
    return value.replace(/^(\d{2})(\d{4})(\d{0,4}).*/, '($1) $2-$3');
  } else if (value.length > 2) {
    return value.replace(/^(\d{2})(\d{0,5}).*/, '($1) $2');
  } else {
    return value;
  }
};

watch(phoneNumber, (newVal) => {
  phoneNumber.value = formatPhone(newVal);
});

const handleLogin = () => {
  if (phoneNumber.value.length < 15) return;

  isLoading.value = true;

  setTimeout(() => {
    isLoading.value = false;
    emit('login-success', phoneNumber.value);
  }, 1500);
};
</script>

<template>
  <div class="modal-overlay">
    <div class="login-card">
      
      <div class="header">
        <div class="icon-glow">
          <svg viewBox="0 0 24 24" fill="none" stroke="#f59e0b" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="user-icon"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg>
        </div>
        <h2>IDENTIFY YOURSELF</h2>
        <p>Enter your phone number to access your Dragon Rewards.</p>
      </div>

      <div class="input-group">
        <label>MOBILE NUMBER</label>
        <div class="input-wrapper">
          <span class="country-code">🇧🇷 +55</span>
          <input 
            type="tel" 
            v-model="phoneNumber" 
            placeholder="(11) 99999-9999" 
            maxlength="15"
            @keyup.enter="handleLogin"
          />
        </div>
      </div>

      <button 
        class="action-btn" 
        :class="{ 'loading': isLoading, 'disabled': phoneNumber.length < 14 }"
        @click="handleLogin"
        :disabled="phoneNumber.length < 14 || isLoading"
      >
        <span v-if="!isLoading">ACCESS REWARDS</span>
        <span v-else class="spinner"></span>
      </button>

      <p class="terms">
        By continuing, you agree to our <a href="#">Terms of Service</a> & <a href="#">Privacy Policy</a>.
      </p>

    </div>
  </div>
</template>

<style scoped>

.login-card {
  width: 100%;
  max-width: 400px; /* Mantive apenas o limite máximo para não estourar em telas grandes */
  background: linear-gradient(145deg, #2a0a0a, #1a0505);
  border: 1px solid rgba(185, 28, 28, 0.3);
  border-radius: 24px;
  padding: 40px 30px;
  text-align: center;
  box-shadow: 0 20px 50px rgba(0,0,0,0.6);
  position: relative;
  overflow: hidden;
  animation: slideUp 0.4s ease-out;
  margin: 0 20px;
}

.login-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 4px;
  background: linear-gradient(90deg, transparent, #f59e0b, transparent);
}

.header {
  margin-bottom: 30px;
}

.icon-glow {
  width: 60px;
  height: 60px;
  background: rgba(245, 158, 11, 0.1);
  border-radius: 50%;
  margin: 0 auto 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid rgba(245, 158, 11, 0.2);
  box-shadow: 0 0 20px rgba(245, 158, 11, 0.1);
}

.user-icon {
  width: 32px;
  height: 32px;
}

h2 {
  color: #f59e0b;
  font-size: 1.5rem;
  font-weight: 800;
  margin: 0 0 10px;
  letter-spacing: 1px;
}

p {
  color: #a8a8a8;
  font-size: 0.9rem;
  line-height: 1.5;
  margin: 0;
}

.input-group {
  text-align: left;
  margin-bottom: 30px;
}

.input-group label {
  display: block;
  color: #cfa6a6;
  font-size: 0.75rem;
  font-weight: 600;
  margin-bottom: 8px;
  letter-spacing: 1px;
}

.input-wrapper {
  display: flex;
  align-items: center;
  background: rgba(0, 0, 0, 0.3);
  border: 1px solid #5a2626;
  border-radius: 12px;
  padding: 0 15px;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.input-wrapper:focus-within {
  border-color: #f59e0b;
  box-shadow: 0 0 10px rgba(245, 158, 11, 0.2);
}

.country-code {
  color: #fff;
  font-weight: 600;
  margin-right: 10px;
  padding-right: 10px;
  border-right: 1px solid #5a2626;
  display: flex;
  align-items: center;
  gap: 5px;
  font-size: 0.9rem;
}

input {
  flex: 1;
  background: transparent;
  border: none;
  padding: 16px 0;
  color: white;
  font-size: 1.1rem;
  font-weight: 500;
  outline: none;
  font-family: 'Montserrat', sans-serif;
}

input::placeholder {
  color: #555;
}

.action-btn {
  width: 100%;
  padding: 16px;
  border-radius: 12px;
  border: none;
  background: linear-gradient(to right, #dc2626, #991b1b);
  color: white;
  font-weight: 700;
  font-size: 1rem;
  letter-spacing: 1px;
  cursor: pointer;
  transition: all 0.3s;
  box-shadow: 0 5px 15px rgba(220, 38, 38, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.action-btn:hover:not(.disabled) {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(220, 38, 38, 0.4);
}

.action-btn:active:not(.disabled) {
  transform: translateY(1px);
}

.action-btn.disabled {
  background: #3a1010;
  color: #666;
  cursor: not-allowed;
  box-shadow: none;
}

.spinner {
  width: 20px;
  height: 20px;
  border: 3px solid rgba(255,255,255,0.3);
  border-radius: 50%;
  border-top-color: white;
  animation: spin 0.8s linear infinite;
}

.terms {
  font-size: 0.75rem;
  margin-top: 25px;
  color: #666;
}

.terms a {
  color: #888;
  text-decoration: underline;
  transition: color 0.2s;
}

.terms a:hover {
  color: #f59e0b;
}

@keyframes slideUp {
  from { opacity: 0; transform: translateY(30px) scale(0.95); }
  to { opacity: 1; transform: translateY(0) scale(1); }
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
</style>