<script setup lang="ts">
import { ContentWrap } from '@/components/ContentWrap'
import { useI18n } from '@/hooks/web/useI18n'
import { Table } from '@/components/Table'
import { getTableListApi } from '@/api/table'
import { TableData } from '@/api/table/types'
import { ref, h, reactive, unref } from 'vue'
import { ElTag, ElButton } from 'element-plus'
import { useTable } from '@/hooks/web/useTable'
import { Pagination, TableColumn, TableSlotDefault } from '@/types/table'

const { register, tableObject, methods, elTableRef } = useTable<TableData>({
  getListApi: getTableListApi,
  response: {
    list: 'list',
    total: 'total'
  }
})

const { getList } = methods

getList()

const { t } = useI18n()

// 表字段定义
const columns = reactive<TableColumn[]>([
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
])

// 一些操作
const actionFn = (data: TableSlotDefault) => {
  console.log(data)
}

const paginationObj = ref<Pagination>()

const showPagination = (show: boolean) => {
  if (show) {
    paginationObj.value = {
      total: tableObject.total
    }
  } else {
    paginationObj.value = undefined
  }
}

const reserveIndex = (custom: boolean) => {
  const { setProps } = methods
  setProps({
    reserveIndex: custom
  })
}

const showSelections = (show: boolean) => {
  const { setProps } = methods
  setProps({
    selection: show
  })
}

const index = ref(1)

const changeTitle = () => {
  const { setColumn } = methods
  setColumn([
    {
      field: 'title',
      path: 'label',
      value: `${t('tableDemo.title')}${unref(index)}`
    }
  ])
  index.value++
}

const showExpandedRows = (show: boolean) => {
  const { setProps } = methods
  setProps({
    expand: show
  })
}

const selectAllNone = () => {
  unref(elTableRef)?.toggleAllSelection()
}
</script>

<template>
  <ContentWrap :title="`UseTable ${t('tableDemo.operate')}`">
    <ElButton @click="showPagination(true)">
      {{ t('tableDemo.show') }} {{ t('tableDemo.pagination') }}
    </ElButton>
    <ElButton @click="showPagination(false)">
      {{ t('tableDemo.hidden') }} {{ t('tableDemo.pagination') }}
    </ElButton>

    <ElButton @click="reserveIndex(true)">{{ t('tableDemo.reserveIndex') }}</ElButton>
    <ElButton @click="reserveIndex(false)">{{ t('tableDemo.restoreIndex') }}</ElButton>

    <ElButton @click="showSelections(true)">{{ t('tableDemo.showSelections') }}</ElButton>
    <ElButton @click="showSelections(false)">{{ t('tableDemo.hiddenSelections') }}</ElButton>

    <ElButton @click="changeTitle">{{ t('tableDemo.changeTitle') }}</ElButton>

    <ElButton @click="showExpandedRows(true)">{{ t('tableDemo.showExpandedRows') }}</ElButton>
    <ElButton @click="showExpandedRows(false)">{{ t('tableDemo.hiddenExpandedRows') }}</ElButton>

    <ElButton @click="selectAllNone">{{ t('tableDemo.selectAllNone') }}</ElButton>
  </ContentWrap>
  <ContentWrap :title="`UseTable ${t('tableDemo.example')}`">
    <Table
      v-model:pageSize="tableObject.pageSize"
      v-model:currentPage="tableObject.currentPage"
      :columns="columns"
      :data="tableObject.tableList"
      :loading="tableObject.loading"
      :pagination="paginationObj"
      @register="register"
    >
      <template #action="data">
        <ElButton type="primary" @click="actionFn(data as TableSlotDefault)">
          {{ t('tableDemo.action') }}
        </ElButton>
      </template>

      <template #expand="data">
        <div class="ml-30px">
          <div>{{ t('tableDemo.title') }}：{{ data.row.title }}</div>
          <div>{{ t('tableDemo.author') }}：{{ data.row.author }}</div>
          <div>{{ t('tableDemo.displayTime') }}：{{ data.row.display_time }}</div>
        </div>
      </template>
    </Table>
  </ContentWrap>
</template>
