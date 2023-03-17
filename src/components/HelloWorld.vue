<template>
  <v-container class="fill-height">
    <v-row>
      <v-col
        cols="12"
        md="8"
      >
        <div class="text-h5">Form</div>
        <v-text-field v-model="text1" label="Label 1" />
        <v-textarea v-model="text2" label="Label 2" />
      </v-col>
      <v-col
        cols="12"
        md="4"
      >
        <div class="text-h5">Variable List</div>
        <template
          v-for="item in varInputList"
          :key="item"
        >
          <v-text-field
            v-model="varList[item]"
            :label="item"
          />
        </template>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts" setup>
import { computed, ref, watch } from 'vue'
import type { Ref } from 'vue'

const text1 = ref('')
const text2 = ref('')
const varList: Ref<{[key:string]: string}> =  ref({})

const varInputList = computed(() => {
  return Object.keys(varList.value);
})

const allText = computed(() => {
  const rxp = /(?<!{){{([^{}]+)}}(?!})/gm
  const str = text1.value + ' ' + text2.value
  const arrayVar = [...str.matchAll(rxp)].map(m => m[1])
  const arrayUniqVar = [...new Set(arrayVar)]
  return arrayUniqVar;
})

watch(allText, async (newArrayVar, oldArrayVar) => {
  const deleteList = [...oldArrayVar]
  for (const item of newArrayVar) {
    const indexOfOldItem = deleteList.indexOf(item)
    if (indexOfOldItem > -1) {
      deleteList.splice(indexOfOldItem, 1);
    } else {
      varList.value[item] = ''
    }
  }
  for (const item of deleteList) {
    delete varList.value[item]
  }
})
</script>
