<script setup lang="ts">
import settings from '../lib/settings'

const props = defineProps<{
  tplData: Array<any>,
  path: (string|number)[]
}>()

const ci = getCurrentInstance()
const customValue = ref<string[]>(props.tplData)

function update() {
  customValue.value = (settings.get().customs.value?.getIn(props.path) as any)?.items || []
}

function save() {
  settings.get().customs.value?.setIn([...props.path], customValue.value)
}

watch(settings.get().customs, (customs) => {
  update()
  ci?.proxy?.$forceUpdate()
})

function onChange(index: number, val: string) {
  customValue.value[index] = val
  save()
}

function remove(index: number) {
  customValue.value.splice(index, 1)
  save()
}

function add() {
  customValue.value.push('')
  save()
}
</script>

<template>
  <div class="arr-grp">
    <div v-for="(item, index) in customValue" class="arr-item">
      <input
        type="text"
        :value="String(item)"
        @change="onChange(index, ($event.target as any).value)"
      />
      <button class="act-btn" @click="remove(index)">-</button>
    </div>
    <div class="act-grp">
      <button class="act-btn" @click="add()">+</button>
    </div>
  </div>
</template>

<style scoped lang="scss">
.arr-item {
  position: relative;
  margin-bottom: 20px;
  margin-left: 10px;
  padding-right: 50px;

  .act-btn {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    right: 10px;
  }
}

.act-grp {
  margin-left: 10px;

  .act-btn {
    padding: 2px 30px;
  }
}

.act-btn {
  display: inline-block;
  padding: 2px 10px;
  cursor: pointer;
  border: 0;
  color: #5f6369;
  background: #f4f4f4;
  border-radius: 2px;

  &:hover {
    color: #1967d2;
    background: #e5ecfa;
  }
}
</style>
