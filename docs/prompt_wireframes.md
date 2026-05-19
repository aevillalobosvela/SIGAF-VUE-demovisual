# Prompt para Generación de Wireframes — SIGAF

> **Nota:** Este documento es agnóstico al framework. El prototipo interactivo que implementa estas pantallas está desarrollado en **Vue 3 + TypeScript + Vite + Tailwind CSS v4**. Los wireframes descritos aquí son la especificación visual de referencia.

---

## PROMPT PRINCIPAL

Design a complete set of low-fidelity wireframes for **SIGAF**, a Fixed Assets Management System for a Bolivian public university (Universidad Técnica de Oruro). The system is a desktop-first web application used by administrative staff to register, track, and manage institutional assets throughout their full lifecycle.

---

## APPLICATION LAYOUT

The app uses a persistent shell layout:
- **Left sidebar** (fixed, ~240px wide): vertical navigation menu grouped by module. Menu items visible depend on the user's role. Includes system logo at top and logged-in user info at bottom.
- **Top bar** (fixed, full width): breadcrumb navigation on the left, user name + active role badge + notifications bell + logout button on the right.
- **Main content area**: scrollable central zone where all screens render. Starts with a page header (title + primary action button).

Roles that affect visible menu items: **Administrador** (full access), **Operador** (assigned modules only), **Consulta** (read-only).

---

## SCREENS TO WIREFRAME

### SCREEN 1 — Login
Full-page centered layout, no sidebar. Contains: system logo, "SIGAF" title, username input, password input, "Ingresar" (Login) button, error message area below the button.

---

### SCREEN 2 — Dashboard
Post-login home screen. Contains:
- 4 summary stat cards in a row: "Total Activos Fijos" / "Total Materiales de Control" / "Procesos Pendientes" / "Procesos Cerrados este Mes" — each card shows a large number and an icon.
- Quick action buttons below cards (varies by role): "Registrar Activo", "Nuevo Proceso", "Iniciar Inventario".
- Alerts/notifications panel: list of pending tasks (unclosed processes, assigned work orders).

---

### SCREEN 3 — Fixed Assets List (Activos Fijos)
Full data table screen. Contains:
- Filter bar at top: text search (by NIA code or description), dropdowns for Category, Unit, Responsible person, and Status (Activo / Baja / Transferido / Faltante).
- "Registrar Nuevo Activo" primary button (top right).
- Paginated data table with columns: NIA | Código | Descripción | Categoría | Unidad | Responsable | Estado (colored badge) | Acciones (Ver / Editar icons).
- Pagination controls at bottom.

---

### SCREEN 4 — Fixed Asset Detail (Ficha de Activo)
Detail view with 7 tabs:
1. **Datos Generales**: two-column read-only fields — NIA, Código, Descripción, Categoría contable, Subcuenta, Costo de compra (Bs.), Fecha de adquisición, Fuente de financiamiento, Estado, Régimen.
2. **Responsable y Ubicación**: Responsable actual (name + EP/cargo), Unidad actual, history of previous assignments as a small table.
3. **Fotografías**: image gallery grid, "Agregar Foto" button, each photo has a delete icon.
4. **Documentos**: file list with upload button. Warning banner if asset value >50,000 Bs. and no documents attached.
5. **Historial de Movimientos**: vertical timeline — date, movement type, origin → destination, linked process number.
6. **Depreciación**: table — Gestión | Dep. Período | Dep. Acumulada | Vida Útil Restante (months) | Revalúo. Note: "Método lineal — Decreto 24051".
7. **Código QR**: centered QR code image, NIA below it, "Imprimir Etiqueta" button.

Action buttons in page header: "Editar" and "Generar Acta" (dropdown: ARAF, AIAF, ATAF, ABAF, ADAF).

---

### SCREEN 5 — New / Edit Fixed Asset Form
Two-column form layout. Fields: Descripción (full width), Categoría contable, Subcuenta (dependent on category), Costo de compra, Fecha de adquisición, Fuente de financiamiento, Unidad, Responsable/EP, Estado inicial, Régimen, Foto del activo (upload with preview), Documentos adjuntos (required if cost >50,000 Bs.).

Footer: "Guardar" (primary) and "Cancelar" (secondary) buttons.

---

### SCREEN 6 — Control Materials List (Materiales de Control)
Same layout as Screen 3 but for control materials. Key differences: NIM instead of NIA, Group 20 category codes, no depreciation columns, button "Registrar Nuevo Material".

---

### SCREEN 7 — Control Material Detail
Same tab structure as Screen 4 but with only 6 tabs (no Depreciación tab). Acta types: ARMC, AIMC, ATMC, ABMC, ADMC.

---

### SCREEN 8 — Administrative Processes List (Procesos Administrativos)
Filter bar: process number, process type (26 types grouped by category), status, unit, date range, created-by user.
Table: N° Proceso | Tipo | Unidad | Fecha | Estado (badge) | Creado por | Acciones.
Status badge colors: Pendiente = yellow, Procesado = blue, Cerrado = green.

---

### SCREEN 9 — Process Detail (Detalle de Proceso)
Four-section layout:
1. Header info with process number, type, unit, date, status badge, action buttons (Agregar Bienes, Generar Acta, Cerrar Proceso, Reabrir).
2. Associated Assets table: NIA/NIM | Descripción | Responsable Origen/Destino | Unidad Origen/Destino | Fecha Efectiva.
3. Attached Documents list with "Adjuntar Documento" button.
4. Status Timeline stepper: Pendiente → Procesado → Cerrado with dates and users.

---

### SCREEN 10 — New Process Form
Tipo de proceso (grouped select), Unidad de referencia, Observaciones, Asset selection table with checkboxes.
Footer: "Crear Proceso" and "Cancelar".

---

### SCREEN 11 — Transfer Form (Transferencia)
Proceso asociado, Unidad Origen/Destino, Responsable Origen (auto-filled), Responsable Destino, Asset selection table, Observaciones.
Footer: "Registrar Transferencia" and "Cancelar".

---

### SCREEN 12 — Responsible Change Form (Cambio de Responsable)
Proceso asociado, Unidad (no change), Responsable Actual (read-only), Nuevo Responsable, Asset selection table, Observaciones.
Footer: "Registrar Cambio" and "Cancelar".

---

### SCREEN 13 — Inventory Sessions List (Inventario Físico)
Table: Fecha | Unidad | Estado (En curso / Finalizado) | Bienes Escaneados (with progress bar) | Faltantes | Acciones.
"Nueva Sesión de Inventario" button.

---

### SCREEN 14 — QR Scan Screen (Escaneo de Inventario)
Mobile/tablet-optimized portrait layout:
- Large camera viewfinder (top 60%) with QR scanning overlay frame.
- Progress indicator: "Escaneados: 47 / 120".
- Recent scans list (bottom 30%): NIA, description, status icon (✓ / ✗).
- "Finalizar Sesión" button at bottom.

---

### SCREEN 15 — Inventory Result (Resultado de Inventario)
3 large stat cards: "Encontrados" (green) / "Faltantes" (red) / "Sobrantes" (orange).
Tables: Bienes Faltantes and Bienes Encontrados side by side.
Buttons: "Generar Acta A.Inv.AF" and "Generar Acta A.Inv.MC".

---

### SCREEN 16 — Depreciation Panel
Hub screen with 4 action cards: Ejecutar Cálculo (blue), Ver Registros (green), Registrar Revalúo (orange), Ver Asientos Contables (purple).

---

### SCREEN 17 — Depreciation Records Table
Filter bar: NIA, year (gestión), category.
Table: NIA | Descripción | Gestión | Dep. Período (Bs.) | Dep. Acumulada (Bs.) | Vida Útil Restante (months) | N° Revalúos.

---

### SCREEN 18 — Revaluation Form (Revalúo)
Activo (search by NIA with dropdown), Fecha del revalúo, Nuevo valor en libros, Nueva vida útil (months), Documento de respaldo (upload), Observaciones.
Footer: "Registrar Revalúo" and "Cancelar".

---

### SCREEN 19 — Actas List
Filter bar: acta type (12 types), process number, date range, generated-by user.
Table: Código | Tipo | N° Proceso | Fecha | Generada por | Acciones (Ver / Descargar PDF).

---

### SCREEN 20 — Acta Preview
Document-style layout (A4 proportions):
- Institutional header (university logo, division name, acta type title).
- Body: process data and assets table.
- Footer: generation date/time, generated-by user, signature lines.
- "Imprimir" and "Descargar PDF" buttons outside the document area.

---

### SCREEN 21 — Reports Menu
Grid of report type cards (3 columns): Valor Total de Activos, Activos por Depreciar, Activos por Tipo/Cuenta/Unidad, Activos Faltantes, Reporte SENAPE, Reporte SIPAP, Balance General, Historial Individual, Estadísticas por Gestión.

---

### SCREEN 22 — Report with Filters and Table
Collapsible filter panel (date range, unit, category, funding source, responsible, status) + paginated results table + "Exportar Excel" / "Exportar PDF" buttons.

---

### SCREEN 23 — Statistics / Charts
4 chart areas: line (asset value by year), pie/donut (by category), bar (by funding source), line (accumulated depreciation by year).

---

### SCREEN 24 — NIA/NIM Range Management
Table: Inventariador | Tipo | Desde | Hasta | Actual | Disponibles | Estado | Acciones.
"Asignar Nuevo Rango" button.

---

### SCREEN 25 — Assign Range Form
Fields: Tipo (NIA / NIM radio), Inventariador, Número Desde, Número Hasta, Observaciones.

---

### SCREEN 26 — Transfer Range Form
Rango origen (read-only card), Números a transferir (Desde/Hasta), Funcionario destino, Observaciones.

---

### SCREEN 27 — User Management (Admin)
Table: Usuario | Nombre completo | Rol (badge) | Estado | Último acceso | Acciones.
"Nuevo Usuario" button.

---

### SCREEN 28 — User Form
Fields: Nombre completo, Usuario, Contraseña, Confirmar contraseña, Rol, Perfiles (checkboxes: Elaboración / Proceso / Firma-Cierre), Estado (toggle).

---

### SCREEN 29 — System Parameters (Parámetros)
7 tabs: Unidades (hierarchy), Secciones, Estructuras Programáticas, Cuentas Contables, Clasificadores Internos, UFV, Responsables.

---

### SCREEN 30 — Audit Log
Filter bar: user, action type, module, date range.
Table: Fecha/Hora | Usuario | Módulo | Acción | Detalle (expandable row).

---

## DESIGN GUIDELINES FOR WIREFRAMES

- **Style**: low-fidelity wireframes, grayscale only, no color except for status badges.
- **Density**: medium-density UI — desktop monitors (1280px+ width minimum).
- **Typography**: all labels and button text in Spanish.
- **Tables**: show at least 5-6 rows of placeholder data.
- **Status badges**: rounded pill shapes — Pendiente, Procesado, Cerrado for processes; Activo, Baja, Transferido, Faltante for assets.
- **Modals/dialogs**: include at least one confirmation dialog (for "Cerrar Proceso").
- **Sidebar navigation**: show all module groups labeled.

---

## REALISTIC PLACEHOLDER DATA

**Sample assets:**
- NIA: 2024-001 | Código: 03-01-A01 | Armario metálico de 4 puertas | Muebles y Enseres | Secretaría Rectorado | Lic. María Flores | Activo
- NIA: 2024-002 | Código: 04-02-C01 | Computadora de escritorio HP | Equipos de Computación | Dirección Académica | Ing. Carlos Mamani | Activo
- NIA: 2023-089 | Código: 05-01-V01 | Vagoneta Toyota Hilux | Vehículos | Rectorado | Dr. Juan Quispe | Activo

**Sample process:**
- N° 4498 | Cambio de Responsable | Facultad de Derecho | 12/03/2026 | Cerrado | Inv. Pedro Condori

**Sample actas:** ARAF-2026-001, ATAF-2026-045, ABAF-2026-012

**Sample NIA/NIM ranges:**
- Pedro Condori | NIA | Desde: 1001 | Hasta: 1100 | Actual: 1067 | Disponibles: 33

---

## NAVIGATION FLOW

- Login → Dashboard
- Dashboard → Fixed Assets List
- Fixed Assets List → Fixed Asset Detail (click row)
- Fixed Assets List → New Asset Form (click button)
- Fixed Asset Detail → Edit Form (click Editar)
- Processes List → Process Detail (click row)
- Processes List → New Process Form (click button)
- Process Detail → Transfer Form
- Process Detail → Responsible Change Form
- Inventory Sessions → QR Scan Screen → Inventory Result
- Reports Menu → Report with Filters Table
