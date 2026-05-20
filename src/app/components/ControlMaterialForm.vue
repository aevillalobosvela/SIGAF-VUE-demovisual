<template>
  <div class="space-y-6">
    <!-- Page header -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <div>
        <h1 class="text-2xl text-gray-900">
          {{ isEdit ? 'Editar Material de Control' : 'Registrar Nuevo Material de Control' }}
        </h1>
        <p v-if="isEdit" class="text-sm text-gray-600 mt-1">NIM-2024-0001</p>
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
            placeholder="Ej: Escritorio Metálico Simple"
            class="w-full px-3 py-2 border-2 border-gray-300 rounded"
            required
          />
        </div>

        <!-- Two-column grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Grupo (Categoría Grupo 20) -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Grupo <span class="text-red-600">*</span>
            </label>
            <select
              v-model="formData.grupo"
              @change="formData.subcuenta = ''"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded"
              required
            >
              <option value="">Seleccione...</option>
              <option value="20-01">20-01 Muebles</option>
              <option value="20-02">20-02 Maquinaria</option>
              <option value="20-03">20-03 Herramientas</option>
              <option value="20-04">20-04 Equipos Varios</option>
            </select>
          </div>

          <!-- Subcuenta (dependiente de grupo) -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Subcuenta <span class="text-red-600">*</span>
            </label>
            <select
              v-model="formData.subcuenta"
              :disabled="!formData.grupo"
              class="w-full px-3 py-2 border-2 border-gray-300 rounded disabled:bg-gray-100 disabled:cursor-not-allowed"
              required
            >
              <option value="">Seleccione grupo primero...</option>
              <template v-if="formData.grupo === '20-01'">
                <option value="20-01-A">20-01-A Muebles de Oficina</option>
                <option value="20-01-B">20-01-B Muebles de Aula</option>
              </template>
              <template v-if="formData.grupo === '20-02'">
                <option value="20-02-A">20-02-A Maquinaria Liviana</option>
                <option value="20-02-B">20-02-B Maquinaria Pesada</option>
              </template>
              <template v-if="formData.grupo === '20-03'">
                <option value="20-03-A">20-03-A Herramientas Manuales</option>
                <option value="20-03-B">20-03-B Herramientas Eléctricas</option>
              </template>
              <template v-if="formData.grupo === '20-04'">
                <option value="20-04-A">20-04-A Equipos de Limpieza</option>
                <option value="20-04-B">20-04-B Equipos Varios</option>
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
              <option value="taller">Taller Mecánica</option>
              <option value="almacenes">Almacenes</option>
              <option value="mantenimiento">Mantenimiento</option>
              <option value="biblioteca">Biblioteca</option>
              <option value="aulas">Aulas</option>
            </select>
          </div>

          <!-- Responsable / EP -->
          <div>
            <label class="block text-sm text-gray-700 mb-2">
              Responsable / Estructura Programática <span class="text-red-600">*</span>
            </label>
            <select v-model="formData.responsable" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
              <option value="">Seleccione...</option>
              <option value="pedro-quispe">Pedro Quispe Flores (Auxiliar Administrativo)</option>
              <option value="roberto-vargas">Roberto Vargas Nina (Técnico Taller)</option>
              <option value="lucia-apaza">Lucia Apaza Condori (Encargada Auditorio)</option>
              <option value="carlos-mamani">Carlos Mamani López (Coordinador)</option>
              <option value="sofia-machaca">Sofia Machaca Poma (Encargada Almacenes)</option>
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

        <!-- Foto del material -->
        <div>
          <label class="block text-sm text-gray-700 mb-2">Foto del Material</label>
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
          <label class="block text-sm text-gray-700 mb-2">Documentos Adjuntos</label>
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
        <strong>Wireframe Notes - P-05 Control Material Form:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Similar a FixedAssetForm pero sin campos de depreciación (vida útil, método)</li>
          <li>Categoría usa Grupos 20 (20-01 Muebles, 20-02 Maquinaria, 20-03 Herramientas, 20-04 Equipos Varios)</li>
          <li>Subcuenta dependiente del grupo seleccionado</li>
          <li>Sin advertencia de documentos por costo (materiales no tienen umbral Bs. 50.000)</li>
          <li>Mismo patrón de foto y documentos adjuntos que FixedAssetForm</li>
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

const props = defineProps<{ userRole: UserRole; materialId: string | null }>()
const emit = defineEmits<{ cancel: [] }>()

const isEdit = computed(() => props.materialId !== null)

const formData = ref({
  descripcion: '', grupo: '', subcuenta: '', costo: '',
  fechaAdquisicion: '', fuenteFinanciamiento: '', unidad: '',
  responsable: '', estado: 'Activo', regimen: 'Patrimonial',
})

const photoPreview = ref<string | null>(null)
const uploadedDocs = ref<DocFile[]>([])

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
