<template>
  <div class="container">
    <!-- イントロアニメーション -->
    <div v-if="!introComplete" class="intro">
      <div ref="introText" class="intro-text" :class="{ 'fade-out': startFadeOut }">
        Welcome to 25卒 Page
      </div>
      <div ref="introElements" class="intro-elements">
        <!-- イントロで使用する要素 -->
        <div class="circle"></div>
        <div class="square"></div>
        <div class="triangle"></div>
        <div class="circle"></div>
        <div class="square"></div>
      </div>
    </div>

    <!-- メインコンテンツ（Lottieアニメーション） -->
    <div v-else class="main-content">
      <div class="background-container">
        <lottie-player
          class="background-lottie"
          src="lottie/Animation - 1740141869932.json"
          background="transparent"
          speed="1"
          loop
          autoplay
        />
      </div>

      <div class="characters-container">
        <div
          v-for="(character, index) in 6"
          :key="index"
          class="character-wrapper"
          :style="{ animationDelay: `${index * 0.2}s` }"
          @click="openPopup(index)"
        >
          <lottie-player
            class="character-lottie"
            src="lottie/Animation - 1740142123704.json"
            background="transparent"
            :speed="0.8 + index * 0.1"
            loop
            autoplay
          />
        </div>
      </div>
      <!-- ポップアップ -->
      <div v-if="selectedCharacter !== null" class="popup-overlay" @click="closePopup">
        <div class="popup-content" @click.stop>
          <button class="close-button" @click="closePopup">×</button>
          <div class="popup-character">
            <lottie-player
              src="lottie/Animation - 1740142123704.json"
              background="transparent"
              speed="1"
              loop
              autoplay
              style="width: 200px; height: 200px"
            />
          </div>
          <h2>{{ characters[selectedCharacter].name }}</h2>
          <p>{{ characters[selectedCharacter].description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { gsap } from 'gsap'
import '@lottiefiles/lottie-player'

const introComplete = ref(false)
const startFadeOut = ref(false)
const introText = ref(null)
const introElements = ref(null)

const selectedCharacter = ref(null)
const characters = ref([
  { name: 'Name 1', description: 'sample' },
  { name: 'Name 2', description: 'sample' },
  { name: 'Name 3', description: 'sample' },
  { name: 'Name 4', description: 'sample' },
  { name: 'Name 5', description: 'sample' },
  { name: 'Name 6', description: 'sample' },
])

const openPopup = (index) => {
  selectedCharacter.value = index
}

const closePopup = () => {
  selectedCharacter.value = null
}

onMounted(() => {
  // イントロアニメーションのタイムライン
  const tl = gsap.timeline({
    onComplete: () => {
      startFadeOut.value = true
      // フェードアウト後にイントロを非表示に
      setTimeout(() => {
        introComplete.value = true
      }, 1000)
    },
  })

  // テキストのアニメーション
  tl.from(introText.value, {
    opacity: 0,
    y: 50,
    duration: 1,
    ease: 'power2.out',
  })

  // 図形要素のアニメーション
  tl.from('.circle', {
    scale: 0,
    opacity: 0,
    duration: 0.6,
    ease: 'back.out(1.7)',
  })
    .from(
      '.square',
      {
        rotation: 180,
        opacity: 0,
        duration: 0.6,
        ease: 'power1.out',
      },
      '-=0.3',
    )
    .from(
      '.triangle',
      {
        y: -50,
        opacity: 0,
        duration: 0.6,
        ease: 'bounce.out',
      },
      '-=0.3',
    )

  // 全体を右にスライドアウト
  tl.to([introText.value, introElements.value], {
    x: '100vw',
    duration: 1,
    ease: 'power2.inOut',
    delay: 1,
  })
})
</script>

<style scoped>
.container {
  overflow: hidden;
}

.intro {
  overflow: hidden;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #fff;
}

.intro-text {
  font-size: 3rem;
  margin-bottom: 2rem;
  color: #35495e;
}

.fade-out {
  opacity: 0;
}

.intro-elements {
  display: flex;
  gap: 2rem;
}

.circle {
  width: 50px;
  height: 50px;
  background: #42b883;
  border-radius: 50%;
}

.square {
  width: 50px;
  height: 50px;
  background: #35495e;
}

.triangle {
  width: 0;
  height: 0;
  border-left: 25px solid transparent;
  border-right: 25px solid transparent;
  border-bottom: 50px solid #ff7e67;
}

.background-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  overflow: hidden;
}

.background-lottie {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  min-width: 100%;
  min-height: 100%;
  width: auto;
  height: auto;
  /* 画面比率に合わせて調整 */
  object-fit: cover;
}

.characters-container {
  position: absolute;
  bottom: 10%;
  left: 0;
  width: 100%;
  display: flex;
  justify-content: center;
  gap: 2rem;
  z-index: 2;
}

.character-wrapper {
  opacity: 0;
  transform: translateY(50px);
  animation: characterAppear 0.8s ease-out forwards;
  cursor: pointer;
  transition: transform 0.3s ease;
}

.character-wrapper:hover {
  transform: translateY(-10px);
}

.character-lottie {
  width: 250px;
  height: 250px;
}

@keyframes characterAppear {
  from {
    opacity: 0;
    transform: translateY(50px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* レスポンシブ対応 */
@media (max-width: 1024px) {
  .characters-container {
    gap: 1rem;
  }

  .character-lottie {
    width: 120px;
    height: 120px;
  }
}

@media (max-width: 768px) {
  .character-lottie {
    width: 80px;
    height: 80px;
  }
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease;
}

.popup-content {
  background: white;
  padding: 2rem;
  border-radius: 1rem;
  position: relative;
  width: 90%;
  max-width: 500px;
  text-align: center;
  animation: scaleIn 0.3s ease;
}

.close-button {
  position: absolute;
  top: 1rem;
  right: 1rem;
  width: 2rem;
  height: 2rem;
  border: none;
  background: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: #666;
}

.popup-character {
  margin-bottom: 1rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

h2 {
  color: #333;
  margin-bottom: 1rem;
}

p {
  color: #666;
  line-height: 1.6;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes scaleIn {
  from {
    transform: scale(0.8);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

/* レスポンシブ対応 */
@media (max-width: 768px) {
  .popup-content {
    padding: 1.5rem;
    width: 95%;
  }
}
</style>
