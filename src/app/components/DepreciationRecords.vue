<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div>
      <h1 class="text-2xl text-gray-900">Registros de Depreciación</h1>
      <p class="text-sm text-gray-600 mt-1">Consulta de valores depreciados por activo y gestión</p>
    </div>

    <!-- Filter bar -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <!-- NIA search -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">NIA del Activo</label>
          <div class="relative">
            <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
            <input v-model="searchNia" type="text" placeholder="Ej: AF-2024-0001" class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm" />
          </div>
        </div>
        <!-- Gestión -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Gestión (Año)</label>
          <select v-model="selectedYear" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option value="2024">2024</option>
            <option value="2023">2023</option>
            <option value="2022">2022</option>
            <option value="2021">2021</option>
          </select>
        </div>
        <!-- Categoría -->
        <div>
          <label class="block text-xs text-gray-600 mb-1">Categoría</label>
          <select v-model="selectedCategory" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
            <option value="">Todas</option>
            <option value="muebles">Muebles y Enseres</option>
            <option value="equipos">Equipos de Computación</option>
            <option value="vehiculos">Vehículos</option>
            <option value="maquinaria">Maquinaria y Equipo</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Records table -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">NIA</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Gestión</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Dep. Período (Bs.)</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Dep. Acumulada (Bs.)</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Vida Útil Restante</th>
              <th class="px-4 py-3 text-center text-xs text-gray-700">N° Revalúos</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="record in mockRecords" :key="record.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ record.nia }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ record.descripcion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ record.gestion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700 text-right">{{ record.depPeriodo }}</td>
              <td class="px-4 py-3 text-sm text-gray-700 text-right">{{ record.depAcumulada }}</td>
              <td class="px-4 py-3 text-sm text-gray-700 text-right">{{ record.vidaUtilRestante }} meses</td>
              <td class="px-4 py-3 text-sm text-gray-700 text-center">
                <span v-if="record.numRevaluos > 0" class="px-2 py-1 bg-blue-100 text-blue-800 rounded text-xs">
                  {{ record.numRevaluos }}
                </span>
                <span v-else class="text-gray-400">—</span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Pagination -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} -
          {{ Math.min(currentPage * itemsPerPage, mockRecords.length) }} de
          {{ mockRecords.length }} registros
        </div>
        <div class="flex items-center gap-2">
          <button @click="currentPage = Math.max(1, currentPage - 1)" :disabled="currentPage === 1" class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed">
            <ChevronLeft class="w-4 h-4" />
          </button>
          <button class="px-3 py-1 bg-gray-700 text-white rounded">1</button>
          <button @click="currentPage++" class="p-2 border border-gray-300 rounded hover:bg-gray-100">
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Depreciation Records:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Filter bar with asset NIA search, year (gestión), and category dropdowns</li>
          <li>Table columns: NIA, Descripción, Gestión, Dep. Período (Bs.), Dep. Acumulada (Bs.), Vida Útil Restante (months), N° Revalúos</li>
          <li>Monetary values right-aligned for better readability</li>
          <li>N° Revalúos shown as badge if > 0, otherwise shows "—"</li>
          <li>All roles can view this screen (read-only data)</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Search, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()

const searchNia       = ref('')
const selectedYear    = ref('')
const selectedCategory = ref('')
const currentPage     = ref(1)
const itemsPerPage    = 10

const mockRecords = [
  { id: '1', nia: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', gestion: '2024', depPeriodo: '2,125.00',  depAcumulada: '2,125.00',  vidaUtilRestante: 36, numRevaluos: 0 },
  { id: '2', nia: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',       gestion: '2024', depPeriodo: '450.00',    depAcumulada: '450.00',    vidaUtilRestante: 84, numRevaluos: 0 },
  { id: '3', nia: 'AF-2023-0145', descripcion: 'Vehículo Toyota Hilux 4x4',            gestion: '2024', depPeriodo: '12,500.00', depAcumulada: '25,000.00', vidaUtilRestante: 24, numRevaluos: 1 },
  { id: '4', nia: 'AF-2022-0089', descripcion: 'Impresora Multifuncional Canon',        gestion: '2024', depPeriodo: '850.00',    depAcumulada: '2,550.00',  vidaUtilRestante: 9,  numRevaluos: 0 },
  { id: '5', nia: 'AF-2021-0234', descripcion: 'Torno Industrial CNC',                 gestion: '2024', depPeriodo: '8,900.00',  depAcumulada: '35,600.00', vidaUtilRestante: 12, numRevaluos: 1 },
]
</script>
