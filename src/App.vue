<script setup>
import { ref, onMounted } from 'vue'
import angryCat from "./assets/audio/angry-cat.mp3"
import happyCat from "./assets/audio/happy-cat.mp3"
const bigSize = ref(1)
const smallSize = ref(1)

const catImages = [
  new URL("./assets/cats/cat-1.png", import.meta.url).href,
  new URL("./assets/cats/cat-2.png", import.meta.url).href,
  new URL("./assets/cats/cat-3.png", import.meta.url).href,
  new URL("./assets/cats/cat-4.png", import.meta.url).href,
]

const cats = ref([]) // each: { id, src, x, y }

const spawnCat = () => {
  const randomSrc = catImages[Math.floor(Math.random() * catImages.length)]

  const x = 10 + Math.random() * 80 
  const y = 10 + Math.random() * 80 

  const cat = { id: crypto.randomUUID(), src: randomSrc, x, y }
  cats.value.push(cat)

  setTimeout(() => {
    cats.value = cats.value.filter(c => c.id !== cat.id)
  }, 1000)
}

async function playAngryCat() {
  const audio = new Audio(angryCat)
  audio.currentTime = 0
  audio.play()
}

async function playHappyCat() {
  const audio = new Audio(happyCat)
  audio.currentTime = 0
  audio.play()
}

async function noPress() {
  playAngryCat()
  spawnCat()
  smallSize.value -= .05
  bigSize.value += .05
  btnNo.style.scale = smallSize.value
  btnYes.style.scale = bigSize.value

  console.log(bigSize.value, smallSize.value)
}

async function yesPress() {
  playHappyCat()
  console.log(bigSize.value, smallSize.value)
}

onMounted(() => {
  const btnNo = document.getElementById('btnNo')
  const btnYes = document.getElementById('btnYes')
})

</script>

<template>
  <div class="overflow-hidden relative h-screen">
    <div class="catBg absolute z-0 top-[-50%] left-[-50%]">

    </div>
    <div class="pointer-events-none fixed inset-0 z-30">
      <img
        v-for="cat in cats"
        :key="cat.id"
        :src="cat.src"
        class="cat-pop"
        :style="{ left: cat.x + 'vw', top: cat.y + 'vh' }"
        alt=""
      />
    </div>
    <div class="p-4 bg-transparent z-20 relative h-screen align-middle flex flex-col justify-center items-center">
      <h1 class="text-3xl font-bold bg-pink-300 p-4 rounded-lg border-2 border-white">
        Will you be my valentine? :D
      </h1>
      <div class="grid grid-cols-2 gap-2 mt-10 font-bold px-4">
        <button
        id="btnYes"
      class="w-full h-18 rounded-lg border-2 border-white bg-[#f2a7d8] px-4 py-2 text-[1em] text-white
            transition-colors
            hover:bg-pink-200
            active:bg-[#e38ac4]
            active:ring-2 active:ring-black
            focus:outline-none"
              @click="yesPress()"
    >
          YES
        </button>
        <button
        id="btnNo"
      class="w-full h-18 rounded-lg border-2 border-white bg-[#f2a7d8] px-4 py-2 text-[1em] text-white
            transition-colors
            hover:bg-pink-200
            active:bg-[#e38ac4]
            active:ring-2 active:ring-black
            focus:outline-none"
              @click="noPress()"
    >
          no.<br/>
          <span class="text-xs">...you smell</span>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
