<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Transferir Rango de Identificadores</h1>
    </div>

    <form @submit.prevent="handleSubmit" class="space-y-6">
      <!-- Rango origen (solo lectura) -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Rango Origen</h2>
        </div>
        <div class="p-6">
          <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
            <div>
              <label class="block text-xs text-gray-600 mb-1">Inventariador Actual</label>
              <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-900">
                {{ rangoOrigen.inventariador }}
              </div>
            </div>
            <div>
              <label class="block text-xs text-gray-600 mb-1">Tipo</label>
              <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-900">
                {{ rangoOrigen.tipo }}
              </div>
            </div>
            <div>
              <label class="block text-xs text-gray-600 mb-1">Rango Completo</label>
              <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-900 font-mono">
                {{ rangoOrigen.desde }} — {{ rangoOrigen.hasta }}
              </div>
            </div>
            <div>
              <label class="block text-xs text-gray-600 mb-1">Número Actual / Disponibles</label>
              <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-900 font-mono">
                {{ rangoOrigen.actual }} / {{ rangoOrigen.hasta - rangoOrigen.actual + 1 }} disp.
              </div>
            </div>
          </div>

          <!-- Barra de consumo del rango origen -->
          <div class="mt-4">
            <div class="flex items-center justify-between text-xs text-gray-600 mb-1">
              <span>Consumo del rango</span>
              <span>{{ consumoPct }}% utilizado</span>
            </div>
            <div class="w-full bg-gray-200 rounded-full h-3">
              <div
                class="h-3 rounded-full"
                :class="consumoPct >= 90 ? 'bg-red-500' : consumoPct >= 70 ? 'bg-yellow-500' : 'bg-gray-700'"
                :style="{ width: `${consumoPct}%` }"
              />
            </div>
          </div>
        </div>
      </div>

      <!-- Números a transferir -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Números a Transferir</h2>
        </div>
        <div class="p-6 space-y-6">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div>
              <label class="block text-sm text-gray-700 mb-2">
                Desde <span class="text-red-600">*</span>
              </label>
              <input
                v-model.number="formData.transferDesde"
                type="number"
                :min="rangoOrigen.actual"
                :max="rangoOrigen.hasta"
                :placeholder="`Mín: ${rangoOrigen.actual}`"
                class="w-full px-3 py-2 border-2 border-gray-300 rounded"
                required
              />
              <p class="text-xs text-gray-600 mt-1">Solo se pueden transferir números no utilizados (desde {{ rangoOrigen.actual }}).</p>
            </div>
            <div>
              <label class="block text-sm text-gray-700 mb-2">
                Hasta <span class="text-red-600">*</span>
              </label>
              <input
                v-model.number="formData.transferHasta"
                type="number"
                :min="formData.transferDesde || rangoOrigen.actual"
                :max="rangoOrigen.hasta"
                :placeholder="`Máx: ${rangoOrigen.hasta}`"
                class="w-full px-3 py-2 border-2 border-gray-300 rounded"
                required
              />
            </div>
          </div>

          <!-- Resumen de la transferencia -->
          <div v-if="cantidadTransferir > 0" class="px-4 py-3 bg-gray-50 border border-gray-300 rounded">
            <div class="grid grid-cols-3 gap-4 text-center">
              <div>
                <div class="text-xs text-gray-600 mb-1">Números a transferir</div>
                <div class="text-xl text-gray-900">{{ cantidadTransferir }}</div>
              </div>
              <div>
                <div class="text-xs text-gray-600 mb-1">Quedan en origen</div>
                <div class="text-xl text-gray-900">{{ rangoOrigen.hasta - (formData.transferHasta || rangoOrigen.actual) }}</div>
              </div>
              <div>
                <div class="text-xs text-gray-600 mb-1">Rango transferido</div>
                <div class="text-sm text-gray-900 font-mono mt-1">{{ formData.transferDesde }} — {{ formData.transferHasta }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Funcionario destino -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Funcionario Destino</h2>
        </div>
        <div class="p-6 grid grid-cols-1 md:grid-cols-2 gap-6">
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Inventariador Destino <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.inventariadorDestino" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione el funcionario...</option>
              <option value="Tec. Rodrigo Choque Apaza">Tec. Rodrigo Choque Apaza</option>
              <option value="Lic. María Flores Quispe">Lic. María Flores Quispe</option>
              <option value="Ing. Carlos Mamani López">Ing. Carlos Mamani López</option>
              <option value="Inv. Ana Gutiérrez Salinas">Inv. Ana Gutiérrez Salinas</option>
            </select>
          </div>
          <div>
            <label class="block text-sm text-gray-700 mb-2">Fecha de Transferencia <span class="text-red-600">*</span></label>
            <input v-model="formData.fecha" type="date" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required />
          </div>
          <div class="md:col-span-2">
            <label class="block text-sm text-gray-700 mb-2">Observaciones</label>
            <textarea
              v-model="formData.observaciones"
              rows="3"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              placeholder="Motivo de la transferencia..."
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
          :disabled="!formularioValido"
          class="flex items-center gap-2 px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
        >
          <ArrowLeftRight class="w-4 h-4" />
          <span>Registrar Transferencia</span>
        </button>
      </div>
    </form>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-35 Transferir Rango:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Solo se pueden transferir números no utilizados (desde el número actual en adelante).</li>
          <li>El rango origen se muestra en modo solo lectura como referencia.</li>
          <li>El resumen se actualiza en tiempo real al ingresar los números.</li>
          <li>La transferencia queda registrada en el historial del rango (TransferenciaRangoIdentificadores).</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, ArrowLeftRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ cancel: []; save: [] }>()

const rangoOrigen = {
  inventariador: 'Inv. Pedro Condori Mamani',
  tipo: 'NIA',
  desde: 1001,
  hasta: 1100,
  actual: 1067,
}

const consumoPct = computed(() =>
  Math.round(((rangoOrigen.actual - rangoOrigen.desde) / (rangoOrigen.hasta - rangoOrigen.desde)) * 100)
)

const formData = ref({
  transferDesde: null as number | null,
  transferHasta: null as number | null,
  inventariadorDestino: '',
  fecha: '',
  observaciones: '',
})

const cantidadTransferir = computed(() => {
  if (!formData.value.transferDesde || !formData.value.transferHasta) return 0
  if (formData.value.transferHasta < formData.value.transferDesde) return 0
  return formData.value.transferHasta - formData.value.transferDesde + 1
})

const formularioValido = computed(() =>
  cantidadTransferir.value > 0 &&
  formData.value.inventariadorDestino &&
  formData.value.fecha
)

function handleSubmit() {
  if (!formularioValido.value) return
  emit('save')
}
</script>
