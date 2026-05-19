<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Procesos Administrativos</h1>
      <button
        v-if="canCreate"
        @click="emit('new-process')"
        class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
      >
        <Plus class="w-5 h-5" />
        <span>Crear Nuevo Proceso</span>
      </button>
    </div>

    <!-- Filter bar -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-6 gap-4">
        <!-- N° Proceso -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">N° Proceso</label>
          <div class="relative">
            <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
            <input v-model="searchText" type="text" placeholder="Ej: TRANS-2024-001" class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm" />
          </div>
        </div>
        <!-- Tipo -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Tipo de Proceso</label>
          <select v-model="selectedType" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <optgroup v-for="(cat, idx) in processTypes" :key="idx" :label="cat.category">
              <option v-for="(type, tIdx) in cat.types" :key="tIdx" :value="type">{{ type }}</option>
            </optgroup>
          </select>
        </div>
        <!-- Estado -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Estado</label>
          <select v-model="selectedStatus" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="pendiente">Pendiente</option>
            <option value="procesado">Procesado</option>
            <option value="cerrado">Cerrado</option>
          </select>
        </div>
        <!-- Unidad -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Unidad</label>
          <select class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option value="admin">Administración Central</option>
            <option value="rectorado">Rectorado</option>
            <option value="sistemas">Sistemas</option>
            <option value="rrhh">RRHH</option>
          </select>
        </div>
        <!-- Desde -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Desde</label>
          <input type="date" class="w-full px-3 py-2 border border-gray-300 rounded text-sm" />
        </div>
        <!-- Hasta -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Hasta</label>
          <input type="date" class="w-full px-3 py-2 border border-gray-300 rounded text-sm" />
        </div>
      </div>
      <!-- Segunda fila: Creado por -->
      <div class="mt-4 grid grid-cols-1 md:grid-cols-6 gap-4">
        <div class="md:col-span-2">
          <label class="block text-xs text-gray-600 mb-1">Creado por</label>
          <select class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="juan">Juan Pérez</option>
            <option value="maria">María González</option>
            <option value="carlos">Carlos Mamani</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Data table -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">N° Proceso</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Creado por</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="process in mockProcesses" :key="process.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ process.numero }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ process.tipo }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ process.unidad }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ process.fecha }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getStatusColor(process.estado)]">
                  {{ process.estado }}
                </span>
              </td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ process.creadoPor }}</td>
              <td class="px-4 py-3">
                <button
                  @click="emit('view-process', process.id)"
                  class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded"
                  title="Ver detalle"
                >
                  <Eye class="w-4 h-4" />
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Pagination -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} -
          {{ Math.min(currentPage * itemsPerPage, mockProcesses.length) }} de
          {{ mockProcesses.length }} registros
        </div>
        <div class="flex items-center gap-2">
          <button @click="currentPage = Math.max(1, currentPage - 1)" :disabled="currentPage === 1" class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed">
            <ChevronLeft class="w-4 h-4" />
          </button>
          <div class="flex items-center gap-1">
            <button class="px-3 py-1 bg-gray-700 text-white rounded">1</button>
          </div>
          <button @click="currentPage++" class="p-2 border border-gray-300 rounded hover:bg-gray-100">
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Processes List:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Filter bar with process number search, process type (26 types grouped by category), status, unit, date range picker, and created-by user</li>
          <li>"Crear Nuevo Proceso" button visible only for Administrador and Operador roles</li>
          <li>Table columns: N° Proceso, Tipo, Unidad, Fecha, Estado (colored badge), Creado por, Acciones</li>
          <li>Status badge colors: Pendiente = yellow, Procesado = blue, Cerrado = green</li>
          <li>Process types grouped by category: Activos Fijos, Materiales de Control, Inventarios, Otros</li>
          <li>Date range filter with From and To date pickers for flexible date filtering</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, Plus, Eye, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

type EstadoProceso = 'Pendiente' | 'Procesado' | 'Cerrado'

interface Process {
  id: string; numero: string; tipo: string; unidad: string
  fecha: string; estado: EstadoProceso; creadoPor: string
}

const props = defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ 'view-process': [id: string]; 'new-process': [] }>()

const searchText    = ref('')
const selectedType   = ref('')
const selectedStatus = ref('')
const currentPage    = ref(1)
const itemsPerPage   = 10

const canCreate = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const mockProcesses: Process[] = [
  { id: '1', numero: 'TRANS-2024-001', tipo: 'Transferencia entre Unidades',  unidad: 'Administración Central', fecha: '15/03/2024', estado: 'Pendiente',  creadoPor: 'Juan Pérez'    },
  { id: '2', numero: 'ALTA-2024-045',  tipo: 'Alta de Activos Fijos',          unidad: 'Rectorado',              fecha: '14/03/2024', estado: 'Procesado',  creadoPor: 'María González'},
  { id: '3', numero: 'ASIG-2024-078',  tipo: 'Asignación de Responsable',      unidad: 'Sistemas',               fecha: '12/03/2024', estado: 'Cerrado',    creadoPor: 'Carlos Mamani' },
  { id: '4', numero: 'BAJA-2024-012',  tipo: 'Baja de Activos Fijos',          unidad: 'Almacenes',              fecha: '10/03/2024', estado: 'Cerrado',    creadoPor: 'Ana Flores'    },
  { id: '5', numero: 'INV-2024-003',   tipo: 'Inventario Físico',              unidad: 'Biblioteca',             fecha: '08/03/2024', estado: 'Procesado',  creadoPor: 'Pedro Quispe'  },
  { id: '6', numero: 'TRANS-2024-002', tipo: 'Transferencia entre Unidades',   unidad: 'RRHH',                   fecha: '05/03/2024', estado: 'Pendiente',  creadoPor: 'Sofia Machaca' },
]

const processTypes = [
  { category: 'Activos Fijos',          types: ['Alta de Activos Fijos', 'Baja de Activos Fijos', 'Transferencia entre Unidades', 'Cambio de Responsable'] },
  { category: 'Materiales de Control',  types: ['Alta de Materiales', 'Baja de Materiales', 'Transferencia de Materiales'] },
  { category: 'Inventarios',            types: ['Inventario Físico', 'Inventario Selectivo', 'Inventario Anual'] },
  { category: 'Otros',                  types: ['Asignación de Responsable', 'Revalorización', 'Mantenimiento Mayor'] },
]

function getStatusColor(estado: EstadoProceso) {
  switch (estado) {
    case 'Pendiente':  return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    case 'Procesado':  return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Cerrado':    return 'bg-green-100 text-green-800 border-green-300'
  }
}
</script>
