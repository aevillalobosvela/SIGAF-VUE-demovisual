<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Asignar Nuevo Rango</h1>
    </div>

    <form @submit.prevent="handleSubmit" class="space-y-6">
      <!-- Datos del rango -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Datos del Rango</h2>
        </div>
        <div class="p-6 space-y-6">
          <!-- Tipo: radio buttons -->
          <div>
            <label class="block text-sm text-gray-700 mb-3">
              Tipo de Identificador <span class="text-red-600">*</span>
            </label>
            <div class="flex items-center gap-6">
              <label class="flex items-center gap-3 p-4 border-2 rounded cursor-pointer transition-colors flex-1"
                :class="formData.tipo === 'NIA' ? 'border-gray-700 bg-gray-50' : 'border-gray-300 hover:bg-gray-50'"
              >
                <input type="radio" v-model="formData.tipo" value="NIA" class="w-4 h-4" />
                <div>
                  <div class="text-sm text-gray-900">NIA — Número de Inventario de Activo</div>
                  <div class="text-xs text-gray-600 mt-0.5">Para Activos Fijos (grupos 01–13)</div>
                </div>
              </label>
              <label class="flex items-center gap-3 p-4 border-2 rounded cursor-pointer transition-colors flex-1"
                :class="formData.tipo === 'NIM' ? 'border-gray-700 bg-gray-50' : 'border-gray-300 hover:bg-gray-50'"
              >
                <input type="radio" v-model="formData.tipo" value="NIM" class="w-4 h-4" />
                <div>
                  <div class="text-sm text-gray-900">NIM — Número de Inventario de Material</div>
                  <div class="text-xs text-gray-600 mt-0.5">Para Materiales de Control (grupo 20)</div>
                </div>
              </label>
            </div>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div>
              <label class="block text-sm text-gray-700 mb-2">
                Inventariador <span class="text-red-600">*</span>
              </label>
              <select v-model="formData.inventariador" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
                <option value="">Seleccione el inventariador...</option>
                <option value="Inv. Pedro Condori Mamani">Inv. Pedro Condori Mamani</option>
                <option value="Tec. Rodrigo Choque Apaza">Tec. Rodrigo Choque Apaza</option>
                <option value="Lic. María Flores Quispe">Lic. María Flores Quispe</option>
                <option value="Ing. Carlos Mamani López">Ing. Carlos Mamani López</option>
              </select>
            </div>
            <div>
              <label class="block text-sm text-gray-700 mb-2">Fecha de Asignación <span class="text-red-600">*</span></label>
              <input v-model="formData.fecha" type="date" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required />
            </div>
            <div>
              <label class="block text-sm text-gray-700 mb-2">
                Número Desde <span class="text-red-600">*</span>
              </label>
              <input
                v-model.number="formData.desde"
                type="number"
                min="1"
                placeholder="Ej: 1301"
                class="w-full px-3 py-2 border-2 border-gray-300 rounded"
                required
              />
              <p class="text-xs text-gray-600 mt-1">Último rango asignado terminó en: <strong>1300</strong></p>
            </div>
            <div>
              <label class="block text-sm text-gray-700 mb-2">
                Número Hasta <span class="text-red-600">*</span>
              </label>
              <input
                v-model.number="formData.hasta"
                type="number"
                :min="(formData.desde ?? 0) + 1"
                placeholder="Ej: 1400"
                class="w-full px-3 py-2 border-2 border-gray-300 rounded"
                required
              />
            </div>
          </div>

          <!-- Resumen del rango -->
          <div v-if="formData.desde && formData.hasta && formData.hasta > (formData.desde ?? 0)" class="px-4 py-3 bg-gray-50 border border-gray-300 rounded">
            <div class="grid grid-cols-3 gap-4 text-center">
              <div>
                <div class="text-xs text-gray-600 mb-1">Total identificadores</div>
                <div class="text-xl text-gray-900">{{ formData.hasta - formData.desde + 1 }}</div>
              </div>
              <div>
                <div class="text-xs text-gray-600 mb-1">Primer número</div>
                <div class="text-xl text-gray-900 font-mono">{{ formData.desde }}</div>
              </div>
              <div>
                <div class="text-xs text-gray-600 mb-1">Último número</div>
                <div class="text-xl text-gray-900 font-mono">{{ formData.hasta }}</div>
              </div>
            </div>
          </div>
          <div v-else-if="formData.desde && formData.hasta && formData.hasta <= (formData.desde ?? 0)" class="px-4 py-3 bg-red-50 border border-red-300 rounded text-sm text-red-700">
            El número "Hasta" debe ser mayor que el número "Desde".
          </div>

          <div>
            <label class="block text-sm text-gray-700 mb-2">Observaciones</label>
            <textarea
              v-model="formData.observaciones"
              rows="3"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              placeholder="Notas adicionales sobre la asignación..."
            />
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
          :disabled="!rangoValido"
          class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
        >
          Asignar Rango
        </button>
      </div>
    </form>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-34 Asignar Rango:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>El sistema sugiere el número "Desde" basándose en el último rango asignado del mismo tipo.</li>
          <li>El resumen se actualiza en tiempo real al ingresar los números.</li>
          <li>No se pueden asignar rangos que se superpongan con rangos existentes.</li>
          <li>Solo accesible para Administrador y Operador.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ cancel: []; save: [] }>()

const formData = ref({
  tipo: 'NIA' as 'NIA' | 'NIM',
  inventariador: '',
  fecha: '',
  desde: null as number | null,
  hasta: null as number | null,
  observaciones: '',
})

const rangoValido = computed(() =>
  formData.value.tipo &&
  formData.value.inventariador &&
  formData.value.fecha &&
  formData.value.desde &&
  formData.value.hasta &&
  formData.value.hasta > formData.value.desde
)

function handleSubmit() {
  if (!rangoValido.value) return
  emit('save')
}
</script>
