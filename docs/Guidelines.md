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
  ├── Inventarios
  └── Rangos NIA / NIM

Procesos
  ├── Procesos Administrativos
  ├── Transferencias
  ├── Cambio de Responsable
  └── Bajas

Reportes
  ├── Reportes
  ├── Actas
  └── Depreciación

Configuración  (solo Administrador)
  ├── Usuarios
  ├── Parámetros del Sistema
  └── Auditoría
```

---

## Pantallas implementadas

### Dashboard
Pantalla principal con:
- 4 tarjetas de resumen: Total Activos Fijos, Total Materiales de Control, Procesos Pendientes, Procesos Cerrados este Mes
- Acciones rápidas (visibles solo para Administrador y Operador) — **pendiente: conectar navegación**
- Panel de alertas y notificaciones

### Activos Fijos — Listado
Tabla paginada con filtros por búsqueda de texto (NIA/descripción), categoría, unidad y estado. Acciones por fila: ver detalle y editar (editar solo para Admin/Operador). **Pendiente: conectar botón Editar por fila.**

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

Desde el encabezado se puede generar actas: **ARAF** (Alta), **AIAF** (Inventario), **ATAF** (Transferencia), **ABAF** (Baja), **ADAF** (Descargo). **Pendiente: conectar dropdown Generar Acta y botón Editar.**

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
Similar a Activos Fijos pero para bienes de menor valor que no se deprecian. Misma estructura de tabla y detalle. **Pendiente: agregar botón Registrar Nuevo Material y conectar botón Editar.**

### Procesos Administrativos — Listado
Tabla de procesos con filtros por número, tipo, estado, unidad, rango de fechas y usuario creador.

**Estados:** `Pendiente` (amarillo), `Procesado` (azul), `Cerrado` (verde)

### Procesos Administrativos — Detalle
Vista del proceso con información general, lista de activos involucrados y línea de tiempo de estados. **Pendiente: conectar botón Generar Acta.**

### Baja de Activos
Formulario con selección de bienes por checkboxes, tipo y motivo de baja, documentación de respaldo y resumen. Genera acta ABAF o ABMC según el tipo. Incluye modal de confirmación destructivo.

### Inventarios — Sesiones
Listado de sesiones de inventario con estado y progreso. **Pendiente: conectar botón Nueva Sesión de Inventario.**

### Inventarios — Escaneo QR
Pantalla optimizada para dispositivos móviles/tablets con visor de cámara, barra de progreso y lista de escaneos recientes.

### Depreciación
Panel hub con 4 acciones: Ejecutar Cálculo, Ver Registros, Registrar Revalúo, Ver Asientos Contables. Los últimos tres navegan a sus respectivas pantallas.

### Asientos Contables
Tabla de asientos generados por depreciación, revalúos y bajas. Filas expandibles con líneas Debe/Haber por cuenta contable. Filtros por gestión, mes, tipo y estado.

### Actas — Listado y Vista Previa
Listado de actas generadas con filtros y vista previa en formato A4.

### Reportes — Menú
Grid de tarjetas agrupadas en 3 categorías: Inventario y Valoración, Movimientos y Estado, Institucionales y Normativos. Acceso rápido a Estadísticas. **Pendiente: pasar tipo de reporte seleccionado a ReportView.**

### Reportes — Vista de Reporte
Filtros colapsables + tabla paginada con fila de totales + botones Exportar Excel/PDF.

### Reportes — Estadísticas y Gráficas
4 gráficas SVG: línea (valor por gestión), barras horizontales (por categoría), donut (por fuente de financiamiento), barras verticales (depreciación por gestión).

### Rangos NIA / NIM
Tabla de rangos asignados con barra de consumo (gris → amarilla al 70% → roja al 90%). Formulario de asignación y formulario de transferencia entre funcionarios.

### Gestión de Usuarios
Tabla con filtros por nombre, rol y estado. Toggle activar/desactivar por fila. Formulario con perfiles granulares (Elaboración, Proceso, Firma-Cierre) que aparecen solo para rol Operador.

### Parámetros del Sistema
7 tabs: Unidades (con jerarquía), Secciones, Estructuras Programáticas, Cuentas Contables, Clasificadores Internos, UFV, Responsables.

### Registro de Auditoría
Tabla de eventos del sistema con filtros. Filas expandibles con detalle del cambio (valores antes/después). Solo lectura.

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
├── main.ts                          # Punto de entrada
├── style.css                        # Tailwind v4 + tema shadcn
├── lib/
│   └── utils.ts                     # Helper cn() para clases Tailwind
└── app/
    ├── types.ts                     # Tipos compartidos: UserRole, Screen
    ├── App.vue                      # Controlador de navegación y estado global
    └── components/
        ├── AppLayout.vue            # Layout principal (sidebar + topbar)
        ├── Sidebar.vue              # Menú lateral con control de roles
        ├── LoginScreen.vue          # P-00 Login
        ├── FieldDisplay.vue         # Componente reutilizable para campos de solo lectura
        ├── DashboardContent.vue     # P-01 Dashboard
        ├── FixedAssetsList.vue      # P-02 Listado Activos Fijos
        ├── FixedAssetDetail.vue     # P-03 Ficha Activo Fijo
        ├── FixedAssetForm.vue       # P-04 Formulario Activo Fijo
        ├── ControlMaterialsList.vue # P-06 Listado Materiales de Control
        ├── ControlMaterialDetail.vue# P-07 Ficha Material de Control
        ├── ProcessesList.vue        # P-10 Listado Procesos
        ├── ProcessDetail.vue        # P-11 Detalle Proceso
        ├── NewProcessForm.vue       # P-12 Nuevo Proceso
        ├── TransferForm.vue         # P-14 Transferencia
        ├── ResponsibleChangeForm.vue# P-15 Cambio de Responsable
        ├── AssetWriteOffForm.vue    # P-16 Baja de Activos
        ├── InventorySessionsList.vue# P-17 Sesiones de Inventario
        ├── InventoryScanScreen.vue  # P-18 Escaneo QR
        ├── InventoryResult.vue      # P-19 Resultado Inventario
        ├── DepreciationPanel.vue    # P-20 Panel Depreciación
        ├── DepreciationRecords.vue  # P-22 Registros Depreciación
        ├── RevaluationForm.vue      # P-23 Formulario Revalúo
        ├── AccountingEntries.vue    # P-24 Asientos Contables
        ├── ActasList.vue            # P-25 Listado Actas
        ├── ActaPreview.vue          # P-26 Vista Previa Acta
        ├── ReportsMenu.vue          # P-30 Menú de Reportes
        ├── ReportView.vue           # P-31 Vista de Reporte
        ├── ReportCharts.vue         # P-32 Estadísticas y Gráficas
        ├── IdentifierRangesList.vue # P-33 Rangos NIA/NIM
        ├── IdentifierRangeForm.vue  # P-34 Asignar Rango
        ├── IdentifierRangeTransfer.vue # P-35 Transferir Rango
        ├── UsersList.vue            # P-37 Gestión de Usuarios
        ├── UserForm.vue             # P-38 Formulario de Usuario
        ├── SystemParameters.vue     # P-39 Parámetros del Sistema
        └── AuditLog.vue             # P-40 Registro de Auditoría
```

---

## Notas de diseño

- El prototipo usa **Tailwind CSS v4** para todos los estilos. Los colores principales son grises (`gray-700`, `gray-800`) para elementos de acción y fondo del sidebar.
- Los íconos provienen de **lucide-vue-next**. A diferencia de React, los iconos se pasan como referencias de componente en arrays de datos y se renderizan con `<component :is="item.icon" />`.
- Cada pantalla incluye un bloque de anotaciones en azul (`Wireframe Notes`) que describe el comportamiento esperado. Estos bloques pueden eliminarse cuando las pantallas estén aprobadas.
- La navegación **no usa Vue Router** — está implementada con un `ref<Screen>` en `App.vue` que controla qué pantalla se muestra. Esto es intencional para simplificar el prototipo.
- Los modales de confirmación usan `<Teleport to="body">` para montarse fuera del árbol del componente, equivalente al portal de React.

---

## Pendientes / Gaps de navegación

Estas pantallas **existen** pero tienen botones sin conectar a navegación:

| Componente | Botón / Acción | Corrección requerida |
|---|---|---|
| `FixedAssetsList.vue` | Ícono Editar por fila | Emitir `edit-asset(id)` → navegar a `asset-form` |
| `FixedAssetDetail.vue` | Botón Editar en encabezado | Emitir `edit-asset(id)` → navegar a `asset-form` |
| `FixedAssetDetail.vue` | Dropdown Generar Acta (5 ítems) | Cada ítem debe navegar a `acta-preview` |
| `ControlMaterialsList.vue` | Botón Registrar Nuevo Material | Agregar botón + pantalla P-05 o reutilizar `FixedAssetForm` |
| `ControlMaterialDetail.vue` | Botón Editar en encabezado | Emitir evento → navegar a formulario de material |
| `ControlMaterialDetail.vue` | Dropdown Generar Acta (5 ítems) | Cada ítem debe navegar a `acta-preview` |
| `DashboardContent.vue` | Acción Rápida “Registrar Activo” | Emitir evento → navegar a `asset-form` |
| `DashboardContent.vue` | Acción Rápida “Nuevo Proceso” | Emitir evento → navegar a `new-process` |
| `DashboardContent.vue` | Acción Rápida “Iniciar Inventario” | Emitir evento → navegar a `inventory-sessions` |
| `InventorySessionsList.vue` | Botón Nueva Sesión de Inventario | Emitir evento → flujo de creación |
| `ProcessDetail.vue` | Botón Generar Acta | Navegar a `acta-preview` |
| `ReportsMenu.vue` | Tarjetas de reporte | Pasar prop `reportType` a `ReportView` |
