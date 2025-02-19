<script setup lang="ts">
import { DiskIOMode, DiskIOType, UploadChokingAlgorithm, UploadSlotsBehavior, UtpTcpMixedMode } from '@/constants/qbit/AppPreferences'
import { qbit } from '@/services'
import { usePreferenceStore } from '@/stores/preferences'
import { onBeforeMount, ref } from 'vue'
import { useI18n } from 'vue-i18n'

const { t } = useI18n()
const preferenceStore = usePreferenceStore()

const networkInterfaceOptions = ref([{ title: t('settings.advanced.qbittorrent.networking.networkInterfaces.any'), value: '' }])
const ipAddressesOptions = ref([
  { title: t('settings.advanced.qbittorrent.networking.ipAddress.all'), value: '' },
  { title: t('settings.advanced.qbittorrent.networking.ipAddress.allIPv4'), value: '0.0.0.0' },
  { title: t('settings.advanced.qbittorrent.networking.ipAddress.allIPv6'), value: '::' }
])
const diskIoTypeOptions = ref([
  { title: t('constants.diskIoType.default'), value: DiskIOType.DEFAULT },
  { title: t('constants.diskIoType.memoryMappedFiles'), value: DiskIOType.MEMORY_MAPPED_FILES },
  { title: t('constants.diskIoType.posixCompliant'), value: DiskIOType.POSIX_COMPLIANT }
])
const diskIoModeReadOptions = ref([
  { title: t('constants.diskIoMode.disableOsCache'), value: DiskIOMode.DISABLE_OS_CACHE },
  { title: t('constants.diskIoMode.enableOsCache'), value: DiskIOMode.ENABLE_OS_CACHE }
])
const diskIoModeWriteOptions = ref([
  { title: t('constants.diskIoMode.disableOsCache'), value: DiskIOMode.DISABLE_OS_CACHE },
  { title: t('constants.diskIoMode.enableOsCache'), value: DiskIOMode.ENABLE_OS_CACHE },
  { title: t('constants.diskIoMode.writeThrough'), value: DiskIOMode.WRITE_THROUGH }
])
const utpTcpMixedModeOptions = ref([
  { title: t('constants.utpTcpMixedMode.preferTcp'), value: UtpTcpMixedMode.PREFER_TCP },
  { title: t('constants.utpTcpMixedMode.peerProportional'), value: UtpTcpMixedMode.PEER_PROPORTIONAL }
])
const uploadSlotsBehaviorOptions = ref([
  { title: t('constants.uploadSlotsBehavior.fixedSlots'), value: UploadSlotsBehavior.FIXED_SLOTS },
  { title: t('constants.uploadSlotsBehavior.uploadRateBased'), value: UploadSlotsBehavior.UPLOAD_RATE_BASED }
])
const uploadChokingAlgorithmOptions = ref([
  { title: t('constants.uploadChokingAlgorithm.roundRobin'), value: UploadChokingAlgorithm.ROUND_ROBIN },
  { title: t('constants.uploadChokingAlgorithm.fastestUpload'), value: UploadChokingAlgorithm.FASTEST_UPLOAD },
  { title: t('constants.uploadChokingAlgorithm.antiLeech'), value: UploadChokingAlgorithm.ANTI_LEECH }
])

onBeforeMount(async () => {
  const networkInterfaces = await qbit.getNetworkInterfaces()
  for (const networkInterface of networkInterfaces) {
    networkInterfaceOptions.value.push({ title: networkInterface.name, value: networkInterface.value })
  }

  const ipAddresses = await qbit.getAddresses(preferenceStore.preferences!.current_network_interface)
  for (const ipAddress of ipAddresses) {
    ipAddressesOptions.value.push({ title: ipAddress, value: ipAddress })
  }
})
</script>

<template>
  <v-list>
    <v-list-subheader>
      {{ t('settings.advanced.qbittorrent.subheader') }} (<a
        href="https://github.com/qbittorrent/qBittorrent/wiki/Explanation-of-Options-in-qBittorrent#Advanced"
        target="_blank"
        >{{ t('settings.advanced.openDoc') }}</a
      >)
    </v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.memory_working_set_limit"
            type="number"
            hide-details
            suffix="MiB"
            :label="t('settings.advanced.qbittorrent.allocatedRam')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.save_resume_data_interval"
            type="number"
            hide-details
            :suffix="t('units.minutes')"
            :label="t('settings.advanced.qbittorrent.saveInterval')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-checkbox v-model="preferenceStore.preferences!.recheck_completed_torrents" hide-details :label="t('settings.advanced.qbittorrent.recheckOnCompletion')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.refresh_interval"
            type="number"
            hide-details
            suffix="ms"
            :label="t('settings.advanced.qbittorrent.refreshInterval')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-checkbox v-model="preferenceStore.preferences!.resolve_peer_countries" hide-details :label="t('settings.advanced.qbittorrent.resolveCountries')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-checkbox v-model="preferenceStore.preferences!.reannounce_when_address_changed" hide-details :label="t('settings.advanced.qbittorrent.reannounceOnIpPortChanged')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10" />
    <v-list-subheader>{{ t('settings.advanced.qbittorrent.networking.title') }}</v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-select
            v-model="preferenceStore.preferences!.current_network_interface"
            hide-details
            :items="networkInterfaceOptions"
            :label="t('settings.advanced.qbittorrent.networking.networkInterfaces.label')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-select
            v-model="preferenceStore.preferences!.current_interface_address"
            hide-details
            :items="ipAddressesOptions"
            :label="t('settings.advanced.qbittorrent.networking.ipAddress.label')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10 mt-3" />
    <v-list-subheader>{{ t('settings.advanced.qbittorrent.embeddedTracker.title') }}</v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" class="py-0">
          <v-checkbox v-model="preferenceStore.preferences!.enable_embedded_tracker" hide-details :label="t('settings.advanced.qbittorrent.embeddedTracker.enable')" />
        </v-col>
        <v-col cols="12" class="py-0">
          <v-text-field
            v-model="preferenceStore.preferences!.embedded_tracker_port"
            :disabled="!preferenceStore.preferences!.enable_embedded_tracker"
            type="number"
            hide-details
            :label="t('settings.advanced.qbittorrent.embeddedTracker.port')" />
        </v-col>
        <v-col cols="12" class="pt-0">
          <v-checkbox
            v-model="preferenceStore.preferences!.embedded_tracker_port_forwarding"
            hide-details
            :label="t('settings.advanced.qbittorrent.embeddedTracker.portForward')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider />

    <v-list-subheader>
      {{ t('settings.advanced.libtorrent.subheader') }} (
      <a href="https://www.libtorrent.org/reference-Settings.html" target="_blank">
        {{ t('settings.advanced.openDoc') }}
      </a>
      )
    </v-list-subheader>

    <v-list-subheader>{{ t('settings.advanced.libtorrent.threads.subheader') }}</v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.async_io_threads" type="number" hide-details :label="t('settings.advanced.libtorrent.threads.asyncIoThreads')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.hashing_threads" type="number" hide-details :label="t('settings.advanced.libtorrent.threads.hashingThreads')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.file_pool_size" type="number" hide-details :label="t('settings.advanced.libtorrent.threads.filePoolSize')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.checking_memory_use"
            type="number"
            hide-details
            suffix="MiB"
            :label="t('settings.advanced.libtorrent.threads.outstandingMemory')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10 mt-3" />
    <v-list-subheader>{{ t('settings.advanced.libtorrent.disk.subheader') }}</v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.disk_cache" type="number" hide-details suffix="MiB" :label="t('settings.advanced.libtorrent.disk.diskCache')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.disk_cache_ttl"
            type="number"
            hide-details
            :suffix="t('units.seconds')"
            :label="t('settings.advanced.libtorrent.disk.diskCacheExpiry')" />
        </v-col>

        <v-col cols="12">
          <v-text-field
            v-model="preferenceStore.preferences!.disk_queue_size"
            type="number"
            hide-details
            suffix="kiB"
            :label="t('settings.advanced.libtorrent.disk.diskQueueSize')" />
        </v-col>

        <v-col cols="12" sm="4">
          <v-select v-model="preferenceStore.preferences!.disk_io_type" hide-details :items="diskIoTypeOptions" :label="t('settings.advanced.libtorrent.disk.diskIoType')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-select
            v-model="preferenceStore.preferences!.disk_io_read_mode"
            hide-details
            :items="diskIoModeReadOptions"
            :label="t('settings.advanced.libtorrent.disk.diskIoReadMode')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-select
            v-model="preferenceStore.preferences!.disk_io_write_mode"
            hide-details
            :items="diskIoModeWriteOptions"
            :label="t('settings.advanced.libtorrent.disk.diskIoWriteMode')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10 mt-3" />

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="4">
          <v-checkbox v-model="preferenceStore.preferences!.enable_coalesce_read_write" hide-details :label="t('settings.advanced.libtorrent.coalesceReadsWrites')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-checkbox v-model="preferenceStore.preferences!.enable_piece_extent_affinity" hide-details :label="t('settings.advanced.libtorrent.pieceExtentAffinity')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-checkbox v-model="preferenceStore.preferences!.enable_upload_suggestions" hide-details :label="t('settings.advanced.libtorrent.sendUploadPieceSuggestions')" />
        </v-col>

        <v-col cols="12" sm="4">
          <v-text-field
            v-model="preferenceStore.preferences!.send_buffer_watermark"
            type="number"
            hide-details
            suffix="kiB"
            :label="t('settings.advanced.libtorrent.sendBufferWatermark')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-text-field
            v-model="preferenceStore.preferences!.send_buffer_low_watermark"
            type="number"
            hide-details
            suffix="kiB"
            :label="t('settings.advanced.libtorrent.sendBufferLowWatermark')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-text-field
            v-model="preferenceStore.preferences!.send_buffer_watermark_factor"
            type="number"
            hide-details
            suffix="%"
            :label="t('settings.advanced.libtorrent.sendBufferWatermarkFactor')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.connection_speed"
            type="number"
            hide-details
            :label="t('settings.advanced.libtorrent.outgoingConnectionsPerSecond')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.socket_backlog_size" type="number" hide-details :label="t('settings.advanced.libtorrent.socketBacklogSize')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10 mt-3" />
    <v-list-subheader>{{ t('settings.advanced.libtorrent.networking.subheader') }}</v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.outgoing_ports_min"
            type="number"
            hide-details
            :label="t('settings.advanced.libtorrent.networking.outgoingPortsMin')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.outgoing_ports_max"
            type="number"
            hide-details
            :label="t('settings.advanced.libtorrent.networking.outgoingPortsMax')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.upnp_lease_duration"
            type="number"
            hide-details
            :label="t('settings.advanced.libtorrent.networking.upnpLeaseDuration')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.peer_tos" type="number" hide-details :label="t('settings.advanced.libtorrent.networking.peerTos')" />
        </v-col>

        <v-col cols="12">
          <v-select
            v-model="preferenceStore.preferences!.utp_tcp_mixed_mode"
            hide-details
            :items="utpTcpMixedModeOptions"
            :label="t('settings.advanced.libtorrent.networking.utpTcpMixedModeAlgorithm')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10 mt-3" />
    <v-list-subheader>{{ t('settings.advanced.libtorrent.security.subheader') }}</v-list-subheader>

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-checkbox v-model="preferenceStore.preferences!.idn_support_enabled" hide-details :label="t('settings.advanced.libtorrent.security.idnSupport')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-checkbox
            v-model="preferenceStore.preferences!.enable_multi_connections_from_same_ip"
            hide-details
            :label="t('settings.advanced.libtorrent.security.allowMultipleConnectionsFromTheSameIPAddress')" />
        </v-col>

        <v-col cols="12" sm="4">
          <v-checkbox
            v-model="preferenceStore.preferences!.validate_https_tracker_certificate"
            hide-details
            :label="t('settings.advanced.libtorrent.security.validateHTTPSTrackerCertificate')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-checkbox v-model="preferenceStore.preferences!.ssrf_mitigation" hide-details :label="t('settings.advanced.libtorrent.security.mitigateSSRF')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-checkbox
            v-model="preferenceStore.preferences!.block_peers_on_privileged_ports"
            hide-details
            :label="t('settings.advanced.libtorrent.security.blockPeersOnPrivilegedPorts')" />
        </v-col>
      </v-row>
    </v-list-item>

    <v-divider class="mx-10 my-3" />

    <v-list-item>
      <v-row>
        <v-col cols="12" sm="6">
          <v-select
            v-model="preferenceStore.preferences!.upload_slots_behavior"
            hide-details
            :items="uploadSlotsBehaviorOptions"
            :label="t('settings.advanced.libtorrent.uploadSlotsBehavior')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-select
            v-model="preferenceStore.preferences!.upload_choking_algorithm"
            hide-details
            :items="uploadChokingAlgorithmOptions"
            :label="t('settings.advanced.libtorrent.uploadChokingAlgorithm')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-checkbox v-model="preferenceStore.preferences!.announce_to_all_trackers" hide-details :label="t('settings.advanced.libtorrent.announceAllTrackers')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-checkbox v-model="preferenceStore.preferences!.announce_to_all_tiers" hide-details :label="t('settings.advanced.libtorrent.announceAllTiers')" />
        </v-col>

        <v-col cols="12">
          <v-text-field v-model="preferenceStore.preferences!.announce_ip" type="number" hide-details :label="t('settings.advanced.libtorrent.announceIP')" />
        </v-col>

        <v-col cols="12" sm="6">
          <v-text-field
            v-model="preferenceStore.preferences!.max_concurrent_http_announces"
            type="number"
            hide-details
            :label="t('settings.advanced.libtorrent.maxConcurrentHTTPAnnounces')" />
        </v-col>
        <v-col cols="12" sm="6">
          <v-text-field v-model="preferenceStore.preferences!.stop_tracker_timeout" type="number" hide-details :label="t('settings.advanced.libtorrent.stopTrackerTimeout')" />
        </v-col>

        <v-col cols="12" sm="4">
          <v-text-field v-model="preferenceStore.preferences!.peer_turnover" type="number" hide-details suffix="%" :label="t('settings.advanced.libtorrent.peerTurnover')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-text-field
            v-model="preferenceStore.preferences!.peer_turnover_cutoff"
            type="number"
            hide-details
            suffix="%"
            :label="t('settings.advanced.libtorrent.peerTurnoverCutoff')" />
        </v-col>
        <v-col cols="12" sm="4">
          <v-text-field
            v-model="preferenceStore.preferences!.peer_turnover_interval"
            type="number"
            hide-details
            :suffix="t('units.seconds')"
            :label="t('settings.advanced.libtorrent.peerTurnoverInterval')" />
        </v-col>

        <v-col cols="12">
          <v-text-field v-model="preferenceStore.preferences!.request_queue_size" type="number" hide-details :label="t('settings.advanced.libtorrent.requestQueueSize')" />
        </v-col>
      </v-row>
    </v-list-item>
  </v-list>
</template>

<style scoped lang="scss"></style>
