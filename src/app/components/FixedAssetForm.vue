<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <div>
        <h1 class="text-2xl text-gray-900">
          {{ isEdit ? 'Editar Activo Fijo' : 'Registrar Nuevo Activo Fijo' }}
        </h1>
        <p v-if="isEdit" class="text-sm text-gray-600 mt-1">AF-2024-0001</p>
      </div>
    </div>

    <!-- Form -->
    <form @submit.prevent="handleSubmit" class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <div class="space-y-6">
        <!-- Descripción - full width -->
        <div class="w-full">
          <label class="block text-sm text-gray-700 mb-2">
            Descripción <span class="text-red-600">*</span>
          </label>
          <input
            v-model="formData.descripcion"
            type="text"
            placeholder="Ej: Computadora Portátil HP ProBook 450"
            class="w-full px-3 py-2 border-2 border-gray-300 rounded"
            required
          />
        </div>

        <!-- Two-column grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Categoría contable -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Categoría Contable <span class="text-red-600">*</span>
            </label>
            <select
              v-model="formData.categoria"
              @change="formData.subcuenta = ''"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              required
            >
              <option value="">Seleccione...</option>
              <option value="muebles">Muebles y Enseres</option>
              <option value="equipos-computacion">Equipos de Computación</option>
              <option value="vehiculos">Vehículos</option>
              <option value="maquinaria">Maquinaria y Equipo</option>
              <option value="equipos-varios">Equipos Varios</option>
            </select>
          </div>

          <!-- Subcuenta (dependiente de categoría) -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Subcuenta <span class="text-red-600">*</span>
            </label>
            <select
              v-model="formData.subcuenta"
              :disabled="!formData.categoria"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded disabled:bg-gray-100 disabled:cursor-not-allowed"
              required
            >
              <option value="">Seleccione categoría primero...</option>
              <template v-if="formData.categoria === 'equipos-computacion'">
                <option value="03-01">03-01 - Equipos de Computación</option>
                <option value="03-02">03-02 - Equipos de Comunicación</option>
              </template>
              <template v-if="formData.categoria === 'muebles'">
                <option value="04-01">04-01 - Muebles de Oficina</option>
                <option value="04-02">04-02 - Enseres</option>
              </template>
            </select>
          </div>

          <!-- Costo de compra -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Costo de Compra (Bs.) <span class="text-red-600">*</span>
            </label>
            <input
              v-model="formData.costo"
              type="number"
              step="0.01"
              placeholder="0.00"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              required
            />
          </div>

          <!-- Fecha de adquisición -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Fecha de Adquisición <span class="text-red-600">*</span>
            </label>
            <input
              v-model="formData.fechaAdquisicion"
              type="date"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              required
            />
          </div>

          <!-- Fuente de financiamiento -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Fuente de Financiamiento <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.fuenteFinanciamiento" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione...</option>
              <option value="IDH">IDH (Impuesto Directo a los Hidrocarburos)</option>
              <option value="TGN">TGN (Tesoro General de la Nación)</option>
              <option value="fondos-propios">Fondos Propios</option>
              <option value="donacion-externa">Donación Externa</option>
              <option value="recursos-especificos">Recursos Específicos</option>
            </select>
          </div>

          <!-- Unidad -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Unidad <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.unidad" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione...</option>
              <option value="admin-central">Administración Central</option>
              <option value="rectorado">Rectorado</option>
              <option value="sistemas">Sistemas</option>
              <option value="rrhh">Recursos Humanos</option>
              <option value="transporte">Transporte</option>
              <option value="biblioteca">Biblioteca</option>
            </select>
          </div>

          <!-- Responsable / EP -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Responsable / Estructura Programática <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.responsable" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione...</option>
              <option value="juan-perez">Juan Pérez Mamani (Jefe de Sistemas)</option>
              <option value="maria-gonzalez">María González Quispe (Jefa de RRHH)</option>
              <option value="carlos-mamani">Carlos Mamani López (Coordinador)</option>
              <option value="ana-flores">Ana Flores Choque (Técnico)</option>
            </select>
          </div>

          <!-- Estado inicial -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Estado Inicial <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.estado" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="Activo">Activo</option>
              <option value="En Proceso">En Proceso</option>
            </select>
          </div>

          <!-- Régimen -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Régimen <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.regimen" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="Patrimonial">Patrimonial</option>
              <option value="Custodia">Custodia</option>
              <option value="Préstamo">Préstamo</option>
              <option value="Comodato">Comodato</option>
              <option value="Convenio">Convenio</option>
            </select>
          </div>
        </div>

        <!-- Foto del activo -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">Foto del Activo</label>
          <div v-if="photoPreview" class="relative inline-block">
            <div class="w-48 h-48 border-2 border-gray-300 rounded bg-gray-100 flex items-center justify-center">
              <span class="text-gray-400 text-xs">VISTA PREVIA</span>
            </div>
            <button
              type="button"
              @click="photoPreview = null"
              class="absolute -top-2 -right-2 p-1 bg-red-500 text-white rounded-full hover:bg-red-600"
            >
              <X class="w-4 h-4" />
            </button>
          </div>
          <label v-else class="inline-flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 cursor-pointer">
            <Upload class="w-4 h-4" />
            <span>Subir Foto</span>
            <input type="file" accept="image/*" @change="handlePhotoUpload" class="hidden" />
          </label>
        </div>

        <!-- Documentos adjuntos -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Documentos Adjuntos
            <span v-if="requiresDocuments" class="text-red-600 ml-1">* (Requerido para activos &gt; Bs. 50,000)</span>
          </label>

          <div v-if="requiresDocuments && uploadedDocs.length === 0" class="mb-3 p-3 bg-yellow-50 border border-yellow-300 rounded text-sm text-yellow-800">
            <strong>Advertencia:</strong> Este activo requiere documentación respaldatoria (factura, garantías, etc.)
          </div>

          <div v-if="uploadedDocs.length > 0" class="mb-3 space-y-2">
            <div
              v-for="doc in uploadedDocs"
              :key="doc.id"
              class="flex items-center justify-between p-2 border border-gray-300 rounded bg-gray-50"
            >
              <div class="flex items-center gap-2">
                <FileText class="w-4 h-4 text-gray-600" />
                <span class="text-sm text-gray-900">{{ doc.name }}</span>
                <span class="text-xs text-gray-600">({{ doc.size }})</span>
              </div>
              <button type="button" @click="removeDocument(doc.id)" class="p-1 text-red-600 hover:text-red-800">
                <X class="w-4 h-4" />
              </button>
            </div>
          </div>

          <label class="inline-flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 cursor-pointer">
            <Upload class="w-4 h-4" />
            <span>Adjuntar Documentos</span>
            <input type="file" multiple accept=".pdf,.doc,.docx,.jpg,.jpeg,.png" @change="handleDocumentUpload" class="hidden" />
          </label>
          <p class="text-xs text-gray-600 mt-1">Formatos permitidos: PDF, Word, imágenes (JPG, PNG)</p>
        </div>
      </div>

      <!-- Form footer -->
      <div class="flex items-center justify-end gap-3 mt-8 pt-6 border-t-2 border-gray-300">
        <button type="button" @click="emit('cancel')" class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
          Cancelar
        </button>
        <button type="submit" class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">
          Guardar
        </button>
      </div>
    </form>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Asset Form:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Two-column form layout with full-width Descripción field at top</li>
          <li>Subcuenta dropdown is dependent on Categoría selection (populated dynamically)</li>
          <li>Unidad and Responsable use searchable select dropdowns (simulated here)</li>
          <li>Photo upload with preview thumbnail and remove button</li>
          <li>Multi-file document upload with file list display</li>
          <li>Documents marked as required if Costo > Bs. 50,000 with warning banner</li>
          <li>Footer with "Guardar" (primary) and "Cancelar" (secondary) buttons</li>
          <li>Form can be used for both new registration and editing existing assets</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Upload, X, FileText } from 'lucide-vue-next'
import type { UserRole } from '../types'

interface DocFile { id: string; name: string; size: string }

const props = defineProps<{ userRole: UserRole; assetId: string | null }>()
const emit = defineEmits<{ cancel: [] }>()

const isEdit = computed(() => props.assetId !== null)

const formData = ref({
  descripcion: '', categoria: '', subcuenta: '', costo: '',
  fechaAdquisicion: '', fuenteFinanciamiento: '', unidad: '',
  responsable: '', estado: 'Activo', regimen: 'Patrimonial',
})

const photoPreview  = ref<string | null>(null)
const uploadedDocs  = ref<DocFile[]>([])

const requiresDocuments = computed(() => {
  const v = parseFloat(formData.value.costo) || 0
  return v > 50000
})

function handlePhotoUpload(e: Event) {
  const file = (e.target as HTMLInputElement).files?.[0]
  if (file) photoPreview.value = 'photo-preview.jpg'
}

function handleDocumentUpload(e: Event) {
  const files = (e.target as HTMLInputElement).files
  if (!files) return
  const newDocs: DocFile[] = Array.from(files).map((file, idx) => ({
    id: `doc-${Date.now()}-${idx}`,
    name: file.name,
    size: `${Math.round(file.size / 1024)} KB`,
  }))
  uploadedDocs.value.push(...newDocs)
}

function removeDocument(id: string) {
  uploadedDocs.value = uploadedDocs.value.filter((d) => d.id !== id)
}

function handleSubmit() { /* wireframe */ }
</script>
