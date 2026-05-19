<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">{{ isEditing ? 'Editar Usuario' : 'Nuevo Usuario' }}</h1>
    </div>

    <form @submit.prevent="handleSubmit" class="space-y-6">
      <!-- Datos personales -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Datos Personales</h2>
        </div>
        <div class="p-6 grid grid-cols-1 md:grid-cols-2 gap-6">
          <div class="md:col-span-2">
            <label class="block text-sm text-gray-700 mb-2">
              Nombre Completo <span class="text-red-600">*</span>
            </label>
            <input
              v-model="formData.nombre"
              type="text"
              placeholder="Ej: Lic. Pedro Condori Mamani"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              required
            />
          </div>
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Nombre de Usuario <span class="text-red-600">*</span>
            </label>
            <input
              v-model="formData.username"
              type="text"
              placeholder="Ej: p.condori"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              required
            />
            <p class="text-xs text-gray-600 mt-1">Solo letras minúsculas, números y puntos.</p>
          </div>
          <div>
            <label class="block text-sm text-gray-700 mb-2">Correo Electrónico</label>
            <input
              v-model="formData.email"
              type="email"
              placeholder="usuario@uto.edu.bo"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
            />
          </div>
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Contraseña <span v-if="!isEditing" class="text-red-600">*</span>
            </label>
            <input
              v-model="formData.password"
              type="password"
              placeholder="Mínimo 8 caracteres"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              :required="!isEditing"
            />
            <p v-if="isEditing" class="text-xs text-gray-600 mt-1">Dejar en blanco para mantener la contraseña actual.</p>
          </div>
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Confirmar Contraseña <span v-if="!isEditing" class="text-red-600">*</span>
            </label>
            <input
              v-model="formData.passwordConfirm"
              type="password"
              placeholder="Repetir contraseña"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              :required="!isEditing"
            />
            <p v-if="passwordMismatch" class="text-xs text-red-600 mt-1">Las contraseñas no coinciden.</p>
          </div>
        </div>
      </div>

      <!-- Rol y perfiles -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Rol y Perfiles de Acceso</h2>
        </div>
        <div class="p-6 space-y-6">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div>
              <label class="block text-sm text-gray-700 mb-2">
                Rol <span class="text-red-600">*</span>
              </label>
              <select
                v-model="formData.rol"
                class="w-full px-3 py-2 border-2 border-gray-300 rounded"
                required
              >
                <option value="">Seleccione un rol...</option>
                <option value="Administrador">Administrador</option>
                <option value="Operador">Operador</option>
                <option value="Consulta">Consulta</option>
              </select>
            </div>
            <div>
              <label class="block text-sm text-gray-700 mb-2">Estado</label>
              <div class="flex items-center gap-3 mt-3">
                <button
                  type="button"
                  @click="formData.estado = formData.estado === 'Activo' ? 'Inactivo' : 'Activo'"
                  :class="[
                    'relative inline-flex h-6 w-11 items-center rounded-full transition-colors',
                    formData.estado === 'Activo' ? 'bg-gray-700' : 'bg-gray-300'
                  ]"
                >
                  <span
                    :class="[
                      'inline-block h-4 w-4 transform rounded-full bg-white transition-transform',
                      formData.estado === 'Activo' ? 'translate-x-6' : 'translate-x-1'
                    ]"
                  />
                </button>
                <span class="text-sm text-gray-700">{{ formData.estado }}</span>
              </div>
            </div>
          </div>

          <!-- Descripción del rol -->
          <div v-if="formData.rol" class="px-4 py-3 bg-gray-50 border border-gray-300 rounded text-sm text-gray-700">
            <template v-if="formData.rol === 'Administrador'">
              <strong>Administrador:</strong> Acceso total al sistema. Gestiona usuarios, parámetros y configuración.
            </template>
            <template v-else-if="formData.rol === 'Operador'">
              <strong>Operador:</strong> Acceso operativo según los perfiles asignados a continuación.
            </template>
            <template v-else-if="formData.rol === 'Consulta'">
              <strong>Consulta:</strong> Solo lectura. Puede visualizar listados, detalles y reportes, pero no puede crear ni modificar registros.
            </template>
          </div>

          <!-- Perfiles granulares (solo Operador) -->
          <div v-if="formData.rol === 'Operador'">
            <label class="block text-sm text-gray-700 mb-3">
              Perfiles Operativos <span class="text-red-600">*</span>
            </label>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
              <label
                v-for="perfil in perfilesDisponibles"
                :key="perfil.value"
                class="flex items-start gap-3 p-4 border-2 border-gray-300 rounded cursor-pointer hover:bg-gray-50 transition-colors"
                :class="{ 'border-gray-700 bg-gray-50': formData.perfiles.includes(perfil.value) }"
              >
                <input
                  type="checkbox"
                  :value="perfil.value"
                  v-model="formData.perfiles"
                  class="w-4 h-4 mt-0.5"
                />
                <div>
                  <div class="text-sm text-gray-900">{{ perfil.label }}</div>
                  <div class="text-xs text-gray-600 mt-1">{{ perfil.descripcion }}</div>
                </div>
              </label>
            </div>
          </div>
        </div>
      </div>

      <!-- Pie del formulario -->
      <div class="flex items-center justify-end gap-3 pt-2">
        <button
          type="button"
          @click="emit('cancel')"
          class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors"
        >
          Cancelar
        </button>
        <button
          type="submit"
          :disabled="passwordMismatch"
          class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
        >
          {{ isEditing ? 'Guardar Cambios' : 'Crear Usuario' }}
        </button>
      </div>
    </form>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-38 Formulario de Usuario:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Los perfiles granulares (Elaboración, Proceso, Firma-Cierre) solo se muestran si el rol es Operador.</li>
          <li>Administrador y Consulta no tienen perfiles adicionales.</li>
          <li>El toggle de estado permite activar/desactivar sin eliminar el usuario.</li>
          <li>En modo edición, la contraseña es opcional (se mantiene si se deja en blanco).</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft } from 'lucide-vue-next'
import type { UserRole } from '../types'

const props = defineProps<{
  userRole: UserRole
  userId?: string | null
}>()

const emit = defineEmits<{
  cancel: []
  save: []
}>()

const isEditing = computed(() => !!props.userId)

const perfilesDisponibles = [
  {
    value: 'Elaboración',
    label: 'Elaboración',
    descripcion: 'Puede crear y editar procesos en estado borrador.',
  },
  {
    value: 'Proceso',
    label: 'Proceso',
    descripcion: 'Puede procesar y ejecutar movimientos sobre bienes.',
  },
  {
    value: 'Firma-Cierre',
    label: 'Firma / Cierre',
    descripcion: 'Puede firmar y cerrar procesos administrativos.',
  },
]

const formData = ref({
  nombre: isEditing.value ? 'Inv. Pedro Condori Mamani' : '',
  username: isEditing.value ? 'p.condori' : '',
  email: isEditing.value ? 'p.condori@uto.edu.bo' : '',
  password: '',
  passwordConfirm: '',
  rol: isEditing.value ? ('Operador' as UserRole | '') : ('' as UserRole | ''),
  perfiles: isEditing.value ? ['Elaboración', 'Proceso'] : [] as string[],
  estado: 'Activo' as 'Activo' | 'Inactivo',
})

const passwordMismatch = computed(() =>
  !!formData.value.password &&
  formData.value.password !== formData.value.passwordConfirm
)

function handleSubmit() {
  if (passwordMismatch.value) return
  emit('save')
}
</script>
