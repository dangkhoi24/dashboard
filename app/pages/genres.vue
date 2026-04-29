<script setup lang="ts">
import { h, resolveComponent } from 'vue'
import type { TableColumn } from '@nuxt/ui'

definePageMeta({ title: 'Genres' })

const UButton = resolveComponent('UButton')

interface Genre {
  id:   number
  name: string
}

const rows = ref<Genre[]>([
  { id: 1, name: 'Drama' },
  { id: 2, name: 'Action' },
  { id: 3, name: 'Comedy' },
  { id: 4, name: 'Thriller' },
  { id: 5, name: 'Documentary' },
])

const isOpen = ref(false)
const name   = ref('')

function save() {
  // TODO: call Go API POST /api/genres
  rows.value.push({ id: Date.now(), name: name.value })
  isOpen.value = false
  name.value = ''
}

function remove(row: Genre) {
  // TODO: call Go API DELETE /api/genres/:id
  rows.value = rows.value.filter(r => r.id !== row.id)
}

const columns: TableColumn<Genre>[] = [
  { accessorKey: 'name', header: 'Name' },
  {
    id: 'actions',
    header: '',
    cell: ({ row }) => h('div', { class: 'flex justify-end' }, [
      h(UButton, {
        icon: 'i-lucide-trash-2',
        color: 'error',
        variant: 'ghost',
        size: 'xs',
        onClick: () => remove(row.original),
      }),
    ]),
  },
]
</script>

<template>
  <UDashboardPanel id="genres">
    <template #header>
      <UDashboardNavbar title="Genres">
        <template #leading>
          <UDashboardSidebarCollapse />
        </template>
        <template #right>
          <UButton icon="i-lucide-plus" @click="isOpen = true">Add Genre</UButton>
        </template>
      </UDashboardNavbar>
    </template>

    <UTable :data="rows" :columns="columns" class="w-full" />

    <UModal v-model:open="isOpen" title="Add Genre">
      <template #body>
        <UFormField label="Genre name">
          <UInput v-model="name" placeholder="e.g. Horror" class="w-full" />
        </UFormField>
      </template>
      <template #footer>
        <div class="flex justify-end gap-2">
          <UButton color="neutral" variant="ghost" @click="isOpen = false">Cancel</UButton>
          <UButton :disabled="!name.trim()" @click="save">Add</UButton>
        </div>
      </template>
    </UModal>
  </UDashboardPanel>
</template>
