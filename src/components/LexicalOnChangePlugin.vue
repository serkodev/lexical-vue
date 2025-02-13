<script setup lang="ts">
import type { EditorState, LexicalEditor } from 'lexical'
import { $getRoot } from 'lexical'
import { useEditor } from '../composables'
import { useMounted } from '../composables/useMounted'

const props = withDefaults(defineProps<{
  ignoreInitialChange?: boolean
  ignoreSelectionChange?: boolean
  modelValue?: string
}>(), {
  ignoreInitialChange: true,
  ignoreSelectionChange: false,
})

const emit = defineEmits<{
  (e: 'change', editorState: EditorState, editor: LexicalEditor): void
  (e: 'update:modelValue', payload: string): void
}>()

const editor = useEditor()

const getRoot = $getRoot

useMounted(() => {
  return editor.registerUpdateListener(({ editorState, dirtyElements, dirtyLeaves, prevEditorState }) => {
    if (
      props.ignoreSelectionChange
        && dirtyElements.size === 0
        && dirtyLeaves.size === 0
    )
      return

    if (props.ignoreInitialChange && prevEditorState.isEmpty())
      return

    emit('change', editorState, editor)

    editorState.read(() => {
      emit('update:modelValue', getRoot().getTextContent())
    })
  })
})
</script>

<template />
