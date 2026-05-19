<template>
  <div class="min-h-screen bg-gray-100 flex flex-col">
    <!-- Top bar - mobile optimized -->
    <div class="bg-gray-800 text-white p-4 flex items-center gap-3">
      <button @click="emit('back')" class="p-2 hover:bg-gray-700 rounded">
        <ArrowLeft class="w-5 h-5" />
      </button>
      <div class="flex-1">
        <h1 class="text-lg">Escaneo de Inventario</h1>
        <p class="text-xs text-gray-300">Administración Central</p>
      </div>
    </div>

    <!-- Camera viewfinder area -->
    <div class="relative bg-black flex-[60] flex items-center justify-center" style="min-height: 320px">
      <div class="absolute inset-0 flex items-center justify-center">
        <Camera class="w-16 h-16 text-gray-600" />
        <span class="absolute bottom-8 text-gray-500 text-sm">VISTA DE CÁMARA</span>
      </div>
      <!-- QR scanning overlay frame -->
      <div class="relative w-64 h-64">
        <div class="absolute top-0 left-0 w-8 h-8 border-t-4 border-l-4 border-green-400"></div>
        <div class="absolute top-0 right-0 w-8 h-8 border-t-4 border-r-4 border-green-400"></div>
        <div class="absolute bottom-0 left-0 w-8 h-8 border-b-4 border-l-4 border-green-400"></div>
        <div class="absolute bottom-0 right-0 w-8 h-8 border-b-4 border-r-4 border-green-400"></div>
        <div class="absolute inset-0 flex items-center justify-center">
          <div class="text-green-400 text-sm bg-black/50 px-3 py-1 rounded">Apunte al código QR</div>
        </div>
      </div>
    </div>

    <!-- Progress indicator -->
    <div class="bg-white border-t-2 border-gray-300 p-4">
      <div class="flex items-center justify-between mb-2">
        <span class="text-sm text-gray-700">Progreso de Inventario</span>
        <span class="text-lg text-gray-900">
          <strong>{{ scannedCount }}</strong> / {{ totalCount }}
        </span>
      </div>
      <div class="w-full bg-gray-200 rounded-full h-3">
        <div
          class="bg-blue-600 h-3 rounded-full transition-all flex items-center justify-end pr-2"
          :style="{ width: `${progressPercentage}%` }"
        >
          <span class="text-white text-xs">{{ Math.round(progressPercentage) }}%</span>
        </div>
      </div>
    </div>

    <!-- Recent scans list -->
    <div class="bg-white flex-[30] flex flex-col border-t-2 border-gray-300" style="min-height: 200px">
      <div class="px-4 py-3 border-b border-gray-300 bg-gray-50">
        <h2 class="text-sm text-gray-900">Escaneos Recientes</h2>
      </div>
      <div class="flex-1 overflow-y-auto">
        <div
          v-for="scan in mockRecentScans"
          :key="scan.id"
          class="px-4 py-3 border-b border-gray-200 flex items-start gap-3 hover:bg-gray-50"
        >
          <div class="flex-shrink-0 mt-0.5">
            <CheckCircle v-if="scan.status === 'found'" class="w-5 h-5 text-green-600" />
            <XCircle v-else class="w-5 h-5 text-red-600" />
          </div>
          <div class="flex-1 min-w-0">
            <div class="text-sm text-gray-900 mb-0.5">{{ scan.nia }}</div>
            <div class="text-xs text-gray-600 truncate">{{ scan.descripcion }}</div>
          </div>
          <div class="text-xs text-gray-500 flex-shrink-0">{{ scan.timestamp }}</div>
        </div>
      </div>
    </div>

    <!-- Bottom action button -->
    <div class="bg-white border-t-2 border-gray-300 p-4">
      <button class="w-full py-3 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors">
        Finalizar Sesión
      </button>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - QR Scan Screen:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Mobile/tablet-optimized layout in portrait orientation</li>
          <li>Top bar with back button, title, and unit name</li>
          <li>Large camera viewfinder area (60% of screen) with QR scanning overlay frame and corner brackets</li>
          <li>Progress indicator below viewfinder showing "Escaneados: 47 / 120" with visual progress bar</li>
          <li>Recent scans list (30% of screen) with scrollable entries</li>
          <li>Each scan shows NIA, description, status icon (✓ found / ✗ not found), and timestamp</li>
          <li>"Finalizar Sesión" button at bottom in red/destructive color</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { ArrowLeft, Camera, CheckCircle, XCircle } from 'lucide-vue-next'

defineProps<{ inventoryId: string | null }>()
const emit = defineEmits<{ back: [] }>()

const scannedCount = 47
const totalCount   = 120
const progressPercentage = computed(() => (scannedCount / totalCount) * 100)

const mockRecentScans = [
  { id: '1', nia: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', status: 'found',     timestamp: '10:45:23' },
  { id: '2', nia: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',       status: 'found',     timestamp: '10:44:18' },
  { id: '3', nia: 'AF-2023-0145', descripcion: 'Impresora Multifuncional Canon',        status: 'not-found', timestamp: '10:43:05' },
  { id: '4', nia: 'NIM-2024-0001',descripcion: 'Escritorio Metálico Simple',           status: 'found',     timestamp: '10:42:12' },
  { id: '5', nia: 'AF-2023-0201', descripcion: 'Proyector Epson PowerLite',             status: 'found',     timestamp: '10:40:45' },
]
</script>
