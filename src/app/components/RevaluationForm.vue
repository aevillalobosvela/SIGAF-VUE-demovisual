<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Registrar Revalúo</h1>
    </div>

    <!-- Form -->
    <form @submit.prevent="handleSubmit" class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <div class="space-y-6">
        <!-- Asset search -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Activo Fijo <span class="text-red-600">*</span>
          </label>
          <div class="relative">
            <div class="flex gap-2">
              <input
                v-model="formData.assetNia"
                type="text"
                @focus="showAssetSearch = true"
                placeholder="Buscar por NIA..."
                class="flex-1 px-3 py-2 border-2 border-gray-300 rounded"
                required
              />
              <button type="button" @click="showAssetSearch = !showAssetSearch" class="px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
                <Search class="w-5 h-5" />
              </button>
            </div>
            <!-- Asset search dropdown -->
            <div
              v-if="showAssetSearch"
              class="absolute top-full left-0 right-0 mt-1 bg-white border-2 border-gray-300 rounded-lg shadow-lg z-10 max-h-64 overflow-y-auto"
            >
              <button
                v-for="asset in filteredAssets"
                :key="asset.nia"
                type="button"
                @click="handleAssetSelect(asset)"
                class="w-full px-4 py-3 text-left hover:bg-gray-100 border-b border-gray-200 last:border-b-0"
              >
                <div class="text-sm text-gray-900 mb-1">{{ asset.nia }}</div>
                <div class="text-xs text-gray-600">{{ asset.descripcion }}</div>
              </button>
            </div>
          </div>
        </div>

        <!-- Selected asset info -->
        <div v-if="selectedAsset" class="bg-blue-50 border-2 border-blue-200 rounded-lg p-4">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <div class="text-xs text-blue-700 mb-1">Descripción</div>
              <div class="text-sm text-blue-900">{{ selectedAsset.descripcion }}</div>
            </div>
            <div>
              <div class="text-xs text-blue-700 mb-1">Valor Actual en Libros</div>
              <div class="text-sm text-blue-900">Bs. {{ selectedAsset.valorActual }}</div>
            </div>
            <div>
              <div class="text-xs text-blue-700 mb-1">Vida Útil Restante</div>
              <div class="text-sm text-blue-900">{{ selectedAsset.vidaUtilRestante }} meses</div>
            </div>
          </div>
        </div>

        <!-- Two-column grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Fecha del revalúo -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Fecha del Revalúo <span class="text-red-600">*</span>
            </label>
            <input v-model="formData.fechaRevaluo" type="date" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required />
          </div>
          <!-- Nuevo valor -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Nuevo Valor en Libros (Bs.) <span class="text-red-600">*</span>
            </label>
            <input v-model="formData.nuevoValor" type="number" step="0.01" placeholder="0.00" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required />
          </div>
          <!-- Nueva vida útil -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Nueva Vida Útil (meses) <span class="text-red-600">*</span>
            </label>
            <input v-model="formData.nuevaVidaUtil" type="number" placeholder="60" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required />
            <p class="text-xs text-gray-600 mt-1">Vida útil restante después del revalúo</p>
          </div>
        </div>

        <!-- Document upload -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Documento de Respaldo <span class="text-red-600">*</span>
          </label>
          <div v-if="uploadedDoc" class="flex items-center justify-between p-3 border-2 border-gray-300 rounded bg-gray-50">
            <div class="flex items-center gap-2">
              <Upload class="w-4 h-4 text-gray-600" />
              <span class="text-sm text-gray-900">{{ uploadedDoc.name }}</span>
              <span class="text-xs text-gray-600">({{ uploadedDoc.size }})</span>
            </div>
            <button type="button" @click="uploadedDoc = null" class="p-1 text-red-600 hover:text-red-800">
              <X class="w-4 h-4" />
            </button>
          </div>
          <label v-else class="inline-flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 cursor-pointer">
            <Upload class="w-4 h-4" />
            <span>Adjuntar Documento</span>
            <input type="file" accept=".pdf,.doc,.docx,.jpg,.jpeg,.png" @change="handleDocumentUpload" class="hidden" />
          </label>
          <p class="text-xs text-gray-600 mt-1">Informe técnico, resolución administrativa u otro documento que justifique el revalúo</p>
        </div>

        <!-- Observaciones -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">Observaciones</label>
          <textarea v-model="formData.observaciones" rows="4" class="w-full px-3 py-2 border-2 border-gray-300 rounded" placeholder="Motivo del revalúo, detalles adicionales..." />
        </div>
      </div>

      <!-- Form footer -->
      <div class="flex items-center justify-end gap-3 mt-8 pt-6 border-t-2 border-gray-300">
        <button type="button" @click="emit('cancel')" class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">Cancelar</button>
        <button type="submit" class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">Registrar Revalúo</button>
      </div>
    </form>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Revaluation Form:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Asset search by NIA with dropdown showing matching results</li>
          <li>Selected asset info displayed in blue box showing current value and remaining useful life</li>
          <li>Fecha del revalúo, Nuevo valor en libros, Nueva vida útil fields</li>
          <li>Documento de respaldo - file upload (required)</li>
          <li>Observaciones textarea for additional notes</li>
          <li>Footer with "Registrar Revalúo" (primary) and "Cancelar" (secondary) buttons</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Search, Upload, X } from 'lucide-vue-next'
import type { UserRole } from '../types'

interface AssetOption { nia: string; descripcion: string; valorActual: string; vidaUtilRestante: number }
interface DocFile { name: string; size: string }

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ cancel: [] }>()

const formData = ref({ assetNia: '', fechaRevaluo: '', nuevoValor: '', nuevaVidaUtil: '', observaciones: '' })
const selectedAsset = ref<AssetOption | null>(null)
const uploadedDoc   = ref<DocFile | null>(null)
const showAssetSearch = ref(false)

const mockAssets: AssetOption[] = [
  { nia: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', valorActual: '6,375.00',   vidaUtilRestante: 36 },
  { nia: 'AF-2023-0145', descripcion: 'Vehículo Toyota Hilux 4x4',            valorActual: '225,000.00', vidaUtilRestante: 24 },
  { nia: 'AF-2021-0234', descripcion: 'Torno Industrial CNC',                 valorActual: '124,400.00', vidaUtilRestante: 12 },
]

const filteredAssets = computed(() => {
  const q = formData.value.assetNia.toLowerCase()
  if (!q) return mockAssets
  return mockAssets.filter(
    (a) => a.nia.toLowerCase().includes(q) || a.descripcion.toLowerCase().includes(q),
  )
})

function handleAssetSelect(asset: AssetOption) {
  selectedAsset.value = asset
  formData.value.assetNia = asset.nia
  showAssetSearch.value = false
}

function handleDocumentUpload(e: Event) {
  const file = (e.target as HTMLInputElement).files?.[0]
  if (file) uploadedDoc.value = { name: file.name, size: `${Math.round(file.size / 1024)} KB` }
}

function handleSubmit() { /* wireframe */ }
</script>
