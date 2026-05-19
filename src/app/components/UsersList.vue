<template>
  <div class="space-y-6">
    <!-- Encabezado -->
    <div class="flex items-center justify-between">
      <h1 class="text-2xl text-gray-900">Gestión de Usuarios</h1>
      <button
        @click="emit('new-user')"
        class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
      >
        <UserPlus class="w-5 h-5" />
        <span>Nuevo Usuario</span>
      </button>
    </div>

    <!-- Filtros -->
    <div class="bg-white border-2 border-gray-300 rounded-lg p-4">
      <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
        <div class="relative">
          <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
          <input
            v-model="filters.search"
            type="text"
            placeholder="Nombre o usuario..."
            class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
          />
        </div>
        <select v-model="filters.rol" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
          <option value="">Todos los roles</option>
          <option value="Administrador">Administrador</option>
          <option value="Operador">Operador</option>
          <option value="Consulta">Consulta</option>
        </select>
        <select v-model="filters.estado" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
          <option value="">Todos los estados</option>
          <option value="Activo">Activo</option>
          <option value="Inactivo">Inactivo</option>
        </select>
        <button
          @click="resetFilters"
          class="flex items-center gap-2 px-4 py-2 border border-gray-300 rounded text-sm hover:bg-gray-100 transition-colors"
        >
          <X class="w-4 h-4" />
          <span>Limpiar</span>
        </button>
      </div>
    </div>

    <!-- Tabla -->
    <div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-100 border-b-2 border-gray-300">
            <tr>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Usuario</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Nombre Completo</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Rol</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Perfiles</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Estado</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Último Acceso</th>
              <th class="px-4 py-3 text-left text-xs text-gray-700">Acciones</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-200">
            <tr v-for="user in filteredUsers" :key="user.id" class="hover:bg-gray-50">
              <td class="px-4 py-3 text-sm text-gray-900 font-mono">{{ user.username }}</td>
              <td class="px-4 py-3 text-sm text-gray-900">{{ user.nombre }}</td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', getRolColor(user.rol)]">
                  {{ user.rol }}
                </span>
              </td>
              <td class="px-4 py-3 text-sm text-gray-700">
                <div class="flex flex-wrap gap-1">
                  <span
                    v-for="perfil in user.perfiles"
                    :key="perfil"
                    class="inline-block px-2 py-0.5 text-xs bg-gray-100 border border-gray-300 rounded"
                  >
                    {{ perfil }}
                  </span>
                  <span v-if="user.perfiles.length === 0" class="text-gray-400 text-xs">—</span>
                </div>
              </td>
              <td class="px-4 py-3">
                <span :class="['inline-block px-2 py-1 text-xs border rounded', user.estado === 'Activo' ? 'bg-green-100 text-green-800 border-green-300' : 'bg-red-100 text-red-800 border-red-300']">
                  {{ user.estado }}
                </span>
              </td>
              <td class="px-4 py-3 text-sm text-gray-700">{{ user.ultimoAcceso }}</td>
              <td class="px-4 py-3">
                <div class="flex items-center gap-1">
                  <button
                    @click="emit('edit-user', user.id)"
                    class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded"
                    title="Editar usuario"
                  >
                    <Pencil class="w-4 h-4" />
                  </button>
                  <button
                    @click="toggleEstado(user.id)"
                    :class="['p-1 rounded', user.estado === 'Activo' ? 'text-red-600 hover:text-red-800 hover:bg-red-50' : 'text-green-600 hover:text-green-800 hover:bg-green-50']"
                    :title="user.estado === 'Activo' ? 'Desactivar' : 'Activar'"
                  >
                    <UserX v-if="user.estado === 'Activo'" class="w-4 h-4" />
                    <UserCheck v-else class="w-4 h-4" />
                  </button>
                </div>
              </td>
            </tr>
            <tr v-if="filteredUsers.length === 0">
              <td colspan="7" class="px-4 py-8 text-center text-sm text-gray-400">
                No se encontraron usuarios con los filtros aplicados.
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Paginación -->
      <div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
        <div class="text-sm text-gray-600">
          Mostrando {{ filteredUsers.length }} de {{ mockUsers.length }} usuarios
        </div>
        <div class="flex items-center gap-2">
          <button class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50" disabled>
            <ChevronLeft class="w-4 h-4" />
          </button>
          <button class="px-3 py-1 bg-gray-700 text-white rounded text-sm">1</button>
          <button class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50" disabled>
            <ChevronRight class="w-4 h-4" />
          </button>
        </div>
      </div>
    </div>

    <!-- Wireframe Notes -->
    <div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
      <div class="text-xs text-blue-800">
        <strong>Wireframe Notes - P-37 Gestión de Usuarios:</strong>
        <ul class="list-disc list-inside mt-2 space-y-1">
          <li>Solo visible para rol Administrador.</li>
          <li>El botón de activar/desactivar cambia el estado sin eliminar el usuario.</li>
          <li>Los perfiles (Elaboración, Proceso, Firma-Cierre) solo aplican al rol Operador.</li>
          <li>No se puede desactivar al propio usuario logueado.</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Search, X, UserPlus, Pencil, UserX, UserCheck, ChevronLeft, ChevronRight } from 'lucide-vue-next'
import type { UserRole } from '../types'

defineProps<{ userRole: UserRole }>()

const emit = defineEmits<{
  'new-user': []
  'edit-user': [id: string]
}>()

interface Usuario {
  id: string
  username: string
  nombre: string
  rol: UserRole
  perfiles: string[]
  estado: 'Activo' | 'Inactivo'
  ultimoAcceso: string
}

const mockUsers = ref<Usuario[]>([
  { id: '1', username: 'admin.uto',    nombre: 'Lic. Roberto Vargas Flores',    rol: 'Administrador', perfiles: [],                                    estado: 'Activo',   ultimoAcceso: '15/06/2025 08:32' },
  { id: '2', username: 'p.condori',    nombre: 'Inv. Pedro Condori Mamani',     rol: 'Operador',      perfiles: ['Elaboración', 'Proceso'],             estado: 'Activo',   ultimoAcceso: '15/06/2025 09:15' },
  { id: '3', username: 'm.flores',     nombre: 'Lic. María Flores Quispe',      rol: 'Operador',      perfiles: ['Elaboración', 'Proceso', 'Firma-Cierre'], estado: 'Activo',   ultimoAcceso: '14/06/2025 16:47' },
  { id: '4', username: 'c.mamani',     nombre: 'Ing. Carlos Mamani López',      rol: 'Operador',      perfiles: ['Elaboración'],                        estado: 'Activo',   ultimoAcceso: '13/06/2025 11:20' },
  { id: '5', username: 'j.quispe',     nombre: 'Dr. Juan Quispe Torrez',        rol: 'Consulta',      perfiles: [],                                    estado: 'Activo',   ultimoAcceso: '10/06/2025 14:05' },
  { id: '6', username: 'a.gutierrez',  nombre: 'Lic. Ana Gutiérrez Salinas',    rol: 'Consulta',      perfiles: [],                                    estado: 'Inactivo', ultimoAcceso: '02/03/2025 09:00' },
  { id: '7', username: 'r.choque',     nombre: 'Tec. Rodrigo Choque Apaza',     rol: 'Operador',      perfiles: ['Proceso', 'Firma-Cierre'],            estado: 'Activo',   ultimoAcceso: '15/06/2025 07:58' },
])

const filters = ref({ search: '', rol: '', estado: '' })

const filteredUsers = computed(() =>
  mockUsers.value.filter((u) => {
    const matchSearch = !filters.value.search ||
      u.nombre.toLowerCase().includes(filters.value.search.toLowerCase()) ||
      u.username.toLowerCase().includes(filters.value.search.toLowerCase())
    const matchRol    = !filters.value.rol    || u.rol === filters.value.rol
    const matchEstado = !filters.value.estado || u.estado === filters.value.estado
    return matchSearch && matchRol && matchEstado
  })
)

function getRolColor(rol: UserRole) {
  switch (rol) {
    case 'Administrador': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Operador':      return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    case 'Consulta':      return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}

function toggleEstado(id: string) {
  const user = mockUsers.value.find((u) => u.id === id)
  if (user) user.estado = user.estado === 'Activo' ? 'Inactivo' : 'Activo'
}

function resetFilters() {
  filters.value = { search: '', rol: '', estado: '' }
}
</script>
