<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { random } from 'lodash'
import NOTES from './notes.json'

type Note = {
  id: string
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
  document.addEventListener('keydown', (e) => {
    if (e.code === 'Space') {
      e.preventDefault()
      handleButtonPress()
    }
  })
})

function reset() {
  notes.value = NOTES.map((val) => ({
    ...val,
    sound: new Audio(val.sound),
    repeat: random(0, 1),
  }))
  hasShown.value = false
  selectNote()
}

function selectNote() {
  let nextNote: Note
  let index: number = -1 
  do {
    index = random(0, notes.value.length - 1)
    nextNote = notes.value[index]
    if (notes.value.length === 1 && nextNote.repeat > 0) {
      console.log(JSON.stringify(notes.value, null, 2))
      nextNote.repeat = 0
    }
  } while(nextNote?.id === note.value?.id)
  note.value = nextNote 
  if (nextNote.repeat === 0) {
   notes.value = notes.value.filter((n, i) => i !== index)
    return
  }
  if (index !== -1) notes.value[index].repeat = nextNote.repeat - 1
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
    <div :class="{blur: !hasShown}" class="answer">{{ hasShown ? note?.message : 'Press Show Answer' }}</div>
    <button tabindex="-1" @click="handleButtonPress">{{ hasShown ? 'Next' : 'Show Answer' }}</button>
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
