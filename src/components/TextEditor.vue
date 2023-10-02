<template>
  <div :class="`flex flex-col gap-y-4 ${props.class}`">
    <div class="flex items-center gap-4 flex-wrap">
      <button @click="applyBold" class="button">Bold</button>
      <button @click="applyItalic" class="button">Italic</button>
      <button @click="applyHeading" class="button">Heading</button>
      <button @click="applyUl" class="button">UL</button>
      <button @click="applyOl" class="button">OL</button>
      <button @click="undo" class="button">Undo</button>
      <button @click="redo" class="button">Redo</button>
      <button @click="insertImage" class="button">Insert Image</button>
      <button @click="insertHtml" class="button">Insert HTML</button>
      <button @click="insertLink" class="button">Insert Link</button>
      <button @click="insertTable" class="button">Insert Table</button>

      <input type="file" @change="handleFileUpload" />
      <button @click="toggleHtmlMode" class="button">
        <span v-if="htmlMode">Pure View</span>
        <span v-else>HTML View</span>
      </button>
    </div>

    <div class="relative h-[500px]">
      <div
        v-show="!htmlMode"
        v-html="props.modelValue"
        autofocus
        @paste.prevent="handlePaste"
        @mouseup="handleClick"
        @blur="onInput"
        contenteditable="true"
        class="wysiwyg-output absolute top-0 left-0 outline-none border-2 p-4 rounded-lg border-gray-300 focus:border-green-300 h-[500px] overflow-auto w-full h-full"
      />

      <textarea
        v-show="htmlMode"
        class="absolute top-0 left-0 p-4 text-slate-200 bg-slate-900 rounded-lg overflow-auto w-full h-full"
        :value="props.modelValue"
        @input="onInput2"
      >
      </textarea>
    </div>

    <!-- <div
      contenteditable="true"
      class="wysiwyg-output outline-none border-2 p-4 rounded-lg border-gray-300 focus:border-green-300"
    /> -->

    <!-- <div>{{ input }}</div> -->

    <img v-if="url" :src="url" class="w-80 h-80" />

    <!-- <div>{{ url }}</div> -->
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

interface Props {
  class?: string
  modelValue: string
  // value: string
}

interface Emits {
  (event: 'update:modelValue', newValue: string): void
  (event: 'click'): void
  (event: 'input', newValue: string): void
}

const props = withDefaults(defineProps<Props>(), {
  class: ''
})

const emits = defineEmits<Emits>()

// const input = ref<string>('<p><br></p>')
const url = ref<string>('')
const htmlMode = ref<boolean>(false)

const onInput = (event: any) => {
  //     const turndown = new TurndownService({
  //       emDelimiter: '_',
  //       linkStyle: 'inlined',
  //       headingStyle: 'atx'
  //     })
  // this.$emit('input', turndown.turndown(event.target.innerHTML))
  // console.log('event', event.target.innerHTML)
  // input.value = event.target.innerHTML
  // emits('input', event.target.innerHTML)
  emits('update:modelValue', event.target.innerHTML)
}

const onInput2 = (event: any) => {
  // console.log(event)
  emits('update:modelValue', event.target.value)
}

const applyBold = () => {
  document.execCommand('bold')
}

const applyItalic = () => {
  document.execCommand('italic')
}

const applyHeading = () => {
  document.execCommand('formatBlock', false, '<h1>')
}

const applyUl = () => {
  document.execCommand('insertUnorderedList')
}

const applyOl = () => {
  document.execCommand('insertOrderedList')
}

const undo = () => {
  document.execCommand('undo')
}

const redo = () => {
  document.execCommand('redo')
}

const handleClick = (event: MouseEvent) => {
  // const test = document.getSelection()
  // console.log(test)
}

const insertImage = () => {
  document.execCommand('insertImage', true, 'https://picsum.photos/200/300')
}

const insertHtml = () => {
  document.execCommand(
    'insertHtml',
    false,
    '<iframe src="https://storm.mg/" style="width:300px; height:300px;"></iframe>'
  )
}

const insertLink = () => {
  document.execCommand('createLink', false, 'https://google.com/')
}

const insertTable = () => {}

const toggleHtmlMode = () => {
  htmlMode.value = !htmlMode.value
}

const handleFileUpload = (event: any) => {
  console.log(event)
  const file = event.target.files[0]
  if (file) {
    url.value = URL.createObjectURL(file)
  } else {
    url.value = ''
  }
}

const handlePaste = (event: ClipboardEvent) => {
  const text = event.clipboardData?.getData('text/plain')

  if (text) {
    // emits('update:modelValue', text)
    document.execCommand("insertText", false, text);
  }
}

onMounted(() => {
  document.execCommand('defaultParagraphSeparator', false, 'p')
})
</script>

<style>
.button {
  @apply bg-slate-700 hover:bg-slate-600 text-gray-200 rounded-full px-4 py-2 uppercase disabled:bg-slate-500 disabled:cursor-not-allowed;
}

.wysiwyg-output h1 {
  @apply text-2xl;
  @apply font-bold;
  @apply pb-4;
}
.wysiwyg-output p {
  @apply pb-4;
}
.wysiwyg-output p {
  @apply pb-4;
}
.wysiwyg-output ul {
  @apply ml-6;
  @apply list-disc;
}
.wysiwyg-output ol {
  @apply ml-6;
  @apply list-decimal;
}

.wysiwyg-output a {
  @apply underline text-blue-700;
}
</style>
