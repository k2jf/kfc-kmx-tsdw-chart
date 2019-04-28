# kfc-kmx-tsdw-chart

## 功能
用于展示时序图表

## 维护者
youli

## 依赖
npm install --save echarts

## 属性

| 属性         | 说明     | 类型   | 默认值 |
| ----------- | -------- | ------ | ------ |
| title | 图表标题 | String | -      |
| path | 请求参数 | String | 'filestore://' |
| queueName | 请求参数 | String | 'default_queue' |
| selectType | 请求参数 | String | -      |
| selectTable | 请求参数 | String | -      |
| wfid | 请求参数 | String | '140604' |
| wtid | 请求参数 | String | '140604006' |
| startTime | 请求参数 | String | '2019-01-11 00:00:00.000' |
| endTime | 请求参数 | String | '2019-01-11 23:59:59.000' |

## 示例
```
<template>
  <TsdwChart
    :title="title"
    :path="path"
    :queueName="queueName"
    :selectType="selectType"
    :selectTable="selectTable"
    :wfid="wfid"
    :wtid="wtid"
    :startTime="startTime"
    :endTime="endTime"
  />
</template>

<script>
import TsdwChart from '@/components/kfc-kmx-tsdw-chart'

export default {
  components: {
    TsdwChart
  },
  data () {
    return {
      title: '新疆哈密烟墩大二期整装天润风电场-F084机组',
      path: 'filestore://',
      queueName: 'default_queue',
      selectType: 'ts,wfid,wtid,WTUR_WSpd_Ra_F32,WTUR_Temp_Ra_F32',
      selectTable: 'gw_scada_7s_extension',
      wfid: '140604',
      wtid: '140604006',
      startTime: '2019-01-11 00:00:00.000',
      endTime: '2019-01-11 23:59:59.000'
    }
  }
}
</script>
```
