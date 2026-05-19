<template>
  <div class="space-y-6">
    <!-- Page header with actions outside document -->
    <div class="flex items-center justify-between">
      <div class="flex items-center gap-4">
        <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
          <ArrowLeft class="w-5 h-5 text-gray-700" />
        </button>
        <h1 class="text-2xl text-gray-900">Vista Previa de Acta</h1>
      </div>
      <div class="flex items-center gap-3">
        <button class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
          <Printer class="w-5 h-5" />
          <span>Imprimir</span>
        </button>
        <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800">
          <Download class="w-5 h-5" />
          <span>Descargar PDF</span>
        </button>
      </div>
    </div>

    <!-- Document preview - A4 proportions -->
    <div class="bg-white border-2 border-gray-300 rounded-lg shadow-lg mx-auto max-w-4xl">
      <div class="p-12">
        <!-- Institutional header -->
        <div class="text-center mb-8 pb-6 border-b-2 border-gray-300">
          <div class="flex justify-center mb-4">
            <div class="w-20 h-20 border-4 border-gray-400 rounded-full flex items-center justify-center">
              <span class="text-gray-400 text-xs">LOGO</span>
            </div>
          </div>
          <h1 class="text-xl text-gray-900 mb-1">UNIVERSIDAD TÉCNICA DE ORURO</h1>
          <h2 class="text-base text-gray-700 mb-3">Departamento de Activos Fijos</h2>
          <h3 class="text-lg text-gray-900 border-t border-b border-gray-300 py-2 mt-4">
            {{ acta.tipo }}
          </h3>
          <div class="text-sm text-gray-700 mt-2">{{ acta.codigo }}</div>
        </div>

        <!-- Document body -->
        <div class="space-y-6 mb-8">
          <!-- Process info -->
          <div class="grid grid-cols-2 gap-4 text-sm">
            <div><span class="text-gray-600">N° de Proceso:</span> <span class="text-gray-900">{{ acta.proceso }}</span></div>
            <div><span class="text-gray-600">Fecha:</span> <span class="text-gray-900">{{ acta.fecha }}</span></div>
            <div><span class="text-gray-600">Unidad Origen:</span> <span class="text-gray-900">{{ acta.unidadOrigen }}</span></div>
            <div><span class="text-gray-600">Unidad Destino:</span> <span class="text-gray-900">{{ acta.unidadDestino }}</span></div>
            <div><span class="text-gray-600">Responsable Origen:</span> <span class="text-gray-900">{{ acta.responsableOrigen }}</span></div>
            <div><span class="text-gray-600">Responsable Destino:</span> <span class="text-gray-900">{{ acta.responsableDestino }}</span></div>
          </div>

          <!-- Description paragraph -->
          <p class="text-sm text-gray-700 leading-relaxed">
            En la ciudad de Oruro, a los catorce días del mes de marzo de dos mil veinticuatro, en las oficinas del Departamento de Activos Fijos de la Universidad Técnica de Oruro, se procede a realizar la transferencia de los siguientes bienes:
          </p>

          <!-- Assets table -->
          <div class="border-2 border-gray-300 rounded overflow-hidden">
            <table class="w-full text-sm">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-3 py-2 text-left text-xs text-gray-700">N°</th>
                  <th class="px-3 py-2 text-left text-xs text-gray-700">NIA</th>
                  <th class="px-3 py-2 text-left text-xs text-gray-700">Descripción</th>
                  <th class="px-3 py-2 text-right text-xs text-gray-700">Valor</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="(asset, idx) in assets" :key="asset.nia">
                  <td class="px-3 py-2 text-gray-700">{{ idx + 1 }}</td>
                  <td class="px-3 py-2 text-gray-900">{{ asset.nia }}</td>
                  <td class="px-3 py-2 text-gray-900">{{ asset.descripcion }}</td>
                  <td class="px-3 py-2 text-gray-700 text-right">{{ asset.valor }}</td>
                </tr>
              </tbody>
              <tfoot class="bg-gray-50 border-t-2 border-gray-300">
                <tr>
                  <td colspan="3" class="px-3 py-2 text-right text-xs text-gray-700"><strong>Total:</strong></td>
                  <td class="px-3 py-2 text-right text-xs text-gray-900"><strong>Bs. 11,700.00</strong></td>
                </tr>
              </tfoot>
            </table>
          </div>
        </div>

        <!-- Footer with generation info and signatures -->
        <div class="border-t-2 border-gray-300 pt-6 space-y-6">
          <div class="text-xs text-gray-600">
            <div>Generado el: {{ acta.fecha }} 10:30:15</div>
            <div>Por: Juan Pérez Mamani (Administrador)</div>
          </div>
          <!-- Signature lines -->
          <div class="grid grid-cols-2 gap-8 mt-12">
            <div class="text-center">
              <div class="border-t-2 border-gray-900 pt-2 mt-16">
                <div class="text-sm text-gray-900">{{ acta.responsableOrigen }}</div>
                <div class="text-xs text-gray-600">Responsable Origen</div>
              </div>
            </div>
            <div class="text-center">
              <div class="border-t-2 border-gray-900 pt-2 mt-16">
                <div class="text-sm text-gray-900">{{ acta.responsableDestino }}</div>
                <div class="text-xs text-gray-600">Responsable Destino</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Wireframe annotation -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg max-w-4xl mx-auto">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - Acta Preview:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Document-style layout with A4 proportions (max-width container)</li>
          <li>Institutional header with logo placeholder, university name, division name, and acta type title</li>
          <li>Body section with process data and description paragraph</li>
          <li>Assets table listing all affected items with NIA, Description, and Value columns</li>
          <li>Footer section with generation date/time, generated-by user, and signature lines</li>
          <li>"Imprimir" and "Descargar PDF" buttons positioned outside the document area at the top</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ArrowLeft, Printer, Download } from 'lucide-vue-next'

defineProps<{ actaId: string | null }>()
const emit = defineEmits<{ back: [] }>()

const acta = {
  codigo: 'ATAF-2024-012',
  tipo: 'ACTA DE TRANSFERENCIA DE ACTIVOS FIJOS',
  proceso: 'TRANS-2024-001',
  fecha: '14/03/2024',
  unidadOrigen: 'Rectorado',
  unidadDestino: 'Administración Central',
  responsableOrigen: 'María González Quispe',
  responsableDestino: 'Juan Pérez Mamani',
}

const assets = [
  { nia: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', valor: 'Bs. 8,500.00' },
  { nia: 'AF-2024-0002', descripcion: 'Escritorio Ejecutivo de Madera',       valor: 'Bs. 3,200.00' },
]
</script>
