<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Inventario Físico</h1>
      <button
        v-if="canCreate"
        class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
      >
        <Plus class="w-5 h-5" />
        <span>Nueva Sesión de Inventario</span>
      </button>
    </div>

    <!-- Sessions table -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <table class="w-full">
        <thead class="bg-gray-100 border-b-2 border-gray-300">
          <tr>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Bienes Escaneados</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Faltantes</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
          </tr>
        </thead>
        <tbody class="divide-y divide-gray-200">
          <tr v-for="session in mockSessions" :key="session.id" class="hover:bg-gray-50">
            <td class="px-4 py-3 text-sm text-gray-900">{{ session.fecha }}</td>
            <td class="px-4 py-3 text-sm text-gray-700">{{ session.unidad }}</td>
            <td class="px-4 py-3">
              <span :class="['inline-block px-2 py-1 text-xs border rounded', getStatusColor(session.estado)]">
                {{ session.estado }}
              </span>
            </td>
            <td class="px-4 py-3 text-sm text-gray-700">
              {{ session.bienesEscaneados }} / {{ session.totalBienes }}
              <div class="w-full bg-gray-200 rounded-full h-1.5 mt-1">
                <div
                  class="bg-blue-600 h-1.5 rounded-full"
                  :style="{ width: `${(session.bienesEscaneados / session.totalBienes) * 100}%` }"
                ></div>
              </div>
            </td>
            <td class="px-4 py-3 text-sm">
              <template v-if="session.estado === 'Finalizado'">
                <span :class="session.faltantes > 0 ? 'text-red-600' : 'text-green-600'">
                  {{ session.faltantes }}
                </span>
              </template>
              <span v-else class="text-gray-400">—</span>
            </td>
            <td class="px-4 py-3">
              <div class="flex items-center gap-2">
                <button
                  v-if="session.estado === 'Finalizado'"
                  @click="emit('view-inventory', session.id, 'result')"
                  class="flex items-center gap-1 px-3 py-1 text-sm border border-gray-300 rounded hover:bg-gray-100"
                >
                  <Eye class="w-4 h-4" />
                  <span>Ver resultado</span>
                </button>
                <button
                  v-else
                  @click="emit('view-inventory', session.id, 'scan')"
                  class="flex items-center gap-1 px-3 py-1 text-sm bg-blue-600 text-white rounded hover:bg-blue-700"
                >
                  <PlayCircle class="w-4 h-4" />
                  <span>Continuar escaneo</span>
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Inventory Sessions List:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>"Nueva Sesión de Inventario" button at top right for authorized roles</li>
          <li>Table columns: Fecha, Unidad, Estado (colored badge), Bienes Escaneados (with progress bar), Faltantes, Acciones</li>
          <li>Estado badge colors: "En curso" = blue, "Finalizado" = green</li>
          <li>Bienes Escaneados shows count and visual progress bar (e.g., 47/120)</li>
          <li>Faltantes shows count only for finalized inventories (red if > 0, green if 0)</li>
          <li>Actions: "Ver resultado" button for finalized sessions, "Continuar escaneo" for in-progress sessions</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { Plus, Eye, PlayCircle } from 'lucide-vue-next'
import type { UserRole } from '../types'

type EstadoSesion = 'En curso' | 'Finalizado'

interface InventorySession {
  id: string; fecha: string; unidad: string; estado: EstadoSesion
  bienesEscaneados: number; totalBienes: number; faltantes: number
}

const props = defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ 'view-inventory': [id: string, tab: 'scan' | 'result'] }>()

const canCreate = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const mockSessions: InventorySession[] = [
  { id: '1', fecha: '18/03/2024', unidad: 'Administración Central', estado: 'En curso',   bienesEscaneados: 47,  totalBienes: 120, faltantes: 0 },
  { id: '2', fecha: '15/03/2024', unidad: 'Rectorado',              estado: 'Finalizado', bienesEscaneados: 85,  totalBienes: 85,  faltantes: 3 },
  { id: '3', fecha: '12/03/2024', unidad: 'Sistemas',               estado: 'Finalizado', bienesEscaneados: 42,  totalBienes: 42,  faltantes: 1 },
  { id: '4', fecha: '08/03/2024', unidad: 'Biblioteca',             estado: 'Finalizado', bienesEscaneados: 156, totalBienes: 156, faltantes: 5 },
]

function getStatusColor(estado: EstadoSesion) {
  return estado === 'En curso'
    ? 'bg-blue-100 text-blue-800 border-blue-300'
    : 'bg-green-100 text-green-800 border-green-300'
}
</script>
