<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center justify-between">
      <div>
        <h1 class="text-2xl text-gray-900">Gestión de Rangos NIA / NIM</h1>
        <p class="text-sm text-gray-600 mt-0.5">Control de identificadores asignados a inventariadores</p>
      </div>
      <button
        v-if="userRole !== 'Consulta'"
        @click="emit('new-range')"
        class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
      >
        <Plus class="w-5 h-5" />
        <span>Asignar Nuevo Rango</span>
      </button>
    </div>

    <!-- Filtros -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
        <div class="relative">
          <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
          <input
            v-model="filters.inventariador"
            type="text"
            placeholder="Inventariador..."
            class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
          />
        </div>
        <select v-model="filters.tipo" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
          <option value="">NIA y NIM</option>
          <option value="NIA">Solo NIA</option>
          <option value="NIM">Solo NIM</option>
        </select>
        <select v-model="filters.estado" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
          <option value="">Todos los estados</option>
          <option value="Activo">Activo</option>
          <option value="Agotado">Agotado</option>
          <option value="Transferido">Transferido</option>
        </select>
        <button
          @click="filters = { inventariador: '', tipo: '', estado: '' }"
          class="flex items-center gap-2 px-4 py-2 border border-gray-300 rounded text-sm hover:bg-gray-100 transition-colors"
        >
          <X class="w-4 h-4" />
          <span>Limpiar</span>
        </button>
      </div>
    </div>

    <!-- Tabla -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Inventariador</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Desde</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Hasta</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Actual</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Disponibles</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700 w-48">Consumo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="rango in filteredRangos" :key="rango.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ rango.inventariador }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', rango.tipo === 'NIA' ? 'bg-blue-100 text-blue-800 border-blue-300' : 'bg-orange-100 text-orange-800 border-orange-300']">
                  {{ rango.tipo }}
                </span>
              </td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right font-mono">{{ rango.desde }}</td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right font-mono">{{ rango.hasta }}</td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right font-mono">{{ rango.actual }}</td>
              <td class="px-4 py-3 text-sm text-right" :class="(rango.hasta - rango.actual + 1) <= 10 ? 'text-red-600' : 'text-gray-700'">
                {{ rango.hasta - rango.actual + 1 }}
              </td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-2">
                  <div class="flex-1 bg-gray-200 rounded-full h-2">
                    <div
                      class="h-2 rounded-full transition-all"
                      :class="consumoPct(rango) >= 90 ? 'bg-red-500' : consumoPct(rango) >= 70 ? 'bg-yellow-500' : 'bg-gray-700'"
                      :style="{ width: `${consumoPct(rango)}%` }"
                    />
                  </div>
                  <span class="text-xs text-gray-600 w-8 text-right">{{ consumoPct(rango) }}%</span>
                </div>
              </td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getEstadoColor(rango.estado)]">
                  {{ rango.estado }}
                </span>
              </td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-1">
                  <button
                    v-if="userRole !== 'Consulta' && rango.estado === 'Activo'"
                    @click="emit('transfer-range', rango.id)"
                    class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded"
                    title="Transferir rango"
                  >
                    <ArrowLeftRight class="w-4 h-4" />
                  </button>
                  <button
                    class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded"
                    title="Ver historial"
                  >
                    <History class="w-4 h-4" />
                  </button>
                </div>
              </td>
            </tr>
            <tr v-if="filteredRangos.length === 0">
              <td colspan="9" class="px-4 py-8 text-center text-sm text-gray-400">
                No se encontraron rangos con los filtros aplicados.
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Paginación -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ filteredRangos.length }} de {{ mockRangos.length }} rangos
        </div>
        <div class="flex items-center gap-2">
          <button class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50" disabled>
            <ChevronLeft class="w-4 h-4" />
          </button>
          <button class="px-3 py-1 bg-gray-700 text-white rounded text-sm">1</button>
          <button class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50" disabled>
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-33 Rangos NIA/NIM:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Los rangos se consumen de menor a mayor (RN-10).</li>
          <li>La barra de consumo se vuelve amarilla al 70% y roja al 90%.</li>
          <li>El campo "Disponibles" se resalta en rojo si quedan 10 o menos.</li>
          <li>El inventariador solo puede ver los registros que él mismo capturó (RN-11).</li>
          <li>El botón de transferir solo aparece para rangos en estado Activo.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, X, Plus, ArrowLeftRight, History, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

const props = defineProps<{ userRole: UserRole }>()
const emit  = defineEmits<{ 'new-range': []; 'transfer-range': [id: string] }>()

interface Rango {
  id: string; inventariador: string; tipo: 'NIA' | 'NIM'
  desde: number; hasta: number; actual: number; estado: 'Activo' | 'Agotado' | 'Transferido'
}

const mockRangos = ref<Rango[]>([
  { id: '1', inventariador: 'Inv. Pedro Condori Mamani',  tipo: 'NIA', desde: 1001, hasta: 1100, actual: 1067, estado: 'Activo'      },
  { id: '2', inventariador: 'Inv. Pedro Condori Mamani',  tipo: 'NIM', desde: 2001, hasta: 2050, actual: 2048, estado: 'Activo'      },
  { id: '3', inventariador: 'Tec. Rodrigo Choque Apaza',  tipo: 'NIA', desde: 1101, hasta: 1200, actual: 1145, estado: 'Activo'      },
  { id: '4', inventariador: 'Tec. Rodrigo Choque Apaza',  tipo: 'NIM', desde: 2051, hasta: 2100, actual: 2100, estado: 'Agotado'     },
  { id: '5', inventariador: 'Lic. María Flores Quispe',   tipo: 'NIA', desde: 901,  hasta: 1000, actual: 1000, estado: 'Agotado'     },
  { id: '6', inventariador: 'Ing. Carlos Mamani López',   tipo: 'NIA', desde: 1201, hasta: 1300, actual: 1201, estado: 'Activo'      },
  { id: '7', inventariador: 'Inv. Pedro Condori Mamani',  tipo: 'NIA', desde: 801,  hasta: 900,  actual: 900,  estado: 'Transferido' },
])

const filters = ref({ inventariador: '', tipo: '', estado: '' })

const filteredRangos = computed(() =>
  mockRangos.value.filter((r) => {
    const matchInv    = !filters.value.inventariador || r.inventariador.toLowerCase().includes(filters.value.inventariador.toLowerCase())
    const matchTipo   = !filters.value.tipo   || r.tipo === filters.value.tipo
    const matchEstado = !filters.value.estado || r.estado === filters.value.estado
    return matchInv && matchTipo && matchEstado
  })
)

const consumoPct  = (r: Rango) => Math.round(((r.actual - r.desde) / (r.hasta - r.desde)) * 100)

function getEstadoColor(estado: string) {
  switch (estado) {
    case 'Activo':      return 'bg-green-100 text-green-800 border-green-300'
    case 'Agotado':     return 'bg-red-100 text-red-800 border-red-300'
    case 'Transferido': return 'bg-blue-100 text-blue-800 border-blue-300'
    default:            return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}
</script>
