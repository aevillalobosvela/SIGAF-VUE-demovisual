<template>
  <div class="size-full flex items-center justify-center bg-gray-50">
    <div class="w-full max-w-md p-8">
      <!-- Logo placeholder -->
      <div class="flex justify-center mb-6">
        <div class="w-20 h-20 border-4 border-gray-400 rounded-lg flex items-center justify-center">
          <span class="text-gray-400 text-xs">LOGO</span>
        </div>
      </div>

      <!-- Title -->
      <h1 class="text-center text-3xl mb-8 text-gray-800">SIGAF</h1>
      <p class="text-center text-sm text-gray-600 mb-8">Sistema de Gestión de Activos Fijos</p>

      <!-- Login form -->
      <form @submit.prevent="handleSubmit" class="space-y-6">
        <!-- Username input -->
        <div>
          <label class="block text-sm mb-2 text-gray-700">Usuario</label>
          <input
            v-model="username"
            type="text"
            class="w-full px-4 py-3 border-2 border-gray-300 rounded bg-white text-gray-800"
            placeholder="Ingrese su usuario"
          />
        </div>

        <!-- Password input -->
        <div>
          <label class="block text-sm mb-2 text-gray-700">Contraseña</label>
          <input
            v-model="password"
            type="password"
            class="w-full px-4 py-3 border-2 border-gray-300 rounded bg-white text-gray-800"
            placeholder="Ingrese su contraseña"
          />
        </div>

        <!-- Login button -->
        <button
          type="submit"
          class="w-full py-3 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
        >
          Ingresar
        </button>

        <!-- Error message -->
        <div
          v-if="error"
          class="text-red-600 text-sm text-center p-2 bg-red-50 border border-red-200 rounded"
        >
          {{ error }}
        </div>
      </form>

      <!-- Demo role selector (for wireframe testing) -->
      <div class="mt-8 p-4 bg-blue-50 border border-blue-200 rounded">
        <p class="text-xs text-blue-800 mb-2">Wireframe Demo - Seleccionar Rol:</p>
        <div class="flex gap-2">
          <button
            @click="emit('login', 'Administrador')"
            class="flex-1 py-2 text-xs bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            Administrador
          </button>
          <button
            @click="emit('login', 'Operador')"
            class="flex-1 py-2 text-xs bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            Operador
          </button>
          <button
            @click="emit('login', 'Consulta')"
            class="flex-1 py-2 text-xs bg-blue-600 text-white rounded hover:bg-blue-700"
          >
            Consulta
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import type { UserRole } from '../types'

const emit = defineEmits<{
  login: [role: UserRole]
}>()

const username = ref('')
const password = ref('')
const error = ref('')

function handleSubmit() {
  if (username.value && password.value) {
    emit('login', 'Administrador')
  } else {
    error.value = 'Por favor ingrese usuario y contraseña'
  }
}
</script>
