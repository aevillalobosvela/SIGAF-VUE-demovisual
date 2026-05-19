<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <div>
        <h1 class="text-2xl text-gray-900">Resultado de Inventario</h1>
        <p class="text-sm text-gray-600 mt-1">Administración Central - 15/03/2024</p>
      </div>
    </div>

    <!-- Summary stat cards -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <!-- Encontrados -->
      <div class="bg-white border-2 border-green-300 rounded-lg p-6">
        <div class="flex items-center gap-2 mb-3">
          <CheckCircle class="w-6 h-6 text-green-600" />
          <h3 class="text-sm text-gray-700">Encontrados</h3>
        </div>
        <div class="text-4xl text-green-600 mb-1">{{ foundCount }}</div>
        <div class="text-xs text-gray-600">{{ ((foundCount / totalExpected) * 100).toFixed(1) }}% del total esperado</div>
      </div>
      <!-- Faltantes -->
      <div class="bg-white border-2 border-red-300 rounded-lg p-6">
        <div class="flex items-center gap-2 mb-3">
          <AlertCircle class="w-6 h-6 text-red-600" />
          <h3 class="text-sm text-gray-700">Faltantes</h3>
        </div>
        <div class="text-4xl text-red-600 mb-1">{{ missingCount }}</div>
        <div class="text-xs text-gray-600">{{ ((missingCount / totalExpected) * 100).toFixed(1) }}% del total esperado</div>
      </div>
      <!-- Sobrantes -->
      <div class="bg-white border-2 border-orange-300 rounded-lg p-6">
        <div class="flex items-center gap-2 mb-3">
          <AlertTriangle class="w-6 h-6 text-orange-600" />
          <h3 class="text-sm text-gray-700">Sobrantes</h3>
        </div>
        <div class="text-4xl text-orange-600 mb-1">{{ extraCount }}</div>
        <div class="text-xs text-gray-600">No registrados en esta unidad</div>
      </div>
    </div>

    <!-- Tables side by side -->
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
      <!-- Faltantes -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-4 py-3 bg-red-50 border-b-2 border-gray-300">
          <h2 class="text-lg text-gray-900 flex items-center gap-2">
            <AlertCircle class="w-5 h-5 text-red-600" />
            Bienes Faltantes ({{ missingCount }})
          </h2>
        </div>
        <div class="overflow-x-auto">
          <table class="w-full">
            <thead class="bg-gray-100 border-b-2 border-gray-300">
              <tr>
                <th class="px-4 py-2 text-left text-xs text-gray-700">NIA/NIM</th>
                <th class="px-4 py-2 text-left text-xs text-gray-700">Descripción</th>
                <th class="px-4 py-2 text-left text-xs text-gray-700">Responsable</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-gray-200">
              <tr v-for="asset in mockMissingAssets" :key="asset.id" class="hover:bg-gray-50">
                <td class="px-4 py-2 text-sm text-gray-900">{{ asset.niaNim }}</td>
                <td class="px-4 py-2 text-sm text-gray-700">{{ asset.descripcion }}</td>
                <td class="px-4 py-2 text-sm text-gray-700">{{ asset.responsable }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Encontrados -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-4 py-3 bg-green-50 border-b-2 border-gray-300">
          <h2 class="text-lg text-gray-900 flex items-center gap-2">
            <CheckCircle class="w-5 h-5 text-green-600" />
            Bienes Encontrados ({{ foundCount }})
          </h2>
        </div>
        <div class="overflow-x-auto">
          <table class="w-full">
            <thead class="bg-gray-100 border-b-2 border-gray-300">
              <tr>
                <th class="px-4 py-2 text-left text-xs text-gray-700">NIA/NIM</th>
                <th class="px-4 py-2 text-left text-xs text-gray-700">Descripción</th>
                <th class="px-4 py-2 text-left text-xs text-gray-700">Responsable</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-gray-200">
              <tr v-for="asset in mockFoundAssets.slice(0, 5)" :key="asset.id" class="hover:bg-gray-50">
                <td class="px-4 py-2 text-sm text-gray-900">{{ asset.niaNim }}</td>
                <td class="px-4 py-2 text-sm text-gray-700">{{ asset.descripcion }}</td>
                <td class="px-4 py-2 text-sm text-gray-700">{{ asset.responsable }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div v-if="foundCount > 5" class="px-4 py-2 text-xs text-gray-600 bg-gray-50 border-t border-gray-200">
          Mostrando 5 de {{ foundCount }} bienes encontrados
        </div>
      </div>
    </div>

    <!-- Sobrantes (si hay) -->
    <div v-if="extraCount > 0" class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-4 py-3 bg-orange-50 border-b-2 border-gray-300">
        <h2 class="text-lg text-gray-900 flex items-center gap-2">
          <AlertTriangle class="w-5 h-5 text-orange-600" />
          Bienes Sobrantes ({{ extraCount }})
        </h2>
        <p class="text-xs text-gray-600 mt-1">Bienes encontrados que no están registrados en esta unidad</p>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-2 text-left text-xs text-gray-700">NIA/NIM</th>
              <th class="px-4 py-2 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-2 text-left text-xs text-gray-700">Unidad Registrada</th>
              <th class="px-4 py-2 text-left text-xs text-gray-700">Responsable</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="asset in mockExtraAssets" :key="asset.id" class="hover:bg-gray-50">
              <td class="px-4 py-2 text-sm text-gray-900">{{ asset.niaNim }}</td>
              <td class="px-4 py-2 text-sm text-gray-700">{{ asset.descripcion }}</td>
              <td class="px-4 py-2 text-sm text-gray-700">{{ asset.unidad }}</td>
              <td class="px-4 py-2 text-sm text-gray-700">{{ asset.responsable }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Action buttons -->
    <div class="flex items-center gap-3">
      <button class="flex items-center gap-2 px-6 py-3 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">
        <FileText class="w-5 h-5" />
        <span>Generar Acta A.Inv.AF</span>
      </button>
      <button class="flex items-center gap-2 px-6 py-3 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
        <FileText class="w-5 h-5" />
        <span>Generar Acta A.Inv.MC</span>
      </button>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Inventory Result:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Summary screen after inventory session with 3 large stat cards</li>
          <li>Stat cards: "Encontrados" (green), "Faltantes" (red), "Sobrantes" (orange)</li>
          <li>Two tables side by side (or stacked on mobile): "Bienes Faltantes" and "Bienes Encontrados"</li>
          <li>Optional third table for "Bienes Sobrantes" (items found but registered to different unit)</li>
          <li>Action buttons: "Generar Acta A.Inv.AF" and "Generar Acta A.Inv.MC"</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { ArrowLeft, CheckCircle, AlertCircle, AlertTriangle, FileText } from 'lucide-vue-next'

defineProps<{ inventoryId: string | null }>()
const emit = defineEmits<{ back: [] }>()

interface Asset { id: string; niaNim: string; descripcion: string; unidad: string; responsable: string }

const mockFoundAssets: Asset[] = [
  { id: '1', niaNim: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', unidad: 'Administración Central', responsable: 'Juan Pérez'    },
  { id: '2', niaNim: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',       unidad: 'Administración Central', responsable: 'María González' },
  { id: '3', niaNim: 'NIM-2024-0001',descripcion: 'Escritorio Metálico Simple',           unidad: 'Administración Central', responsable: 'Carlos Mamani'  },
]
const mockMissingAssets: Asset[] = [
  { id: '4', niaNim: 'AF-2023-0145', descripcion: 'Impresora Multifuncional Canon',  unidad: 'Administración Central', responsable: 'Ana Flores'   },
  { id: '5', niaNim: 'AF-2022-0089', descripcion: 'Proyector Epson PowerLite',        unidad: 'Administración Central', responsable: 'Pedro Quispe' },
]
const mockExtraAssets: Asset[] = [
  { id: '6', niaNim: 'AF-2023-0234', descripcion: 'Silla Giratoria Ergonómica', unidad: 'Rectorado', responsable: 'Sofia Machaca' },
]

const foundCount   = computed(() => mockFoundAssets.length)
const missingCount = computed(() => mockMissingAssets.length)
const extraCount   = computed(() => mockExtraAssets.length)
const totalExpected = computed(() => foundCount.value + missingCount.value)
</script>
