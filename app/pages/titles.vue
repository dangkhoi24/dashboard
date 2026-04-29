<script setup lang="ts">
import { h, resolveComponent } from 'vue'
import type { TableColumn } from '@nuxt/ui'

definePageMeta({ title: 'Titles' })

const UBadge   = resolveComponent('UBadge')
const UButton  = resolveComponent('UButton')

interface Title {
  id:            number
  title:         string
  type:          'movie' | 'series'
  year:          string
  rating:        string
  youtube_id:    string
  thumbnail_url: string
  banner_url:    string
  description:   string
}

const rows = ref<Title[]>([
  { id: 1, title: 'House of Ninjas', type: 'series', year: '2024', rating: 'TV-MA', youtube_id: 'dQw4w9WgXcQ', thumbnail_url: '', banner_url: '', description: '' },
  { id: 2, title: 'Inception',       type: 'movie',  year: '2010', rating: 'PG-13', youtube_id: 'YoHD9XEInc0', thumbnail_url: '', banner_url: '', description: '' },
])

const isOpen   = ref(false)
const selected = ref<Title | null>(null)

const form = reactive<Omit<Title, 'id'>>({
  title: '', type: 'movie', year: '', rating: '',
  youtube_id: '', thumbnail_url: '', banner_url: '', description: '',
})

const typeOptions = [
  { label: 'Movie',  value: 'movie' },
  { label: 'Series', value: 'series' },
]

function openAdd() {
  selected.value = null
  Object.assign(form, { title: '', type: 'movie', year: '', rating: '', youtube_id: '', thumbnail_url: '', banner_url: '', description: '' })
  isOpen.value = true
}

function openEdit(row: Title) {
  selected.value = row
  Object.assign(form, row)
  isOpen.value = true
}

function save() {
  // TODO: call Go API POST/PUT /api/titles
  if (selected.value) {
    const idx = rows.value.findIndex(r => r.id === selected.value!.id)
    if (idx !== -1) rows.value[idx] = { ...selected.value, ...form }
  } else {
    rows.value.push({ id: Date.now(), ...form })
  }
  isOpen.value = false
}

function remove(row: Title) {
  // TODO: call Go API DELETE /api/titles/:id
  rows.value = rows.value.filter(r => r.id !== row.id)
}

const columns: TableColumn<Title>[] = [
  {
    accessorKey: 'title',
    header: 'Title',
  },
  {
    accessorKey: 'type',
    header: 'Type',
    cell: ({ row }) => h(UBadge, {
      color: row.getValue('type') === 'series' ? 'blue' : 'violet',
      variant: 'subtle',
      class: 'capitalize',
    }, () => row.getValue('type')),
  },
  {
    accessorKey: 'year',
    header: 'Year',
  },
  {
    accessorKey: 'rating',
    header: 'Rating',
  },
  {
    accessorKey: 'youtube_id',
    header: 'YouTube ID',
  },
  {
    id: 'actions',
    header: '',
    cell: ({ row }) => h('div', { class: 'flex gap-2 justify-end' }, [
      h(UButton, {
        icon: 'i-lucide-pencil',
        color: 'neutral',
        variant: 'ghost',
        size: 'xs',
        onClick: () => openEdit(row.original),
      }),
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
  <UDashboardPanel id="titles">
    <template #header>
      <UDashboardNavbar title="Titles">
        <template #leading>
          <UDashboardSidebarCollapse />
        </template>
        <template #right>
          <UButton icon="i-lucide-plus" @click="openAdd">Add Title</UButton>
        </template>
      </UDashboardNavbar>
    </template>

    <UTable :data="rows" :columns="columns" class="w-full" />

    <UModal v-model:open="isOpen" :title="selected ? 'Edit Title' : 'Add Title'" class="max-w-xl">
      <template #body>
        <div class="grid grid-cols-2 gap-4">
          <UFormField label="Title" class="col-span-2">
            <UInput v-model="form.title" placeholder="Movie or series title" class="w-full" />
          </UFormField>
          <UFormField label="Type">
            <USelect v-model="form.type" :items="typeOptions" value-key="value" label-key="label" class="w-full" />
          </UFormField>
          <UFormField label="Year">
            <UInput v-model="form.year" placeholder="2024" class="w-full" />
          </UFormField>
          <UFormField label="Rating">
            <UInput v-model="form.rating" placeholder="TV-MA / PG-13 / R" class="w-full" />
          </UFormField>
          <UFormField label="YouTube ID">
            <UInput v-model="form.youtube_id" placeholder="YoHD9XEInc0" class="w-full" />
          </UFormField>
          <UFormField label="Thumbnail URL" class="col-span-2">
            <UInput v-model="form.thumbnail_url" placeholder="https://image.tmdb.org/..." class="w-full" />
          </UFormField>
          <UFormField label="Banner URL" class="col-span-2">
            <UInput v-model="form.banner_url" placeholder="https://image.tmdb.org/..." class="w-full" />
          </UFormField>
          <UFormField label="Description" class="col-span-2">
            <UTextarea v-model="form.description" placeholder="Short description..." :rows="3" class="w-full" />
          </UFormField>
        </div>
      </template>
      <template #footer>
        <div class="flex justify-end gap-2">
          <UButton color="neutral" variant="ghost" @click="isOpen = false">Cancel</UButton>
          <UButton @click="save">{{ selected ? 'Save' : 'Add' }}</UButton>
        </div>
      </template>
    </UModal>
  </UDashboardPanel>
</template>
