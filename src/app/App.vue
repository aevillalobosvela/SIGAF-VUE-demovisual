<template>
  <div class="size-full bg-gray-100">
    <LoginScreen v-if="currentScreen === 'login'" @login="handleLogin" />
    <AppLayout
      v-else
      :userRole="userRole"
      :currentScreen="currentScreen"
      @logout="handleLogout"
      @navigate="handleNavigate"
    >
      <!-- Dashboard -->
      <DashboardContent
        v-if="currentScreen === 'dashboard'"
        :userRole="userRole"
        @navigate="handleNavigate"
      />

      <!-- Activos Fijos -->
      <FixedAssetsList
        v-else-if="currentScreen === 'fixed-assets'"
        :userRole="userRole"
        @view-asset="handleViewAsset"
        @new-asset="handleNewAsset"
        @edit-asset="handleEditAsset"
      />
      <FixedAssetDetail
        v-else-if="currentScreen === 'asset-detail'"
        :userRole="userRole"
        :assetId="selectedAssetId"
        @back="handleNavigate('fixed-assets')"
      />
      <FixedAssetForm
        v-else-if="currentScreen === 'asset-form'"
        :userRole="userRole"
        :assetId="selectedAssetId"
        @cancel="handleNavigate('fixed-assets')"
      />

      <!-- Materiales de Control -->
      <ControlMaterialsList
        v-else-if="currentScreen === 'control-materials'"
        :userRole="userRole"
        @view-material="handleViewMaterial"
        @new-material="handleNewMaterial"
        @edit-material="handleEditMaterial"
      />
      <ControlMaterialDetail
        v-else-if="currentScreen === 'material-detail'"
        :userRole="userRole"
        :materialId="selectedMaterialId"
        @back="handleNavigate('control-materials')"
      />

      <!-- Procesos -->
      <ProcessesList
        v-else-if="currentScreen === 'processes'"
        :userRole="userRole"
        @view-process="handleViewProcess"
        @new-process="handleNewProcess"
      />
      <ProcessDetail
        v-else-if="currentScreen === 'process-detail'"
        :userRole="userRole"
        :processId="selectedProcessId"
        @back="handleNavigate('processes')"
      />
      <NewProcessForm
        v-else-if="currentScreen === 'new-process'"
        :userRole="userRole"
        @cancel="handleNavigate('processes')"
      />

      <!-- Movimientos -->
      <TransferForm
        v-else-if="currentScreen === 'transfer'"
        :userRole="userRole"
        @cancel="handleNavigate('processes')"
      />
      <ResponsibleChangeForm
        v-else-if="currentScreen === 'responsible-change'"
        :userRole="userRole"
        @cancel="handleNavigate('processes')"
      />

      <!-- Inventario -->
      <InventorySessionsList
        v-else-if="currentScreen === 'inventory-sessions'"
        :userRole="userRole"
        @view-inventory="handleViewInventory"
        @new-inventory="handleNavigate('inventory-scan')"
      />
      <InventoryScanScreen
        v-else-if="currentScreen === 'inventory-scan'"
        :inventoryId="selectedInventoryId"
        @back="handleNavigate('inventory-sessions')"
      />
      <InventoryResult
        v-else-if="currentScreen === 'inventory-result'"
        :inventoryId="selectedInventoryId"
        @back="handleNavigate('inventory-sessions')"
      />

      <!-- Depreciación -->
      <DepreciationPanel
        v-else-if="currentScreen === 'depreciation'"
        :userRole="userRole"
        @navigate="handleNavigate"
      />
      <DepreciationRecords
        v-else-if="currentScreen === 'depreciation-records'"
        :userRole="userRole"
      />
      <RevaluationForm
        v-else-if="currentScreen === 'revaluation'"
        :userRole="userRole"
        @cancel="handleNavigate('depreciation')"
      />

      <!-- Actas -->
      <ActasList
        v-else-if="currentScreen === 'actas'"
        :userRole="userRole"
        @view-acta="handleViewActa"
      />
      <ActaPreview
        v-else-if="currentScreen === 'acta-preview'"
        :actaId="selectedActaId"
        @back="handleNavigate('actas')"
      />

      <!-- Configuración -->
      <UsersList
        v-else-if="currentScreen === 'users'"
        :userRole="userRole"
        @new-user="handleNewUser"
        @edit-user="handleViewUser"
      />
      <UserForm
        v-else-if="currentScreen === 'user-form'"
        :userRole="userRole"
        :userId="selectedUserId"
        @cancel="handleNavigate('users')"
        @save="handleNavigate('users')"
      />
      <SystemParameters
        v-else-if="currentScreen === 'system-parameters'"
        :userRole="userRole"
      />
      <AuditLog
        v-else-if="currentScreen === 'audit-log'"
        :userRole="userRole"
      />

      <!-- Reportes -->
      <ReportsMenu
        v-else-if="currentScreen === 'reports'"
        :userRole="userRole"
        @navigate="handleNavigate"
      />
      <ReportView
        v-else-if="currentScreen === 'report-view'"
        :userRole="userRole"
        @back="handleNavigate('reports')"
      />
      <ReportCharts
        v-else-if="currentScreen === 'report-charts'"
        :userRole="userRole"
        @back="handleNavigate('reports')"
      />

      <!-- Bajas -->
      <AssetWriteOffForm
        v-else-if="currentScreen === 'write-off'"
        :userRole="userRole"
        @cancel="handleNavigate('processes')"
      />

      <!-- Rangos NIA/NIM -->
      <IdentifierRangesList
        v-else-if="currentScreen === 'identifier-ranges'"
        :userRole="userRole"
        @new-range="handleNavigate('identifier-range-form')"
        @transfer-range="handleTransferRange"
      />
      <IdentifierRangeForm
        v-else-if="currentScreen === 'identifier-range-form'"
        :userRole="userRole"
        @cancel="handleNavigate('identifier-ranges')"
        @save="handleNavigate('identifier-ranges')"
      />
      <IdentifierRangeTransfer
        v-else-if="currentScreen === 'identifier-range-transfer'"
        :userRole="userRole"
        @cancel="handleNavigate('identifier-ranges')"
        @save="handleNavigate('identifier-ranges')"
      />

      <!-- Asientos Contables -->
      <AccountingEntries
        v-else-if="currentScreen === 'accounting-entries'"
        :userRole="userRole"
        @back="handleNavigate('depreciation')"
      />

      <!-- Fallback -->
      <DashboardContent v-else :userRole="userRole" />
    </AppLayout>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import type { UserRole, Screen } from './types'

import LoginScreen from './components/LoginScreen.vue'
import AppLayout from './components/AppLayout.vue'
import DashboardContent from './components/DashboardContent.vue'
import FixedAssetsList from './components/FixedAssetsList.vue'
import FixedAssetDetail from './components/FixedAssetDetail.vue'
import FixedAssetForm from './components/FixedAssetForm.vue'
import ControlMaterialsList from './components/ControlMaterialsList.vue'
import ControlMaterialDetail from './components/ControlMaterialDetail.vue'
import ProcessesList from './components/ProcessesList.vue'
import ProcessDetail from './components/ProcessDetail.vue'
import NewProcessForm from './components/NewProcessForm.vue'
import TransferForm from './components/TransferForm.vue'
import ResponsibleChangeForm from './components/ResponsibleChangeForm.vue'
import InventorySessionsList from './components/InventorySessionsList.vue'
import InventoryScanScreen from './components/InventoryScanScreen.vue'
import InventoryResult from './components/InventoryResult.vue'
import DepreciationPanel from './components/DepreciationPanel.vue'
import DepreciationRecords from './components/DepreciationRecords.vue'
import RevaluationForm from './components/RevaluationForm.vue'
import ActasList from './components/ActasList.vue'
import ActaPreview from './components/ActaPreview.vue'
import UsersList from './components/UsersList.vue'
import UserForm from './components/UserForm.vue'
import SystemParameters from './components/SystemParameters.vue'
import AuditLog from './components/AuditLog.vue'
import ReportsMenu from './components/ReportsMenu.vue'
import ReportView from './components/ReportView.vue'
import ReportCharts from './components/ReportCharts.vue'
import AssetWriteOffForm from './components/AssetWriteOffForm.vue'
import IdentifierRangesList from './components/IdentifierRangesList.vue'
import IdentifierRangeForm from './components/IdentifierRangeForm.vue'
import IdentifierRangeTransfer from './components/IdentifierRangeTransfer.vue'
import AccountingEntries from './components/AccountingEntries.vue'

// ── Estado de navegación ──────────────────────────────────────────────────────
const currentScreen = ref<Screen>('login')
const userRole = ref<UserRole>('Administrador')

const selectedAssetId = ref<string | null>(null)
const selectedMaterialId = ref<string | null>(null)
const selectedProcessId = ref<string | null>(null)
const selectedInventoryId = ref<string | null>(null)
const selectedActaId = ref<string | null>(null)
const selectedUserId = ref<string | null>(null)
const selectedRangeId = ref<string | null>(null)

// ── Handlers ──────────────────────────────────────────────────────────────────
function handleLogin(role: UserRole) {
  userRole.value = role
  currentScreen.value = 'dashboard'
}

function handleLogout() {
  currentScreen.value = 'login'
  selectedAssetId.value = null
  selectedMaterialId.value = null
  selectedProcessId.value = null
  selectedInventoryId.value = null
  selectedActaId.value = null
}

function handleNavigate(screen: Screen, assetId?: string) {
  currentScreen.value = screen
  if (assetId) selectedAssetId.value = assetId
}

function handleViewAsset(assetId: string) {
  selectedAssetId.value = assetId
  currentScreen.value = 'asset-detail'
}

function handleNewAsset() {
  selectedAssetId.value = null
  currentScreen.value = 'asset-form'
}

function handleEditAsset(assetId: string) {
  selectedAssetId.value = assetId
  currentScreen.value = 'asset-form'
}

function handleNewMaterial() {
  selectedMaterialId.value = null
  currentScreen.value = 'control-materials'
}

function handleEditMaterial(materialId: string) {
  selectedMaterialId.value = materialId
  currentScreen.value = 'control-materials'
}

function handleViewMaterial(materialId: string) {
  selectedMaterialId.value = materialId
  currentScreen.value = 'material-detail'
}

function handleViewProcess(processId: string) {
  selectedProcessId.value = processId
  currentScreen.value = 'process-detail'
}

function handleNewProcess() {
  currentScreen.value = 'new-process'
}

function handleViewInventory(inventoryId: string, tab: 'scan' | 'result') {
  selectedInventoryId.value = inventoryId
  currentScreen.value = tab === 'scan' ? 'inventory-scan' : 'inventory-result'
}

function handleViewActa(actaId: string) {
  selectedActaId.value = actaId
  currentScreen.value = 'acta-preview'
}

function handleViewUser(userId: string) {
  selectedUserId.value = userId
  currentScreen.value = 'user-form'
}

function handleNewUser() {
  selectedUserId.value = null
  currentScreen.value = 'user-form'
}

function handleTransferRange(rangeId: string) {
  selectedRangeId.value = rangeId
  currentScreen.value = 'identifier-range-transfer'
}
</script>
