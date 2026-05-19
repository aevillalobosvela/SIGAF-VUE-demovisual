# SIGAF — Guía del Prototipo Interactivo (Vue 3)

## ¿Qué es este proyecto?

Este proyecto es un **prototipo interactivo de alta fidelidad** del Sistema de Gestión de Activos Fijos (SIGAF) de la UTO, implementado con **Vue 3 + TypeScript + Vite**. Su propósito es permitir que los usuarios y responsables del sistema puedan **revisar, navegar y proponer cambios** en las pantallas, flujos y funcionalidades antes de iniciar cualquier desarrollo con backend real.

No hay base de datos ni servicios conectados. Todos los datos son ficticios y están definidos directamente en el código de cada componente.

---

## Cómo ejecutar el prototipo

```bash
# Instalar dependencias
npm install

# Iniciar el servidor de desarrollo
npm run dev
```

Luego abrir el navegador en `http://localhost:5173`.

---

## Navegación del prototipo

### Pantalla de inicio — Login

Al abrir la aplicación se muestra la pantalla de login. Para el prototipo, no es necesario ingresar credenciales reales. En la parte inferior hay un selector de rol para probar las distintas vistas:

| Rol | Acceso |
|---|---|
| **Administrador** | Acceso completo a todas las pantallas y acciones |
| **Operador** | Acceso operativo: puede registrar, editar y ejecutar procesos, pero no accede a Configuración ni Usuarios |
| **Consulta** | Solo lectura: puede ver listados y detalles, pero no puede crear, editar ni ejecutar acciones |

### Estructura de navegación

La aplicación tiene una barra lateral izquierda con las siguientes secciones:

```
Principal
  └── Dashboard

Activos
  ├── Activos Fijos
  ├── Materiales de Control
  └── Inventarios

Procesos
  ├── Procesos Administrativos
  ├── Transferencias
  ├── Cambio de Responsable
  └── Bajas (sin pantalla aún)

Reportes
  ├── Reportes (sin pantalla aún)
  ├── Actas
  └── Depreciación

Configuración
  ├── Usuarios (sin pantalla aún)
  └── Configuración (sin pantalla aún)
```

Los ítems marcados como "sin pantalla aún" están visibles en el menú pero no navegan a ningún lado todavía.

---

## Pantallas implementadas

### Dashboard
Pantalla principal con:
- 4 tarjetas de resumen: Total Activos Fijos, Total Materiales de Control, Procesos Pendientes, Procesos Cerrados este Mes
- Acciones rápidas (visibles solo para Administrador y Operador)
- Panel de alertas y notificaciones

### Activos Fijos — Listado
Tabla paginada con filtros por búsqueda de texto (NIA/descripción), categoría, unidad y estado. Acciones por fila: ver detalle y editar (editar solo para Admin/Operador).

**Estados posibles de un activo:**
- `Activo` — verde
- `Baja` — rojo
- `Transferido` — azul
- `Faltante` — amarillo

### Activos Fijos — Detalle
Ficha completa del activo con 7 pestañas:

| Pestaña | Contenido |
|---|---|
| Datos Generales | NIA, código, descripción, categoría, costo, fecha, fuente de financiamiento, estado, régimen |
| Responsable y Ubicación | Responsable actual, unidad, historial de asignaciones |
| Fotografías | Galería de imágenes con opción de agregar/eliminar (Admin/Operador) |
| Documentos | Lista de archivos adjuntos con descarga y eliminación |
| Historial de Movimientos | Línea de tiempo con Alta, Transferencias y Cambios de Responsable |
| Depreciación | Tabla anual con depreciación del período, acumulada, vida útil restante y revalúo |
| Código QR | Código QR del activo con botón de impresión de etiqueta |

Desde el encabezado se puede generar actas: **ARAF** (Alta), **AIAF** (Inventario), **ATAF** (Transferencia), **ABAF** (Baja), **ADAF** (Descargo).

### Activos Fijos — Formulario de Registro/Edición
Formulario en dos columnas con los campos:
- Descripción (ancho completo)
- Categoría Contable → Subcuenta (dependiente de categoría)
- Costo de Compra, Fecha de Adquisición
- Fuente de Financiamiento, Unidad, Responsable
- Estado Inicial, Régimen
- Foto del activo (upload con vista previa)
- Documentos adjuntos (requeridos si el costo supera Bs. 50.000)

### Materiales de Control — Listado y Detalle
Similar a Activos Fijos pero para bienes de menor valor que no se deprecian. Misma estructura de tabla y detalle.

### Procesos Administrativos — Listado
Tabla de procesos con filtros por número, tipo, estado, unidad, rango de fechas y usuario creador.

**Estados:** `Pendiente` (amarillo), `Procesado` (azul), `Cerrado` (verde)

### Procesos Administrativos — Detalle
Vista del proceso con información general, lista de activos involucrados y línea de tiempo de estados.

### Inventarios — Sesiones
Listado de sesiones de inventario con estado y progreso.

### Inventarios — Escaneo QR
Pantalla optimizada para dispositivos móviles/tablets con visor de cámara, barra de progreso y lista de escaneos recientes.

### Depreciación
Panel hub con 4 acciones: Ejecutar Cálculo, Ver Registros, Registrar Revalúo, Ver Asientos Contables.

### Actas — Listado y Vista Previa
Listado de actas generadas con filtros y vista previa en formato A4.

---

## Cómo modificar el prototipo

### Cambiar datos de ejemplo

Cada componente tiene sus datos mock definidos localmente. Por ejemplo, en `FixedAssetsList.vue`:

```typescript
const mockAssets: Asset[] = [
  { id: '1', nia: 'AF-2024-0001', descripcion: 'Computadora Portátil HP ProBook 450', ... },
  // Agregar, modificar o eliminar registros aquí
]
```

### Agregar una nueva pantalla

1. Crear el componente en `src/app/components/NombreComponente.vue`
2. Importarlo en `src/app/App.vue`
3. Agregar el tipo de pantalla al tipo `Screen` en `src/app/types.ts`
4. Agregar el bloque `v-else-if` correspondiente en el `<template>` de `App.vue`
5. Agregar la entrada en `breadcrumbMap` en `AppLayout.vue`
6. Agregar el ítem al menú en `Sidebar.vue` con su `screen` correspondiente

### Agregar un ítem al menú lateral

En `src/app/components/Sidebar.vue`, dentro del array `menuSections`, agregar el ítem a la sección correspondiente:

```typescript
{
  label: 'Nombre del Módulo',
  icon: IconoLucide,           // componente de lucide-vue-next, no JSX
  roles: ['Administrador', 'Operador', 'Consulta'] as UserRole[],
  screen: 'nombre-pantalla' as Screen  // debe coincidir con el tipo Screen en types.ts
}
```

### Modificar campos de un formulario

Los formularios usan estado reactivo con `ref()`. Para agregar un campo nuevo:

1. Agregar la propiedad al objeto `ref` del estado inicial
2. Agregar el elemento `<input>` o `<select>` en el template
3. Conectarlo con `v-model` al estado

```vue
<script setup lang="ts">
import { ref } from 'vue'

const formData = ref({
  campoExistente: '',
  nuevoCampo: '',   // 1. agregar aquí
})
</script>

<template>
  <!-- 2 y 3. agregar en el template con v-model -->
  <input v-model="formData.nuevoCampo" type="text" class="..." />
</template>
```

### Cambiar la visibilidad por rol

La lógica de roles se aplica con `computed` o condiciones directas. Ejemplo:

```typescript
const canEdit = computed(() =>
  userRole === 'Administrador' || userRole === 'Operador'
)
```

```html
<button v-if="canEdit">Editar</button>
```

---

## Estructura de archivos

```
src/
├── main.ts                     # Punto de entrada
├── style.css                   # Tailwind v4 + tema shadcn
├── lib/
│   └── utils.ts                # Helper cn() para clases Tailwind
└── app/
    ├── types.ts                # Tipos compartidos: UserRole, Screen
    ├── App.vue                 # Controlador de navegación y estado global
    └── components/
        ├── AppLayout.vue       # Layout principal (sidebar + topbar)
        ├── Sidebar.vue         # Menú lateral con control de roles
        ├── LoginScreen.vue     # Pantalla de login
        ├── FieldDisplay.vue    # Componente reutilizable para campos de solo lectura
        ├── DashboardContent.vue
        ├── FixedAssetsList.vue
        ├── FixedAssetDetail.vue
        ├── FixedAssetForm.vue
        ├── ControlMaterialsList.vue
        ├── ControlMaterialDetail.vue
        ├── ProcessesList.vue
        ├── ProcessDetail.vue
        ├── NewProcessForm.vue
        ├── TransferForm.vue
        ├── ResponsibleChangeForm.vue
        ├── InventorySessionsList.vue
        ├── InventoryScanScreen.vue
        ├── InventoryResult.vue
        ├── DepreciationPanel.vue
        ├── DepreciationRecords.vue
        ├── RevaluationForm.vue
        ├── ActasList.vue
        └── ActaPreview.vue
```

---

## Notas de diseño

- El prototipo usa **Tailwind CSS v4** para todos los estilos. Los colores principales son grises (`gray-700`, `gray-800`) para elementos de acción y fondo del sidebar.
- Los íconos provienen de **lucide-vue-next**. A diferencia de React, los iconos se pasan como referencias de componente en arrays de datos y se renderizan con `<component :is="item.icon" />`.
- Cada pantalla incluye un bloque de anotaciones en azul (`Wireframe Notes`) que describe el comportamiento esperado. Estos bloques pueden eliminarse cuando las pantallas estén aprobadas.
- La navegación **no usa Vue Router** — está implementada con un `ref<Screen>` en `App.vue` que controla qué pantalla se muestra. Esto es intencional para simplificar el prototipo.
- Los modales de confirmación usan `<Teleport to="body">` para montarse fuera del árbol del componente, equivalente al portal de React.

---

## Pendientes / Pantallas sin implementar

| Módulo | Estado |
|---|---|
| Bajas de Activos | Sin pantalla |
| Reportes generales | Sin pantalla |
| Gestión de Usuarios | Sin pantalla |
| Configuración del sistema | Sin pantalla |
| Asientos Contables (desde Depreciación) | Sin pantalla |
