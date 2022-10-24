<script setup lang="ts">
import { ContentWrap } from '@/components/ContentWrap'
import { useI18n } from '@/hooks/web/useI18n'
import { Table } from '@/components/Table'
import { getTableListApi } from '@/api/table'
import { TableData } from '@/api/table/types'
import { ref, h } from 'vue'
import { ElTag, ElButton } from 'element-plus'
import { TableColumn, TableSlotDefault } from '@/types/table'

interface Params {
  pageIndex?: number
  pageSize?: number
}

const { t } = useI18n()

const columns: TableColumn[] = [
  {
    field: 'index',
    label: t('tableDemo.index'),
    type: 'index'
  },
  {
    field: 'repo',
    label: t('tableDemo.repo')
  },
  {
    field: 'stars',
    label: t('tableDemo.stars')
  },
  {
    field: 'forks',
    label: t('tableDemo.forks')
  },
  {
    field: 'open_issues_prs_count',
    label: t('tableDemo.open_issues_prs_count')
  },
  {
    field: 'action',
    label: t('tableDemo.action')
  }
]

const loading = ref(true)

let tableDataList = ref<TableData[]>([])

const getTableList = async (params?: Params) => {
  const res = await getTableListApi(
    params || {
      pageIndex: 1,
      pageSize: 10,
      github_id: 'AgoraIO'
    }
  )
    .catch((e) => {
      console.error(e)
    })
    .finally(() => {
      loading.value = false
    })

  const arr: {
    id: string
    repo: string
    stars: string
    forks: string
    open_issues_prs_count: number
  }[] = []

  if (res) {
    for (const d of res) {
      arr.push({
        id: d.id,
        repo: d.name,
        stars: d.stargazers_count,
        forks: d.forks_count,
        open_issues_prs_count: d.open_issues_count
      })
    }

    tableDataList.value = arr
  }
}

getTableList()

const actionFn = (data: TableSlotDefault) => {
  console.log(data)
}

const showExpandedRows = (show: boolean) => {
  const { setProps } = getTableList
  setProps({
    expand: show
  })
}
</script>

<template>
  <ContentWrap :title="t('tableDemo.table')" :message="t('tableDemo.tableDes')">
    <ElButton @click="showExpandedRows(true)">{{ t('tableDemo.showExpandedRows') }}</ElButton>
    <ElButton @click="showExpandedRows(false)">{{ t('tableDemo.hiddenExpandedRows') }}</ElButton>

    <Table :columns="columns" :data="tableDataList" :loading="loading">
      <template #expand="data">
        <div class="ml-30px">
          <div>{{ t('tableDemo.title') }}：{{ data.row.repo }}</div>
          <div>{{ t('tableDemo.author') }}：{{ data.row.stars }}</div>
          <div>{{ t('tableDemo.displayTime') }}：{{ data.row.forks }}</div>
          <div>{{ t('tableDemo.displayTime') }}：{{ data.row.open_issues_prs_count }}</div>
        </div>
      </template>
    </Table>
  </ContentWrap>
</template>
