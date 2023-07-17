<script setup lang="ts">
import { NGrid, NGridItem, NCard, NTabs, NTabPane, NSpace, NLayout, NLayoutHeader, NLayoutContent } from 'naive-ui'

</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" />
    petrtopor Test Task
  </header>

  <main>
    <n-grid cols="1 800:3">
      <n-grid-item span="1">
        <n-card title="Источник данных" style="margin-bottom: 16px">
          <n-tabs type="line" animated>
            <n-tab-pane name="upload" tab="upload">
              Загрузить файл
            </n-tab-pane>
            <n-tab-pane name="text" tab="text">
              Вставить текст
            </n-tab-pane>
            <n-tab-pane name="link" tab="link">
              Взять по ссылке
            </n-tab-pane>
          </n-tabs>
        </n-card>
      </n-grid-item>
      <n-grid-item span="1 800:2">
        <n-card title="Отображение данных" style="margin-bottom: 16px">
          <n-tabs type="line" animated>
            <n-tab-pane name="graph" tab="graph">
              <n-space vertical size="large">
                <n-layout>
                  <n-layout-header>График</n-layout-header>
                  <n-layout-content content-style="padding: 24px;">
                    Всё, что касается графика.
                  </n-layout-content>
                </n-layout>
              </n-space>
            </n-tab-pane>
            <n-tab-pane name="table" tab="table">
              <n-space vertical size="large">
                <n-layout>
                  <n-layout-header>Таблица</n-layout-header>
                  <n-layout-content content-style="padding: 24px;">
                    Всё, что касается таблицы.
                  </n-layout-content>
                </n-layout>
              </n-space>
            </n-tab-pane>
            <n-tab-pane name="candle" tab="candle">
              <n-space vertical size="large">
                <n-layout>
                  <n-layout-header>Токийская свеча</n-layout-header>
                  <n-layout-content content-style="padding: 24px;">
                    Всё, что касается токийской свечи.
                  </n-layout-content>
                </n-layout>
              </n-space>
            </n-tab-pane>
          </n-tabs>
        </n-card>
      </n-grid-item>
    </n-grid>
  </main>
</template>

<style scoped lang="scss">
main {
  flex-grow: 1;
  display: flex;

  .n-grid {
    flex-grow: 1;

    .n-card {
      height: 100%;
    }
  }
}
</style>
