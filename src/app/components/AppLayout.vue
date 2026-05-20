<template>
  <div class="size-full flex bg-gray-100">
    <!-- Left Sidebar -->
    <Sidebar
      :userRole="userRole"
      :currentScreen="currentScreen"
      @navigate="emit('navigate', $event)"
    />

    <!-- Main content area -->
    <div class="flex-1 flex flex-col">
      <!-- Top Bar -->
      <header class="h-16 bg-white border-b border-gray-200 flex items-center justify-between px-6">
        <!-- Breadcrumb -->
        <div class="flex items-center gap-2 text-sm text-gray-600">
          <template v-for="(crumb, idx) in breadcrumbs" :key="idx">
            <ChevronRight v-if="idx > 0" class="w-4 h-4" />
            <span :class="idx === breadcrumbs.length - 1 ? 'text-gray-900' : ''">
              {{ crumb }}
            </span>
          </template>
        </div>

        <!-- Right side: User info, notifications, logout -->
        <div class="flex items-center gap-4">
          <!-- User name and role badge -->
          <div class="flex items-center gap-2">
            <span class="text-sm text-gray-700">Admin Usuario</span>
            <span class="px-2 py-1 bg-gray-200 text-gray-700 text-xs rounded">
              {{ userRole }}
            </span>
          </div>

          <!-- Notifications -->
          <button class="relative p-2 hover:bg-gray-100 rounded">
            <Bell class="w-5 h-5 text-gray-600" />
            <span class="absolute top-1 right-1 w-2 h-2 bg-red-500 rounded-full"></span>
          </button>

          <!-- Logout -->
          <button
            @click="emit('logout')"
            class="flex items-center gap-2 px-3 py-2 text-sm text-gray-700 hover:bg-gray-100 rounded"
          >
            <LogOut class="w-4 h-4" />
            <span>Salir</span>
          </button>
        </div>
      </header>

      <!-- Main scrollable content -->
      <main class="flex-1 overflow-auto p-6">
        <slot />
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { Bell, LogOut, ChevronRight } from 'lucide-vue-next'
import Sidebar from './Sidebar.vue'
import type { UserRole, Screen } from '../types'

const props = defineProps<{
  userRole: UserRole
  currentScreen: Screen
}>()

const emit = defineEmits<{
  logout: []
  navigate: [screen: Screen]
}>()

const breadcrumbMap: Record<string, string[]> = {
  dashboard: ['Inicio', 'Dashboard'],
  'fixed-assets': ['Inicio', 'Activos', 'Activos Fijos'],
  'asset-detail': ['Inicio', 'Activos', 'Activos Fijos', 'Detalle'],
  'asset-form': ['Inicio', 'Activos', 'Activos Fijos', 'Formulario'],
  'control-materials': ['Inicio', 'Activos', 'Materiales de Control'],
  'material-detail': ['Inicio', 'Activos', 'Materiales de Control', 'Detalle'],
  'control-material-form': ['Inicio', 'Activos', 'Materiales de Control', 'Formulario'],
  processes: ['Inicio', 'Procesos', 'Procesos Administrativos'],
  'process-detail': ['Inicio', 'Procesos', 'Procesos Administrativos', 'Detalle'],
  'new-process': ['Inicio', 'Procesos', 'Nuevo Proceso'],
  transfer: ['Inicio', 'Procesos', 'Transferencia'],
  'responsible-change': ['Inicio', 'Procesos', 'Cambio de Responsable'],
  'inventory-sessions': ['Inicio', 'Activos', 'Inventarios'],
  'inventory-scan': ['Inicio', 'Activos', 'Inventarios', 'Escaneo'],
  'inventory-result': ['Inicio', 'Activos', 'Inventarios', 'Resultado'],
  depreciation: ['Inicio', 'Activos', 'Depreciación'],
  'depreciation-records': ['Inicio', 'Activos', 'Depreciación', 'Registros'],
  revaluation: ['Inicio', 'Activos', 'Depreciación', 'Revalúo'],
  actas: ['Inicio', 'Reportes', 'Actas'],
  'acta-preview': ['Inicio', 'Reportes', 'Actas', 'Vista Previa'],
  users: ['Inicio', 'Configuración', 'Usuarios'],
  'user-form': ['Inicio', 'Configuración', 'Usuarios', 'Formulario'],
  'system-parameters': ['Inicio', 'Configuración', 'Parámetros del Sistema'],
  'audit-log': ['Inicio', 'Configuración', 'Registro de Auditoría'],
  reports: ['Inicio', 'Reportes'],
  'report-view': ['Inicio', 'Reportes', 'Vista de Reporte'],
  'report-charts': ['Inicio', 'Reportes', 'Estadísticas y Gráficas'],
  'write-off': ['Inicio', 'Procesos', 'Baja de Activos'],
  'identifier-ranges': ['Inicio', 'Activos', 'Rangos NIA / NIM'],
  'identifier-range-form': ['Inicio', 'Activos', 'Rangos NIA / NIM', 'Asignar Rango'],
  'identifier-range-transfer': ['Inicio', 'Activos', 'Rangos NIA / NIM', 'Transferir Rango'],
  'accounting-entries': ['Inicio', 'Reportes', 'Asientos Contables'],
}

const breadcrumbs = computed(
  () => breadcrumbMap[props.currentScreen] ?? ['Inicio'],
)
</script>
