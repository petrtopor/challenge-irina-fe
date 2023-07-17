<script setup lang="ts">
// @ts-nocheck
import { ref, computed, watch } from 'vue'
import { parse } from 'papaparse'
import Chart from 'chart.js/auto'
import {
  NGrid,
  NGridItem,
  NCard,
  NTabs,
  NTabPane,
  NSpace,
  NLayout,
  NLayoutHeader,
  NLayoutContent,
  NSelect,
  NDataTable
} from 'naive-ui'

const parsedData = ref()
const parseConfig = {
  delimiter: '',
  header: false,
  dynamicTyping: false,
  skipEmptyLines: false,
  preview: 0,
  encoding: '',
  worker: false,
  comments: '',
  download: false,
  transform: false,
  complete: ({data}: {data: []}) => {
    parsedData.value = data
  }
}

const onFileChange = ({ target }: { target: HTMLInputElement }) => {
  if(!target.files) return
  const file = target.files[0]
  parse(file, parseConfig)
}

const axisOptions = computed(
  () => (parsedData.value && parsedData.value[1]?.map((text, value) => ({ text, value }))) ?? []
)
const xAxisOptionSelected = ref()
const yAxisOptionSelected = ref()

const labels = computed(() =>
  parsedData.value?.slice(2, -1).map((row) => row[xAxisOptionSelected.value])
)
const datasetData = computed(() =>
  parsedData.value?.slice(2, -1).map((row) => parseFloat(row[yAxisOptionSelected.value]))
)
let chart
const plotChart = () => {
  const ctx = document.getElementById('chartCanvas').getContext('2d')

  if (chart) {
    chart.destroy()
  }
  chart = new Chart(ctx, {
    type: 'line',
    data: {
      labels: labels.value,
      datasets: [
        {
          label: axisOptions.value[xAxisOptionSelected.value].text,
          data: datasetData.value,
          fill: false,
          borderColor: 'rgb(75, 192, 192)',
          tension: 0.1
        }
      ]
    },
    options: {
      responsive: true,
      plugins: {
        legend: {
          position: 'top'
        },
        title: {
          display: true,
          text: `${axisOptions.value[yAxisOptionSelected.value].text} Chart`
        }
      }
    }
  })
}

const axisOptionsSelected = computed(() => xAxisOptionSelected.value && yAxisOptionSelected.value)
watch(axisOptionsSelected, (value) => {
  if (!value || !datasetData.value || !labels.value) {
    console.log('REMOVE CHART')
  } else {
    console.log('DRAW CHART')
    plotChart()
  }
})

const columns = computed(
  () =>
    (parsedData.value &&
      parsedData.value[1]?.map((text, textIndex) => ({ title: text, key: textIndex }))) ??
    []
)
const columnsOptions = computed(
  () =>
    columns.value.length && columns.value.map(({ title, key }) => ({ label: title, value: key }))
)

const columnsSelected = ref([0, 1, 2, 3])
const columnsTable = computed(
  () =>
    columns.value.length &&
    columns.value.filter((column) => columnsSelected.value.includes(column.key))
)

const tableData = computed(
  () =>
    (parsedData.value &&
      parsedData.value
        .slice(2)
        .map((row) => row.reduce((obj, val, idx) => ({ ...obj, [idx]: val }), {}))) ??
    []
)
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
              <input
                type="file"
                @change="onFileChange"
                placeholder="Select the .csv-file"
                accept=".csv"
              />
            </n-tab-pane>
            <n-tab-pane name="text" tab="text"> Вставить текст </n-tab-pane>
            <n-tab-pane name="link" tab="link"> Взять по ссылке </n-tab-pane>
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
                    <n-grid cols="1 240:2">
                      <n-grid-item>
                        <label for="x-axis-select">Выберите ось х:</label>
                        <select
                          :disabled="!axisOptions.length"
                          id="x-axis-select"
                          v-model="xAxisOptionSelected"
                        >
                          <option
                            v-for="(xAxisOption, xAxisOptionIndex) in axisOptions"
                            :key="`x-axis-option-${xAxisOptionIndex}`"
                            :value="xAxisOption.value"
                          >
                            {{ xAxisOption.text }}
                          </option>
                        </select>
                      </n-grid-item>
                      <n-grid-item>
                        <label for="y-axis-select">Выберите ось у:</label>
                        <select
                          :disabled="!axisOptions.length"
                          id="x-axis-select"
                          v-model="yAxisOptionSelected"
                        >
                          <option
                            v-for="(yAxisOption, yAxisOptionIndex) in axisOptions"
                            :key="`y-axis-option-${yAxisOptionIndex}`"
                            :value="yAxisOption.value"
                          >
                            {{ yAxisOption.text }}
                          </option>
                        </select>
                      </n-grid-item>
                    </n-grid>
                    <n-grid cols="1">
                      <n-grid-item>
                        <canvas id="chartCanvas" />
                      </n-grid-item>
                    </n-grid>
                  </n-layout-content>
                </n-layout>
              </n-space>
            </n-tab-pane>
            <n-tab-pane name="table" tab="table">
              <n-space vertical size="large">
                <n-layout>
                  <n-layout-header>Таблица</n-layout-header>
                  <n-layout-content content-style="padding: 24px;">
                    <n-grid cols="1">
                      <n-grid-item>
                        <n-select
                          v-model:value="columnsSelected"
                          multiple
                          :options="columnsOptions"
                        />
                      </n-grid-item>
                    </n-grid>
                    <n-grid cols="1">
                      <n-grid-item>
                        <n-data-table
                          v-if="columns.length"
                          :columns="columnsTable"
                          :data="tableData"
                          :bordered="false"
                          :max-height="480"
                          virtual-scroll
                        />
                      </n-grid-item>
                    </n-grid>
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
