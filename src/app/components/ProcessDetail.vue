<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Detalle de Proceso</h1>
    </div>

    <!-- SECTION 1 — Header info -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <div class="flex items-start justify-between mb-6">
        <div class="flex-1">
          <div class="text-3xl text-gray-900 mb-2">{{ process.numero }}</div>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-3">
            <div>
              <span class="text-sm text-gray-600">Tipo: </span>
              <span class="text-sm text-gray-900">{{ process.tipo }}</span>
            </div>
            <div>
              <span class="text-sm text-gray-600">Unidad de Referencia: </span>
              <span class="text-sm text-gray-900">{{ process.unidadReferencia }}</span>
            </div>
            <div>
              <span class="text-sm text-gray-600">Fecha de Creación: </span>
              <span class="text-sm text-gray-900">{{ process.fechaCreacion }}</span>
            </div>
            <div>
              <span class="text-sm text-gray-600">Creado por: </span>
              <span class="text-sm text-gray-900">{{ process.creadoPor }}</span>
            </div>
          </div>
        </div>
        <span :class="['inline-block px-3 py-1 text-sm border rounded', getStatusColor(process.estado)]">
          {{ process.estado }}
        </span>
      </div>

      <!-- Action buttons -->
      <div class="flex items-center gap-3 pt-4 border-t-2 border-gray-300">
        <template v-if="canEdit && process.estado !== 'Cerrado'">
          <button class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
            <Plus class="w-4 h-4" />
            <span>Agregar Bienes</span>
          </button>
          <button class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
            <FileText class="w-4 h-4" />
            <span>Generar Acta</span>
          </button>
          <button
            @click="showCloseConfirm = true"
            class="flex items-center gap-2 px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 transition-colors"
          >
            <AlertTriangle class="w-4 h-4" />
            <span>Cerrar Proceso</span>
          </button>
        </template>
        <button
          v-if="canReopen && process.estado === 'Cerrado'"
          class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
        >
          <span>Reabrir</span>
        </button>
      </div>
    </div>

    <!-- SECTION 2 — Bienes asociados -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
        <h2 class="text-lg text-gray-900">Bienes Asociados</h2>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">NIA/NIM</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable Origen</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable Destino</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad Origen</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad Destino</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha Efectiva</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="asset in associatedAssets" :key="asset.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ asset.niaNim }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ asset.descripcion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.responsableOrigen }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.responsableDestino }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.unidadOrigen }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.unidadDestino }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ asset.fechaEfectiva }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- SECTION 3 — Documentos adjuntos -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50 flex items-center justify-between">
        <h2 class="text-lg text-gray-900">Documentos Adjuntos</h2>
        <button
          v-if="canEdit && process.estado !== 'Cerrado'"
          class="flex items-center gap-2 px-3 py-1.5 text-sm border-2 border-gray-300 rounded hover:bg-gray-100"
        >
          <Upload class="w-4 h-4" />
          <span>Adjuntar Documento</span>
        </button>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Nombre</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Subido por</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Tamaño</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="doc in documents" :key="doc.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900">{{ doc.nombre }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ doc.tipo }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ doc.fecha }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ doc.subidoPor }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ doc.size }}</td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-2">
                  <button class="p-1 text-gray-600 hover:text-gray-900" title="Descargar"><Download class="w-4 h-4" /></button>
                  <button v-if="canEdit && process.estado !== 'Cerrado'" class="p-1 text-red-600 hover:text-red-800" title="Eliminar"><Trash2 class="w-4 h-4" /></button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- SECTION 4 — Línea de tiempo -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <h2 class="text-lg text-gray-900 mb-6">Línea de Tiempo del Proceso</h2>
      <div class="relative">
        <!-- Línea de fondo -->
        <div class="absolute top-5 left-0 right-0 h-0.5 bg-gray-300">
          <div
            class="h-full bg-gray-700 transition-all"
            :style="{ width: `${(timeline.filter(t => t.completed).length / timeline.length) * 100}%` }"
          ></div>
        </div>
        <!-- Pasos -->
        <div class="relative flex justify-between">
          <div v-for="(step, idx) in timeline" :key="idx" class="flex flex-col items-center">
            <div
              :class="[
                'w-10 h-10 rounded-full border-2 flex items-center justify-center z-10',
                step.completed ? 'bg-gray-700 border-gray-700' : 'bg-white border-gray-300'
              ]"
            >
              <CheckCircle v-if="step.completed" class="w-5 h-5 text-white" />
              <div v-else class="w-3 h-3 bg-gray-300 rounded-full"></div>
            </div>
            <div class="mt-3 text-center">
              <div :class="['text-sm mb-1', step.completed ? 'text-gray-900' : 'text-gray-500']">{{ step.estado }}</div>
              <div v-if="step.fecha"   class="text-xs text-gray-600">{{ step.fecha }}</div>
              <div v-if="step.usuario" class="text-xs text-gray-600">{{ step.usuario }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal de confirmación de cierre -->
    <Teleport to="body">
      <div v-if="showCloseConfirm" class="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
        <div class="bg-white rounded-lg p-6 max-w-md w-full mx-4 border-2 border-gray-300">
          <div class="flex items-start gap-3 mb-4">
            <AlertTriangle class="w-6 h-6 text-red-600 flex-shrink-0 mt-0.5" />
            <div>
              <h3 class="text-lg text-gray-900 mb-2">Confirmar Cierre de Proceso</h3>
              <p class="text-sm text-gray-600">
                ¿Está seguro que desea cerrar el proceso <strong>{{ process.numero }}</strong>?
                Esta acción no se puede deshacer sin autorización especial.
              </p>
            </div>
          </div>
          <div class="flex items-center justify-end gap-3 mt-6">
            <button @click="showCloseConfirm = false" class="px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
              Cancelar
            </button>
            <button @click="handleCloseProcess" class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700">
              Cerrar Proceso
            </button>
          </div>
        </div>
      </div>
    </Teleport>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Process Detail:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Four-section layout stacked vertically</li>
          <li>Section 1 (Header): Process number (large), type, reference unit, creation date, status badge, created-by user, action buttons</li>
          <li>Action buttons: "Agregar Bienes", "Generar Acta", "Cerrar Proceso" (destructive, requires confirmation), "Reabrir" (admin only)</li>
          <li>Section 2: Associated Assets table with columns for NIA/NIM, Description, Responsible Origin/Destination, Unit Origin/Destination, Effective Date</li>
          <li>Section 3: Attached Documents table with file type, date, uploader, and "Adjuntar Documento" button</li>
          <li>Section 4: Status Timeline - horizontal stepper showing Pendiente → Procesado → Cerrado with dates and users for each transition</li>
          <li>Close confirmation dialog with warning icon and destructive action confirmation</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Plus, FileText, Upload, Download, Trash2, AlertTriangle, CheckCircle } from 'lucide-vue-next'
import type { UserRole } from '../types'

type EstadoProceso = 'Pendiente' | 'Procesado' | 'Cerrado'

const props = defineProps<{ userRole: UserRole; processId: string | null }>()
const emit = defineEmits<{ back: [] }>()

const showCloseConfirm = ref(false)

const canEdit   = computed(() => props.userRole === 'Administrador' || props.userRole === 'Operador')
const canReopen = computed(() => props.userRole === 'Administrador')

const process = {
  numero: 'TRANS-2024-001', tipo: 'Transferencia entre Unidades',
  unidadReferencia: 'Administración Central', fechaCreacion: '15/03/2024',
  estado: 'Pendiente' as EstadoProceso, creadoPor: 'Juan Pérez Mamani',
}

const associatedAssets = [
  { id: '1', niaNim: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', responsableOrigen: 'María González', responsableDestino: 'Carlos Mamani', unidadOrigen: 'Rectorado',              unidadDestino: 'Administración Central', fechaEfectiva: '20/03/2024' },
  { id: '2', niaNim: 'AF-2023-0145', descripcion: 'Escritorio Ejecutivo de Madera',       responsableOrigen: 'Ana Flores',     responsableDestino: 'Carlos Mamani', unidadOrigen: 'RRHH',                   unidadDestino: 'Administración Central', fechaEfectiva: '20/03/2024' },
]

const documents = [
  { id: '1', nombre: 'Solicitud-Transferencia.pdf', tipo: 'Solicitud',     fecha: '15/03/2024', subidoPor: 'Juan Pérez',    size: '245 KB' },
  { id: '2', nombre: 'Autorización-Director.pdf',   tipo: 'Autorización',  fecha: '16/03/2024', subidoPor: 'María González', size: '180 KB' },
]

const timeline = [
  { estado: 'Pendiente',  fecha: '15/03/2024',  usuario: 'Juan Pérez Mamani', completed: true  },
  { estado: 'Procesado',  fecha: null,           usuario: null,                completed: false },
  { estado: 'Cerrado',    fecha: null,           usuario: null,                completed: false },
]

function getStatusColor(estado: EstadoProceso) {
  switch (estado) {
    case 'Pendiente': return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    case 'Procesado': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Cerrado':   return 'bg-green-100 text-green-800 border-green-300'
  }
}

function handleCloseProcess() {
  showCloseConfirm.value = false
}
</script>
