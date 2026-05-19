<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Crear Nuevo Proceso</h1>
    </div>

    <!-- Form -->
    <form @submit.prevent="handleSubmit" class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <div class="space-y-6">
        <!-- Tipo de proceso -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Tipo de Proceso <span class="text-red-600">*</span>
          </label>
          <select v-model="formData.tipoProceso" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
            <option value="">Seleccione el tipo de proceso...</option>
            <optgroup v-for="(cat, idx) in processCategories" :key="idx" :label="cat.category">
              <option v-for="(type, tIdx) in cat.types" :key="tIdx" :value="type">{{ type }}</option>
            </optgroup>
          </select>
        </div>

        <!-- Unidad de referencia -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Unidad de Referencia <span class="text-red-600">*</span>
          </label>
          <select v-model="formData.unidadReferencia" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
            <option value="">Seleccione la unidad...</option>
            <option value="admin-central">Administración Central</option>
            <option value="rectorado">Rectorado</option>
            <option value="sistemas">Sistemas</option>
            <option value="rrhh">Recursos Humanos</option>
            <option value="transporte">Transporte</option>
            <option value="biblioteca">Biblioteca</option>
            <option value="almacenes">Almacenes</option>
          </select>
          <p class="text-xs text-gray-600 mt-1">Unidad responsable del proceso</p>
        </div>

        <!-- Observaciones -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">Observaciones</label>
          <textarea
            v-model="formData.observaciones"
            rows="4"
            class="w-full px-3 py-2 border-2 border-gray-300 rounded"
            placeholder="Notas adicionales sobre el proceso..."
          />
        </div>

        <!-- Asset selection -->
        <div class="pt-6 border-t-2 border-gray-300">
          <div class="flex items-center justify-between mb-4">
            <h3 class="text-lg text-gray-900">
              Bienes Asociados
              <span v-if="selectedCount > 0" class="ml-2 text-sm text-gray-600">({{ selectedCount }} seleccionados)</span>
            </h3>
            <div class="relative w-64">
              <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
              <input
                v-model="assetSearchText"
                type="text"
                placeholder="Buscar bienes..."
                class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
              />
            </div>
          </div>

          <div class="border-2 border-gray-300 rounded-lg overflow-hidden">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 w-12">
                    <input type="checkbox" :checked="allSelected" @change="toggleAllAssets" class="w-4 h-4" />
                  </th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">NIA/NIM</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr
                  v-for="asset in filteredAssets"
                  :key="asset.id"
                  :class="['hover:bg-gray-50', asset.selected ? 'bg-blue-50' : '']"
                >
                  <td class="px-4 py-3">
                    <input type="checkbox" :checked="asset.selected" @change="toggleAsset(asset.id)" class="w-4 h-4" />
                  </td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ asset.niaNim }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ asset.descripcion }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ asset.unidad }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ asset.responsable }}</td>
                </tr>
              </tbody>
            </table>
          </div>
          <p class="text-xs text-gray-600 mt-2">Seleccione los bienes que se incluirán en este proceso</p>
        </div>
      </div>

      <!-- Form footer -->
      <div class="flex items-center justify-end gap-3 mt-8 pt-6 border-t-2 border-gray-300">
        <button type="button" @click="emit('cancel')" class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
          Cancelar
        </button>
        <button type="submit" class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">
          Crear Proceso
        </button>
      </div>
    </form>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - New Process Form:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Single-page form with Tipo de proceso (grouped select by 6 categories)</li>
          <li>Categories: Alta/Incorporación, Movimiento, Regularización, Baja/Salida, Custodia y Préstamo, Incidencias</li>
          <li>Unidad de referencia (searchable select - simulated here as regular select)</li>
          <li>Observaciones textarea for additional notes</li>
          <li>Asset selection table with search bar and checkboxes for multiple selection</li>
          <li>Selected count indicator shows how many assets are currently selected</li>
          <li>Select all checkbox in table header for bulk selection</li>
          <li>Footer with "Crear Proceso" (primary) and "Cancelar" (secondary) buttons</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Search } from 'lucide-vue-next'
import type { UserRole } from '../types'

interface Asset {
  id: string; niaNim: string; descripcion: string
  unidad: string; responsable: string; selected: boolean
}

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ cancel: [] }>()

const formData = ref({ tipoProceso: '', unidadReferencia: '', observaciones: '' })
const assetSearchText = ref('')

const assets = ref<Asset[]>([
  { id: '1', niaNim: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', unidad: 'Administración Central', responsable: 'Juan Pérez',    selected: false },
  { id: '2', niaNim: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',       unidad: 'Rectorado',              responsable: 'María González', selected: false },
  { id: '3', niaNim: 'NIM-2024-0001',descripcion: 'Escritorio Metálico Simple',           unidad: 'Sistemas',               responsable: 'Carlos Mamani',  selected: false },
  { id: '4', niaNim: 'AF-2023-0145', descripcion: 'Vehículo Toyota Hilux 4x4',            unidad: 'Transporte',             responsable: 'Ana Flores',     selected: false },
])

const processCategories = [
  { category: 'Alta / Incorporación', types: ['Alta de Activos Fijos', 'Alta de Materiales de Control', 'Incorporación por Donación'] },
  { category: 'Movimiento',           types: ['Transferencia entre Unidades', 'Cambio de Responsable', 'Cambio de Ubicación'] },
  { category: 'Regularización',       types: ['Revalorización', 'Actualización de Datos', 'Regularización de Inventario'] },
  { category: 'Baja / Salida',        types: ['Baja por Obsolescencia', 'Baja por Deterioro', 'Baja por Robo/Extravío', 'Donación/Transferencia Externa'] },
  { category: 'Custodia y Préstamo',  types: ['Asignación en Custodia', 'Préstamo Temporal', 'Devolución de Préstamo'] },
  { category: 'Incidencias',          types: ['Reporte de Faltante', 'Reporte de Daño', 'Ajuste por Inventario'] },
]

const filteredAssets = computed(() => {
  const q = assetSearchText.value.toLowerCase()
  if (!q) return assets.value
  return assets.value.filter(
    (a) => a.niaNim.toLowerCase().includes(q) || a.descripcion.toLowerCase().includes(q),
  )
})

const selectedCount = computed(() => assets.value.filter((a) => a.selected).length)
const allSelected   = computed(() => assets.value.every((a) => a.selected))

function toggleAsset(id: string) {
  const asset = assets.value.find((a) => a.id === id)
  if (asset) asset.selected = !asset.selected
}

function toggleAllAssets() {
  const next = !allSelected.value
  assets.value.forEach((a) => (a.selected = next))
}

function handleSubmit() {
  // wireframe — no backend
}
</script>
