<template>
  <div class="container">
    <!-- Floating Hearts Background -->
    <div class="hearts-container" v-if="!messageComplete">
      <div v-for="n in 20" :key="n" class="heart" :style="randomHeartStyle()"></div>
    </div>

    <!-- Title and Message -->

    <h2>🎂 Happy Birthday 🎈<br />Stella</h2>

    <p class="birthday-message">Wishing you a beautiful day filled with love and laughter! 🥳</p>

    <p class="love-message" ref="loveMessage"></p>

    <!-- Game Section -->
    <!-- Game Section -->
    <div v-if="messageComplete && !showGift">
      <!-- Before Gotcha -->
      <div v-if="!gotchaTriggered">
        <p class="hold-text">Press and hold this button for 2 minutes 🕒</p>
        <button class="surprise-button" @click="startCountdown">Hold for 2 minutes</button>
      </div>

      <!-- After 1 minute -->
      <transition name="fade">
        <div v-if="gotchaTriggered">
          <p class="gotcha-text">Gotcha!!! We still have time 🎉</p>
          <button class="surprise-button" @click="revealSurprise">
            🎁 Press me for a surprise! 🎊
          </button>
        </div>
      </transition>
    </div>

    <!-- Confetti Animation -->
    <div v-if="showConfetti">
      <div v-for="n in 100" :key="n" class="confetti" :style="randomConfettiStyle()"></div>
    </div>

    <!-- Gift Card -->
    <transition name="fade">
      <div v-if="showGift" class="gift-card">
        <p>🎉 Here's your gift! 🎁</p>
        <p><strong>🎁 Gift Code:</strong> <code>PL6-GJM-tAA</code> ✨</p>

        <a
          href="https://app.wunschgutschein.de/?_gl=1%2ak3vok0%2a_gcl_aw%2aR0NMLjE3NTAxODY2MzMuQ2p3S0NBandwTVRDQmhBLUVpd0FfLU1zbWFTYVFma2dYMEM5MjNBQlQtd1U4NFhDZy14VnNQZGtOc0lnU0liWWtkbVJDTTFTVldPcWd4b0NxNUlRQXZEX0J3RQ..%2a_gcl_au%2aMTcyNzU5MjkwNy4xNzUwMTQ2ODY5%2a_ga%2aMjMyMjA2NDQ0LjE3NTAxNDY4Njc.%2a_ga_NCE5M9XEQX%2aczE3NTAxODY2MzIkbzUkZzAkdDE3NTAxODY2MzIkajYwJGwwJGgw"
          target="_blank"
          >wunschgutschein.de</a
        >
        <img src="https://media.giphy.com/media/l0MYt5jPR6QX5pnqM/giphy.gif" alt="Gift" />
      </div>
    </transition>



    <!-- Background Music -->
    <audio ref="bgMusic" autoplay preload="auto" style="display: none">
      <source src="../public/sound-effect-happy-birthday-music-box-333245.mp3" type="audio/mpeg" />
    </audio>
  </div>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'

export default defineComponent({
  setup() {
    const cookieAccepted = ref(false)
    const showConfetti = ref(false)
    const showGift = ref(false)
    const messageComplete = ref(false)
    const gotchaTriggered = ref(false)
    const countdownStarted = ref(false)
    const loveMessage = ref(null)

    const acceptCookies = () => {
      cookieAccepted.value = true
      localStorage.setItem('cookieAccepted', 'true')
    }

    const revealSurprise = () => {
      showConfetti.value = true
      showGift.value = true
    }

    const startCountdown = () => {
      if (countdownStarted.value) return
      countdownStarted.value = true

      setTimeout(() => {
        gotchaTriggered.value = true
      }, 10 * 1000) // 1 minute
    }

    const randomConfettiStyle = () => {
      const left = Math.random() * 100
      const size = 6 + Math.random() * 8
      const color = ['#ff69b4', '#ffd700', '#87cefa', '#ffb6c1'][Math.floor(Math.random() * 4)]
      const duration = 3 + Math.random() * 2
      const delay = Math.random()
      return {
        left: `${left}%`,
        width: `${size}px`,
        height: `${size}px`,
        backgroundColor: color,
        animation: `floatUp ${duration}s ease-in-out ${delay}s forwards`,
      }
    }

    const randomHeartStyle = () => {
      const left = Math.random() * 100
      const duration = 5 + Math.random() * 5
      const delay = Math.random() * 5
      return {
        left: `${left}%`,
        animationDuration: `${duration}s`,
        animationDelay: `${delay}s`,
      }
    }

    const typeText = async (el, text) => {
      for (let i = 0; i < text.length; i++) {
        el.innerHTML += text[i]
        await new Promise((resolve) => setTimeout(resolve, 100))
      }
      el.innerHTML += '<span class="blinking-cursor">|</span>'
      messageComplete.value = true
    }

    onMounted(() => {
      if (localStorage.getItem('cookieAccepted') === 'true') {
        cookieAccepted.value = true
      }

      const bg = document.querySelector('audio')
      if (bg) {
        bg.volume = 0.02
        bg.play().catch(() => {
          console.warn('Autoplay blocked. Will play on click.')
        })
        document.addEventListener(
          'click',
          () => {
            if (bg.paused) bg.play()
          },
          { once: true },
        )
      }

      const msg =
        "Every second I spend thinking of you adds something sharp and vivid to my day. Your wit, your perspective, and your unapologetic way of being you are unforgettable. On your birthday, I hope this little surprise shows just how much you're valued and how much of an impact you make, just by being yourself."

      if (loveMessage.value) typeText(loveMessage.value, msg)

      document.addEventListener('mousemove', (e) => {
        const sparkle = document.createElement('div')
        sparkle.className = 'sparkle'
        sparkle.style.left = `${e.pageX}px`
        sparkle.style.top = `${e.pageY}px`
        document.body.appendChild(sparkle)
        setTimeout(() => sparkle.remove(), 500)
      })
    })

    return {
      cookieAccepted,
      acceptCookies,
      showConfetti,
      showGift,
      revealSurprise,
      randomConfettiStyle,
      randomHeartStyle,
      loveMessage,
      messageComplete,
      gotchaTriggered,
      countdownStarted,
      startCountdown,
    }
  },
})
</script>

<style>
html,
body {
  font-family: 'Segoe UI', sans-serif;
  background: linear-gradient(to bottom, #ffe6f0, #ffccd5);
  margin: 0;
  padding: 0;
  height: 100%;
  overflow-x: hidden;
  touch-action: manipulation;
}

.container {
  max-width: 420px;
  margin: auto;
  background: white;
  border-radius: 2rem;
  padding: 2rem;
  box-shadow: 0 0 20px rgba(255, 182, 193, 0.4);
  text-align: center;
  position: relative;
  overflow: hidden;
  min-height: 50vh;
}

h2 {
  font-size: 2rem;
  color: #d63384;
  margin-bottom: 0.5rem;
}

.birthday-message {
  font-size: 1.2rem;
  color: #6f42c1;
  margin-bottom: 1.5rem;
}

.love-message {
  font-size: 1.1rem;
  color: #e91e63;
  font-style: italic;
  margin: 1rem 0 2rem;
  line-height: 1.6;
  min-height: 6rem;
  white-space: pre-line;
  max-width: 100%;
  word-wrap: break-word;
  word-break: break-word;
  overflow-wrap: break-word;
  display: block;
  text-align: center;
}

.surprise-button {
  background-color: #ff69b4;
  color: white;
  border: none;
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
  border-radius: 9999px;
  cursor: pointer;
  margin-bottom: 1.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease;
}

.surprise-button:hover {
  transform: scale(1.05);
}

.confetti {
  position: absolute;
  bottom: 0;
  border-radius: 50%;
  opacity: 0.8;
  pointer-events: none;
}

.gift-card {
  margin-top: 2rem;
  padding: 1rem;
  background: #fff0f6;
  border: 2px dashed #ff69b4;
  border-radius: 1rem;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  animation: fadeIn 1s ease forwards;
}

.gift-card img {
  max-width: 100%;
  margin-top: 1rem;
  border-radius: 0.5rem;
}

.heart {
  position: absolute;
  bottom: -30px;
  font-size: 20px;
  animation: floatHeart 8s linear infinite;
  color: #ff69b4;
}

.hearts-container::before {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.heart::after {
  content: '💗';
}

@keyframes floatHeart {
  0% {
    transform: translateY(0) scale(1);
    opacity: 1;
  }
  100% {
    transform: translateY(-100vh) scale(1.5);
    opacity: 0;
  }
}

.sparkle {
  position: fixed;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: radial-gradient(circle, #ff69b4, #fff);
  pointer-events: none;
  animation: sparkleFade 0.5s ease-out forwards;
  z-index: 9999;
}

@keyframes sparkleFade {
  from {
    transform: scale(1);
    opacity: 1;
  }
  to {
    transform: scale(2);
    opacity: 0;
  }
}

@keyframes floatUp {
  0% {
    transform: translateY(0);
    opacity: 1;
  }
  100% {
    transform: translateY(-100vh);
    opacity: 0;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.cookie-banner {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  background-color: rgba(214, 51, 132, 0.95);
  color: white;
  padding: 1rem;
  text-align: center;
  font-size: 0.95rem;
  z-index: 999;
  box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.1);
}

.cookie-banner button {
  margin-left: 1rem;
  background: white;
  color: #d63384;
  font-weight: bold;
  border: none;
  border-radius: 10px;
  padding: 0.4rem 1rem;
  cursor: pointer;
  transition: background 0.3s ease;
}

.cookie-banner button:hover {
  background: #ffe6f0;
}

.blinking-cursor {
  display: inline-block;
  margin-left: 2px;
  animation: blink 1s step-start infinite;
  color: #e91e63;
  font-weight: bold;
}

@keyframes blink {
  50% {
    opacity: 0;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.hold-text {
  font-size: 1.1rem;
  margin-bottom: 1rem;
  color: #6f42c1;
}

.gotcha-text {
  font-size: 1.3rem;
  font-weight: bold;
  margin-bottom: 1rem;
  color: #e91e63;
}
</style>
