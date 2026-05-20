<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div>
      <h1 class="text-2xl text-gray-900">Reportes</h1>
      <p class="text-sm text-gray-600 mt-1">Seleccione el tipo de reporte que desea generar o consultar</p>
    </div>

    <!-- Grupo: Inventario y Valoración -->
    <div class="space-y-3">
      <h2 class="text-sm text-gray-700 px-1">Inventario y Valoración</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <button
          v-for="card in inventarioCards"
          :key="card.id"
          @click="emit('navigate', card.screen)"
          class="bg-white border-2 border-gray-300 rounded-lg p-5 text-left hover:border-gray-500 hover:shadow-sm transition-all"
        >
          <div class="flex items-start gap-4">
            <div :class="['p-2 rounded-lg', card.iconBg]">
              <component :is="card.icon" :class="['w-6 h-6', card.iconColor]" />
            </div>
            <div class="flex-1">
              <div class="text-sm text-gray-900 mb-1">{{ card.titulo }}</div>
              <div class="text-xs text-gray-600">{{ card.descripcion }}</div>
            </div>
          </div>
        </button>
      </div>
    </div>

    <!-- Grupo: Movimientos y Estado -->
    <div class="space-y-3">
      <h2 class="text-sm text-gray-700 px-1">Movimientos y Estado</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <button
          v-for="card in movimientosCards"
          :key="card.id"
          @click="emit('navigate', card.screen)"
          class="bg-white border-2 border-gray-300 rounded-lg p-5 text-left hover:border-gray-500 hover:shadow-sm transition-all"
        >
          <div class="flex items-start gap-4">
            <div :class="['p-2 rounded-lg', card.iconBg]">
              <component :is="card.icon" :class="['w-6 h-6', card.iconColor]" />
            </div>
            <div class="flex-1">
              <div class="text-sm text-gray-900 mb-1">{{ card.titulo }}</div>
              <div class="text-xs text-gray-600">{{ card.descripcion }}</div>
            </div>
          </div>
        </button>
      </div>
    </div>

    <!-- Grupo: Institucionales y Normativos -->
    <div class="space-y-3">
      <h2 class="text-sm text-gray-700 px-1">Institucionales y Normativos</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <button
          v-for="card in institucionalesCards"
          :key="card.id"
          @click="emit('navigate', card.screen)"
          class="bg-white border-2 border-gray-300 rounded-lg p-5 text-left hover:border-gray-500 hover:shadow-sm transition-all"
        >
          <div class="flex items-start gap-4">
            <div :class="['p-2 rounded-lg', card.iconBg]">
              <component :is="card.icon" :class="['w-6 h-6', card.iconColor]" />
            </div>
            <div class="flex-1">
              <div class="text-sm text-gray-900 mb-1">{{ card.titulo }}</div>
              <div class="text-xs text-gray-600">{{ card.descripcion }}</div>
            </div>
          </div>
        </button>
      </div>
    </div>

    <!-- Acceso rápido a estadísticas -->
    <div
      class="bg-white border-2 border-gray-300 rounded-lg p-5 flex items-center justify-between cursor-pointer hover:border-gray-500 hover:shadow-sm transition-all"
      @click="emit('navigate', 'report-charts')"
    >
      <div class="flex items-center gap-4">
        <div class="p-2 rounded-lg bg-purple-100">
          <BarChart2 class="w-6 h-6 text-purple-600" />
        </div>
        <div>
          <div class="text-sm text-gray-900">Estadísticas y Gráficas</div>
          <div class="text-xs text-gray-600">Visualización gráfica del inventario por categoría, fuente de financiamiento y evolución histórica</div>
        </div>
      </div>
      <ChevronRight class="w-5 h-5 text-gray-400" />
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-30 Menú de Reportes:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Accesible para todos los roles (Administrador, Operador, Consulta).</li>
          <li>Cada tarjeta navega a ReportView con el tipo de reporte preseleccionado.</li>
          <li>Los reportes SENAPE y SIPAP son de uso normativo obligatorio para la universidad.</li>
          <li>El reporte de Historial Individual muestra todos los movimientos de un bien específico.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  Package, TrendingDown, AlertTriangle, FileText,
  ArrowLeftRight, History, BarChart2, BookOpen,
  ChevronRight, Building2,
} from 'lucide-vue-next'
import type { UserRole, Screen } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ navigate: [screen: Screen] }>()

const inventarioCards = [
  {
    id: 'valor-total',
    titulo: 'Valor Total de Activos',
    descripcion: 'Valor en libros de todos los activos fijos agrupados por categoría contable.',
    icon: Package,
    iconBg: 'bg-blue-100',
    iconColor: 'text-blue-600',
    screen: 'report-view' as Screen,
  },
  {
    id: 'por-depreciar',
    titulo: 'Activos por Depreciar',
    descripcion: 'Activos con vida útil restante y proyección de depreciación por gestión.',
    icon: TrendingDown,
    iconBg: 'bg-orange-100',
    iconColor: 'text-orange-600',
    screen: 'report-view' as Screen,
  },
  {
    id: 'balance',
    titulo: 'Balance General de Activos',
    descripcion: 'Resumen contable: valor original, depreciación acumulada y valor neto.',
    icon: BookOpen,
    iconBg: 'bg-green-100',
    iconColor: 'text-green-600',
    screen: 'report-view' as Screen,
  },
]

const movimientosCards = [
  {
    id: 'faltantes',
    titulo: 'Activos Faltantes',
    descripcion: 'Bienes marcados como faltantes en sesiones de inventario físico.',
    icon: AlertTriangle,
    iconBg: 'bg-red-100',
    iconColor: 'text-red-600',
    screen: 'report-view' as Screen,
  },
  {
    id: 'transferencias',
    titulo: 'Transferencias por Período',
    descripcion: 'Movimientos de bienes entre unidades en un rango de fechas.',
    icon: ArrowLeftRight,
    iconBg: 'bg-yellow-100',
    iconColor: 'text-yellow-600',
    screen: 'report-view' as Screen,
  },
  {
    id: 'historial',
    titulo: 'Historial Individual de Bien',
    descripcion: 'Trazabilidad completa de un activo desde su alta hasta el estado actual.',
    icon: History,
    iconBg: 'bg-gray-100',
    iconColor: 'text-gray-600',
    screen: 'report-view' as Screen,
  },
]

const institucionalesCards = [
  {
    id: 'senape',
    titulo: 'Reporte SENAPE',
    descripcion: 'Reporte normativo para la Secretaría Nacional de Pensiones. Activos > Bs. 50.000.',
    icon: FileText,
    iconBg: 'bg-blue-100',
    iconColor: 'text-blue-700',
    screen: 'report-view' as Screen,
  },
  {
    id: 'sipap',
    titulo: 'Reporte SIPAP',
    descripcion: 'Reporte para el Sistema de Programación de Administración de Personal.',
    icon: FileText,
    iconBg: 'bg-blue-100',
    iconColor: 'text-blue-700',
    screen: 'report-view' as Screen,
  },
  {
    id: 'por-unidad',
    titulo: 'Activos por Unidad',
    descripcion: 'Inventario detallado agrupado por unidad organizacional y responsable.',
    icon: Building2,
    iconBg: 'bg-gray-100',
    iconColor: 'text-gray-600',
    screen: 'report-view' as Screen,
  },
]
</script>
