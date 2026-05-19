# SIGAF — Guía de Estilos y Convenciones Estéticas (Vue 3)

Esta guía documenta todos los patrones visuales del prototipo. Toda pantalla nueva debe seguir estas convenciones para mantener coherencia visual. Los estilos son idénticos al proyecto React original — solo cambia la sintaxis del template.

---

## 1. Paleta de colores

### Colores base (grises — uso principal)

| Uso | Clase Tailwind |
|---|---|
| Fondo general de la app | `bg-gray-100` |
| Fondo de tarjetas y contenedores | `bg-white` |
| Sidebar | `bg-gray-800` |
| Botón primario / tab activo / paso completado | `bg-gray-700` |
| Hover de botón primario | `hover:bg-gray-800` |
| Texto principal | `text-gray-900` |
| Texto secundario / labels | `text-gray-700` |
| Texto de apoyo / subtítulos | `text-gray-600` |
| Texto deshabilitado / placeholders | `text-gray-400` |
| Bordes de contenedores principales | `border-gray-300` |
| Bordes de inputs | `border-gray-300` |
| Fondo de encabezados de tabla | `bg-gray-100` |
| Fondo de secciones de tarjeta | `bg-gray-50` |
| Separadores de filas de tabla | `divide-gray-200` |
| Hover de filas de tabla | `hover:bg-gray-50` |
| Hover de botones secundarios | `hover:bg-gray-100` |

### Colores semánticos (estados y alertas)

| Significado | Fondo | Texto | Borde |
|---|---|---|---|
| Éxito / Activo / Encontrado | `bg-green-100` | `text-green-800` | `border-green-300` |
| Error / Baja / Faltante | `bg-red-100` | `text-red-800` | `border-red-300` |
| Advertencia / Pendiente | `bg-yellow-100` | `text-yellow-800` | `border-yellow-300` |
| Información / Procesado / En curso | `bg-blue-100` | `text-blue-800` | `border-blue-300` |
| Sobrante / Revalúo | `bg-orange-100` | `text-orange-800` | `border-orange-300` |
| Anotaciones wireframe | `bg-blue-50` | `text-blue-800` | `border-blue-200` |

### Colores de acciones destructivas

| Uso | Clase |
|---|---|
| Botón destructivo (Cerrar proceso, Finalizar sesión) | `bg-red-600 text-white hover:bg-red-700` |
| Ícono de eliminar | `text-red-600 hover:text-red-800` |
| Botón de eliminar foto (hover) | `bg-red-500 text-white` |

---

## 2. Tipografía

| Elemento | Clases |
|---|---|
| Título de página (h1) | `text-2xl text-gray-900` |
| Número grande en detalle de proceso | `text-3xl text-gray-900` |
| Número grande en tarjetas de stats | `text-3xl text-gray-900` o `text-4xl` |
| Subtítulo de sección (h2) | `text-lg text-gray-900` |
| Subtítulo de subsección (h3) | `text-sm text-gray-700` |
| Texto de tabla — columna principal | `text-sm text-gray-900` |
| Texto de tabla — columnas secundarias | `text-sm text-gray-700` |
| Encabezados de tabla (th) | `text-xs text-gray-700` |
| Labels de formulario | `text-sm text-gray-700` |
| Labels de filtros | `text-xs text-gray-600` |
| Texto de apoyo / hints | `text-xs text-gray-600` |
| Texto en sidebar — sección | `text-xs text-gray-400` |
| Texto en sidebar — ítem | `text-sm text-gray-300` |
| Texto en badges de estado | `text-xs` |
| Nota al pie / disclaimer | `text-xs text-gray-600 italic` |

---

## 3. Espaciado y layout

Todas las pantallas usan `space-y-6` como contenedor raíz:

```html
<div class="space-y-6">
  <!-- encabezado -->
  <!-- filtros -->
  <!-- tabla o contenido -->
  <!-- anotación wireframe -->
</div>
```

### Padding interno de contenedores

| Contenedor | Padding |
|---|---|
| Tarjetas / formularios principales | `p-6` |
| Barra de filtros | `p-4` |
| Encabezados de sección dentro de tarjeta | `px-6 py-4` |
| Celdas de tabla | `px-4 py-3` |
| Celdas de tabla compacta | `px-4 py-2` |
| Paginación | `px-4 py-3` |
| Badges de estado | `px-2 py-1` |
| Botones primarios | `px-4 py-2` o `px-6 py-2` |
| Botones de acción en tabla (íconos) | `p-1` |
| Botón de back | `p-2` |

### Grid de filtros

```html
<!-- Filtros estándar (5 columnas) -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4">

<!-- Filtros simples (3 columnas) -->
<div class="grid grid-cols-1 md:grid-cols-3 gap-4">

<!-- Formulario en dos columnas -->
<div class="grid grid-cols-1 md:grid-cols-2 gap-6">
```

---

## 4. Contenedores y tarjetas

### Tarjeta estándar

```html
<div class="bg-white border-2 border-gray-300 rounded-lg p-6">
```

### Tarjeta con encabezado de sección

```html
<div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
  <div class="px-6 py-4 border-b-2 border-gray-300 bg-gray-50">
    <h2 class="text-lg text-gray-900">Título de sección</h2>
  </div>
  <!-- contenido -->
</div>
```

### Bloque de anotación wireframe

```html
<div class="p-4 bg-blue-50 border-2 border-blue-200 rounded-lg">
  <div class="text-xs text-blue-800">
    <strong>Wireframe Notes - Nombre Pantalla:</strong>
    <ul class="list-disc list-inside mt-2 space-y-1">
      <li>Descripción del comportamiento esperado</li>
    </ul>
  </div>
</div>
```

---

## 5. Botones

### Botón primario

```html
<button class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">
  <IconoLucide class="w-5 h-5" />
  <span>Etiqueta</span>
</button>
```

### Botón secundario

```html
<button class="flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
  <IconoLucide class="w-4 h-4" />
  <span>Editar</span>
</button>
```

### Botón destructivo

```html
<button class="flex items-center gap-2 px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 transition-colors">
  <AlertTriangle class="w-4 h-4" />
  <span>Cerrar Proceso</span>
</button>
```

### Botón de ícono (acciones en tabla)

```html
<button class="p-1 text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded" title="Ver detalle">
  <Eye class="w-4 h-4" />
</button>
```

### Botón de volver (back)

```html
<button @click="emit('back')" class="p-2 hover:bg-gray-200 rounded">
  <ArrowLeft class="w-5 h-5 text-gray-700" />
</button>
```

### Botón con dropdown (Generar Acta)

```html
<div class="relative">
  <button
    @click="showDropdown = !showDropdown"
    class="flex items-center gap-2 px-4 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors"
  >
    <span>Generar Acta</span>
    <ChevronDown class="w-4 h-4" />
  </button>
  <div v-if="showDropdown" class="absolute right-0 mt-2 w-48 bg-white border-2 border-gray-300 rounded shadow-lg z-10">
    <button class="w-full px-4 py-2 text-left text-sm hover:bg-gray-100">Opción 1</button>
  </div>
</div>
```

### Pie de formulario (patrón estándar)

```html
<div class="flex items-center justify-end gap-3 mt-8 pt-6 border-t-2 border-gray-300">
  <button type="button" @click="emit('cancel')" class="px-6 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 transition-colors">
    Cancelar
  </button>
  <button type="submit" class="px-6 py-2 bg-gray-700 text-white rounded hover:bg-gray-800 transition-colors">
    Guardar
  </button>
</div>
```

---

## 6. Tablas

### Tabla estándar

```html
<div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
  <div class="overflow-x-auto">
    <table class="w-full">
      <thead class="bg-gray-100 border-b-2 border-gray-300">
        <tr>
          <th class="px-4 py-3 text-left text-xs text-gray-700">Columna</th>
        </tr>
      </thead>
      <tbody class="divide-y divide-gray-200">
        <tr v-for="item in items" :key="item.id" class="hover:bg-gray-50">
          <td class="px-4 py-3 text-sm text-gray-900">{{ item.principal }}</td>
          <td class="px-4 py-3 text-sm text-gray-700">{{ item.secundario }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>
```

### Tabla con checkboxes (selección múltiple)

Filas seleccionadas usan `bg-blue-50`:

```html
<tr
  v-for="item in items"
  :key="item.id"
  :class="['hover:bg-gray-50', item.selected ? 'bg-blue-50' : '']"
>
  <td class="px-4 py-3">
    <input type="checkbox" :checked="item.selected" @change="toggle(item.id)" class="w-4 h-4" />
  </td>
</tr>
```

---

## 7. Paginación

```html
<div class="border-t-2 border-gray-300 px-4 py-3 flex items-center justify-between">
  <div class="text-sm text-gray-600">
    Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} -
    {{ Math.min(currentPage * itemsPerPage, total) }} de {{ total }} registros
  </div>
  <div class="flex items-center gap-2">
    <button
      @click="currentPage = Math.max(1, currentPage - 1)"
      :disabled="currentPage === 1"
      class="p-2 border border-gray-300 rounded hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed"
    >
      <ChevronLeft class="w-4 h-4" />
    </button>
    <button class="px-3 py-1 bg-gray-700 text-white rounded">1</button>
    <button @click="currentPage++" class="p-2 border border-gray-300 rounded hover:bg-gray-100">
      <ChevronRight class="w-4 h-4" />
    </button>
  </div>
</div>
```

---

## 8. Badges de estado

```html
<span :class="['inline-block px-2 py-1 text-xs border rounded', getStatusColor(estado)]">
  {{ estado }}
</span>
```

```typescript
function getStatusColor(estado: string) {
  switch (estado) {
    case 'Activo':      return 'bg-green-100 text-green-800 border-green-300'
    case 'Baja':        return 'bg-red-100 text-red-800 border-red-300'
    case 'Transferido': return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Faltante':    return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    case 'Pendiente':   return 'bg-yellow-100 text-yellow-800 border-yellow-300'
    case 'Procesado':   return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Cerrado':     return 'bg-green-100 text-green-800 border-green-300'
    case 'En curso':    return 'bg-blue-100 text-blue-800 border-blue-300'
    case 'Finalizado':  return 'bg-green-100 text-green-800 border-green-300'
    default:            return 'bg-gray-100 text-gray-800 border-gray-300'
  }
}
```

---

## 9. Formularios

### Estructura general

```html
<form @submit.prevent="handleSubmit" class="bg-white border-2 border-gray-300 rounded-lg p-6">
  <div class="space-y-6">
    <!-- campos -->
  </div>
  <!-- pie de formulario -->
</form>
```

### Campo de texto

```html
<div>
  <label class="block text-sm text-gray-700 mb-2">
    Nombre del Campo <span class="text-red-600">*</span>
  </label>
  <input
    v-model="formData.campo"
    type="text"
    placeholder="Texto de ejemplo..."
    class="w-full px-3 py-2 border-2 border-gray-300 rounded"
    required
  />
</div>
```

### Select / Dropdown

```html
<select v-model="formData.campo" class="w-full px-3 py-2 border-2 border-gray-300 rounded" required>
  <option value="">Seleccione...</option>
  <option value="opcion1">Opción 1</option>
</select>
```

Select con grupos:

```html
<select v-model="formData.tipo" class="w-full px-3 py-2 border-2 border-gray-300 rounded">
  <option value="">Seleccione el tipo...</option>
  <optgroup v-for="(cat, idx) in categorias" :key="idx" :label="cat.nombre">
    <option v-for="(tipo, i) in cat.tipos" :key="i" :value="tipo">{{ tipo }}</option>
  </optgroup>
</select>
```

Select deshabilitado (dependiente):

```html
<select
  v-model="formData.subcuenta"
  :disabled="!formData.categoria"
  class="w-full px-3 py-2 border-2 border-gray-300 rounded disabled:bg-gray-100 disabled:cursor-not-allowed"
>
  <option value="">Seleccione categoría primero...</option>
</select>
```

### Campo de solo lectura (auto-completado)

```html
<div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-700">
  {{ valor || 'Auto-completar al seleccionar...' }}
</div>
```

### Textarea

```html
<textarea
  v-model="formData.observaciones"
  rows="4"
  class="w-full px-3 py-2 border-2 border-gray-300 rounded"
  placeholder="Notas adicionales..."
/>
```

### Upload de archivo

```html
<label class="inline-flex items-center gap-2 px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100 cursor-pointer">
  <Upload class="w-4 h-4" />
  <span>Subir Archivo</span>
  <input type="file" accept=".pdf,.doc,.docx,.jpg,.jpeg,.png" @change="handleUpload" class="hidden" />
</label>
```

---

## 10. Filtros de búsqueda

**Regla clave:** Los filtros usan `border` (1px) y `text-sm`. Los formularios de registro/edición usan `border-2` y labels `text-sm`. Esta diferencia visual distingue claramente los dos contextos.

```html
<!-- Input de búsqueda con ícono -->
<div class="relative">
  <Search class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400" />
  <input
    v-model="searchText"
    type="text"
    placeholder="NIA o descripción..."
    class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded text-sm"
  />
</div>

<!-- Select de filtro -->
<select v-model="selectedStatus" class="w-full px-3 py-2 border border-gray-300 rounded text-sm">
  <option value="">Todos</option>
</select>
```

---

## 11. Pestañas (tabs)

```html
<div class="bg-white border-2 border-gray-300 rounded-lg overflow-hidden">
  <!-- Barra de pestañas -->
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
  <!-- Contenido -->
  <div class="p-6">
    <div v-if="activeTab === 'general'">...</div>
    <div v-else-if="activeTab === 'location'">...</div>
  </div>
</div>
```

---

## 12. Componente FieldDisplay (campos de solo lectura)

Componente reutilizable en `src/app/components/FieldDisplay.vue`:

```html
<!-- Uso -->
<FieldDisplay label="NIA" :value="asset.nia" />
<FieldDisplay label="Descripción" :value="asset.descripcion" class-name="md:col-span-2" />
```

```html
<!-- Definición (FieldDisplay.vue) -->
<template>
  <div :class="className">
    <label class="block text-xs text-gray-600 mb-1">{{ label }}</label>
    <div class="px-3 py-2 bg-gray-100 border border-gray-300 rounded text-sm text-gray-900">
      {{ value }}
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{ label: string; value: string; className?: string }>()
</script>
```

---

## 13. Iconos en arrays de datos

A diferencia de React (donde se puede usar JSX inline), en Vue los iconos en arrays de datos se pasan como referencias de componente y se renderizan con `<component :is>`:

```typescript
// ✅ Correcto en Vue
import { Home, Package } from 'lucide-vue-next'

const menuItems = [
  { label: 'Dashboard', icon: Home,    screen: 'dashboard' },
  { label: 'Activos',   icon: Package, screen: 'fixed-assets' },
]
```

```html
<!-- En el template -->
<component :is="item.icon" class="w-5 h-5" />
```

---

## 14. Modales de confirmación

Usar `<Teleport to="body">` para montar el modal fuera del árbol del componente:

```html
<Teleport to="body">
  <div v-if="showConfirm" class="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
    <div class="bg-white rounded-lg p-6 max-w-md w-full mx-4 border-2 border-gray-300">
      <div class="flex items-start gap-3 mb-4">
        <AlertTriangle class="w-6 h-6 text-red-600 flex-shrink-0 mt-0.5" />
        <div>
          <h3 class="text-lg text-gray-900 mb-2">Confirmar acción</h3>
          <p class="text-sm text-gray-600">¿Está seguro que desea continuar?</p>
        </div>
      </div>
      <div class="flex items-center justify-end gap-3 mt-6">
        <button @click="showConfirm = false" class="px-4 py-2 border-2 border-gray-300 rounded hover:bg-gray-100">
          Cancelar
        </button>
        <button @click="handleConfirm" class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700">
          Confirmar
        </button>
      </div>
    </div>
  </div>
</Teleport>
```
