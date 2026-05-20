<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('cancel')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <h1 class="text-2xl text-gray-900">Baja de Activos</h1>
    </div>

    <!-- Sección 1: Datos del proceso de baja -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
        <h2 class="text-lg text-gray-900">Datos del Proceso</h2>
      </div>
      <div class="p-6 grid grid-cols-1 md:grid-cols-2 gap-6">
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Tipo de Baja <span class="text-red-600">*</span>
          </label>
          <select v-model="formData.tipoBaja" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
            <option value="">Seleccione el tipo...</option>
            <optgroup label="Activos Fijos">
              <option value="baja-af">Baja de Activos Fijos</option>
              <option value="baja-af-faltante">Baja de Activos Faltantes</option>
            </optgroup>
            <optgroup label="Materiales de Control">
              <option value="baja-mc">Baja de Materiales de Control</option>
              <option value="baja-mc-faltante">Baja de Materiales Faltantes</option>
            </optgroup>
          </select>
        </div>
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Motivo de Baja <span class="text-red-600">*</span>
          </label>
          <select v-model="formData.motivo" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
            <option value="">Seleccione el motivo...</option>
            <option value="obsolescencia">Obsolescencia tecnológica</option>
            <option value="deterioro">Deterioro irreparable</option>
            <option value="siniestro">Siniestro (robo, incendio, etc.)</option>
            <option value="donacion-salida">Donación a terceros</option>
            <option value="venta">Venta o remate</option>
            <option value="faltante-inventario">Faltante en inventario</option>
            <option value="otro">Otro</option>
          </select>
        </div>
        <div>
          <label class="block text-sm text-gray-700 mb-2">
            Unidad de Referencia <span class="text-red-600">*</span>
          </label>
          <select v-model="formData.unidad" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
            <option value="">Seleccione la unidad...</option>
            <option value="Rectorado">Rectorado</option>
            <option value="Administración Central">Administración Central</option>
            <option value="Facultad de Ingeniería">Facultad de Ingeniería</option>
            <option value="Depto. de Sistemas">Depto. de Sistemas</option>
            <option value="Laboratorio de Computación">Laboratorio de Computación</option>
          </select>
        </div>
        <div>
          <label class="block text-sm text-gray-700 mb-2">Fecha de Baja <span class="text-red-600">*</span></label>
          <input v-model="formData.fecha" type="date" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required />
        </div>
        <div class="md:col-span-2">
          <label class="block text-sm text-gray-700 mb-2">Observaciones</label>
          <textarea
            v-model="formData.observaciones"
            rows="3"
            class="w-full px-3 py-2 border-2 border-gray-300 rounded"
            placeholder="Descripción detallada del motivo de baja..."
          />
        </div>
      </div>
    </div>

    <!-- Sección 2: Selección de bienes -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50 flex items-center justify-between">
        <div>
          <h2 class="text-lg text-gray-900">Bienes a Dar de Baja</h2>
          <p class="text-xs text-gray-600 mt-0.5">
            {{ selectedCount }} bien{{ selectedCount !== 1 ? 'es' : '' }} seleccionado{{ selectedCount !== 1 ? 's' : '' }}
          </p>
        </div>
        <div class="relative">
          <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
          <input
            v-model="searchBien"
            type="text"
            placeholder="Buscar por NIA o descripción..."
            class="pl-10 pr-4 py-2 border border-gray-300 rounded text-sm w-64"
          />
        </div>
      </div>
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 w-10">
                <input type="checkbox" :checked="allSelected" @change="toggleAll" class="w-4 h-4" />
              </th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">NIA / NIM</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Categoría</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable</th>
              <th class="px-4 py-3 text-right text-xs text-gray-700">Valor Neto (Bs.)</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr
              v-for="bien in filteredBienes"
              :key="bien.id"
              :class="['hover:bg-gray-50 cursor-pointer', bien.selected ? 'bg-blue-50' : '']"
              @click="toggleBien(bien.id)"
            >
              <td class="px-4 py-3">
                <input type="checkbox" :checked="bien.selected" @click.stop="toggleBien(bien.id)" class="w-4 h-4" />
              </td>
              <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ bien.nia }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ bien.descripcion }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ bien.categoria }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ bien.unidad }}</td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ bien.responsable }}</td>
              <td class="px-4 py-3 text-sm text-gray-900 text-right">{{ bien.valorNeto }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getEstadoColor(bien.estado)]">
                  {{ bien.estado }}
                </span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Sección 3: Documentación de respaldo -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
        <h2 class="text-lg text-gray-900">Documentación de Respaldo</h2>
      </div>
      <div class="p-6 space-y-4">
        <div class="flex items-center gap-4">
          <label class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 cursor-pointer transition-colors">
            <Upload class="w-4 h-4" />
            <span class="text-sm">Adjuntar Documento</span>
            <input type="file" accept=".pdf,.doc,.docx,.jpg,.jpeg,.png" @change="handleUpload" class="hidden" multiple />
          </label>
          <span class="text-xs text-gray-600">PDF, Word o imágenes. Máx. 10 MB por archivo.</span>
        </div>
        <div v-if="adjuntos.length > 0" class="space-y-2">
          <div
            v-for="(doc, i) in adjuntos"
            :key="i"
            class="flex items-center justify-between px-4 py-2 bg-gray-50 border border-gray-300 rounded"
          >
            <div class="flex items-center gap-2">
              <FileText class="w-4 h-4 text-gray-500" />
              <span class="text-sm text-gray-900">{{ doc.nombre }}</span>
              <span class="text-xs text-gray-500">{{ doc.size }}</span>
            </div>
            <button @click="adjuntos.splice(i, 1)" class="p-1 text-red-600 hover:text-red-800 hover:bg-red-50 rounded">
              <X class="w-4 h-4" />
            </button>
          </div>
        </div>
        <div v-else class="px-4 py-3 bg-yellow-50 border border-yellow-300 rounded text-sm text-yellow-800">
          Se recomienda adjuntar el acta o resolución que autoriza la baja.
        </div>
      </div>
    </div>

    <!-- Resumen -->
    <div v-if="selectedCount > 0" class="bg-white border-2 border-gray-300 rounded-lg p-6">
      <h2 class="text-lg text-gray-900 mb-4">Resumen de la Baja</h2>
      <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
        <div>
          <div class="text-xs text-gray-600 mb-1">Bienes seleccionados</div>
          <div class="text-2xl text-gray-900">{{ selectedCount }}</div>
        </div>
        <div>
          <div class="text-xs text-gray-600 mb-1">Valor neto total (Bs.)</div>
          <div class="text-2xl text-gray-900">{{ totalValorNeto }}</div>
        </div>
        <div>
          <div class="text-xs text-gray-600 mb-1">Acta a generar</div>
          <div class="text-sm text-gray-900 mt-1">
            <span class="px-2 py-1 bg-gray-100 border border-gray-300 rounded text-xs">
              {{ actaTipo }}
            </span>
          </div>
        </div>
        <div>
          <div class="text-xs text-gray-600 mb-1">Documentos adjuntos</div>
          <div class="text-2xl text-gray-900">{{ adjuntos.length }}</div>
        </div>
      </div>
    </div>

    <!-- Pie del formulario -->
    <div class="flex items-center justify-end gap-3 pt-2">
      <button
        type="button"
        @click="emit('cancel')"
        class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors"
      >
        Cancelar
      </button>
      <button
        type="button"
        @click="showConfirm = true"
        :disabled="selectedCount === 0 || !formData.tipoBaja || !formData.motivo"
        class="flex items-center gap-2 px-6 py-2 bg-red-600 text-white rounded hover:bg-red-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
      >
        <AlertTriangle class="w-4 h-4" />
        <span>Registrar Baja</span>
      </button>
    </div>

    <!-- Modal de confirmación -->
    <Teleport to="body">
      <div v-if="showConfirm" class="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
        <div class="bg-white rounded-lg p-6 max-w-md w-full mx-4 border-2 border-gray-300">
          <div class="flex items-start gap-3 mb-4">
            <AlertTriangle class="w-6 h-6 text-red-600 flex-shrink-0 mt-0.5" />
            <div>
              <h3 class="text-lg text-gray-900 mb-2">Confirmar Baja de Activos</h3>
              <p class="text-sm text-gray-600">
                Se dará de baja a <strong>{{ selectedCount }} bien{{ selectedCount !== 1 ? 'es' : '' }}</strong>
                con un valor neto total de <strong>Bs. {{ totalValorNeto }}</strong>.
                Esta acción generará el acta <strong>{{ actaTipo }}</strong> y no puede revertirse sin autorización especial.
              </p>
            </div>
          </div>
          <div class="flex items-center justify-end gap-3 mt-6">
            <button @click="showConfirm = false" class="px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
              Cancelar
            </button>
            <button @click="handleSubmit" class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700">
              Confirmar Baja
            </button>
          </div>
        </div>
      </div>
    </Teleport>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-16 Baja de Activos:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Solo accesible para Administrador y Operador con perfil Firma-Cierre.</li>
          <li>La selección de bienes es por checkboxes — puede ser parcial (RN-03).</li>
          <li>El tipo de acta (ABAF o ABMC) se determina automáticamente según el tipo de baja.</li>
          <li>El botón "Registrar Baja" está deshabilitado hasta seleccionar al menos un bien y completar los campos obligatorios.</li>
          <li>El proceso cerrado no puede modificarse (RN-01).</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft, Search, Upload, FileText, X, AlertTriangle } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ cancel: [] }>()

const showConfirm = ref(false)
const searchBien  = ref('')

const formData = ref({
  tipoBaja: '', motivo: '', unidad: '', fecha: '', observaciones: '',
})

interface Bien {
  id: string; nia: string; descripcion: string; categoria: string
  unidad: string; responsable: string; valorNeto: string; estado: string; selected: boolean
}

const bienes = ref<Bien[]>([
  { id: '1', nia: 'AF-2019-0312', descripcion: 'UPS APC Smart-UPS 1500VA',           categoria: 'Equipos de Computación', unidad: 'Laboratorio de Computación', responsable: 'Ing. Carlos Mamani',  valorNeto: '0,00',      estado: 'Activo',   selected: false },
  { id: '2', nia: 'AF-2020-0178', descripcion: 'Computadora de Escritorio HP',        categoria: 'Equipos de Computación', unidad: 'Depto. de Sistemas',         responsable: 'Ing. Carlos Mamani',  valorNeto: '0,00',      estado: 'Activo',   selected: false },
  { id: '3', nia: 'AF-2018-0045', descripcion: 'Impresora Matricial Epson LX-350',    categoria: 'Equipos de Computación', unidad: 'Administración Central',      responsable: 'Lic. Roberto Vargas', valorNeto: '0,00',      estado: 'Activo',   selected: false },
  { id: '4', nia: 'AF-2021-0056', descripcion: 'Silla de Oficina sin ruedas',         categoria: 'Muebles y Enseres',      unidad: 'Rectorado',                  responsable: 'Lic. María Flores',   valorNeto: '120,00',    estado: 'Activo',   selected: false },
  { id: '5', nia: 'AF-2022-0099', descripcion: 'Monitor LCD 19" Samsung',             categoria: 'Equipos de Computación', unidad: 'Depto. de Sistemas',         responsable: 'Ing. Carlos Mamani',  valorNeto: '350,00',    estado: 'Faltante', selected: false },
  { id: '6', nia: 'AF-2017-0201', descripcion: 'Escritorio de Madera deteriorado',    categoria: 'Muebles y Enseres',      unidad: 'Facultad de Ingeniería',     responsable: 'Lic. María Flores',   valorNeto: '0,00',      estado: 'Activo',   selected: false },
])

const adjuntos = ref<{ nombre: string; size: string }[]>([])

const filteredBienes = computed(() =>
  bienes.value.filter((b) =>
    !searchBien.value ||
    b.nia.toLowerCase().includes(searchBien.value.toLowerCase()) ||
    b.descripcion.toLowerCase().includes(searchBien.value.toLowerCase())
  )
)

const selectedCount = computed(() => bienes.value.filter((b) => b.selected).length)
const allSelected   = computed(() => filteredBienes.value.length > 0 && filteredBienes.value.every((b) => b.selected))

const totalValorNeto = computed(() => {
  const total = bienes.value
    .filter((b) => b.selected)
    .reduce((s, b) => s + parseFloat(b.valorNeto.replace(',', '.')), 0)
  return total.toLocaleString('es-BO', { minimumFractionDigits: 2 })
})

const actaTipo = computed(() => {
  if (formData.value.tipoBaja.includes('mc')) return 'ABMC'
  if (formData.value.tipoBaja.includes('af')) return 'ABAF'
  return '—'
})

function toggleBien(id: string) {
  const b = bienes.value.find((b) => b.id === id)
  if (b) b.selected = !b.selected
}

function toggleAll() {
  const newVal = !allSelected.value
  filteredBienes.value.forEach((b) => { b.selected = newVal })
}

function getEstadoColor(estado: string) {
  switch (estado) {
    case 'Activo':   return 'bg-green-100 text-green-800 border-green-300'
    case 'Faltante': return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    default:         return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}

function handleUpload(e: Event) {
  const files = (e.target as HTMLInputElement).files
  if (!files) return
  Array.from(files).forEach((f) => {
    adjuntos.value.push({ nombre: f.name, size: `${(f.size / 1024).toFixed(0)} KB` })
  })
}

function handleSubmit() {
  showConfirm.value = false
  emit('cancel')
}
</script>
