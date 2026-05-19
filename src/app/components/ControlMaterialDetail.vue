<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <div class="flex items-center gap-4">
        <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
          <ArrowLeft class="w-5 h-5 text-gray-700" />
        </button>
        <div>
          <h1 class="text-2xl text-gray-900">Ficha de Material de Control</h1>
          <p class="text-sm text-gray-600 mt-1">{{ material.nim }} - {{ material.descripcion }}</p>
        </div>
      </div>

      <div class="flex items-center gap-2">
        <button
          v-if="canEdit"
          class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors"
        >
          <Edit class="w-4 h-4" />
          <span>Editar</span>
        </button>
        <div class="relative">
          <button
            @click="showActaDropdown = !showActaDropdown"
            class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
          >
            <span>Generar Acta</span>
            <ChevronDown class="w-4 h-4" />
          </button>
          <div
            v-if="showActaDropdown"
            class="absolute right-0 mt-2 w-56 bg-white border-2 border-gray-300 rounded shadow-lg z-10"
          >
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ARMC (Recepción)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">AIMC (Inventario)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ATMC (Transferencia)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ABMC (Baja)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ADMC (Descargo)</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Tabs -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="border-b-2 border-gray-300 flex overflow-x-auto">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          @click="activeTab = tab.id"
          :class="[
            'px-6 py-3 text-sm whitespace-nowrap transition-colors',
            activeTab === tab.id ? 'bg-gray-700 text-white' : 'text-gray-700 hover:bg-gray-100'
          ]"
        >
          {{ tab.label }}
        </button>
      </div>

      <!-- Tab content -->
      <div class="p-6">
        <!-- Datos Generales -->
        <div v-if="activeTab === 'general'" class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-6">
          <FieldDisplay label="NIM"                    :value="material.nim" />
          <FieldDisplay label="Código"                 :value="material.codigo" />
          <FieldDisplay label="Descripción"            :value="material.descripcion" class-name="md:col-span-2" />
          <FieldDisplay label="Grupo"                  :value="material.grupo" />
          <FieldDisplay label="Subcuenta"              :value="material.subcuenta" />
          <FieldDisplay label="Costo de Compra"        :value="`Bs. ${material.costo}`" />
          <FieldDisplay label="Fecha de Adquisición"   :value="material.fechaAdquisicion" />
          <FieldDisplay label="Fuente de Financiamiento" :value="material.fuenteFinanciamiento" />
          <FieldDisplay label="Estado"                 :value="material.estado" />
          <FieldDisplay label="Régimen"                :value="material.regimen" />
        </div>

        <!-- Responsable y Ubicación -->
        <div v-else-if="activeTab === 'location'" class="space-y-6">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-6">
            <FieldDisplay label="Responsable Actual" :value="`${material.responsable} (${material.cargo})`" class-name="md:col-span-2" />
            <FieldDisplay label="Unidad Actual"      :value="material.unidad" class-name="md:col-span-2" />
          </div>
          <div>
            <h3 class="text-sm text-gray-700 mb-3">Historial de Asignaciones</h3>
            <div class="border-2 border-gray-300 rounded overflow-hidden">
              <table class="w-full">
                <thead class="bg-gray-100 border-b-2 border-gray-300">
                  <tr>
                    <th class="px-4 py-2 text-left text-xs text-gray-700">Fecha</th>
                    <th class="px-4 py-2 text-left text-xs text-gray-700">Responsable</th>
                    <th class="px-4 py-2 text-left text-xs text-gray-700">Unidad</th>
                    <th class="px-4 py-2 text-left text-xs text-gray-700">Tipo</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200">
                  <tr v-for="(item, idx) in assignmentHistory" :key="idx">
                    <td class="px-4 py-2 text-sm text-gray-700">{{ item.fecha }}</td>
                    <td class="px-4 py-2 text-sm text-gray-900">{{ item.responsable }}</td>
                    <td class="px-4 py-2 text-sm text-gray-700">{{ item.unidad }}</td>
                    <td class="px-4 py-2 text-sm text-gray-700">{{ item.tipo }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>

        <!-- Fotografías -->
        <div v-else-if="activeTab === 'photos'" class="space-y-4">
          <button v-if="canEdit" class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
            <Plus class="w-4 h-4" />
            <span>Agregar Foto</span>
          </button>
          <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
            <div
              v-for="photo in photos"
              :key="photo.id"
              class="relative border-2 border-gray-300 rounded-lg overflow-hidden group"
            >
              <div class="aspect-square bg-gray-200 flex items-center justify-center">
                <span class="text-gray-400 text-xs">IMAGEN</span>
              </div>
              <div class="p-2 bg-gray-50 border-t border-gray-300">
                <p class="text-xs text-gray-700 truncate">{{ photo.name }}</p>
              </div>
              <button
                v-if="canEdit"
                class="absolute top-2 right-2 p-1 bg-red-500 text-white rounded opacity-0 group-hover:opacity-100 transition-opacity"
              >
                <Trash2 class="w-4 h-4" />
              </button>
            </div>
          </div>
        </div>

        <!-- Documentos -->
        <div v-else-if="activeTab === 'documents'" class="space-y-4">
          <button v-if="canEdit" class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
            <Upload class="w-4 h-4" />
            <span>Subir Documento</span>
          </button>
          <div class="border-2 border-gray-300 rounded-lg overflow-hidden">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-2 text-left text-xs text-gray-700">Nombre</th>
                  <th class="px-4 py-2 text-left text-xs text-gray-700">Tipo</th>
                  <th class="px-4 py-2 text-left text-xs text-gray-700">Fecha</th>
                  <th class="px-4 py-2 text-left text-xs text-gray-700">Tamaño</th>
                  <th class="px-4 py-2 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="doc in documents" :key="doc.id">
                  <td class="px-4 py-2 text-sm text-gray-900">{{ doc.name }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700">{{ doc.tipo }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700">{{ doc.fecha }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700">{{ doc.size }}</td>
                  <td class="px-4 py-2">
                    <div class="flex items-center gap-2">
                      <button class="p-1 text-gray-600 hover:text-gray-900" title="Descargar"><Download class="w-4 h-4" /></button>
                      <button v-if="canEdit" class="p-1 text-red-600 hover:text-red-800" title="Eliminar"><Trash2 class="w-4 h-4" /></button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Historial -->
        <div v-else-if="activeTab === 'history'" class="space-y-4">
          <div class="relative">
            <div class="absolute left-4 top-0 bottom-0 w-0.5 bg-gray-300"></div>
            <div v-for="(movement, idx) in movements" :key="idx" class="relative pl-12 pb-8">
              <div class="absolute left-2.5 top-1 w-3 h-3 bg-gray-700 rounded-full"></div>
              <div class="border-2 border-gray-300 rounded-lg p-4 bg-white">
                <div class="flex items-start justify-between mb-2">
                  <div>
                    <div class="text-sm text-gray-900 mb-1">{{ movement.tipo }}</div>
                    <div class="text-xs text-gray-600">{{ movement.detalle }}</div>
                  </div>
                  <div class="text-xs text-gray-600">{{ movement.fecha }}</div>
                </div>
                <div class="text-xs text-gray-700 mt-2">
                  <span class="text-gray-600">Proceso:</span> {{ movement.proceso }}
                </div>
                <div class="text-xs text-gray-700">
                  <span class="text-gray-600">{{ movement.origen }} → {{ movement.destino }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Código QR -->
        <div v-else-if="activeTab === 'qr'" class="flex flex-col items-center justify-center space-y-6 py-8">
          <div class="w-64 h-64 border-4 border-gray-300 rounded-lg flex items-center justify-center bg-white">
            <div class="w-48 h-48 bg-gray-200 flex items-center justify-center">
              <span class="text-gray-400 text-xs">QR CODE</span>
            </div>
          </div>
          <div class="text-center">
            <div class="text-2xl text-gray-900 mb-1">{{ material.nim }}</div>
            <div class="text-sm text-gray-600">{{ material.descripcion }}</div>
          </div>
          <button class="flex items-center gap-2 px-6 py-3 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">
            <Printer class="w-5 h-5" />
            <span>Imprimir Etiqueta</span>
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Material Detail:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Same tab structure as Fixed Asset Detail but with only 6 tabs (no Depreciación tab)</li>
          <li>Tabs: Datos Generales, Responsable y Ubicación, Fotografías, Documentos, Historial de Movimientos, Código QR</li>
          <li>Acta types specific to materials: ARMC, AIMC, ATMC, ABMC, ADMC</li>
          <li>Materials don't depreciate, so no depreciation tab or calculations</li>
          <li>Same role-based access controls and layout structure as Fixed Asset Detail</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Edit, ChevronDown, Upload, Trash2, Plus, Printer, Download } from 'lucide-vue-next'
import FieldDisplay from './FieldDisplay.vue'
import type { UserRole } from '../types'

type TabId = 'general' | 'location' | 'photos' | 'documents' | 'history' | 'qr'

const props = defineProps<{ userRole: UserRole; materialId: string | null }>()
const emit = defineEmits<{ back: [] }>()

const activeTab = ref<TabId>('general')
const showActaDropdown = ref(false)

const canEdit = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const tabs: { id: TabId; label: string }[] = [
  { id: 'general',   label: 'Datos Generales' },
  { id: 'location',  label: 'Responsable y Ubicación' },
  { id: 'photos',    label: 'Fotografías' },
  { id: 'documents', label: 'Documentos' },
  { id: 'history',   label: 'Historial de Movimientos' },
  { id: 'qr',        label: 'Código QR' },
]

const material = {
  nim: 'NIM-2024-0001', codigo: '20-01-M12', descripcion: 'Escritorio Metálico Simple',
  grupo: '20-01 Muebles', subcuenta: '20-01', costo: '450.00',
  fechaAdquisicion: '20/02/2024', fuenteFinanciamiento: 'Fondos Propios',
  estado: 'Activo', regimen: 'Patrimonial',
  responsable: 'Pedro Quispe Flores', cargo: 'Auxiliar Administrativo',
  unidad: 'Administración Central',
}

const assignmentHistory = [
  { fecha: '20/02/2024', responsable: 'Pedro Quispe Flores', unidad: 'Administración Central', tipo: 'Asignación Inicial' },
]

const photos = [
  { id: '1', name: 'foto-frontal.jpg' },
  { id: '2', name: 'foto-detalle.jpg' },
]

const documents = [
  { id: '1', name: 'Comprobante-Ingreso.pdf', tipo: 'Comprobante', fecha: '20/02/2024', size: '180 KB' },
]

const movements = [
  { fecha: '20/02/2024', tipo: 'Alta', detalle: 'Recepción inicial', proceso: 'ALTA-MC-2024-001', origen: '—', destino: 'Administración Central' },
]
</script>
