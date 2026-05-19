<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div>
      <h1 class="text-2xl text-gray-900">Actas Administrativas</h1>
      <p class="text-sm text-gray-600 mt-1">Consulta y descarga de actas generadas por el sistema</p>
    </div>

    <!-- Filter bar -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4">
        <!-- Tipo de acta -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Tipo de Acta</label>
          <select v-model="selectedType" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option v-for="(type, idx) in actaTypes" :key="idx" :value="type">{{ type }}</option>
          </select>
        </div>
        <!-- N° Proceso -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">N° Proceso</label>
          <div class="relative">
            <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
            <input v-model="searchProcess" type="text" placeholder="Ej: TRANS-2024-001" class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm" />
          </div>
        </div>
        <!-- Desde -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Desde</label>
          <input v-model="dateFrom" type="date" class="w-full px-3 py-2 border border-gray-300 rounded text-sm" />
        </div>
        <!-- Hasta -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Hasta</label>
          <input v-model="dateTo" type="date" class="w-full px-3 py-2 border border-gray-300 rounded text-sm" />
        </div>
        <!-- Generada por -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Generada por</label>
          <select v-model="selectedUser" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="juan">Juan Pérez</option>
            <option value="maria">María González</option>
            <option value="carlos">Carlos Mamani</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Actas table -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Código</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">N° Proceso</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Generada por</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="acta in mockActas" :key="acta.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ acta.codigo }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ acta.tipo }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ acta.numeroProceso }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ acta.fecha }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ acta.generadaPor }}</td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-2">
                  <button @click="emit('view-acta', acta.id)" class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded" title="Ver">
                    <Eye class="w-4 h-4" />
                  </button>
                  <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded" title="Descargar PDF">
                    <Download class="w-4 h-4" />
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Pagination -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} -
          {{ Math.min(currentPage * itemsPerPage, mockActas.length) }} de
          {{ mockActas.length }} registros
        </div>
        <div class="flex items-center gap-2">
          <button @click="currentPage = Math.max(1, currentPage - 1)" :disabled="currentPage === 1" class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed">
            <ChevronLeft class="w-4 h-4" />
          </button>
          <button class="px-3 py-1 bg-gray-700 text-white rounded">1</button>
          <button @click="currentPage++" class="p-2 border border-gray-300 rounded hover:bg-gray-100">
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Actas List:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Filter bar with acta type (select with all 12 types), process number search, date range, and generated-by user</li>
          <li>Table columns: Código, Tipo, N° Proceso, Fecha, Generada por, Acciones</li>
          <li>Actions: "Ver" (eye icon - opens preview), "Descargar PDF" (download icon)</li>
          <li>Available to all user roles for consultation</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Search, Eye, Download, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ 'view-acta': [id: string] }>()

const selectedType   = ref('')
const searchProcess  = ref('')
const selectedUser   = ref('')
const dateFrom       = ref('')
const dateTo         = ref('')
const currentPage    = ref(1)
const itemsPerPage   = 10

const actaTypes = [
  'ARAF (Alta de Activos Fijos)', 'AIAF (Inventario de AF)', 'ATAF (Transferencia de AF)',
  'ABAF (Baja de AF)', 'ADAF (Descargo de AF)', 'ARMC (Recepción de MC)',
  'AIMC (Inventario de MC)', 'ATMC (Transferencia de MC)', 'ABMC (Baja de MC)',
  'ADMC (Descargo de MC)', 'A.Inv.AF (Acta de Inventario AF)', 'A.Inv.MC (Acta de Inventario MC)',
]

const mockActas = [
  { id: '1', codigo: 'ARAF-2024-001', tipo: 'ARAF (Alta de Activos Fijos)',  numeroProceso: 'ALTA-2024-045',    fecha: '15/03/2024', generadaPor: 'Juan Pérez'    },
  { id: '2', codigo: 'ATAF-2024-012', tipo: 'ATAF (Transferencia de AF)',     numeroProceso: 'TRANS-2024-001',   fecha: '14/03/2024', generadaPor: 'María González' },
  { id: '3', codigo: 'AIAF-2024-003', tipo: 'AIAF (Inventario de AF)',        numeroProceso: 'INV-2024-003',     fecha: '12/03/2024', generadaPor: 'Carlos Mamani'  },
  { id: '4', codigo: 'ARMC-2024-005', tipo: 'ARMC (Recepción de MC)',         numeroProceso: 'ALTA-MC-2024-002', fecha: '10/03/2024', generadaPor: 'Ana Flores'     },
  { id: '5', codigo: 'ABAF-2024-007', tipo: 'ABAF (Baja de AF)',              numeroProceso: 'BAJA-2024-012',    fecha: '08/03/2024', generadaPor: 'Pedro Quispe'   },
]
</script>
