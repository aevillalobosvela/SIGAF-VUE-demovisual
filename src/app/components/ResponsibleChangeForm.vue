<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Cambio de Responsable</h1>
    </div>

    <!-- Form -->
    <form @submit.prevent="handleSubmit" class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <div class="space-y-6">
        <!-- Proceso asociado -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Proceso Asociado <span class="text-red-600">*</span>
          </label>
          <div class="flex gap-2">
            <select v-model="formData.procesoAsociado" class="flex-1 px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione un proceso existente...</option>
              <option value="ASIG-2024-001">ASIG-2024-001 - Cambio de Responsable</option>
              <option value="ASIG-2024-002">ASIG-2024-002 - Asignación de Responsable</option>
            </select>
            <button type="button" class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
              <ExternalLink class="w-4 h-4" />
              <span class="text-sm">Crear nuevo proceso</span>
            </button>
          </div>
        </div>

        <!-- Unidad -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">Unidad <span class="text-red-600">*</span></label>
          <select v-model="formData.unidad" @change="handleUnidadChange" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
            <option value="">Seleccione la unidad...</option>
            <option value="admin-central">Administración Central</option>
            <option value="rectorado">Rectorado</option>
            <option value="sistemas">Sistemas</option>
            <option value="rrhh">Recursos Humanos</option>
            <option value="transporte">Transporte</option>
            <option value="biblioteca">Biblioteca</option>
          </select>
          <p class="text-xs text-gray-600 mt-1">La unidad permanece sin cambios (solo cambia el responsable dentro de la misma unidad)</p>
        </div>

        <!-- Responsables grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Responsable Actual (read-only) -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">Responsable Actual</label>
            <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-700">
              {{ formData.responsableActual || 'Auto-completar al seleccionar unidad' }}
            </div>
            <p class="text-xs text-gray-600 mt-1">Auto-completado según unidad seleccionada</p>
          </div>
          <!-- Nuevo Responsable -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">Nuevo Responsable <span class="text-red-600">*</span></label>
            <select v-model="formData.nuevoResponsable" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione...</option>
              <option value="carlos-mamani">Carlos Mamani López (Coordinador)</option>
              <option value="maria-gonzalez">María González Quispe (Jefa de RRHH)</option>
              <option value="ana-flores">Ana Flores Choque (Técnico)</option>
              <option value="pedro-quispe">Pedro Quispe Flores (Auxiliar)</option>
            </select>
          </div>
        </div>

        <!-- Asset selection -->
        <div class="pt-6 border-t-2 border-gray-300">
          <div class="flex items-center justify-between mb-4">
            <h3 class="text-lg text-gray-900">
              Bienes para Cambio de Responsable
              <span v-if="selectedCount > 0" class="ml-2 text-sm text-gray-600">({{ selectedCount }} seleccionados)</span>
            </h3>
            <div class="relative w-64">
              <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
              <input v-model="assetSearchText" type="text" placeholder="Buscar bienes..." class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm" />
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
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable Actual</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr
                  v-for="asset in filteredAssets"
                  :key="asset.id"
                  :class="['hover:bg-gray-50', asset.selected ? 'bg-blue-50' : '']"
                >
                  <td class="px-4 py-3"><input type="checkbox" :checked="asset.selected" @change="toggleAsset(asset.id)" class="w-4 h-4" /></td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ asset.niaNim }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ asset.descripcion }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ asset.responsable }}</td>
                </tr>
              </tbody>
            </table>
          </div>
          <p class="text-xs text-gray-600 mt-2">Puede seleccionar uno o varios bienes para el cambio de responsable (selección parcial permitida)</p>
        </div>

        <!-- Observaciones -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">Observaciones</label>
          <textarea v-model="formData.observaciones" rows="3" class="w-full px-3 py-2 border-2 border-gray-300 rounded" placeholder="Motivo del cambio de responsable u otras notas..." />
        </div>
      </div>

      <!-- Form footer -->
      <div class="flex items-center justify-end gap-3 mt-8 pt-6 border-t-2 border-gray-300">
        <button type="button" @click="emit('cancel')" class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">Cancelar</button>
        <button type="submit" class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">Registrar Cambio</button>
      </div>
    </form>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Responsible Change Form:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Similar to Transfer Form but simpler - no unit change, only responsible change within same unit</li>
          <li>Proceso asociado with dropdown or "Crear nuevo proceso" link</li>
          <li>Unidad (single select) - no unit change, stays the same</li>
          <li>Responsable Actual is read-only and auto-filled based on selected unit</li>
          <li>Nuevo Responsable uses searchable select (simulated here as regular select)</li>
          <li>Asset selection table with checkboxes - partial selection allowed</li>
          <li>Footer with "Registrar Cambio" (primary) and "Cancelar" (secondary) buttons</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Search, ExternalLink } from 'lucide-vue-next'
import type { UserRole } from '../types'

interface Asset { id: string; niaNim: string; descripcion: string; responsable: string; selected: boolean }

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ cancel: [] }>()

const formData = ref({
  procesoAsociado: '', unidad: '', responsableActual: '', nuevoResponsable: '', observaciones: '',
})
const assetSearchText = ref('')

const assets = ref<Asset[]>([
  { id: '1', niaNim: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', responsable: 'Juan Pérez Mamani', selected: false },
  { id: '2', niaNim: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',       responsable: 'Juan Pérez Mamani', selected: false },
  { id: '3', niaNim: 'AF-2023-0145', descripcion: 'Impresora Multifuncional Canon',        responsable: 'Juan Pérez Mamani', selected: false },
  { id: '4', niaNim: 'NIM-2024-0001',descripcion: 'Escritorio Metálico Simple',           responsable: 'Juan Pérez Mamani', selected: false },
])

const filteredAssets = computed(() => {
  const q = assetSearchText.value.toLowerCase()
  if (!q) return assets.value
  return assets.value.filter((a) => a.niaNim.toLowerCase().includes(q) || a.descripcion.toLowerCase().includes(q))
})

const selectedCount = computed(() => assets.value.filter((a) => a.selected).length)
const allSelected   = computed(() => assets.value.every((a) => a.selected))

function toggleAsset(id: string) {
  const a = assets.value.find((x) => x.id === id)
  if (a) a.selected = !a.selected
}
function toggleAllAssets() {
  const next = !allSelected.value
  assets.value.forEach((a) => (a.selected = next))
}
function handleUnidadChange() {
  formData.value.responsableActual =
    formData.value.unidad === 'admin-central'
      ? 'Juan Pérez Mamani (Jefe de Sistemas)'
      : 'Auto-completar según unidad'
}
function handleSubmit() { /* wireframe */ }
</script>
