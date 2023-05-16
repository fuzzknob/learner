<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { random } from 'lodash'
import NOTES from './notes.json'

type Note = {
  message: string,
  img: string
  sound: HTMLAudioElement
  repeat: number
}

const notes = ref<Note[]>([])
const note = ref<Note>()
const hasShown = ref(false)

onMounted(() => {
  reset()
})

function reset() {
  notes.value = NOTES.map((val) => ({
    ...val,
    sound: new Audio(val.sound),
    repeat: random(1, 2),
  }))
  hasShown.value = false
  selectNote()
}

function selectNote() {
  const index = random(0, notes.value.length - 1)
  const currentNote = notes.value[index]
  note.value = currentNote 
  if (currentNote.repeat === 1) {
    notes.value = notes.value.filter((n, i) => i !== index)
    return
  }
  notes.value[index].repeat = currentNote.repeat - 1
}

function handleButtonPress() {
  if (hasShown.value) {
    if (notes.value.length === 0) reset()
    else selectNote()
    hasShown.value = false
    return
  }
  note.value?.sound.play()
  hasShown.value = true
}

</script>

<template>
  <div class="main">
    <div class="image">
      <img :src="note?.img" alt="">
    </div>
    <div :class="{blur: !hasShown}" class="answer">{{ hasShown ? note?.message : 'Press Show Answer Button' }}</div>
    <button @click="handleButtonPress">{{ hasShown ? 'Next' : 'Show Answer' }}</button>
  </div> 
</template>

<style scoped>
.main {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.image {
  max-width: 500px;
  padding: 0 15px;
}

.image img {
  width: 100%;
  border-radius: 5%;
}

.answer {
  margin: 40px 0 40px 0;
  font-size: 25px;
  font-family: 'helvetica';
  color: white;
}

.blur {
  filter: blur(5px);
}

button {
  background: white;
  padding: 10px 20px;
  outline: none;
  border: none;
  font-size: 20px;
  border-radius: 7px;
}
</style>
