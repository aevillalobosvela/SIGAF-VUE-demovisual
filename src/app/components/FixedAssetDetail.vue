<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center justify-between">
      <div class="flex items-center gap-4">
        <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
          <ArrowLeft class="w-5 h-5 text-gray-700" />
        </button>
        <div>
          <h1 class="text-2xl text-gray-900">Ficha de Activo Fijo</h1>
          <p class="text-sm text-gray-600 mt-1">{{ asset.nia }} - {{ asset.descripcion }}</p>
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
            class="absolute right-0 mt-2 w-48 bg-white border-2 border-gray-300 rounded shadow-lg z-10"
          >
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ARAF (Alta)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">AIAF (Inventario)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ATAF (Transferencia)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ABAF (Baja)</button>
            <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">ADAF (Descargo)</button>
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

      <div class="p-6">
        <!-- Datos Generales -->
        <div v-if="activeTab === 'general'" class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-6">
          <FieldDisplay label="NIA"                      :value="asset.nia" />
          <FieldDisplay label="Código"                   :value="asset.codigo" />
          <FieldDisplay label="Descripción"              :value="asset.descripcion"        class-name="md:col-span-2" />
          <FieldDisplay label="Categoría Contable"       :value="asset.categoriaContable" />
          <FieldDisplay label="Subcuenta"                :value="asset.subcuenta" />
          <FieldDisplay label="Costo de Compra"          :value="`Bs. ${asset.costo}`" />
          <FieldDisplay label="Fecha de Adquisición"     :value="asset.fechaAdquisicion" />
          <FieldDisplay label="Fuente de Financiamiento" :value="asset.fuenteFinanciamiento" />
          <FieldDisplay label="Estado"                   :value="asset.estado" />
          <FieldDisplay label="Régimen"                  :value="asset.regimen" />
        </div>

        <!-- Responsable y Ubicación -->
        <div v-else-if="activeTab === 'location'" class="space-y-6">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-6">
            <FieldDisplay label="Responsable Actual" :value="`${asset.responsable} (${asset.cargo})`" class-name="md:col-span-2" />
            <FieldDisplay label="Unidad Actual"      :value="asset.unidad"                            class-name="md:col-span-2" />
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
          <div v-if="showDocWarning" class="p-4 bg-yellow-50 border-2 border-yellow-300 rounded-lg flex items-start gap-3">
            <div class="text-yellow-700 text-sm">
              <strong>Advertencia:</strong> Este activo tiene un valor superior a Bs. 50,000 y no tiene documentos adjuntos. Por favor, adjunte la factura y documentación respaldatoria.
            </div>
          </div>
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

        <!-- Historial de Movimientos -->
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

        <!-- Depreciación -->
        <div v-else-if="activeTab === 'depreciation'" class="space-y-4">
          <div class="border-2 border-gray-300 rounded-lg overflow-hidden">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-2 text-left text-xs text-gray-700">Gestión</th>
                  <th class="px-4 py-2 text-right text-xs text-gray-700">Dep. Período (Bs.)</th>
                  <th class="px-4 py-2 text-right text-xs text-gray-700">Dep. Acumulada (Bs.)</th>
                  <th class="px-4 py-2 text-right text-xs text-gray-700">Vida Útil Restante (meses)</th>
                  <th class="px-4 py-2 text-center text-xs text-gray-700">Revalúo</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="(row, idx) in depreciationData" :key="idx">
                  <td class="px-4 py-2 text-sm text-gray-900">{{ row.gestion }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700 text-right">{{ row.periodo }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700 text-right">{{ row.acumulada }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700 text-right">{{ row.vidaUtil }}</td>
                  <td class="px-4 py-2 text-sm text-gray-700 text-center">{{ row.revaluo }}</td>
                </tr>
              </tbody>
            </table>
          </div>
          <div class="text-xs text-gray-600 italic">
            * Método de depreciación: Lineal — Decreto Supremo 24051
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
            <div class="text-2xl text-gray-900 mb-1">{{ asset.nia }}</div>
            <div class="text-sm text-gray-600">{{ asset.descripcion }}</div>
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
        <strong>Wireframe Notes - Asset Detail:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Detail view with 7 tabs: Datos Generales, Responsable y Ubicación, Fotografías, Documentos, Historial de Movimientos, Depreciación, Código QR</li>
          <li>Page header with back button, asset title, "Editar" button (role-based), and "Generar Acta" dropdown (ARAF, AIAF, ATAF, ABAF, ADAF)</li>
          <li>Datos Generales: two-column read-only fields with all asset metadata</li>
          <li>Responsable y Ubicación: current assignment + history table of previous assignments</li>
          <li>Fotografías: image gallery grid with "Agregar Foto" button and delete icons on hover</li>
          <li>Documentos: file list table with upload button + warning banner if value > Bs. 50,000 and no documents</li>
          <li>Historial de Movimientos: vertical timeline showing Alta, Transferencia, Cambio de Responsable with process numbers</li>
          <li>Depreciación: table with yearly data (Gestión, Dep. Período, Dep. Acumulada, Vida Útil, Revalúo) + note about Decreto 24051</li>
          <li>Código QR: centered QR code image with NIA below and "Imprimir Etiqueta" button</li>
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

type TabId = 'general' | 'location' | 'photos' | 'documents' | 'history' | 'depreciation' | 'qr'

const props = defineProps<{ userRole: UserRole; assetId: string | null }>()
const emit = defineEmits<{ back: [] }>()

const activeTab       = ref<TabId>('general')
const showActaDropdown = ref(false)

const canEdit = computed(() =>
  props.userRole === 'Administrador' || props.userRole === 'Operador',
)

const tabs: { id: TabId; label: string }[] = [
  { id: 'general',      label: 'Datos Generales' },
  { id: 'location',     label: 'Responsable y Ubicación' },
  { id: 'photos',       label: 'Fotografías' },
  { id: 'documents',    label: 'Documentos' },
  { id: 'history',      label: 'Historial de Movimientos' },
  { id: 'depreciation', label: 'Depreciación' },
  { id: 'qr',           label: 'Código QR' },
]

const asset = {
  nia: 'AF-2024-0001', codigo: '03-01-A01',
  descripcion: 'Computadora Portátil HP ProBook 450',
  categoriaContable: 'Equipos de Computación', subcuenta: '03-01',
  costo: '8,500.00', fechaAdquisicion: '15/03/2024',
  fuenteFinanciamiento: 'TGN', estado: 'Activo', regimen: 'Patrimonial',
  responsable: 'Juan Pérez Mamani', cargo: 'Jefe de Sistemas',
  unidad: 'Administración Central',
}

const assignmentHistory = [
  { fecha: '15/03/2024', responsable: 'Juan Pérez Mamani',    unidad: 'Administración Central', tipo: 'Asignación Inicial' },
  { fecha: '10/01/2024', responsable: 'María González Quispe', unidad: 'Rectorado',              tipo: 'Transferencia'      },
]

const photos = [
  { id: '1', name: 'foto-frontal.jpg' },
  { id: '2', name: 'foto-lateral.jpg' },
  { id: '3', name: 'foto-serie.jpg'   },
]

const documents = [
  { id: '1', name: 'Factura-Original-2024-001.pdf', tipo: 'Factura',   fecha: '15/03/2024', size: '245 KB' },
  { id: '2', name: 'Garantia-HP.pdf',               tipo: 'Garantía',  fecha: '15/03/2024', size: '120 KB' },
]

const showDocWarning = computed(() => {
  const costo = parseFloat(asset.costo.replace(/,/g, ''))
  return costo > 50000 && documents.length === 0
})

const movements = [
  { fecha: '15/03/2024', tipo: 'Alta',                 detalle: 'Adquisición inicial',        proceso: 'ALTA-2024-001',  origen: '—',            destino: 'Administración Central' },
  { fecha: '10/01/2024', tipo: 'Transferencia',         detalle: 'Transferencia entre unidades',proceso: 'TRANS-2024-045', origen: 'Rectorado',    destino: 'Administración Central' },
  { fecha: '05/01/2024', tipo: 'Cambio de Responsable', detalle: 'Nuevo responsable asignado',  proceso: 'ASIG-2024-012',  origen: 'María González',destino: 'Juan Pérez Mamani'      },
]

const depreciationData = [
  { gestion: '2024', periodo: '2,125.00', acumulada: '2,125.00', vidaUtil: '36', revaluo: 'No' },
  { gestion: '2023', periodo: '0.00',     acumulada: '0.00',     vidaUtil: '48', revaluo: 'No' },
]
</script>
