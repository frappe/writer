<template>
  <div class="flex gap-2">
    <FormControl
      class="w-15"
      variant="outline"
      type="number"
      v-model="size"
      :min="1"
      :placeholder="font_size"
    />
    <Combobox
      v-model="selected"
      :options="FONT_FAMILIES"
      :placeholder="FONT_FAMILIES.find((k) => k.value === font_family)?.label"
      :open-on-click="true"
      class="min-w-[10rem]"
      variant="outline"
      :style="selected && { fontFamily: `var(--font-${selected})` }"
    />
  </div>
</template>
<script setup>
import { Combobox, FormControl } from 'frappe-ui'
import { ref, watchEffect, watch } from 'vue'
import { FONT_FAMILIES } from '@/utils'

const props = defineProps({
  editor: Object,
  font_size: String,
  font_family: String,
})

const selected = ref(null)
const size = ref(props.font_size)

watchEffect(() => {
  selected.value = FONT_FAMILIES.find((opt) =>
    opt.isActive(props.editor),
  )?.value
  let fontSize = props.editor.getAttributes('textStyle')?.fontSize
  if (fontSize && typeof fontSize !== 'number') {
    fontSize = +fontSize.slice(0, -2)
    size.value = fontSize
  }
})

watch(selected, (val) => {
  if (val) FONT_FAMILIES.find((k) => k.value === val).action(props.editor)
})
watch(size, (val) => {
  props.editor.commands.setFontSize(val + 'px')
})
</script>
