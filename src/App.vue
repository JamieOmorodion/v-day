<script setup>
import { ref, onMounted } from 'vue'
import emailjs from '@emailjs/browser'
import angryCat from "./assets/audio/angry-cat.mp3"
import happyCat from "./assets/audio/happy-cat.mp3"
import fireworks from "./assets/audio/fireworks.mp3"
import yesCat from "./assets/yesCat.jpg"
const bigSize = ref(1)
const smallSize = ref(1)
const yesPressed = ref(false)
const showYesCat = ref(false)
const showYesText = ref(false)

const catImages = [
  new URL("./assets/cats/cat-1.png", import.meta.url).href,
  new URL("./assets/cats/cat-2.png", import.meta.url).href,
  new URL("./assets/cats/cat-3.png", import.meta.url).href,
  new URL("./assets/cats/cat-4.png", import.meta.url).href,
]

const cats = ref([]) // each: { id, src, x, y }

emailjs.init('i4VaIxisADNiXnnn4')

const spawnCat = () => {
  const randomSrc = catImages[Math.floor(Math.random() * catImages.length)]

  const catSizePx = 200 // must match .cat-pop width
  const paddingPx = 8

  const maxX = window.innerWidth - (catSizePx / 2) - paddingPx
  const minX = (catSizePx / 2) + paddingPx

  const maxY = window.innerHeight - (catSizePx / 2) - paddingPx
  const minY = (catSizePx / 2) + paddingPx

  const x = minX + Math.random() * (maxX - minX)
  const y = minY + Math.random() * (maxY - minY)

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
}

async function yesPress() {
  yesPressed.value = true
  const audio = new Audio(fireworks)
  audio.currentTime = 0
  audio.volume = 0.5
  audio.loop = true
  audio.play()
  setTimeout(() => {
    showYesCat.value = true
    const serviceID = 'default_service';
    const templateID = 'template_81gf4qj';
    emailjs.send(serviceID, templateID, this)
    playHappyCat()
  }, 3000)
  setTimeout(() => {
    showYesText.value = true
  }, 6000)
}



onMounted(() => {
  const btnNo = document.getElementById('btnNo')
  const btnYes = document.getElementById('btnYes')
})

</script>

<template>
  <div class="overflow-hidden relative h-dvh">
    <div v-show="yesPressed" class="fireworks">
      <div v-if="showYesCat" class="yes-cat-wrap">
  <img :src="yesCat" class="yes-cat-img" alt="" />
</div>
      <p v-if="showYesText"
      class="w-5/6 text-xl font-bold bg-pink-300 p-4 rounded-lg border-2 border-white fixed bottom-4 left-1/2 transform -translate-x-1/2 z-40 text-center">
        Wow! You clicked YES on the first try!</br>
        Please check your email for a surprise!
      </p>
    </div>
    <div class="catBg absolute z-0 top-[-50%] left-[-50%]">

    </div>
    <div class="pointer-events-none fixed inset-0 z-30">
      
      <img
        v-for="cat in cats"
        :key="cat.id"
        :src="cat.src"
        class="cat-pop"
        :style="{ left: cat.x + 'px', top: cat.y + 'px' }"
        alt=""
      />
      
    </div>
    <div class="p-4 bg-transparent z-20 relative h-dvh align-middle flex flex-col justify-center items-center">
      <h1 class="text-3xl font-bold bg-pink-300 p-4 rounded-lg border-2 border-white">
        My lovely Aleesha,<br/>
        will you be my Valentine?
      </h1>
      
      <!-- <form id="form"> -->
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
      <!-- </form> -->
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
