<template>
  <a-spin :loading="loading" style="width: 100%">
    <a-card
      class="general-card"
      :header-style="{ paddingBottom: '0' }"
      :body-style="{ padding: '17px 20px 21px 20px' }"
    >
      <a-space direction="vertical" :size="10" fill>
        <a-trigger
trigger="click" :unmount-on-close="false"
          auto-fit-popup-width
        >
          <a-input
            placeholder='可按关键字模糊搜索,多个关键字用","隔开;展开高级搜索可使用更多搜索条件'
            allow-clear
          >
            <template #prefix>
              <icon-search />
            </template>
             <template #suffix>
              <div>高级搜索</div>
              <icon-down />
            </template>
          </a-input>
          <template #content>
            <div class="demo-basic">
              <a-empty />
            </div>
          </template>
        </a-trigger>
        <a-table
          :data="renderList"
          :pagination="false"
          :bordered="false"
          :row-selection="rowSelection"
          :scroll="{ x: '100%', y: '264px' }"
        >
          <template #columns>
            <a-table-column title="内容标题" data-index="title">
              <template #cell="{ record }">
                <a-typography-paragraph
                  :ellipsis="{
                    rows: 1,
                  }"
                >
                  {{ record.title }}
                </a-typography-paragraph>
              </template>
            </a-table-column>
            <a-table-column title="点击量" data-index="clickNumber">
            </a-table-column>
            <a-table-column
              title="日涨幅"
              data-index="increases"
              :sortable="{
                sortDirections: ['ascend', 'descend'],
              }"
            >
              <template #cell="{ record }">
                <div class="increases-cell">
                  <span>{{ record.increases }}%</span>
                  <icon-caret-up
                    v-if="record.increases !== 0"
                    style="color: #f53f3f; font-size: 8px"
                  />
                </div>
              </template>
            </a-table-column>
            <a-table-column title="操作" data-index="op" fixed="right">
              <template #cell="{ record }">
                <div>
                  {{ record?.op }}
                  <a-space>
                    <a-button type="primary" size="small">详情</a-button>
                    <a-button type="primary" size="small">原件 </a-button>
                  </a-space>
                </div>
              </template>
            </a-table-column>
          </template>
        </a-table>
      </a-space>
    </a-card>
  </a-spin>
</template>

<script lang="ts" setup>
import { ref, reactive } from 'vue';
import useLoading from '@/hooks/loading';
import { queryPopularList } from '@/api/dashboard';
import type { TableData } from '@arco-design/web-vue/es/table/interface';

const { loading, setLoading } = useLoading();
const rowSelection = reactive({
  type: 'checkbox',
  showCheckedAll: true,
  onlyCurrent: false,
});
const renderList = ref<TableData[]>();
const fetchData = async (contentType: string) => {
  try {
    setLoading(true);
    const { data } = await queryPopularList({ type: contentType });
    renderList.value = data;
  } catch (err) {
    // you can report use errorHandler or other
  } finally {
    setLoading(false);
  }
};

fetchData('text');
</script>

<style scoped lang="less">
.general-card {
  min-height: 395px;
}
:deep(.arco-table-tr) {
  height: 44px;
  .arco-typography {
    margin-bottom: 0;
  }
}
.increases-cell {
  display: flex;
  align-items: center;
  span {
    margin-right: 4px;
  }
}
.demo-basic {
  padding: 10px;
  background-color: var(--color-bg-popup);
  border-radius: 4px;
  box-shadow: 0 2px 8px 0 rgba(0, 0, 0, 0.15);
}
</style>
