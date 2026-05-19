<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Parámetros del Sistema</h1>
    </div>

    <!-- Tabs -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="border-b-2 border-gray-300 flex overflow-x-auto">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          @click="activeTab = tab.id"
          :class="[
            'px-5 py-3 text-sm whitespace-nowrap transition-colors',
            activeTab === tab.id ? 'bg-gray-700 text-white' : 'text-gray-700 hover:bg-gray-100'
          ]"
        >
          {{ tab.label }}
        </button>
      </div>

      <div class="p-6">
        <!-- Tab: Unidades -->
        <div v-if="activeTab === 'unidades'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Unidades Organizacionales</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Nueva Unidad</span>
            </button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Código</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Nombre</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Dependencia</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Nivel</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="u in unidades" :key="u.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ u.codigo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ u.nombre }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ u.dependencia || '—' }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ u.nivel }}</td>
                  <td class="px-4 py-3">
                    <span :class="['inline-block px-2 py-1 text-xs border rounded', u.activo ? 'bg-green-100 text-green-800 border-green-300' : 'bg-red-100 text-red-800 border-red-300']">
                      {{ u.activo ? 'Activo' : 'Inactivo' }}
                    </span>
                  </td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Tab: Secciones -->
        <div v-else-if="activeTab === 'secciones'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Secciones</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Nueva Sección</span>
            </button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Código</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Nombre</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="s in secciones" :key="s.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ s.codigo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ s.nombre }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ s.unidad }}</td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Tab: Estructuras Programáticas -->
        <div v-else-if="activeTab === 'ep'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Estructuras Programáticas (EP)</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Nueva EP</span>
            </button>
          </div>
          <p class="text-sm text-gray-600">Las EP representan cargos institucionales. Los activos se asignan a responsables a través de su EP.</p>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Código EP</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Cargo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Responsable Actual</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="ep in estructurasProgramaticas" :key="ep.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ ep.codigo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ ep.cargo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ ep.unidad }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ ep.responsable }}</td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Tab: Cuentas Contables -->
        <div v-else-if="activeTab === 'cuentas'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Cuentas Contables</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Nueva Cuenta</span>
            </button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Grupo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Subgrupo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Nombre</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Vida Útil (meses)</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="c in cuentasContables" :key="c.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ c.grupo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ c.subgrupo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ c.nombre }}</td>
                  <td class="px-4 py-3">
                    <span :class="['inline-block px-2 py-1 text-xs border rounded', c.tipo === 'AF' ? 'bg-blue-100 text-blue-800 border-blue-300' : 'bg-gray-100 text-gray-800 border-gray-300']">
                      {{ c.tipo === 'AF' ? 'Activo Fijo' : 'Mat. Control' }}
                    </span>
                  </td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ c.vidaUtil ?? '—' }}</td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Tab: Clasificadores Internos -->
        <div v-else-if="activeTab === 'clasificadores'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Clasificadores Internos</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Nuevo Clasificador</span>
            </button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Código</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Descripción</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Tipo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="cl in clasificadores" :key="cl.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ cl.codigo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ cl.descripcion }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ cl.tipo }}</td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Tab: UFV -->
        <div v-else-if="activeTab === 'ufv'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Unidad de Fomento a la Vivienda (UFV)</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Registrar UFV</span>
            </button>
          </div>
          <p class="text-sm text-gray-600">Valores UFV publicados por el Banco Central de Bolivia, usados para el cálculo de revalúos.</p>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Fecha</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Valor UFV (Bs.)</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Registrado por</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="ufv in ufvValues" :key="ufv.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900">{{ ufv.fecha }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ ufv.valor.toFixed(4) }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ ufv.registradoPor }}</td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Tab: Responsables -->
        <div v-else-if="activeTab === 'responsables'" class="space-y-4">
          <div class="flex items-center justify-between">
            <h2 class="text-lg text-gray-900">Responsables</h2>
            <button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors text-sm">
              <Plus class="w-4 h-4" />
              <span>Nuevo Responsable</span>
            </button>
          </div>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-100 border-b-2 border-gray-300">
                <tr>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">CI</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Nombre Completo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Cargo / EP</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Unidad</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Bienes a Cargo</th>
                  <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-200">
                <tr v-for="r in responsables" :key="r.id" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ r.ci }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900">{{ r.nombre }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ r.cargo }}</td>
                  <td class="px-4 py-3 text-sm text-gray-700">{{ r.unidad }}</td>
                  <td class="px-4 py-3 text-sm text-gray-900 text-center">{{ r.bienesACargo }}</td>
                  <td class="px-4 py-3">
                    <button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded">
                      <Pencil class="w-4 h-4" />
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-39 Parámetros del Sistema:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Solo accesible para rol Administrador.</li>
          <li>Las Unidades tienen jerarquía: Facultad → Departamento → Oficina.</li>
          <li>Las Cuentas Contables definen la vida útil para el cálculo de depreciación (Decreto 24051).</li>
          <li>Los Responsables son independientes de los usuarios del sistema.</li>
          <li>Los valores UFV se registran manualmente desde el BCB.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Plus, Pencil } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()

const tabs = [
  { id: 'unidades',       label: 'Unidades' },
  { id: 'secciones',      label: 'Secciones' },
  { id: 'ep',             label: 'Estructuras Programáticas' },
  { id: 'cuentas',        label: 'Cuentas Contables' },
  { id: 'clasificadores', label: 'Clasificadores Internos' },
  { id: 'ufv',            label: 'UFV' },
  { id: 'responsables',   label: 'Responsables' },
]

const activeTab = ref('unidades')

const unidades = [
  { id: '1', codigo: 'U-001', nombre: 'Rectorado',                    dependencia: null,          nivel: 'Facultad',     activo: true },
  { id: '2', codigo: 'U-002', nombre: 'Administración Central',        dependencia: 'Rectorado',   nivel: 'Departamento', activo: true },
  { id: '3', codigo: 'U-003', nombre: 'División de Activos Fijos',     dependencia: 'Adm. Central',nivel: 'Oficina',      activo: true },
  { id: '4', codigo: 'U-004', nombre: 'Facultad de Ingeniería',        dependencia: null,          nivel: 'Facultad',     activo: true },
  { id: '5', codigo: 'U-005', nombre: 'Depto. de Sistemas',            dependencia: 'Fac. Ing.',   nivel: 'Departamento', activo: true },
  { id: '6', codigo: 'U-006', nombre: 'Laboratorio de Computación',    dependencia: 'Depto. Sist.',nivel: 'Oficina',      activo: true },
  { id: '7', codigo: 'U-007', nombre: 'Facultad de Derecho',           dependencia: null,          nivel: 'Facultad',     activo: false },
]

const secciones = [
  { id: '1', codigo: 'S-001', nombre: 'Secretaría Rectorado',       unidad: 'Rectorado' },
  { id: '2', codigo: 'S-002', nombre: 'Contabilidad',               unidad: 'Administración Central' },
  { id: '3', codigo: 'S-003', nombre: 'Almacenes',                  unidad: 'Administración Central' },
  { id: '4', codigo: 'S-004', nombre: 'Laboratorio A',              unidad: 'Laboratorio de Computación' },
]

const estructurasProgramaticas = [
  { id: '1', codigo: 'EP-001', cargo: 'Rector',                    unidad: 'Rectorado',             responsable: 'Dr. Juan Quispe Torrez' },
  { id: '2', codigo: 'EP-002', cargo: 'Director Administrativo',   unidad: 'Administración Central', responsable: 'Lic. Roberto Vargas Flores' },
  { id: '3', codigo: 'EP-003', cargo: 'Jefe División Activos',     unidad: 'División de Activos Fijos', responsable: 'Inv. Pedro Condori Mamani' },
  { id: '4', codigo: 'EP-004', cargo: 'Docente Titular',           unidad: 'Depto. de Sistemas',    responsable: 'Ing. Carlos Mamani López' },
]

const cuentasContables = [
  { id: '1', grupo: '03', subgrupo: '01', nombre: 'Equipos de Computación',  tipo: 'AF', vidaUtil: 60 },
  { id: '2', grupo: '03', subgrupo: '02', nombre: 'Muebles y Enseres',        tipo: 'AF', vidaUtil: 120 },
  { id: '3', grupo: '03', subgrupo: '03', nombre: 'Vehículos',                tipo: 'AF', vidaUtil: 60 },
  { id: '4', grupo: '03', subgrupo: '04', nombre: 'Maquinaria y Equipo',      tipo: 'AF', vidaUtil: 120 },
  { id: '5', grupo: '20', subgrupo: '01', nombre: 'Útiles de Escritorio',     tipo: 'MC', vidaUtil: null },
  { id: '6', grupo: '20', subgrupo: '02', nombre: 'Material de Limpieza',     tipo: 'MC', vidaUtil: null },
]

const clasificadores = [
  { id: '1', codigo: 'CL-001', descripcion: 'Recursos Propios',         tipo: 'Fuente de Financiamiento' },
  { id: '2', codigo: 'CL-002', descripcion: 'IDH',                      tipo: 'Fuente de Financiamiento' },
  { id: '3', codigo: 'CL-003', descripcion: 'Donación Externa',         tipo: 'Fuente de Financiamiento' },
  { id: '4', codigo: 'CL-004', descripcion: 'Tesoro General de la Nación', tipo: 'Fuente de Financiamiento' },
]

const ufvValues = [
  { id: '1', fecha: '01/06/2025', valor: 2.45312, registradoPor: 'admin.uto' },
  { id: '2', fecha: '01/05/2025', valor: 2.44891, registradoPor: 'admin.uto' },
  { id: '3', fecha: '01/04/2025', valor: 2.44103, registradoPor: 'admin.uto' },
  { id: '4', fecha: '01/03/2025', valor: 2.43567, registradoPor: 'admin.uto' },
  { id: '5', fecha: '01/02/2025', valor: 2.42980, registradoPor: 'admin.uto' },
]

const responsables = [
  { id: '1', ci: '4521890',  nombre: 'Dr. Juan Quispe Torrez',        cargo: 'Rector — EP-001',              unidad: 'Rectorado',              bienesACargo: 12 },
  { id: '2', ci: '6234501',  nombre: 'Lic. Roberto Vargas Flores',    cargo: 'Dir. Administrativo — EP-002', unidad: 'Administración Central',  bienesACargo: 8  },
  { id: '3', ci: '7890123',  nombre: 'Inv. Pedro Condori Mamani',     cargo: 'Jefe División — EP-003',       unidad: 'División de Activos Fijos', bienesACargo: 3 },
  { id: '4', ci: '5678234',  nombre: 'Ing. Carlos Mamani López',      cargo: 'Docente Titular — EP-004',     unidad: 'Depto. de Sistemas',      bienesACargo: 24 },
  { id: '5', ci: '3456789',  nombre: 'Lic. María Flores Quispe',      cargo: 'Secretaria — EP-005',          unidad: 'Rectorado',              bienesACargo: 6  },
]
</script>
