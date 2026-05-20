# SIGAF — Descripción Completa del Sistema
**Sistema de Gestión de Activos Fijos**  
**Universidad Técnica de Oruro — División de Activos Fijos**

> Este documento integra toda la lógica de negocio analizada y la definición visual de pantallas del sistema. Es la referencia única del proyecto para diseño, desarrollo y validación.
>
> El prototipo interactivo está implementado en **Vue 3 + TypeScript + Vite + Tailwind CSS v4**.

---

## 1. Propósito del sistema

SIGAF administra los bienes institucionales de la universidad durante todo su ciclo de vida. No es un simple inventario: es un sistema de control administrativo formal donde cada cambio sobre un bien queda registrado como un proceso trazable, con documentos de respaldo y auditoría completa.

El sistema controla:
- **Qué bienes existen** y cuáles son sus características
- **Dónde están** ubicados dentro de la universidad
- **Quién responde** por cada bien en cada momento
- **Qué pasó** con cada bien desde que ingresó hasta que se da de baja
- **Qué documentos** respaldan cada operación

**Principio central:** el cambio de estado de un bien no es una edición de datos — es un proceso administrativo trazable con número único, estado, responsable y acta generada.

---

## 2. Tipos de bienes

| Tipo | Descripción | Codificación | Depreciación |
|------|-------------|--------------|--------------|
| **Activo Fijo (AF)** | Bienes patrimoniales de la universidad | Grupos 01–13, formato `[grupo]-[subgrupo]-[letra]NN` ej: `03-01-A01` | Sí — método lineal, en meses, Decreto 24051 |
| **Material de Control (MC)** | Bienes de gasto corriente | Grupo 20, formato `20-[subgrupo]-[letra]NN` ej: `20-01-A01` | No |

Cada bien tiene además un **régimen administrativo**:
- Patrimonial · No patrimonial · En custodia (alquiler, comodato, convenio, préstamo, renta) · En préstamo

Y un **estado operativo**:
- Registrado · Asignado · Transferido · En mantenimiento · Prestado · En custodia · Faltante · Dado de baja · Reincorporado

---

## 3. Estructura organizacional

- **Unidades**: ambientes físicos donde están los bienes (oficinas, laboratorios, aulas). Se organizan en jerarquía (facultad → departamento → oficina).
- **Responsable**: persona que asume la responsabilidad administrativa de uno o varios bienes.
- **Estructura Programática (EP)**: hace referencia a un cargo. Los activos se asignan a responsables vía EP.

---

## 4. Roles del sistema

| Rol | Acceso | Responsabilidades principales |
|-----|--------|-------------------------------|
| **Administrador** | Total | Gestiona usuarios, perfiles, parámetros del sistema |
| **Operador** | Módulos asignados según perfil | Registra bienes, ejecuta movimientos, genera actas |
| **Consulta** | Solo lectura | Visualiza bienes, procesos y reportes |

Perfiles granulares dentro del Operador: **Elaboración** (crea procesos), **Proceso** (procesa), **Firma/Cierre** (cierra el proceso).

---

## 5. Entidades del dominio

### Entidades nucleares

| Entidad | Propósito |
|---------|-----------|
| `Bien` | Objeto central bajo control institucional |
| `TipoBien` | Catálogo: Activo Fijo / Material de Control |
| `RegimenBien` | Catálogo: Patrimonial / Custodia / Préstamo / etc. |
| `EstadoBien` | Estado operativo actual del bien |
| `Responsable` | Persona responsable administrativamente |
| `Unidad` | Dependencia organizacional, con jerarquía |

### Entidades de proceso y trazabilidad

| Entidad | Propósito |
|---------|-----------|
| `ProcesoAdministrativo` | Contenedor formal de cada operación sobre bienes |
| `TipoProceso` | Catálogo de los 26 tipos de proceso |
| `CategoriaProceso` | Agrupa tipos: Alta / Movimiento / Regularización / Baja / Custodia / Incidencias |
| `EstadoProceso` | Pendiente → Procesado → Cerrado |
| `ProcesoAdministrativoDetalle` | Relación proceso ↔ bien con origen/destino |
| `MovimientoBien` | Historial cronológico de todos los cambios del bien |

### Entidades documentales

| Entidad | Propósito |
|---------|-----------|
| `DocumentoRespaldo` | Archivo adjunto o acta generada para un proceso o bien |
| `TipoDocumento` | Catálogo: ARAF, AIAF, ATAF, ABAF, ADAF, ARMC, AIMC, ATMC, ABMC, ADMC, A.Inv.AF, A.Inv.MC |

### Entidades de identificación

| Entidad | Propósito |
|---------|-----------|
| `IdentificadorBien` | NIA (activos) o NIM (materiales) asignado al bien |
| `RangoIdentificadores` | Bloque de números asignado a un inventariador |
| `TransferenciaRangoIdentificadores` | Historial de traspasos de rangos entre funcionarios |

---

## 6. Los 26 tipos de proceso

### Alta o incorporación
1. Compra de bienes y/o materiales
2. Donación de proyectos externos
3. Donación institución
4. Donación terceros
5. Registro bienes en custodia — alquiler
6. Registro bienes en custodia — comodato
7. Registro bienes en custodia — convenio
8. Registro bienes en custodia — préstamo
9. Registro bienes en custodia — renta
10. Reposición

### Movimiento o reasignación
11. Cambio de responsable
12. Cambio de responsable (no patrimoniales)
13. Transferencia
14. Transferencia (no patrimoniales)
15. Traspaso bienes en custodia

### Regularización o actualización
16. Actualización · 17. Ajuste · 18. Mantenimiento · 19. Reincorporación

### Baja o salida
20. Baja de activos · 21. Baja de activos faltantes · 22. Baja de materiales · 23. Baja de materiales faltantes

### Incidencias
24. Faltantes

### Préstamo y custodia
25. Préstamo dentro de la institución · 26. Préstamo fuera de la institución

---

## 7. Tipos de actas generadas por el sistema

| Sigla | Nombre completo | Se genera en |
|-------|-----------------|--------------|
| ARAF | Acta de Recepción de Activo Fijo | Alta AF |
| AIAF | Acta de Incremento de Activo Fijo | Alta AF |
| ATAF | Acta de Transferencia de Activo Fijo | Movimiento AF |
| ABAF | Acta de Baja de Activo Fijo | Baja AF |
| ADAF | Acta de Desincorporación de Activo Fijo | Desincorporación AF |
| A.Inv.AF | Acta de Inventario de Activo Fijo | Inventario AF |
| ARMC | Acta de Recepción de Material de Control | Alta MC |
| AIMC | Acta de Incremento de Material de Control | Alta MC |
| ATMC | Acta de Transferencia de Material de Control | Movimiento MC |
| ABMC | Acta de Baja de Material de Control | Baja MC |
| ADMC | Acta de Desincorporación de Material de Control | Desincorporación MC |
| A.Inv.MC | Acta de Inventario de Material de Control | Inventario MC |

---

## 8. Reglas de negocio críticas

| # | Regla |
|---|-------|
| RN-01 | Un proceso cerrado no puede modificarse. Solo el Técnico Contable puede reabrirlo. |
| RN-02 | El tipo de proceso solo puede cambiarse si aún no fue procesado ni cerrado. |
| RN-03 | Un proceso puede afectar varios bienes; la selección puede ser parcial (por checkboxes). |
| RN-04 | Una transferencia implica cambio de unidad Y de responsable. |
| RN-05 | El cambio de responsable puede hacerse sin cambiar de unidad. |
| RN-06 | Los activos fijos con valor > 50.000 Bs. deben tener documentación digitalizada para reportes SENAPE. |
| RN-07 | Cada activo debe tener al menos una fotografía asociada. |
| RN-08 | La depreciación es lineal, calculada en meses, según el Decreto 24051. |
| RN-09 | Un revalúo resetea la vida útil y la depreciación desde la fecha del revalúo. |
| RN-10 | Los rangos NIA/NIM se consumen de menor a mayor. |
| RN-11 | El inventariador solo puede ver los registros que él mismo capturó. |
| RN-12 | Toda acta registra fecha/hora y usuario que la generó, impreso en el pie del documento. |
| RN-13 | Todo cambio sobre un bien o proceso queda en el historial de auditoría. |

---

## 9. Pantallas implementadas en el prototipo Vue

El sistema usa un **layout de aplicación web de escritorio** (mínimo 1280px) con:
- **Sidebar izquierdo fijo** (~240px): navegación por módulos, agrupada. Varía según rol.
- **Topbar superior fijo**: breadcrumb a la izquierda · nombre de usuario + badge de rol + notificaciones + cerrar sesión a la derecha.
- **Área de contenido central**: scrollable, con encabezado de página (título + botón de acción principal).

### Pantallas implementadas (35 de 41)

| Pantalla | Componente Vue | Estado |
|---|---|---|
| P-00 Login | `LoginScreen.vue` | ✅ |
| P-01 Dashboard | `DashboardContent.vue` | ✅ |
| P-02 Listado Activos Fijos | `FixedAssetsList.vue` | ✅ |
| P-03 Ficha Activo Fijo | `FixedAssetDetail.vue` | ✅ |
| P-04 Formulario Activo Fijo | `FixedAssetForm.vue` | ✅ |
| P-06 Listado Materiales de Control | `ControlMaterialsList.vue` | ✅ |
| P-07 Ficha Material de Control | `ControlMaterialDetail.vue` | ✅ |
| P-10 Listado Procesos | `ProcessesList.vue` | ✅ |
| P-11 Detalle Proceso | `ProcessDetail.vue` | ✅ |
| P-12 Nuevo Proceso | `NewProcessForm.vue` | ✅ |
| P-14 Transferencia | `TransferForm.vue` | ✅ |
| P-15 Cambio de Responsable | `ResponsibleChangeForm.vue` | ✅ |
| P-16 Baja de Activos | `AssetWriteOffForm.vue` | ✅ |
| P-17 Sesiones de Inventario | `InventorySessionsList.vue` | ✅ |
| P-18 Escaneo QR | `InventoryScanScreen.vue` | ✅ |
| P-19 Resultado Inventario | `InventoryResult.vue` | ✅ |
| P-20 Panel Depreciación | `DepreciationPanel.vue` | ✅ |
| P-22 Registros Depreciación | `DepreciationRecords.vue` | ✅ |
| P-23 Formulario Revalúo | `RevaluationForm.vue` | ✅ |
| P-24 Asientos Contables | `AccountingEntries.vue` | ✅ |
| P-25 Listado Actas | `ActasList.vue` | ✅ |
| P-26 Vista Previa Acta | `ActaPreview.vue` | ✅ |
| P-30 Menú de Reportes | `ReportsMenu.vue` | ✅ |
| P-31 Vista de Reporte | `ReportView.vue` | ✅ |
| P-32 Estadísticas y Gráficas | `ReportCharts.vue` | ✅ |
| P-33 Rangos NIA/NIM | `IdentifierRangesList.vue` | ✅ |
| P-34 Asignar Rango | `IdentifierRangeForm.vue` | ✅ |
| P-35 Transferir Rango | `IdentifierRangeTransfer.vue` | ✅ |
| P-37 Gestión de Usuarios | `UsersList.vue` | ✅ |
| P-38 Formulario de Usuario | `UserForm.vue` | ✅ |
| P-39 Parámetros del Sistema | `SystemParameters.vue` | ✅ |
| P-40 Registro de Auditoría | `AuditLog.vue` | ✅ |

### Pantallas pendientes de implementar

| ID | Pantalla | Estado |
|---|---|---|
| P-05 | Formulario Material de Control | Sin pantalla — no existe botón activo que la requiera aún |

### Gaps de navegación pendientes de corregir

Estas pantallas **existen** pero tienen botones sin conectar:

| Componente | Botón / Acción | Pendiente |
|---|---|---|
| `FixedAssetsList.vue` | Ícono Editar por fila | Debe navegar a `asset-form` con el ID del activo |
| `FixedAssetDetail.vue` | Botón Editar en encabezado | Debe navegar a `asset-form` con el ID del activo |
| `ControlMaterialsList.vue` | Botón Registrar Nuevo Material | Falta el botón y la pantalla P-05 |
| `ControlMaterialDetail.vue` | Botón Editar en encabezado | Debe navegar a formulario de material |
| `DashboardContent.vue` | Acciones Rápidas (3 botones) | Deben navegar a `asset-form`, `new-process`, `inventory-sessions` |
| `InventorySessionsList.vue` | Botón Nueva Sesión de Inventario | Debe emitir evento y navegar |
| `ProcessDetail.vue` | Botón Generar Acta | Debe navegar a `acta-preview` |
| `ProcessDetail.vue` | Botón Agregar Bienes | Sin flujo definido |
| `FixedAssetDetail.vue` | Dropdown Generar Acta (5 ítems) | Cada ítem debe navegar a `acta-preview` |
| `ControlMaterialDetail.vue` | Dropdown Generar Acta (5 ítems) | Cada ítem debe navegar a `acta-preview` |
| `ReportsMenu.vue` | Tarjetas de reporte | Deben pasar el tipo de reporte seleccionado a `ReportView` |

---

## 10. Requerimientos funcionales por módulo

### Módulo 1 — Gestión de bienes
- RF-001 Registrar bien con tipo, régimen, unidad, responsable e identificadores
- RF-002 Editar bien con auditoría de cambios
- RF-003 Consultar ficha completa: estado, responsable, unidad, identificadores, documentos, historial
- RF-004 Clasificar por tipo, régimen y catálogos

### Módulo 4 — Procesos administrativos
- RF-017 Crear proceso con tipo, unidad y bienes
- RF-018 Asociar múltiples bienes a un proceso
- RF-019 Selección parcial de bienes dentro de un proceso
- RF-021 Consultar detalle completo del proceso
- RF-022 Cerrar proceso
- RF-023 Bloquear modificaciones tras cierre

### Módulo 5 — Movimientos
- RF-026 Cambio de responsable (sin cambio de unidad)
- RF-027 Transferencia entre unidades
- RF-028 Transferencia con actualización de responsable

### Módulo 9 — Documentos y actas
- RF-050 Adjuntar documentos a procesos
- RF-051 Adjuntar documentos a bienes
- RF-052 Generar formularios/actas según tipo de proceso

---

## 11. Datos de ejemplo para el prototipo

**Activos:**
- NIA: AF-2024-0001 | Código: 03-01-A01 | Computadora Portátil HP ProBook 450 | Equipos de Computación | Administración Central | Juan Pérez Mamani | Activo
- NIA: AF-2024-0002 | Código: 03-02-M15 | Escritorio Ejecutivo de Madera | Muebles | Rectorado | María González Quispe | Activo
- NIA: AF-2023-0145 | Código: 03-03-V02 | Vehículo Toyota Hilux 4x4 | Vehículos | Transporte | Carlos Mamani López | Activo

**Proceso:**
- N° TRANS-2024-001 | Transferencia entre Unidades | Administración Central | 15/03/2024 | Pendiente | Juan Pérez Mamani

**Actas:** ARAF-2024-001 · ATAF-2024-012 · ABAF-2024-007
