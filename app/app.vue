<template>
  <div class="container">
    <div class="card">
      <h1>📋 Moja PWA - Lista zadań</h1>
      <p class="subtitle">Dodawaj, odhaczaj i usuwaj zadania</p>

      <!-- 🔥 PRZYCISK INSTALACJI PWA -->
      <button v-if="canInstall" class="install" @click="installApp">
        📲 Zainstaluj aplikację
      </button>

      <div class="input-box">
        <input v-model="task" placeholder="Wpisz zadanie..." />
        <button @click="addTask">Dodaj</button>
      </div>

      <ul v-if="tasks.length > 0">
        <li v-for="(t, index) in tasks" :key="index">
          <label class="task">
            <input type="checkbox" v-model="t.done" />
            <span :class="{ done: t.done }">
              {{ t.text }}
            </span>
          </label>

          <button class="delete" @click="removeTask(index)">✕</button>
        </li>
      </ul>

      <p v-else class="empty">Brak zadań 👀</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const task = ref("");
const tasks = ref([]);

/* 🔥 PWA INSTALL LOGIC */
const deferredPrompt = ref(null);
const canInstall = ref(false);

onMounted(() => {
  window.addEventListener("beforeinstallprompt", (e) => {
    e.preventDefault();
    deferredPrompt.value = e;
    canInstall.value = true;
  });
});

const installApp = async () => {
  if (!deferredPrompt.value) return;

  deferredPrompt.value.prompt();
  const choice = await deferredPrompt.value.userChoice;

  if (choice.outcome === "accepted") {
    console.log("PWA zainstalowana");
  }

  deferredPrompt.value = null;
  canInstall.value = false;
};

/* TODO APP */
const addTask = () => {
  if (task.value.trim() !== "") {
    tasks.value.push({
      text: task.value,
      done: false,
    });
    task.value = "";
  }
};

const removeTask = (index) => {
  tasks.value.splice(index, 1);
};
</script>

<style>
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #667eea, #764ba2);
}

.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.card {
  width: 100%;
  max-width: 450px;
  background: white;
  padding: 25px;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
}

h1 {
  margin: 0 0 5px;
  font-size: 22px;
}

.subtitle {
  margin: 0 0 20px;
  color: #666;
  font-size: 14px;
}

.input-box {
  display: flex;
  gap: 10px;
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

button {
  padding: 10px 15px;
  background: #667eea;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

button:hover {
  background: #5a67d8;
}

/* 🔥 INSTALL BUTTON */
.install {
  width: 100%;
  margin-bottom: 15px;
  background: #10b981;
}

.install:hover {
  background: #059669;
}

ul {
  margin-top: 20px;
  padding: 0;
  list-style: none;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #f7f7f7;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 8px;
}

.task {
  display: flex;
  gap: 10px;
  align-items: center;
}

.done {
  text-decoration: line-through;
  color: #999;
}

.delete {
  background: transparent;
  color: red;
  font-weight: bold;
}

.empty {
  margin-top: 20px;
  color: #999;
  text-align: center;
}
</style>
