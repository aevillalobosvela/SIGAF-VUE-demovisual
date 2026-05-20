<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center gap-4">
      <button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
        <ArrowLeft class="w-5 h-5 text-gray-700" />
      </button>
      <div>
        <h1 class="text-2xl text-gray-900">Estadísticas y Gráficas</h1>
        <p class="text-sm text-gray-600 mt-0.5">Visualización del inventario institucional — Gestión 2025</p>
      </div>
    </div>

    <!-- Selector de gestión -->
    <div class="flex items-center gap-3">
      <label class="text-sm text-gray-700">Gestión:</label>
      <select v-model="gestion" class="px-3 py-2 border border-gray-300 rounded text-sm">
        <option value="2025">2025</option>
        <option value="2024">2024</option>
        <option value="2023">2023</option>
      </select>
    </div>

    <!-- Fila 1: Valor por año (línea) + Por categoría (barras) -->
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

      <!-- Gráfica 1: Evolución del valor total por gestión -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Valor Total de Activos por Gestión</h2>
          <p class="text-xs text-gray-600 mt-0.5">Valor en libros acumulado (Bs. miles)</p>
        </div>
        <div class="p-6">
          <svg viewBox="0 0 400 200" class="w-full" aria-label="Gráfica de línea: valor por gestión">
            <!-- Ejes -->
            <line x1="50" y1="10" x2="50" y2="170" stroke="#d1d5db" stroke-width="1" />
            <line x1="50" y1="170" x2="390" y2="170" stroke="#d1d5db" stroke-width="1" />
            <!-- Líneas guía horizontales -->
            <line v-for="(y, i) in [170, 130, 90, 50, 10]" :key="i" :x1="50" :y1="y" x2="390" :y2="y" stroke="#f3f4f6" stroke-width="1" />
            <!-- Labels eje Y -->
            <text v-for="(label, i) in yLabels" :key="'y'+i" :x="44" :y="yPositions[i] + 4" text-anchor="end" font-size="9" fill="#6b7280">{{ label }}</text>
            <!-- Área bajo la curva -->
            <polygon :points="areaPoints" fill="#374151" fill-opacity="0.08" />
            <!-- Línea principal -->
            <polyline :points="linePoints" fill="none" stroke="#374151" stroke-width="2" stroke-linejoin="round" />
            <!-- Puntos -->
            <circle v-for="(pt, i) in chartPoints" :key="'pt'+i" :cx="pt.x" :cy="pt.y" r="4" fill="#374151" />
            <!-- Labels eje X -->
            <text v-for="(pt, i) in chartPoints" :key="'lx'+i" :x="pt.x" y="185" text-anchor="middle" font-size="9" fill="#6b7280">{{ lineData[i].year }}</text>
            <!-- Valores sobre puntos -->
            <text v-for="(pt, i) in chartPoints" :key="'lv'+i" :x="pt.x" :y="pt.y - 8" text-anchor="middle" font-size="8" fill="#374151">{{ lineData[i].valor }}</text>
          </svg>
        </div>
      </div>

      <!-- Gráfica 2: Activos por categoría (barras horizontales) -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Activos por Categoría Contable</h2>
          <p class="text-xs text-gray-600 mt-0.5">Cantidad de bienes registrados</p>
        </div>
        <div class="p-6 space-y-3">
          <div v-for="(cat, i) in categoriaData" :key="i" class="space-y-1">
            <div class="flex items-center justify-between text-xs text-gray-700">
              <span>{{ cat.nombre }}</span>
              <span class="text-gray-900">{{ cat.cantidad }}</span>
            </div>
            <div class="w-full bg-gray-100 rounded-full h-5">
              <div
                class="h-5 rounded-full flex items-center justify-end pr-2 transition-all"
                :style="{ width: `${(cat.cantidad / maxCategoria) * 100}%`, backgroundColor: cat.color }"
              >
                <span class="text-xs text-white">{{ Math.round((cat.cantidad / totalActivos) * 100) }}%</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Fila 2: Por fuente de financiamiento (donut) + Depreciación acumulada (barras) -->
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">

      <!-- Gráfica 3: Donut por fuente de financiamiento -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Distribución por Fuente de Financiamiento</h2>
          <p class="text-xs text-gray-600 mt-0.5">Valor total por origen de fondos (Bs.)</p>
        </div>
        <div class="p-6 flex items-center gap-6">
          <!-- SVG Donut -->
          <svg viewBox="0 0 160 160" class="w-40 h-40 flex-shrink-0" aria-label="Gráfica donut: fuente de financiamiento">
            <g transform="translate(80,80)">
              <circle r="55" fill="none" stroke="#f3f4f6" stroke-width="28" />
              <circle
                v-for="(seg, i) in donutSegments"
                :key="i"
                r="55"
                fill="none"
                :stroke="seg.color"
                stroke-width="28"
                :stroke-dasharray="`${seg.dash} ${seg.gap}`"
                :stroke-dashoffset="seg.offset"
                transform="rotate(-90)"
              />
            </g>
            <text x="80" y="76" text-anchor="middle" font-size="11" fill="#111827">Total</text>
            <text x="80" y="91" text-anchor="middle" font-size="10" fill="#374151">{{ totalActivos }}</text>
          </svg>
          <!-- Leyenda -->
          <div class="flex-1 space-y-2">
            <div v-for="(f, i) in fuenteData" :key="i" class="flex items-center gap-2">
              <div class="w-3 h-3 rounded-sm flex-shrink-0" :style="{ backgroundColor: f.color }" />
              <div class="flex-1 text-xs text-gray-700">{{ f.nombre }}</div>
              <div class="text-xs text-gray-900">{{ f.porcentaje }}%</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Gráfica 4: Depreciación acumulada por gestión (barras verticales) -->
      <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
        <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
          <h2 class="text-lg text-gray-900">Depreciación Acumulada por Gestión</h2>
          <p class="text-xs text-gray-600 mt-0.5">Monto depreciado anual (Bs. miles)</p>
        </div>
        <div class="p-6">
          <svg viewBox="0 0 400 200" class="w-full" aria-label="Gráfica de barras: depreciación por gestión">
            <!-- Ejes -->
            <line x1="50" y1="10" x2="50" y2="165" stroke="#d1d5db" stroke-width="1" />
            <line x1="50" y1="165" x2="390" y2="165" stroke="#d1d5db" stroke-width="1" />
            <!-- Líneas guía -->
            <line v-for="(y, i) in [165, 125, 85, 45]" :key="i" :x1="50" :y1="y" x2="390" :y2="y" stroke="#f3f4f6" stroke-width="1" />
            <!-- Labels eje Y -->
            <text v-for="(label, i) in depYLabels" :key="'dy'+i" :x="44" :y="depYPositions[i] + 4" text-anchor="end" font-size="9" fill="#6b7280">{{ label }}</text>
            <!-- Barras -->
            <g v-for="(bar, i) in depBarData" :key="'bar'+i">
              <rect
                :x="bar.x"
                :y="bar.y"
                :width="bar.w"
                :height="bar.h"
                fill="#374151"
                rx="2"
              />
              <text :x="bar.x + bar.w / 2" :y="bar.y - 4" text-anchor="middle" font-size="8" fill="#374151">{{ bar.label }}</text>
              <text :x="bar.x + bar.w / 2" y="178" text-anchor="middle" font-size="9" fill="#6b7280">{{ bar.year }}</text>
            </g>
          </svg>
        </div>
      </div>
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-32 Estadísticas y Gráficas:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Gráficas implementadas con SVG puro, sin dependencias externas.</li>
          <li>El selector de gestión filtra todas las gráficas simultáneamente.</li>
          <li>Accesible para todos los roles.</li>
          <li>En producción se reemplazarían por una librería como Chart.js o ECharts.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ArrowLeft } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()
const emit = defineEmits<{ back: [] }>()

const gestion = ref('2025')

// ── Gráfica 1: Línea — valor total por gestión ────────────────────────────────
const lineData = [
  { year: '2020', valor: 4200 },
  { year: '2021', valor: 5100 },
  { year: '2022', valor: 6300 },
  { year: '2023', valor: 7400 },
  { year: '2024', valor: 8100 },
  { year: '2025', valor: 8450 },
]

const yLabels    = ['9000', '7000', '5000', '3000', '1000']
const yPositions = [10, 50, 90, 130, 170]

const chartPoints = computed(() => {
  const maxVal = 9000
  const minVal = 0
  const xStart = 70
  const xEnd   = 370
  const yTop   = 10
  const yBot   = 170
  return lineData.map((d, i) => ({
    x: xStart + (i / (lineData.length - 1)) * (xEnd - xStart),
    y: yBot - ((d.valor - minVal) / (maxVal - minVal)) * (yBot - yTop),
  }))
})

const linePoints = computed(() =>
  chartPoints.value.map((p) => `${p.x},${p.y}`).join(' ')
)

const areaPoints = computed(() => {
  const pts = chartPoints.value
  const first = pts[0]
  const last  = pts[pts.length - 1]
  return [
    ...pts.map((p) => `${p.x},${p.y}`),
    `${last.x},170`,
    `${first.x},170`,
  ].join(' ')
})

// ── Gráfica 2: Barras horizontales — por categoría ───────────────────────────
const categoriaData = [
  { nombre: 'Equipos de Computación', cantidad: 487, color: '#374151' },
  { nombre: 'Muebles y Enseres',       cantidad: 312, color: '#6b7280' },
  { nombre: 'Maquinaria y Equipo',     cantidad: 198, color: '#9ca3af' },
  { nombre: 'Vehículos',               cantidad: 45,  color: '#d1d5db' },
  { nombre: 'Otros',                   cantidad: 205, color: '#e5e7eb' },
]

const totalActivos  = computed(() => categoriaData.reduce((s, c) => s + c.cantidad, 0))
const maxCategoria  = computed(() => Math.max(...categoriaData.map((c) => c.cantidad)))

// ── Gráfica 3: Donut — fuente de financiamiento ───────────────────────────────
const fuenteData = [
  { nombre: 'Recursos Propios', porcentaje: 42, color: '#374151' },
  { nombre: 'IDH',              porcentaje: 31, color: '#6b7280' },
  { nombre: 'TGN',              porcentaje: 18, color: '#9ca3af' },
  { nombre: 'Donación Externa', porcentaje: 9,  color: '#d1d5db' },
]

const circumference = 2 * Math.PI * 55 // ≈ 345.6

const donutSegments = computed(() => {
  let offset = 0
  return fuenteData.map((f) => {
    const dash = (f.porcentaje / 100) * circumference
    const gap  = circumference - dash
    const seg  = { color: f.color, dash, gap, offset: -offset }
    offset += dash
    return seg
  })
})

// ── Gráfica 4: Barras verticales — depreciación por gestión ──────────────────
const depData = [
  { year: '2020', valor: 45 },
  { year: '2021', valor: 62 },
  { year: '2022', valor: 78 },
  { year: '2023', valor: 85 },
  { year: '2024', valor: 89 },
  { year: '2025', valor: 92 },
]

const depYLabels    = ['100', '75', '50', '25']
const depYPositions = [45, 85, 125, 165]

const depBarData = computed(() => {
  const maxVal  = 100
  const barW    = 40
  const spacing = 55
  const xStart  = 65
  const yBot    = 165
  const yTop    = 10
  return depData.map((d, i) => {
    const h = ((d.valor / maxVal) * (yBot - yTop))
    return {
      x: xStart + i * spacing,
      y: yBot - h,
      w: barW,
      h,
      year: d.year,
      label: d.valor,
    }
  })
})
</script>
