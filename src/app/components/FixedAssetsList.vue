<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Activos Fijos</h1>
      <button
        v-if="canEdit"
        @click="emit('new-asset')"
        class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
      >
        <Plus class="w-5 h-5" />
        <span>Registrar Nuevo Activo</span>
      </button>
    </div>

    <!-- Filter bar -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4">
        <!-- Search -->
        <div class="lg:col-span-2">
          <label class="block text-xs text-gray-600 mb-1">Buscar</label>
          <div class="relative">
            <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
            <input
              v-model="searchText"
              type="text"
              placeholder="NIA o descripción..."
              class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
            />
          </div>
        </div>
        <!-- Categoría -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Categoría</label>
          <select v-model="selectedCategory" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option value="muebles">Muebles</option>
            <option value="equipos">Equipos de Computación</option>
            <option value="vehiculos">Vehículos</option>
            <option value="maquinaria">Maquinaria</option>
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
          </select>
        </div>
        <!-- Estado -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Estado</label>
          <select v-model="selectedStatus" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="activo">Activo</option>
            <option value="baja">Baja</option>
            <option value="transferido">Transferido</option>
            <option value="faltante">Faltante</option>
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
              <th class="px-4 py-3 text-left text-xs text-gray-700">NIA</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Código</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Categoría</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="asset in mockAssets" :key="asset.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ asset.nia }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.codigo }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ asset.descripcion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.categoria }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.unidad }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.responsable }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getStatusColor(asset.estado)]">
                  {{ asset.estado }}
                </span>
              </td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-2">
                  <button
                    @click="emit('view-asset', asset.id)"
                    class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded"
                    title="Ver detalle"
                  >
                    <Eye class="w-4 h-4" />
                  </button>
                  <button
                    v-if="canEdit"
                    class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded"
                    title="Editar"
                  >
                    <Edit class="w-4 h-4" />
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
          {{ Math.min(currentPage * itemsPerPage, mockAssets.length) }} de
          {{ mockAssets.length }} registros
        </div>
        <div class="flex items-center gap-2">
          <button
            @click="currentPage = Math.max(1, currentPage - 1)"
            :disabled="currentPage === 1"
            class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <ChevronLeft class="w-4 h-4" />
          </button>
          <div class="flex items-center gap-1">
            <button class="px-3 py-1 bg-gray-700 text-white rounded">1</button>
            <button class="px-3 py-1 border border-gray-300 rounded hover:bg-gray-100">2</button>
            <button class="px-3 py-1 border border-gray-300 rounded hover:bg-gray-100">3</button>
          </div>
          <button
            @click="currentPage++"
            class="p-2 border border-gray-300 rounded hover:bg-gray-100"
          >
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Fixed Assets List:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Filter bar with text search (NIA/description) and dropdown filters for Category, Unit, Responsible, and Status</li>
          <li>"Registrar Nuevo Activo" button visible only for Administrador and Operador roles</li>
          <li>Paginated data table with 8 columns: NIA, Código, Descripción, Categoría, Unidad, Responsable, Estado (colored badge), Acciones</li>
          <li>Action icons: Eye (view detail), Edit (edit - only for Admin/Operator)</li>
          <li>Pagination controls at bottom showing current range and page numbers</li>
          <li>Status badges use color coding: Green (Activo), Red (Baja), Blue (Transferido), Yellow (Faltante)</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, Plus, Eye, Edit, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

type EstadoAsset = 'Activo' | 'Baja' | 'Transferido' | 'Faltante'

interface Asset {
  id: string; nia: string; codigo: string; descripcion: string
  categoria: string; unidad: string; responsable: string; estado: EstadoAsset
}

const props = defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ 'view-asset': [id: string]; 'new-asset': [] }>()

const searchText     = ref('')
const selectedCategory = ref('')
const selectedStatus   = ref('')
const currentPage      = ref(1)
const itemsPerPage     = 10

const canEdit = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const mockAssets: Asset[] = [
  { id: '1', nia: 'AF-2024-0001', codigo: '03-01-A01', descripcion: 'Computadora Portátil HP ProBook 450',  categoria: 'Equipos de Computación', unidad: 'Administración Central', responsable: 'Juan Pérez Mamani',      estado: 'Activo'     },
  { id: '2', nia: 'AF-2024-0002', codigo: '03-02-M15', descripcion: 'Escritorio Ejecutivo de Madera',        categoria: 'Muebles',                unidad: 'Rectorado',              responsable: 'María González Quispe',  estado: 'Activo'     },
  { id: '3', nia: 'AF-2023-0145', codigo: '03-03-V02', descripcion: 'Vehículo Toyota Hilux 4x4',             categoria: 'Vehículos',              unidad: 'Transporte',             responsable: 'Carlos Mamani López',    estado: 'Activo'     },
  { id: '4', nia: 'AF-2022-0089', codigo: '03-01-A45', descripcion: 'Impresora Multifuncional Canon',         categoria: 'Equipos de Computación', unidad: 'Sistemas',               responsable: 'Ana Flores Choque',      estado: 'Transferido'},
  { id: '5', nia: 'AF-2021-0234', codigo: '03-04-M89', descripcion: 'Torno Industrial CNC',                  categoria: 'Maquinaria',             unidad: 'Taller Mecánica',        responsable: 'Roberto Vargas Nina',    estado: 'Activo'     },
  { id: '6', nia: 'AF-2020-0156', codigo: '03-02-M34', descripcion: 'Silla Giratoria Ergonómica',            categoria: 'Muebles',                unidad: 'Recursos Humanos',       responsable: 'Lucia Apaza Condori',    estado: 'Baja'       },
  { id: '7', nia: 'AF-2024-0078', codigo: '03-01-A12', descripcion: 'Proyector Epson PowerLite',             categoria: 'Equipos de Computación', unidad: 'Aula Magna',             responsable: 'Pedro Ticona Quispe',    estado: 'Activo'     },
  { id: '8', nia: 'AF-2023-0201', codigo: '03-05-E23', descripcion: 'Aire Acondicionado Split 18000 BTU',    categoria: 'Equipos Varios',         unidad: 'Biblioteca',             responsable: 'Sofia Machaca Poma',     estado: 'Faltante'   },
]

function getStatusColor(estado: EstadoAsset) {
  switch (estado) {
    case 'Activo':      return 'bg-green-100 text-green-800 border-green-300'
    case 'Baja':        return 'bg-red-100 text-red-800 border-red-300'
    case 'Transferido': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Faltante':    return 'bg-yellow-100 text-yellow-800 border-yellow-300'
  }
}
</script>
