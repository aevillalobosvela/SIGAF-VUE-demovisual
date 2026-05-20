<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <div class="flex-1">
        <h1 class="text-2xl text-gray-900">Asientos Contables</h1>
        <p class="text-sm text-gray-600 mt-0.5">Asientos generados por el cálculo de depreciación</p>
      </div>
      <button class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors text-sm">
        <FileDown class="w-4 h-4" />
        <span>Exportar</span>
      </button>
    </div>

    <!-- Filtros -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
        <div>
          <label class="block text-xs text-gray-600 mb-1">Gestión</label>
          <select v-model="filters.gestion" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option value="2025">2025</option>
            <option value="2024">2024</option>
            <option value="2023">2023</option>
          </select>
        </div>
        <div>
          <label class="block text-xs text-gray-600 mb-1">Período (Mes)</label>
          <select v-model="filters.mes" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option v-for="(m, i) in meses" :key="i" :value="String(i + 1).padStart(2, '0')">{{ m }}</option>
          </select>
        </div>
        <div>
          <label class="block text-xs text-gray-600 mb-1">Tipo de Asiento</label>
          <select v-model="filters.tipo" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="Depreciación">Depreciación</option>
            <option value="Revalúo">Revalúo</option>
            <option value="Baja">Baja</option>
          </select>
        </div>
        <div>
          <label class="block text-xs text-gray-600 mb-1">Estado</label>
          <select v-model="filters.estado" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todos</option>
            <option value="Generado">Generado</option>
            <option value="Contabilizado">Contabilizado</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Resumen del período -->
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
      <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
        <div class="text-xs text-gray-600 mb-1">Total Asientos</div>
        <div class="text-2xl text-gray-900">{{ filteredAsientos.length }}</div>
      </div>
      <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
        <div class="text-xs text-gray-600 mb-1">Total Debe (Bs.)</div>
        <div class="text-2xl text-gray-900">{{ formatBs(totalDebe) }}</div>
      </div>
      <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
        <div class="text-xs text-gray-600 mb-1">Total Haber (Bs.)</div>
        <div class="text-2xl text-gray-900">{{ formatBs(totalHaber) }}</div>
      </div>
      <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
        <div class="text-xs text-gray-600 mb-1">Diferencia</div>
        <div class="text-2xl" :class="totalDebe - totalHaber === 0 ? 'text-green-700' : 'text-red-600'">
          {{ formatBs(Math.abs(totalDebe - totalHaber)) }}
        </div>
      </div>
    </div>

    <!-- Tabla de asientos -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 w-8"></th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">N° Asiento</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Debe (Bs.)</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Haber (Bs.)</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
            </tr>
          </thead>
          <tbody>
            <template v-for="asiento in paginatedAsientos" :key="asiento.id">
              <tr
                class="hover:bg-gray-50 border-b border-gray-200 cursor-pointer"
                @click="toggleExpand(asiento.id)"
              >
                <td class="px-4 py-3">
                  <ChevronDown :class="['w-4 h-4 text-gray-400 transition-transform', expandedRows.has(asiento.id) ? 'rotate-180' : '']" />
                </td>
                <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ asiento.numero }}</td>
                <td class="px-4 py-3 text-sm text-gray-700">{{ asiento.fecha }}</td>
                <td class="px-4 py-3">
                  <span :class="['inline-block px-2 py-1 text-xs border rounded', getTipoColor(asiento.tipo)]">
                    {{ asiento.tipo }}
                  </span>
                </td>
                <td class="px-4 py-3 text-sm text-gray-900">{{ asiento.descripcion }}</td>
                <td class="px-4 py-3 text-sm text-gray-900 text-right">{{ formatBs(asiento.debe) }}</td>
                <td class="px-4 py-3 text-sm text-gray-900 text-right">{{ formatBs(asiento.haber) }}</td>
                <td class="px-4 py-3">
                  <span :class="['inline-block px-2 py-1 text-xs border rounded', asiento.estado === 'Contabilizado' ? 'bg-green-100 text-green-800 border-green-300' : 'bg-yellow-100 text-yellow-800 border-yellow-300']">
                    {{ asiento.estado }}
                  </span>
                </td>
              </tr>
              <!-- Detalle del asiento expandido -->
              <tr v-if="expandedRows.has(asiento.id)" class="bg-gray-50">
                <td colspan="8" class="px-8 py-4">
                  <div class="text-xs text-gray-700 mb-2 font-medium">Líneas del asiento:</div>
                  <table class="w-full text-xs border border-gray-200 rounded overflow-hidden">
                    <thead class="bg-gray-100">
                      <tr>
                        <th class="px-3 py-2 text-left text-gray-600">Cuenta</th>
                        <th class="px-3 py-2 text-left text-gray-600">Descripción</th>
                        <th class="px-3 py-2 text-right text-gray-600">Debe (Bs.)</th>
                        <th class="px-3 py-2 text-right text-gray-600">Haber (Bs.)</th>
                      </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-200">
                      <tr v-for="(linea, i) in asiento.lineas" :key="i" class="bg-white">
                        <td class="px-3 py-2 font-mono text-gray-900">{{ linea.cuenta }}</td>
                        <td class="px-3 py-2 text-gray-700">{{ linea.descripcion }}</td>
                        <td class="px-3 py-2 text-right text-gray-900">{{ linea.debe ? formatBs(linea.debe) : '—' }}</td>
                        <td class="px-3 py-2 text-right text-gray-900">{{ linea.haber ? formatBs(linea.haber) : '—' }}</td>
                      </tr>
                    </tbody>
                  </table>
                </td>
              </tr>
            </template>
            <tr v-if="filteredAsientos.length === 0">
              <td colspan="8" class="px-4 py-8 text-center text-sm text-gray-400">
                No se encontraron asientos con los filtros aplicados.
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Paginación -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }}–{{ Math.min(currentPage * itemsPerPage, filteredAsientos.length) }}
          de {{ filteredAsientos.length }} asientos
        </div>
        <div class="flex items-center gap-2">
          <button
            @click="currentPage = Math.max(1, currentPage - 1)"
            :disabled="currentPage === 1"
            class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <ChevronLeft class="w-4 h-4" />
          </button>
          <button
            v-for="p in totalPages"
            :key="p"
            @click="currentPage = p"
            :class="['px-3 py-1 rounded text-sm', currentPage === p ? 'bg-gray-700 text-white' : 'border border-gray-300 hover:bg-gray-100']"
          >
            {{ p }}
          </button>
          <button
            @click="currentPage = Math.min(totalPages, currentPage + 1)"
            :disabled="currentPage === totalPages"
            class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-24 Asientos Contables:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Accesible desde el Panel de Depreciación (botón "Ver Asientos Contables").</li>
          <li>Cada fila es expandible para ver las líneas del asiento (Debe/Haber por cuenta).</li>
          <li>La diferencia Debe-Haber debe ser siempre 0 (asiento cuadrado). Se resalta en rojo si no cuadra.</li>
          <li>Estado "Contabilizado" indica que fue integrado al sistema contable principal.</li>
          <li>Accesible para todos los roles en modo lectura.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, FileDown, ChevronDown, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ back: [] }>()

const currentPage  = ref(1)
const itemsPerPage = 8
const expandedRows = ref<Set<string>>(new Set())

const meses = ['Enero','Febrero','Marzo','Abril','Mayo','Junio','Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre']

const filters = ref({ gestion: '2025', mes: '', tipo: '', estado: '' })

interface LineaAsiento { cuenta: string; descripcion: string; debe?: number; haber?: number }
interface Asiento {
  id: string; numero: string; fecha: string; tipo: string
  descripcion: string; debe: number; haber: number
  estado: 'Generado' | 'Contabilizado'; gestion: string; mes: string
  lineas: LineaAsiento[]
}

const mockAsientos: Asiento[] = [
  {
    id: '1', numero: 'AS-2025-0045', fecha: '31/05/2025', tipo: 'Depreciación',
    descripcion: 'Depreciación mensual — Equipos de Computación — Mayo 2025',
    debe: 12450.75, haber: 12450.75, estado: 'Contabilizado', gestion: '2025', mes: '05',
    lineas: [
      { cuenta: '6.1.3.01', descripcion: 'Gasto Depreciación Equipos Computación', debe: 12450.75 },
      { cuenta: '1.2.3.01', descripcion: 'Depreciación Acumulada Equipos Computación', haber: 12450.75 },
    ],
  },
  {
    id: '2', numero: 'AS-2025-0044', fecha: '31/05/2025', tipo: 'Depreciación',
    descripcion: 'Depreciación mensual — Muebles y Enseres — Mayo 2025',
    debe: 3210.50, haber: 3210.50, estado: 'Contabilizado', gestion: '2025', mes: '05',
    lineas: [
      { cuenta: '6.1.3.02', descripcion: 'Gasto Depreciación Muebles y Enseres', debe: 3210.50 },
      { cuenta: '1.2.3.02', descripcion: 'Depreciación Acumulada Muebles y Enseres', haber: 3210.50 },
    ],
  },
  {
    id: '3', numero: 'AS-2025-0043', fecha: '31/05/2025', tipo: 'Depreciación',
    descripcion: 'Depreciación mensual — Vehículos — Mayo 2025',
    debe: 8333.33, haber: 8333.33, estado: 'Contabilizado', gestion: '2025', mes: '05',
    lineas: [
      { cuenta: '6.1.3.03', descripcion: 'Gasto Depreciación Vehículos', debe: 8333.33 },
      { cuenta: '1.2.3.03', descripcion: 'Depreciación Acumulada Vehículos', haber: 8333.33 },
    ],
  },
  {
    id: '4', numero: 'AS-2025-0038', fecha: '15/05/2025', tipo: 'Revalúo',
    descripcion: 'Revalúo — Vehículo Toyota Hilux 4x4 — AF-2023-0145',
    debe: 15000.00, haber: 15000.00, estado: 'Contabilizado', gestion: '2025', mes: '05',
    lineas: [
      { cuenta: '1.2.1.03', descripcion: 'Vehículos — Ajuste por Revalúo', debe: 15000.00 },
      { cuenta: '3.1.5.01', descripcion: 'Superávit por Revaluación de Activos', haber: 15000.00 },
    ],
  },
  {
    id: '5', numero: 'AS-2025-0031', fecha: '30/04/2025', tipo: 'Depreciación',
    descripcion: 'Depreciación mensual — Equipos de Computación — Abril 2025',
    debe: 12450.75, haber: 12450.75, estado: 'Contabilizado', gestion: '2025', mes: '04',
    lineas: [
      { cuenta: '6.1.3.01', descripcion: 'Gasto Depreciación Equipos Computación', debe: 12450.75 },
      { cuenta: '1.2.3.01', descripcion: 'Depreciación Acumulada Equipos Computación', haber: 12450.75 },
    ],
  },
  {
    id: '6', numero: 'AS-2025-0052', fecha: '10/06/2025', tipo: 'Baja',
    descripcion: 'Baja de activos — Proceso BAJA-2025-003',
    debe: 5500.00, haber: 5500.00, estado: 'Generado', gestion: '2025', mes: '06',
    lineas: [
      { cuenta: '1.2.3.01', descripcion: 'Depreciación Acumulada Equipos (reverso)', debe: 5500.00 },
      { cuenta: '1.2.1.01', descripcion: 'Equipos de Computación (baja)', haber: 5500.00 },
    ],
  },
]

const filteredAsientos = computed(() =>
  mockAsientos.filter((a) => {
    const matchGestion = !filters.value.gestion || a.gestion === filters.value.gestion
    const matchMes     = !filters.value.mes     || a.mes === filters.value.mes
    const matchTipo    = !filters.value.tipo    || a.tipo === filters.value.tipo
    const matchEstado  = !filters.value.estado  || a.estado === filters.value.estado
    return matchGestion && matchMes && matchTipo && matchEstado
  })
)

const totalPages    = computed(() => Math.max(1, Math.ceil(filteredAsientos.value.length / itemsPerPage)))
const paginatedAsientos = computed(() =>
  filteredAsientos.value.slice((currentPage.value - 1) * itemsPerPage, currentPage.value * itemsPerPage)
)
const totalDebe  = computed(() => filteredAsientos.value.reduce((s, a) => s + a.debe, 0))
const totalHaber = computed(() => filteredAsientos.value.reduce((s, a) => s + a.haber, 0))

function getTipoColor(tipo: string) {
  switch (tipo) {
    case 'Depreciación': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Revalúo':      return 'bg-orange-100 text-orange-800 border-orange-300'
    case 'Baja':         return 'bg-red-100 text-red-800 border-red-300'
    default:             return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}

function toggleExpand(id: string) {
  if (expandedRows.value.has(id)) expandedRows.value.delete(id)
  else expandedRows.value.add(id)
}

function formatBs(n: number) {
  return n.toLocaleString('es-BO', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}
</script>
