<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Materiales de Control</h1>
      <button
        v-if="canEdit"
        class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
      >
        <Plus class="w-5 h-5" />
        <span>Registrar Nuevo Material</span>
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
              placeholder="NIM o descripción..."
              class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
            />
          </div>
        </div>
        <!-- Grupo -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Grupo (Cod. 20)</label>
          <select v-model="selectedGroup" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="20-01">20-01 Muebles</option>
            <option value="20-02">20-02 Maquinaria</option>
            <option value="20-03">20-03 Herramientas</option>
            <option value="20-04">20-04 Equipos Varios</option>
          </select>
        </div>
        <!-- Unidad -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Unidad</label>
          <select class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option value="admin">Administración Central</option>
            <option value="taller">Taller Mecánica</option>
            <option value="almacenes">Almacenes</option>
            <option value="mantenimiento">Mantenimiento</option>
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
              <th class="px-4 py-3 text-left text-xs text-gray-700">NIM</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Código</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Grupo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr
              v-for="material in mockMaterials"
              :key="material.id"
              class="hover:bg-gray-50"
            >
              <td class="px-4 py-3 text-sm text-gray-900">{{ material.nim }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ material.codigo }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ material.descripcion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ material.grupo }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ material.unidad }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ material.responsable }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getStatusColor(material.estado)]">
                  {{ material.estado }}
                </span>
              </td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-2">
                  <button
                    @click="emit('view-material', material.id)"
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
          {{ Math.min(currentPage * itemsPerPage, mockMaterials.length) }} de
          {{ mockMaterials.length }} registros
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
        <strong>Wireframe Notes - Control Materials List:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Same layout as Fixed Assets List but for control materials (Group 20 items)</li>
          <li>Identifier column shows NIM (Número de Inventario de Material) instead of NIA</li>
          <li>Group filter uses Group 20 codes: 20-01 Muebles, 20-02 Maquinaria, 20-03 Herramientas, 20-04 Equipos Varios</li>
          <li>No depreciation-related columns (materials don't depreciate like fixed assets)</li>
          <li>Primary button reads "Registrar Nuevo Material" instead of "Registrar Nuevo Activo"</li>
          <li>Same role-based access controls as Fixed Assets List</li>
          <li>Pagination controls and status color coding identical to Fixed Assets List</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, Plus, Eye, Edit, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

type EstadoMaterial = 'Activo' | 'Baja' | 'Transferido' | 'Faltante'

interface Material {
  id: string; nim: string; codigo: string; descripcion: string
  grupo: string; unidad: string; responsable: string; estado: EstadoMaterial
}

const props = defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ 'view-material': [id: string] }>()

const searchText   = ref('')
const selectedGroup  = ref('')
const selectedStatus = ref('')
const currentPage    = ref(1)
const itemsPerPage   = 10

const canEdit = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const mockMaterials: Material[] = [
  { id: '1', nim: 'NIM-2024-0001', codigo: '20-01-M12', descripcion: 'Escritorio Metálico Simple',       grupo: '20-01 Muebles',       unidad: 'Administración Central', responsable: 'Pedro Quispe Flores',    estado: 'Activo'     },
  { id: '2', nim: 'NIM-2024-0002', codigo: '20-02-H45', descripcion: 'Taladro Manual Black & Decker',    grupo: '20-02 Maquinaria',    unidad: 'Taller Mecánica',        responsable: 'Roberto Vargas Nina',    estado: 'Activo'     },
  { id: '3', nim: 'NIM-2023-0234', codigo: '20-01-S23', descripcion: 'Silla Plástica Apilable',          grupo: '20-01 Muebles',       unidad: 'Auditorio',              responsable: 'Lucia Apaza Condori',    estado: 'Activo'     },
  { id: '4', nim: 'NIM-2023-0189', codigo: '20-03-E12', descripcion: 'Destornillador Set 6 Piezas',      grupo: '20-03 Herramientas',  unidad: 'Mantenimiento',          responsable: 'Carlos Mamani López',    estado: 'Activo'     },
  { id: '5', nim: 'NIM-2022-0456', codigo: '20-01-M89', descripcion: 'Estante Metálico 5 Niveles',       grupo: '20-01 Muebles',       unidad: 'Almacenes',              responsable: 'Sofia Machaca Poma',     estado: 'Transferido'},
  { id: '6', nim: 'NIM-2024-0078', codigo: '20-04-V34', descripcion: 'Ventilador de Pedestal',           grupo: '20-04 Equipos Varios',unidad: 'Biblioteca',             responsable: 'Ana Flores Choque',      estado: 'Activo'     },
  { id: '7', nim: 'NIM-2021-0312', codigo: '20-02-M56', descripcion: 'Sierra Circular Manual',           grupo: '20-02 Maquinaria',    unidad: 'Taller Carpintería',     responsable: 'Juan Pérez Mamani',      estado: 'Baja'       },
  { id: '8', nim: 'NIM-2024-0145', codigo: '20-01-P78', descripcion: 'Pizarra Acrílica 120x80 cm',       grupo: '20-01 Muebles',       unidad: 'Aulas',                  responsable: 'María González Quispe',  estado: 'Activo'     },
]

function getStatusColor(estado: EstadoMaterial) {
  switch (estado) {
    case 'Activo':      return 'bg-green-100 text-green-800 border-green-300'
    case 'Baja':        return 'bg-red-100 text-red-800 border-red-300'
    case 'Transferido': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Faltante':    return 'bg-yellow-100 text-yellow-800 border-yellow-300'
  }
}
</script>
