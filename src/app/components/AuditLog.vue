<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Registro de Auditoría</h1>
      <button class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors text-sm">
        <Download class="w-4 h-4" />
        <span>Exportar</span>
      </button>
    </div>

    <!-- Filtros -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4">
        <div class="relative">
          <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
          <input
            v-model="filters.usuario"
            type="text"
            placeholder="Usuario..."
            class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
          />
        </div>
        <select v-model="filters.modulo" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
          <option value="">Todos los módulos</option>
          <option v-for="m in modulos" :key="m" :value="m">{{ m }}</option>
        </select>
        <select v-model="filters.accion" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
          <option value="">Todas las acciones</option>
          <option v-for="a in acciones" :key="a" :value="a">{{ a }}</option>
        </select>
        <input
          v-model="filters.fechaDesde"
          type="date"
          class="w-full px-3 py-2 border border-gray-300 rounded text-sm"
        />
        <input
          v-model="filters.fechaHasta"
          type="date"
          class="w-full px-3 py-2 border border-gray-300 rounded text-sm"
        />
      </div>
      <div class="flex justify-end mt-3">
        <button
          @click="resetFilters"
          class="flex items-center gap-2 px-3 py-1.5 border border-gray-300 rounded text-sm hover:bg-gray-100 transition-colors"
        >
          <X class="w-3 h-3" />
          <span>Limpiar filtros</span>
        </button>
      </div>
    </div>

    <!-- Tabla -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700 w-8"></th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha / Hora</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Usuario</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Módulo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Registro Afectado</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">IP</th>
            </tr>
          </thead>
          <tbody>
            <template v-for="log in filteredLogs" :key="log.id">
              <tr
                class="hover:bg-gray-50 border-b border-gray-200 cursor-pointer"
                @click="toggleExpand(log.id)"
              >
                <td class="px-4 py-3">
                  <ChevronDown
                    :class="['w-4 h-4 text-gray-400 transition-transform', expandedRows.has(log.id) ? 'rotate-180' : '']"
                  />
                </td>
                <td class="px-4 py-3 text-sm text-gray-900 whitespace-nowrap">{{ log.fechaHora }}</td>
                <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ log.usuario }}</td>
                <td class="px-4 py-3 text-sm text-gray-700">{{ log.modulo }}</td>
                <td class="px-4 py-3">
                  <span :class="['inline-block px-2 py-1 text-xs border rounded', getAccionColor(log.accion)]">
                    {{ log.accion }}
                  </span>
                </td>
                <td class="px-4 py-3 text-sm text-gray-700">{{ log.registro }}</td>
                <td class="px-4 py-3 text-sm text-gray-400 font-mono">{{ log.ip }}</td>
              </tr>
              <!-- Fila expandida con detalle -->
              <tr v-if="expandedRows.has(log.id)" class="bg-gray-50">
                <td colspan="7" class="px-8 py-4">
                  <div class="text-xs text-gray-700 space-y-2">
                    <div class="font-medium text-gray-900 mb-2">Detalle del evento:</div>
                    <div class="bg-white border border-gray-300 rounded p-3 font-mono text-xs text-gray-700 whitespace-pre-wrap">{{ log.detalle }}</div>
                  </div>
                </td>
              </tr>
            </template>
            <tr v-if="filteredLogs.length === 0">
              <td colspan="7" class="px-4 py-8 text-center text-sm text-gray-400">
                No se encontraron registros con los filtros aplicados.
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Paginación -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ filteredLogs.length }} de {{ mockLogs.length }} registros
        </div>
        <div class="flex items-center gap-2">
          <button class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50" disabled>
            <ChevronLeft class="w-4 h-4" />
          </button>
          <button class="px-3 py-1 bg-gray-700 text-white rounded text-sm">1</button>
          <button class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50" disabled>
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-40 Registro de Auditoría:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Solo accesible para rol Administrador.</li>
          <li>Cada fila es expandible para ver el detalle completo del cambio (valores antes/después).</li>
          <li>El registro de auditoría es de solo lectura — no se puede modificar ni eliminar.</li>
          <li>El botón Exportar genera un archivo Excel o CSV del resultado filtrado.</li>
          <li>Toda acción del sistema queda registrada automáticamente (RN-13).</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, X, Download, ChevronDown, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()

interface LogEntry {
  id: string
  fechaHora: string
  usuario: string
  modulo: string
  accion: string
  registro: string
  ip: string
  detalle: string
}

const mockLogs: LogEntry[] = [
  { id: '1',  fechaHora: '15/06/2025 09:15:32', usuario: 'p.condori',   modulo: 'Activos Fijos',    accion: 'Crear',       registro: 'AF-2025-0089',    ip: '192.168.1.45',  detalle: 'Nuevo activo registrado.\nDescripción: Laptop Dell Latitude 5540\nCosto: Bs. 8.500,00\nUnidad: Depto. de Sistemas\nResponsable: Ing. Carlos Mamani López' },
  { id: '2',  fechaHora: '15/06/2025 09:02:11', usuario: 'admin.uto',   modulo: 'Usuarios',         accion: 'Modificar',   registro: 'c.mamani',        ip: '192.168.1.10',  detalle: 'Modificación de usuario.\nCampo: perfiles\nAntes: ["Elaboración"]\nDespués: ["Elaboración", "Proceso"]' },
  { id: '3',  fechaHora: '15/06/2025 08:47:05', usuario: 'm.flores',    modulo: 'Procesos',         accion: 'Cerrar',      registro: 'TRANS-2025-034',  ip: '192.168.1.23',  detalle: 'Proceso cerrado.\nTipo: Transferencia entre Unidades\nBienes afectados: 3\nUnidad origen: Rectorado\nUnidad destino: Depto. de Sistemas' },
  { id: '4',  fechaHora: '14/06/2025 16:30:00', usuario: 'p.condori',   modulo: 'Inventario',       accion: 'Crear',       registro: 'INV-2025-012',    ip: '192.168.1.45',  detalle: 'Nueva sesión de inventario iniciada.\nUnidad: Laboratorio de Computación\nBienes a verificar: 47' },
  { id: '5',  fechaHora: '14/06/2025 14:12:44', usuario: 'r.choque',    modulo: 'Activos Fijos',    accion: 'Modificar',   registro: 'AF-2024-0012',    ip: '192.168.1.67',  detalle: 'Modificación de activo.\nCampo: estado\nAntes: Activo\nDespués: En mantenimiento\nObservación: Mantenimiento preventivo anual' },
  { id: '6',  fechaHora: '14/06/2025 11:05:18', usuario: 'admin.uto',   modulo: 'Parámetros',       accion: 'Modificar',   registro: 'UFV 01/06/2025',  ip: '192.168.1.10',  detalle: 'Valor UFV registrado.\nFecha: 01/06/2025\nValor: 2.45312 Bs.' },
  { id: '7',  fechaHora: '13/06/2025 10:22:09', usuario: 'm.flores',    modulo: 'Depreciación',     accion: 'Ejecutar',    registro: 'Gestión 2025',    ip: '192.168.1.23',  detalle: 'Cálculo de depreciación ejecutado.\nGestión: 2025\nActivos procesados: 312\nTotal depreciación período: Bs. 45.230,00' },
  { id: '8',  fechaHora: '13/06/2025 08:55:33', usuario: 'j.quispe',    modulo: 'Activos Fijos',    accion: 'Consultar',   registro: 'AF-2023-0145',    ip: '192.168.1.88',  detalle: 'Consulta de ficha de activo.\nNIA: AF-2023-0145\nDescripción: Vehículo Toyota Hilux 4x4' },
  { id: '9',  fechaHora: '12/06/2025 15:40:27', usuario: 'p.condori',   modulo: 'Procesos',         accion: 'Crear',       registro: 'CAMB-2025-018',   ip: '192.168.1.45',  detalle: 'Nuevo proceso creado.\nTipo: Cambio de Responsable\nUnidad: Administración Central\nBienes seleccionados: 5' },
  { id: '10', fechaHora: '12/06/2025 09:10:00', usuario: 'admin.uto',   modulo: 'Usuarios',         accion: 'Crear',       registro: 'r.choque',        ip: '192.168.1.10',  detalle: 'Nuevo usuario creado.\nNombre: Tec. Rodrigo Choque Apaza\nRol: Operador\nPerfiles: ["Proceso", "Firma-Cierre"]' },
]

const modulos  = ['Activos Fijos', 'Materiales de Control', 'Procesos', 'Inventario', 'Depreciación', 'Usuarios', 'Parámetros']
const acciones = ['Crear', 'Modificar', 'Eliminar', 'Consultar', 'Cerrar', 'Ejecutar', 'Login', 'Logout']

const filters = ref({ usuario: '', modulo: '', accion: '', fechaDesde: '', fechaHasta: '' })
const expandedRows = ref<Set<string>>(new Set())

const filteredLogs = computed(() =>
  mockLogs.filter((l) => {
    const matchUsuario = !filters.value.usuario || l.usuario.toLowerCase().includes(filters.value.usuario.toLowerCase())
    const matchModulo  = !filters.value.modulo  || l.modulo === filters.value.modulo
    const matchAccion  = !filters.value.accion  || l.accion === filters.value.accion
    return matchUsuario && matchModulo && matchAccion
  })
)

function getAccionColor(accion: string) {
  switch (accion) {
    case 'Crear':    return 'bg-green-100 text-green-800 border-green-300'
    case 'Modificar':return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Eliminar': return 'bg-red-100 text-red-800 border-red-300'
    case 'Cerrar':   return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    case 'Ejecutar': return 'bg-orange-100 text-orange-800 border-orange-300'
    default:         return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}

function toggleExpand(id: string) {
  if (expandedRows.value.has(id)) {
    expandedRows.value.delete(id)
  } else {
    expandedRows.value.add(id)
  }
}

function resetFilters() {
  filters.value = { usuario: '', modulo: '', accion: '', fechaDesde: '', fechaHasta: '' }
}
</script>
