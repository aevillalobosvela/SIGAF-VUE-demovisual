<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Dashboard</h1>
    </div>

    <!-- Stats cards -->
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
      <div
        v-for="(stat, idx) in stats"
        :key="idx"
        class="bg-white border-2 border-gray-300 rounded-lg p-6"
      >
        <div class="flex items-start justify-between">
          <div>
            <div class="text-3xl text-gray-900 mb-1">{{ stat.value }}</div>
            <div class="text-sm text-gray-600">{{ stat.label }}</div>
          </div>
          <div class="text-gray-400">
            <component :is="stat.icon" class="w-8 h-8" />
          </div>
        </div>
      </div>
    </div>

    <!-- Quick actions -->
    <div v-if="visibleActions.length > 0">
      <h2 class="text-lg text-gray-900 mb-3">Acciones Rápidas</h2>
      <div class="flex flex-wrap gap-3">
        <button
          v-for="(action, idx) in visibleActions"
          :key="idx"
          class="flex items-center gap-2 px-4 py-3 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
        >
          <component :is="action.icon" class="w-5 h-5" />
          <span>{{ action.label }}</span>
        </button>
      </div>
    </div>

    <!-- Alerts/Notifications panel -->
    <div>
      <h2 class="text-lg text-gray-900 mb-3">Alertas y Notificaciones</h2>
      <div class="bg-white border-2 border-gray-300 rounded-lg divide-y divide-gray-200">
        <div
          v-for="(alert, idx) in alerts"
          :key="idx"
          class="p-4 flex items-start gap-3"
        >
          <div
            :class="[
              'mt-1',
              alert.type === 'warning' ? 'text-yellow-600' :
              alert.type === 'info'    ? 'text-blue-600'   : 'text-green-600'
            ]"
          >
            <AlertCircle class="w-5 h-5" />
          </div>
          <div class="flex-1">
            <div class="text-sm text-gray-900 mb-1">{{ alert.title }}</div>
            <div class="text-xs text-gray-600">{{ alert.description }}</div>
          </div>
          <button class="text-sm text-gray-600 hover:text-gray-900">Ver detalles</button>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="mt-8 p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Layout shows persistent sidebar (~240px) with role-based menu visibility</li>
          <li>Top bar includes breadcrumb navigation, user info, and logout</li>
          <li>Dashboard displays 4 summary stat cards in responsive grid</li>
          <li>Quick actions visible based on user role (hidden for Consulta role)</li>
          <li>Alerts panel shows pending tasks and notifications</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { Package, ClipboardList, Clock, CheckCircle, Plus, FileText, Clipboard, AlertCircle } from 'lucide-vue-next'
import type { UserRole } from '../types'

const props = defineProps<{ userRole: UserRole }>()

const stats = [
  { label: 'Total Activos Fijos',          value: '1,247', icon: Package      },
  { label: 'Total Materiales de Control',  value: '3,582', icon: ClipboardList },
  { label: 'Procesos Pendientes',          value: '12',    icon: Clock        },
  { label: 'Procesos Cerrados este Mes',   value: '28',    icon: CheckCircle  },
]

const quickActions = [
  { label: 'Registrar Activo',  icon: Plus,      roles: ['Administrador', 'Operador'] as UserRole[] },
  { label: 'Nuevo Proceso',     icon: FileText,  roles: ['Administrador', 'Operador'] as UserRole[] },
  { label: 'Iniciar Inventario',icon: Clipboard, roles: ['Administrador', 'Operador'] as UserRole[] },
]

const alerts = [
  { title: 'Proceso de transferencia pendiente', description: 'AF-2024-0123 requiere aprobación',          type: 'warning' },
  { title: 'Inventario asignado',                description: 'Inventario de Edificio Central - Vence en 5 días', type: 'info'    },
  { title: 'Proceso de baja cerrado',            description: 'BAJA-2024-0045 ha sido completado',         type: 'success' },
]

const visibleActions = computed(() =>
  quickActions.filter((a) => a.roles.includes(props.userRole)),
)
</script>
