<template>
  <aside class="w-60 bg-gray-800 text-white flex flex-col">
    <!-- Logo area -->
    <div class="p-4 border-b border-gray-700">
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 border-2 border-gray-400 rounded flex items-center justify-center">
          <span class="text-gray-400 text-xs">LOGO</span>
        </div>
        <div>
          <div class="text-white text-sm">SIGAF</div>
          <div class="text-gray-400 text-xs">UTO</div>
        </div>
      </div>
    </div>

    <!-- Navigation menu -->
    <nav class="flex-1 overflow-y-auto p-4">
      <div
        v-for="(section, idx) in visibleSections"
        :key="idx"
        class="mb-6"
      >
        <div class="text-xs text-gray-400 mb-2 px-3">{{ section.title }}</div>
        <div class="space-y-1">
          <button
            v-for="(item, itemIdx) in section.items"
            :key="itemIdx"
            @click="item.screen && emit('navigate', item.screen)"
            :class="[
              'w-full flex items-center gap-3 px-3 py-2 rounded text-sm transition-colors',
              item.screen === currentScreen
                ? 'bg-gray-700 text-white'
                : 'text-gray-300 hover:bg-gray-700 hover:text-white'
            ]"
          >
            <component :is="item.icon" class="w-5 h-5" />
            <span>{{ item.label }}</span>
          </button>
        </div>
      </div>
    </nav>

    <!-- User info at bottom -->
    <div class="p-4 border-t border-gray-700">
      <div class="flex items-center gap-3">
        <div class="w-8 h-8 bg-gray-600 rounded-full flex items-center justify-center">
          <span class="text-xs">U</span>
        </div>
        <div class="flex-1 text-sm">
          <div class="text-white">Usuario</div>
          <div class="text-gray-400 text-xs">{{ userRole }}</div>
        </div>
      </div>
    </div>
  </aside>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import {
  Home,
  Package,
  ClipboardList,
  FileText,
  Settings,
  Users,
  BarChart,
  ShieldCheck,
  Hash,
} from 'lucide-vue-next'
import type { UserRole, Screen } from '../types'

const props = defineProps<{
  userRole: UserRole
  currentScreen: Screen
}>()

const emit = defineEmits<{
  navigate: [screen: Screen]
}>()

interface MenuItem {
  label: string
  icon: unknown
  roles: UserRole[]
  screen?: Screen
}

interface MenuSection {
  title: string
  items: MenuItem[]
}

const menuSections: MenuSection[] = [
  {
    title: 'Principal',
    items: [
      {
        label: 'Dashboard',
        icon: Home,
        roles: ['Administrador', 'Operador', 'Consulta'],
        screen: 'dashboard',
      },
    ],
  },
  {
    title: 'Activos',
    items: [
      {
        label: 'Activos Fijos',
        icon: Package,
        roles: ['Administrador', 'Operador', 'Consulta'],
        screen: 'fixed-assets',
      },
      {
        label: 'Materiales de Control',
        icon: ClipboardList,
        roles: ['Administrador', 'Operador', 'Consulta'],
        screen: 'control-materials',
      },
      {
        label: 'Inventarios',
        icon: FileText,
        roles: ['Administrador', 'Operador'],
        screen: 'inventory-sessions',
      },
    ],
  },
  {
    title: 'Procesos',
    items: [
      {
        label: 'Procesos Administrativos',
        icon: FileText,
        roles: ['Administrador', 'Operador'],
        screen: 'processes',
      },
      {
        label: 'Transferencias',
        icon: FileText,
        roles: ['Administrador', 'Operador'],
        screen: 'transfer',
      },
      {
        label: 'Cambio de Responsable',
        icon: Users,
        roles: ['Administrador', 'Operador'],
        screen: 'responsible-change',
      },
      {
        label: 'Bajas',
        icon: FileText,
        roles: ['Administrador', 'Operador'] as UserRole[],
        screen: 'write-off' as Screen,
      },
    ],
  },
  {
    title: 'Reportes',
    items: [
      {
        label: 'Reportes',
        icon: BarChart,
        roles: ['Administrador', 'Operador', 'Consulta'] as UserRole[],
        screen: 'reports' as Screen,
      },
      {
        label: 'Actas',
        icon: FileText,
        roles: ['Administrador', 'Operador', 'Consulta'],
        screen: 'actas',
      },
      {
        label: 'Depreciación',
        icon: BarChart,
        roles: ['Administrador', 'Operador'] as UserRole[],
        screen: 'depreciation' as Screen,
      },
      {
        label: 'Rangos NIA / NIM',
        icon: Hash,
        roles: ['Administrador', 'Operador'] as UserRole[],
        screen: 'identifier-ranges' as Screen,
      },
    ],
  },
  {
    title: 'Configuración',
    items: [
      {
        label: 'Usuarios',
        icon: Users,
        roles: ['Administrador'] as UserRole[],
        screen: 'users' as Screen,
      },
      {
        label: 'Parámetros del Sistema',
        icon: Settings,
        roles: ['Administrador'] as UserRole[],
        screen: 'system-parameters' as Screen,
      },
      {
        label: 'Auditoría',
        icon: ShieldCheck,
        roles: ['Administrador'] as UserRole[],
        screen: 'audit-log' as Screen,
      },
    ],
  },
]

const visibleSections = computed(() =>
  menuSections
    .map((section) => ({
      ...section,
      items: section.items.filter((item) => item.roles.includes(props.userRole)),
    }))
    .filter((section) => section.items.length > 0),
)
</script>
