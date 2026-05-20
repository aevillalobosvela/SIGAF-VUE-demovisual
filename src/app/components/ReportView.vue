<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <div class="flex-1">
        <h1 class="text-2xl text-gray-900">{{ reporteTitulo }}</h1>
        <p class="text-sm text-gray-600 mt-0.5">{{ reporteDescripcion }}</p>
      </div>
      <div class="flex items-center gap-2">
        <button
          @click="exportar('excel')"
          class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors text-sm"
        >
          <FileSpreadsheet class="w-4 h-4" />
          <span>Excel</span>
        </button>
        <button
          @click="exportar('pdf')"
          class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm"
        >
          <FileDown class="w-4 h-4" />
          <span>PDF</span>
        </button>
      </div>
    </div>

    <!-- Panel de filtros colapsable -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <button
        @click="filtersOpen = !filtersOpen"
        class="w-full px-6 py-4 flex items-center justify-between hover:bg-gray-50 transition-colors"
      >
        <div class="flex items-center gap-2">
          <SlidersHorizontal class="w-4 h-4 text-gray-600" />
          <span class="text-sm text-gray-700">Filtros de búsqueda</span>
          <span v-if="activeFiltersCount > 0" class="px-2 py-0.5 text-xs bg-gray-700 text-white rounded-full">
            {{ activeFiltersCount }} activo{{ activeFiltersCount > 1 ? 's' : '' }}
          </span>
        </div>
        <ChevronDown :class="['w-4 h-4 text-gray-400 transition-transform', filtersOpen ? 'rotate-180' : '']" />
      </button>

      <div v-if="filtersOpen" class="px-6 pb-6 border-t border-gray-200">
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mt-4">
          <div>
            <label class="block text-xs text-gray-600 mb-1">Unidad</label>
            <select v-model="filters.unidad" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
              <option value="">Todas las unidades</option>
              <option value="Rectorado">Rectorado</option>
              <option value="Administración Central">Administración Central</option>
              <option value="Facultad de Ingeniería">Facultad de Ingeniería</option>
              <option value="Depto. de Sistemas">Depto. de Sistemas</option>
              <option value="Laboratorio de Computación">Laboratorio de Computación</option>
            </select>
          </div>
          <div>
            <label class="block text-xs text-gray-600 mb-1">Categoría Contable</label>
            <select v-model="filters.categoria" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
              <option value="">Todas las categorías</option>
              <option value="Equipos de Computación">Equipos de Computación</option>
              <option value="Muebles y Enseres">Muebles y Enseres</option>
              <option value="Vehículos">Vehículos</option>
              <option value="Maquinaria y Equipo">Maquinaria y Equipo</option>
            </select>
          </div>
          <div>
            <label class="block text-xs text-gray-600 mb-1">Fuente de Financiamiento</label>
            <select v-model="filters.fuente" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
              <option value="">Todas</option>
              <option value="Recursos Propios">Recursos Propios</option>
              <option value="IDH">IDH</option>
              <option value="Donación Externa">Donación Externa</option>
              <option value="TGN">TGN</option>
            </select>
          </div>
          <div>
            <label class="block text-xs text-gray-600 mb-1">Estado</label>
            <select v-model="filters.estado" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
              <option value="">Todos</option>
              <option value="Activo">Activo</option>
              <option value="Baja">Baja</option>
              <option value="Transferido">Transferido</option>
              <option value="Faltante">Faltante</option>
            </select>
          </div>
          <div>
            <label class="block text-xs text-gray-600 mb-1">Fecha Desde</label>
            <input v-model="filters.fechaDesde" type="date" class="w-full px-3 py-2 border border-gray-300 rounded text-sm" />
          </div>
          <div>
            <label class="block text-xs text-gray-600 mb-1">Fecha Hasta</label>
            <input v-model="filters.fechaHasta" type="date" class="w-full px-3 py-2 border border-gray-300 rounded text-sm" />
          </div>
        </div>
        <div class="flex justify-end mt-4">
          <button
            @click="resetFilters"
            class="flex items-center gap-2 px-3 py-1.5 border border-gray-300 rounded text-sm hover:bg-gray-100 transition-colors"
          >
            <X class="w-3 h-3" />
            <span>Limpiar filtros</span>
          </button>
        </div>
      </div>
    </div>

    <!-- Resumen de totales -->
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
      <div v-for="stat in summaryStats" :key="stat.label" class="bg-white border-2 border-gray-300 rounded-lg p-4">
        <div class="text-xs text-gray-600 mb-1">{{ stat.label }}</div>
        <div class="text-2xl text-gray-900">{{ stat.valor }}</div>
      </div>
    </div>

    <!-- Tabla de resultados -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50 flex items-center justify-between">
        <h2 class="text-lg text-gray-900">Resultados</h2>
        <span class="text-sm text-gray-600">{{ filteredRows.length }} registros</span>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">NIA</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Categoría</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Costo (Bs.)</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Dep. Acum. (Bs.)</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Valor Neto (Bs.)</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="row in paginatedRows" :key="row.nia" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ row.nia }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ row.descripcion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ row.categoria }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ row.unidad }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ row.responsable }}</td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right">{{ formatBs(row.costo) }}</td>
              <td class="px-4 py-3 text-sm text-gray-700 text-right">{{ formatBs(row.depAcum) }}</td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right">{{ formatBs(row.costo - row.depAcum) }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getEstadoColor(row.estado)]">
                  {{ row.estado }}
                </span>
              </td>
            </tr>
            <tr v-if="filteredRows.length === 0">
              <td colspan="9" class="px-4 py-8 text-center text-sm text-gray-400">
                No se encontraron registros con los filtros aplicados.
              </td>
            </tr>
          </tbody>
          <!-- Fila de totales -->
          <tfoot v-if="filteredRows.length > 0" class="bg-gray-100 border-t-2 border-gray-300">
            <tr>
              <td colspan="5" class="px-4 py-3 text-sm text-gray-700">
                <strong>Totales ({{ filteredRows.length }} registros)</strong>
              </td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right"><strong>{{ formatBs(totalCosto) }}</strong></td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right"><strong>{{ formatBs(totalDepAcum) }}</strong></td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right"><strong>{{ formatBs(totalCosto - totalDepAcum) }}</strong></td>
              <td></td>
            </tr>
          </tfoot>
        </table>
      </div>

      <!-- Paginación -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }}–{{ Math.min(currentPage * itemsPerPage, filteredRows.length) }}
          de {{ filteredRows.length }} registros
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
        <strong>Wireframe Notes - P-31 Vista de Reporte:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>El panel de filtros es colapsable para maximizar el espacio de la tabla.</li>
          <li>La fila de totales en el pie de tabla suma los valores de los registros filtrados.</li>
          <li>Exportar Excel/PDF aplica los filtros activos al momento de exportar.</li>
          <li>Accesible para todos los roles.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  ArrowLeft, SlidersHorizontal, ChevronDown, ChevronLeft, ChevronRight,
  X, FileSpreadsheet, FileDown,
} from 'lucide-vue-next'
import type { UserRole } from '../types'

const emit = defineEmits<{ back: [] }>()

type ReportConfig = { titulo: string; descripcion: string }

const reportConfigs: Record<string, ReportConfig> = {
  'valor-total':    { titulo: 'Valor Total de Activos Fijos',       descripcion: 'Inventario valorado con depreciación acumulada y valor neto en libros' },
  'por-depreciar':  { titulo: 'Activos por Depreciar',              descripcion: 'Activos con vida útil restante y proyección de depreciación por gestión' },
  'balance':        { titulo: 'Balance General de Activos',         descripcion: 'Resumen contable: valor original, depreciación acumulada y valor neto' },
  'faltantes':      { titulo: 'Activos Faltantes',                  descripcion: 'Bienes marcados como faltantes en sesiones de inventario físico' },
  'transferencias': { titulo: 'Transferencias por Período',         descripcion: 'Movimientos de bienes entre unidades en un rango de fechas' },
  'historial':      { titulo: 'Historial Individual de Bien',       descripcion: 'Trazabilidad completa de un activo desde su alta hasta el estado actual' },
  'senape':         { titulo: 'Reporte SENAPE',                     descripcion: 'Reporte normativo para la Secretaría Nacional de Pensiones. Activos > Bs. 50.000' },
  'sipap':          { titulo: 'Reporte SIPAP',                      descripcion: 'Reporte para el Sistema de Programación de Administración de Personal' },
  'por-unidad':     { titulo: 'Activos por Unidad',                 descripcion: 'Inventario detallado agrupado por unidad organizacional y responsable' },
}

const props = defineProps<{ userRole: UserRole; reportType?: string }>()

const reporteTitulo = computed(() =>
  props.reportType ? (reportConfigs[props.reportType]?.titulo ?? 'Reporte') : 'Valor Total de Activos Fijos'
)
const reporteDescripcion = computed(() =>
  props.reportType ? (reportConfigs[props.reportType]?.descripcion ?? '') : 'Inventario valorado con depreciación acumulada y valor neto en libros'
)

const filtersOpen  = ref(true)
const currentPage  = ref(1)
const itemsPerPage = 8

const filters = ref({
  unidad: '', categoria: '', fuente: '', estado: '', fechaDesde: '', fechaHasta: '',
})

const activeFiltersCount = computed(() =>
  Object.values(filters.value).filter(Boolean).length
)

interface ReportRow {
  nia: string; descripcion: string; categoria: string; unidad: string
  responsable: string; costo: number; depAcum: number; estado: string; fuente: string
}

const mockRows: ReportRow[] = [
  { nia: 'AF-2024-0001', descripcion: 'Laptop Dell Latitude 5540',          categoria: 'Equipos de Computación', unidad: 'Depto. de Sistemas',        responsable: 'Ing. Carlos Mamani',    costo: 8500,    depAcum: 1416.67, estado: 'Activo',      fuente: 'IDH' },
  { nia: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',      categoria: 'Muebles y Enseres',      unidad: 'Rectorado',                  responsable: 'Dr. Juan Quispe',       costo: 3200,    depAcum: 266.67,  estado: 'Activo',      fuente: 'Recursos Propios' },
  { nia: 'AF-2023-0145', descripcion: 'Vehículo Toyota Hilux 4x4',           categoria: 'Vehículos',              unidad: 'Rectorado',                  responsable: 'Dr. Juan Quispe',       costo: 95000,   depAcum: 23750,   estado: 'Activo',      fuente: 'TGN' },
  { nia: 'AF-2023-0089', descripcion: 'Proyector Epson PowerLite',           categoria: 'Equipos de Computación', unidad: 'Facultad de Ingeniería',     responsable: 'Lic. María Flores',     costo: 4800,    depAcum: 1600,    estado: 'Activo',      fuente: 'IDH' },
  { nia: 'AF-2022-0234', descripcion: 'Servidor HP ProLiant DL380',          categoria: 'Equipos de Computación', unidad: 'Administración Central',     responsable: 'Lic. Roberto Vargas',   costo: 52000,   depAcum: 26000,   estado: 'Activo',      fuente: 'Recursos Propios' },
  { nia: 'AF-2022-0101', descripcion: 'Fotocopiadora Ricoh MP 2014',         categoria: 'Maquinaria y Equipo',    unidad: 'Administración Central',     responsable: 'Lic. Roberto Vargas',   costo: 12500,   depAcum: 6250,    estado: 'Activo',      fuente: 'Recursos Propios' },
  { nia: 'AF-2021-0056', descripcion: 'Aire Acondicionado Split 18000 BTU',  categoria: 'Maquinaria y Equipo',    unidad: 'Laboratorio de Computación', responsable: 'Ing. Carlos Mamani',    costo: 6800,    depAcum: 5100,    estado: 'Activo',      fuente: 'IDH' },
  { nia: 'AF-2021-0033', descripcion: 'Silla Ergonómica de Oficina',         categoria: 'Muebles y Enseres',      unidad: 'Rectorado',                  responsable: 'Lic. María Flores',     costo: 1200,    depAcum: 900,     estado: 'Activo',      fuente: 'Recursos Propios' },
  { nia: 'AF-2020-0178', descripcion: 'Computadora de Escritorio HP',        categoria: 'Equipos de Computación', unidad: 'Depto. de Sistemas',        responsable: 'Ing. Carlos Mamani',    costo: 5500,    depAcum: 5500,    estado: 'Baja',        fuente: 'IDH' },
  { nia: 'AF-2020-0045', descripcion: 'Armario Metálico 4 Puertas',          categoria: 'Muebles y Enseres',      unidad: 'Administración Central',     responsable: 'Lic. Roberto Vargas',   costo: 2800,    depAcum: 2100,    estado: 'Transferido', fuente: 'Recursos Propios' },
  { nia: 'AF-2019-0312', descripcion: 'UPS APC Smart-UPS 1500VA',            categoria: 'Equipos de Computación', unidad: 'Laboratorio de Computación', responsable: 'Ing. Carlos Mamani',    costo: 3200,    depAcum: 3200,    estado: 'Baja',        fuente: 'IDH' },
  { nia: 'AF-2024-0015', descripcion: 'Mesa de Reuniones 10 personas',       categoria: 'Muebles y Enseres',      unidad: 'Rectorado',                  responsable: 'Dr. Juan Quispe',       costo: 7500,    depAcum: 625,     estado: 'Activo',      fuente: 'Recursos Propios' },
]

const filteredRows = computed(() =>
  mockRows.filter((r) => {
    const matchUnidad    = !filters.value.unidad    || r.unidad === filters.value.unidad
    const matchCategoria = !filters.value.categoria || r.categoria === filters.value.categoria
    const matchFuente    = !filters.value.fuente    || r.fuente === filters.value.fuente
    const matchEstado    = !filters.value.estado    || r.estado === filters.value.estado
    return matchUnidad && matchCategoria && matchFuente && matchEstado
  })
)

const totalPages   = computed(() => Math.max(1, Math.ceil(filteredRows.value.length / itemsPerPage)))
const paginatedRows = computed(() =>
  filteredRows.value.slice((currentPage.value - 1) * itemsPerPage, currentPage.value * itemsPerPage)
)
const totalCosto   = computed(() => filteredRows.value.reduce((s, r) => s + r.costo, 0))
const totalDepAcum = computed(() => filteredRows.value.reduce((s, r) => s + r.depAcum, 0))

const summaryStats = computed(() => [
  { label: 'Total Registros',       valor: filteredRows.value.length },
  { label: 'Valor Original (Bs.)',  valor: formatBs(totalCosto.value) },
  { label: 'Dep. Acumulada (Bs.)',  valor: formatBs(totalDepAcum.value) },
  { label: 'Valor Neto (Bs.)',      valor: formatBs(totalCosto.value - totalDepAcum.value) },
])

function formatBs(n: number) {
  return n.toLocaleString('es-BO', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}

function getEstadoColor(estado: string) {
  switch (estado) {
    case 'Activo':      return 'bg-green-100 text-green-800 border-green-300'
    case 'Baja':        return 'bg-red-100 text-red-800 border-red-300'
    case 'Transferido': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Faltante':    return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    default:            return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}

function exportar(tipo: 'excel' | 'pdf') {
  alert(`[Prototipo] Exportando ${filteredRows.value.length} registros como ${tipo.toUpperCase()}...`)
}

function resetFilters() {
  filters.value = { unidad: '', categoria: '', fuente: '', estado: '', fechaDesde: '', fechaHasta: '' }
  currentPage.value = 1
}
</script>
