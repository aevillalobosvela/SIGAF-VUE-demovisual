<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div>
      <h1 class="text-2xl text-gray-900 mb-2">Depreciación de Activos Fijos</h1>
      <p class="text-sm text-gray-600">Gestión de depreciación según Decreto Supremo 24051 - Método Lineal</p>
    </div>

    <!-- Info banner -->
    <div class="bg-blue-50 border-2 border-blue-200 rounded-lg p-4">
      <div class="flex items-start gap-3">
        <Calendar class="w-5 h-5 text-blue-600 flex-shrink-0 mt-0.5" />
        <div class="text-sm text-blue-800">
          <strong>Último cálculo de depreciación:</strong> Marzo 2024 (31/03/2024)<br />
          Próximo cálculo programado: Abril 2024
        </div>
      </div>
    </div>

    <!-- Action cards grid -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <button
        v-for="(card, idx) in actionCards"
        :key="idx"
        @click="!isDisabled(card) && handleCardClick(card.action)"
        :disabled="isDisabled(card)"
        :class="[
          card.color, 'border-2 rounded-lg p-6 text-left transition-all',
          isDisabled(card) ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer'
        ]"
      >
        <div class="flex items-start gap-4">
          <div :class="[card.iconColor, 'flex-shrink-0']">
            <component :is="card.icon" class="w-12 h-12" />
          </div>
          <div class="flex-1">
            <h3 class="text-lg text-gray-900 mb-2">{{ card.title }}</h3>
            <p class="text-sm text-gray-600">{{ card.description }}</p>
            <p v-if="isDisabled(card)" class="text-xs text-red-600 mt-2">
              Requiere permisos de Administrador u Operador
            </p>
          </div>
        </div>
      </button>
    </div>

    <!-- Summary stats -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <h2 class="text-lg text-gray-900 mb-4">Resumen de Depreciación</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <div>
          <div class="text-sm text-gray-600 mb-1">Total Activos Depreciables</div>
          <div class="text-2xl text-gray-900">1,247</div>
        </div>
        <div>
          <div class="text-sm text-gray-600 mb-1">Valor Total Original</div>
          <div class="text-2xl text-gray-900">Bs. 8,450,230.50</div>
        </div>
        <div>
          <div class="text-sm text-gray-600 mb-1">Depreciación Acumulada</div>
          <div class="text-2xl text-gray-900">Bs. 2,156,890.25</div>
        </div>
      </div>
    </div>

    <!-- Recent depreciation runs -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
        <h2 class="text-lg text-gray-900">Cálculos Recientes</h2>
      </div>
      <table class="w-full">
        <thead class="bg-gray-100 border-b-2 border-gray-300">
          <tr>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Período</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha de Cálculo</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Activos Procesados</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Monto Depreciado</th>
            <th class="px-4 py-3 text-left text-xs text-gray-700">Ejecutado por</th>
          </tr>
        </thead>
        <tbody class="divide-y divide-gray-200">
          <tr v-for="(run, idx) in recentRuns" :key="idx" class="hover:bg-gray-50">
            <td class="px-4 py-3 text-sm text-gray-900">{{ run.periodo }}</td>
            <td class="px-4 py-3 text-sm text-gray-700">{{ run.fecha }}</td>
            <td class="px-4 py-3 text-sm text-gray-700">{{ run.activos }}</td>
            <td class="px-4 py-3 text-sm text-gray-700">{{ run.monto }}</td>
            <td class="px-4 py-3 text-sm text-gray-700">{{ run.ejecutadoPor }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Depreciation Panel:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Hub screen with 4 large action cards in 2x2 grid layout</li>
          <li>Card 1: "Ejecutar Cálculo" (blue) - requires admin/operator role</li>
          <li>Card 2: "Ver Registros" (green) - available to all roles</li>
          <li>Card 3: "Registrar Revalúo" (orange) - requires admin/operator role</li>
          <li>Card 4: "Ver Asientos Contables" (purple) - available to all roles</li>
          <li>Info banner shows last calculation date and next scheduled calculation</li>
          <li>Summary stats and recent calculations table</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { Calendar, Table2, RefreshCw, BookOpen } from 'lucide-vue-next'
import type { UserRole, Screen } from '../types'

const props = defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ navigate: [screen: Screen] }>()

const canExecute = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const actionCards = [
  {
    title: 'Ejecutar Cálculo de Depreciación',
    description: 'Procesar depreciación mensual o anual según Decreto 24051',
    icon: Calendar,
    color: 'bg-blue-50 border-blue-300 hover:bg-blue-100',
    iconColor: 'text-blue-600',
    action: 'calculate' as Screen | string,
    requiresAuth: true,
  },
  {
    title: 'Ver Registros de Depreciación',
    description: 'Consultar historial de cálculos y valores depreciados',
    icon: Table2,
    color: 'bg-green-50 border-green-300 hover:bg-green-100',
    iconColor: 'text-green-600',
    action: 'depreciation-records' as Screen,
    requiresAuth: false,
  },
  {
    title: 'Registrar Revalúo',
    description: 'Actualizar valores de activos por revalorización',
    icon: RefreshCw,
    color: 'bg-orange-50 border-orange-300 hover:bg-orange-100',
    iconColor: 'text-orange-600',
    action: 'revaluation' as Screen,
    requiresAuth: true,
  },
  {
    title: 'Ver Asientos Contables',
    description: 'Revisar asientos generados por depreciación',
    icon: BookOpen,
    color: 'bg-purple-50 border-purple-300 hover:bg-purple-100',
    iconColor: 'text-purple-600',
    action: 'accounting-entries' as Screen,
    requiresAuth: false,
  },
]

const recentRuns = [
  { periodo: 'Marzo 2024',   fecha: '31/03/2024', activos: '1,247', monto: 'Bs. 89,456.50', ejecutadoPor: 'Juan Pérez Mamani'    },
  { periodo: 'Febrero 2024', fecha: '29/02/2024', activos: '1,245', monto: 'Bs. 88,234.75', ejecutadoPor: 'María González Quispe' },
  { periodo: 'Enero 2024',   fecha: '31/01/2024', activos: '1,240', monto: 'Bs. 87,890.00', ejecutadoPor: 'Juan Pérez Mamani'    },
]

function isDisabled(card: typeof actionCards[0]) {
  return card.requiresAuth && !canExecute.value
}

function handleCardClick(action: string) {
  if (action === 'depreciation-records' || action === 'revaluation' || action === 'accounting-entries') {
    emit('navigate', action as Screen)
  }
}
</script>
