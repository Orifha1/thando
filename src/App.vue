<script setup lang="ts">
import { ref, nextTick } from "vue"
import canvasConfetti from "canvas-confetti"

const stage = ref<"password" | "intro" | "hunt" | "final">("password")
const passwordInput = ref("")
const wrongPassword = ref(false)
const clueCount = ref(0)

const clueMessage = ref<string>("")

const correctPassword = "panda"

function checkPassword() {
  if (passwordInput.value.toLowerCase() === correctPassword) {
    stage.value = "intro"
    wrongPassword.value = false
    triggerConfetti()
  } else {
    wrongPassword.value = true
  }
}

function startHunt() {
  stage.value = "hunt"
}

function increaseAttempt() {
  if (clueCount.value >= 3) return

  clueCount.value++

  const messages = [
    "Oh no… already asking for help? 😌 I still love you though.",
    "You’re adorable when you struggle. Truly.",
    "Even if you're this helpless, you’re still my favourite human ❤️"
  ]

  clueMessage.value = messages[clueCount.value - 1] ?? ""
}

function foundGift() {
  stage.value = "final"
  triggerConfetti()
}

function triggerConfetti() {
  nextTick(() => {
    const duration = 1500
    const end = Date.now() + duration

    const frame = () => {
      canvasConfetti({
        particleCount: 4,
        angle: 60,
        spread: 55,
        origin: { x: 0 }
      })

      canvasConfetti({
        particleCount: 4,
        angle: 120,
        spread: 55,
        origin: { x: 1 }
      })

      if (Date.now() < end) {
        requestAnimationFrame(frame)
      }
    }

    frame()
  })
}
</script>

<template>
  <div class="page">
    <transition name="fade" mode="out-in">

      <!-- PASSWORD -->
      <div v-if="stage === 'password'" key="password" class="card">
        <h1 class="title">Thando’s Private Invitation 🤫</h1>
        <p class="subtitle">
          A surprise awaits somewhere in this house…<br />
          Only you hold the key to begin this journey.<br />
          Enter the secret word to unlock the adventure.
        </p>

        <input v-model="passwordInput" type="password" placeholder="Enter our secret word" class="input" />

        <button class="btn primary" @click="checkPassword">
          Unlock
        </button>

        <p v-if="wrongPassword" class="error">
          That’s not it. Think carefully.
        </p>
      </div>

      <!-- INTRO -->
      <div v-else-if="stage === 'intro'" key="intro" class="card">
        <h1 class="title">Valentine’s Challenge</h1>
        <p class="subtitle">
          Somewhere in this house, there is a gift waiting for you.
          To find it, you must solve what is hidden.
        </p>

        <button class="btn primary full" @click="startHunt">
          Begin
        </button>
      </div>

      <!-- HUNT -->
      <div v-else-if="stage === 'hunt'" key="hunt" class="card">
        <h2 class="title small">Here is your first clue 🙂</h2>

        <p class="cryptic">
          “Where silence folds fabric into shadow,
          and giants guard what travellers carry.
          Seek the cavern that swallows journeys whole —
          within its dark mouth, treasure rests.”
          <br>
          Goodluck.
        </p>

        <p class="progress">
          Help requests used: {{ clueCount }} / 3
        </p>

        <div class="button-group">
          <button class="btn secondary full" @click="increaseAttempt" :disabled="clueCount >= 3">
            I Need Help
          </button>

          <!-- ALWAYS AVAILABLE NOW -->
          <button class="btn primary full" @click="foundGift">
            I Found It
          </button>
          <p v-if="clueMessage" class="clue-message">
            {{ clueMessage }}
          </p>
        </div>
      </div>


      <!-- FINAL -->
      <div v-else key="final" class="card">
        <h2 class="title">You Found It ❤️</h2>

        <img src="/your-photo.jpg" class="photo" />

        <p class="subtitle">
          You solve mysteries the same way you’ve solved my heart -
          effortlessly.
          <br>
          I love you my baby ❤️
          <br /><br />
          Happy Valentine’s Day.
        </p>
      </div>

    </transition>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

.clue-message {
  margin-top: 15px;
  font-size: 0.9rem;
  color: #7a1f2b;
  animation: fadeIn 0.4s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(5px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* MOBILE FIRST */
.page {
  min-height: 100vh;
  background: #f2f2f2;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.card {
  background: white;
  padding: 30px 20px;
  border-radius: 14px;
  width: 100%;
  max-width: 500px;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.08);
  text-align: center;
}

.title {
  font-size: 1.6rem;
  margin-bottom: 15px;
  color: #222;
}

.title.small {
  font-size: 1.3rem;
}

.subtitle {
  color: #555;
  line-height: 1.5;
  font-size: 0.95rem;
}

.cryptic {
  margin: 20px 0;
  font-style: italic;
  color: #444;
  line-height: 1.6;
  font-size: 0.95rem;
}

.progress {
  margin: 10px 0;
  font-size: 0.85rem;
  color: #777;
}

.button-group {
  margin-top: 15px;
}

/* Buttons */
.btn {
  padding: 12px;
  border-radius: 8px;
  border: none;
  font-size: 0.95rem;
  cursor: pointer;
  transition: 0.2s ease;
  margin-top: 10px;
}

.full {
  width: 100%;
}

.primary {
  background: #7a1f2b;
  color: white;
}

.primary:hover {
  background: #8f2734;
}

.secondary {
  background: #ececec;
  color: #444;
}

.secondary:hover {
  background: #e0e0e0;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.input {
  width: 100%;
  max-width: 100%;
  padding: 12px;
  margin: 15px 0;
  border-radius: 8px;
  border: 1px solid #ddd;
  text-align: center;
  font-size: 1rem;
}

.photo {
  width: 100%;
  max-width: 250px;
  border-radius: 12px;
  margin: 20px auto;
  display: block;
}

.error {
  color: #b00020;
  margin-top: 10px;
  font-size: 0.9rem;
}

/* Fade animation */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Desktop adjustments */
@media (min-width: 768px) {
  .card {
    padding: 40px;
  }

  .title {
    font-size: 2rem;
  }

  .subtitle,
  .cryptic {
    font-size: 1rem;
  }
}
</style>
