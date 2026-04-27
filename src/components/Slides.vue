<script setup>
import { ref, computed, onMounted } from 'vue'

const currentSlide = ref(1)
const totalSlides = ref(16)

// Icônes SVG
const icons = {
  film: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="2" y="2" width="20" height="20" rx="2.18" ry="2.18"></rect><line x1="7" y1="2" x2="7" y2="22"></line><line x1="17" y1="2" x2="17" y2="22"></line><line x1="2" y1="12" x2="22" y2="12"></line><line x1="2" y1="7" x2="7" y2="7"></line><line x1="2" y1="17" x2="7" y2="17"></line><line x1="17" y1="17" x2="22" y2="17"></line><line x1="17" y1="7" x2="22" y2="7"></line></svg>',
  question: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"></circle><path d="M12 16v.01M12 12a2 2 0 0 0-2-2 2 2 0 0 0-2 2c0 1 1 3 2 4s2 3 2 4"></path></svg>',
  brain: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M9.5 2A2.5 2.5 0 0 0 7 4.5v3A2.5 2.5 0 0 0 9.5 10H10a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h.5A2.5 2.5 0 0 0 17 7.5v-3A2.5 2.5 0 0 0 14.5 2M6 20a6 6 0 0 1 12 0"></path></svg>',
  tools: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 1 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"></path></svg>',
  code: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="16 18 22 12 16 6"></polyline><polyline points="8 6 2 12 8 18"></polyline></svg>',
  rocket: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 2L11 13M22 2l-7 20-4-9-9-4 20-7z"></path></svg>',
  database: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><ellipse cx="12" cy="5" rx="9" ry="3"></ellipse><path d="M3 5v14a9 3 0 0 0 18 0V5"></path><path d="M3 12a9 3 0 0 0 18 0"></path></svg>',
  star: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polygon points="12 2 15.09 10.26 23.77 11.44 17.73 17.02 19.09 25.72 12 21.77 4.91 25.72 6.27 17.02 0.23 11.44 8.91 10.26 12 2"></polygon></svg>',
  bulb: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path></svg>',
  target: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="1"></circle><circle cx="12" cy="12" r="5"></circle><circle cx="12" cy="12" r="9"></circle></svg>',
}

const slides = [
  { type: 'title', icon: 'film', title: 'Créer les apps que vous utilisez', subtitle: 'Développeur web' },
  { type: 'question', icon: 'question', title: 'Vous utilisez internet ?' },
  { type: 'image', icon: 'question', title: 'Mais… vous savez comment ?' },
  { type: 'image-text', icon: 'tools', title: 'Sous le capot', text: 'C\'est comme regarder sous le capot d\'une voiture pour voir comment ça marche', image: 'https://images.unsplash.com/photo-1486312338219-ce68d2c6f44d?w=600&h=400&fit=crop' },
  { type: 'profile', icon: 'target', title: 'Andriantsoa A. Manjaka', subtitle: '3eme annee en informatique et developper Freelance chez I-Tsika' },
  { type: 'image-text', icon: 'brain', title: 'Créer des systèmes', text: '<strong>User</strong> → Site → <strong>Réponse</strong>', image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?w=600&h=400&fit=crop' },
  { type: 'image-text', icon: 'code', title: 'Commander en ligne', text: 'Imaginez que vous commandez un burger en ligne...', image: 'https://images.unsplash.com/photo-1568901346375-23c9450c58cd?w=600&h=400&fit=crop' },
  { type: 'image-text-subtitle', icon: 'code', title: 'Ce que vous voyez', subtitle: 'Frontend', text: 'Le menu, les images, les boutons... la partie <strong>jolie</strong>', image: 'https://images.unsplash.com/photo-1561070791-2526d30994b5?w=600&h=400&fit=crop' },
  { type: 'image-text-subtitle', icon: 'database', title: 'Ce que vous ne voyez pas', subtitle: 'Backend', text: 'Le site envoie votre commande <strong>en cuisine</strong>', image: 'https://images.unsplash.com/photo-1558618666-fcd25c85cd64?w=600&h=400&fit=crop' },
  { type: 'image-text-subtitle', icon: 'database', title: 'Où sont les infos', subtitle: 'Base de données', text: 'Tout est stocké quelque part', image: 'https://images.unsplash.com/photo-1597872200969-2b65d56bd16b?w=600&h=400&fit=crop' },
  { type: 'punchline', icon: 'star', title: 'Ça ne marche pas 😅', punchline: 'Tu écris du code → ça ne marche pas<br>Tu corriges → ça ne marche toujours pas<br>Tu touches à rien → et ça marche 🎉', image: 'https://images.unsplash.com/photo-1602941425259-8ae2e3a6d9a9?w=600&h=400&fit=crop' },
  { type: 'demo', icon: 'code', title: 'Démonstration', code: '> npm start\n> Application is running\n> Click here! →\n\n✅ Data saved!' },
  { type: 'skills', icon: 'brain', title: 'Réfléchir • Tester • Recommencer', items: [{ title: 'Réfléchir', text: 'Comprendre' }, { title: 'Tester', text: 'Expérimenter' }, { title: 'Recommencer', text: 'Itérer' }] },
  { type: 'list', icon: 'rocket', title: 'Créer • Liberté • Opportunités', items: ['Créer vos propres idées', 'Travailler de partout', 'Utile partout'] },
  { type: 'start', icon: 'bulb', title: 'Commence aujourd\'hui', image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?w=600&h=400&fit=crop' },
  { type: 'conclusion', icon: 'target', title: 'Construire, pas juste utiliser', punchline: 'Aujourd\'hui, vous utilisez internet.<br>Mais vous pouvez aussi le <strong>construire</strong>.<br>Et ça commence juste par essayer. 🚀', image: 'https://images.unsplash.com/photo-1517694712202-14dd9538aa97?w=600&h=400&fit=crop' }
]

const progress = computed(() => (currentSlide.value / totalSlides.value) * 100)

function changeSlide(n) {
  currentSlide.value += n
  if (currentSlide.value > totalSlides.value) currentSlide.value = totalSlides.value
  if (currentSlide.value < 1) currentSlide.value = 1
}

function handleKeydown(e) {
  if (e.key === 'ArrowLeft') changeSlide(-1)
  if (e.key === 'ArrowRight') changeSlide(1)
  if (e.key === 'f' || e.key === 'F') toggleFullscreen()
}

function toggleFullscreen() {
  const element = document.querySelector('.slides-outer')
  if (!document.fullscreenElement) {
    element.requestFullscreen().catch(err => console.log('Fullscreen error:', err))
  } else {
    document.exitFullscreen()
  }
}

function handleImageError(e, fallback) {
  e.target.src = fallback
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
})
</script>

<template>
  <div class="slides-outer">
    <div class="slides-container">
      <div class="slide-main">
        <!-- SLIDE TITLE -->
        <div v-if="slides[currentSlide - 1].type === 'title'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <p class="subtitle">{{ slides[currentSlide - 1].subtitle }}</p>
          <div class="logos-grid">
            <div class="logo-box" style="background: linear-gradient(135deg, #E4405F, #FD5949)">
              <span>📷</span>Instagram
            </div>
            <div class="logo-box" style="background: linear-gradient(135deg, #000, #333)">
              <span>🎵</span>TikTok
            </div>
            <div class="logo-box" style="background: linear-gradient(135deg, #FF0000, #cc0000)">
              <span>▶️</span>YouTube
            </div>
          </div>
        </div>

        <!-- SLIDE QUESTION -->
        <div v-else-if="slides[currentSlide - 1].type === 'question'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <p style="margin-top: 40px; font-size: 1.2rem; color: #7a8a99;">*À vous de lever la main ! 🙋</p>
        </div>

        <!-- SLIDE IMAGE -->
        <div v-else-if="slides[currentSlide - 1].type === 'image'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
        </div>

        <!-- SLIDE IMAGE-TEXT -->
        <div v-else-if="slides[currentSlide - 1].type === 'image-text'" class="slide-content slide-with-image">
          <div class="text-section">
            <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
            <h1>{{ slides[currentSlide - 1].title }}</h1>
            <p v-html="slides[currentSlide - 1].text"></p>
          </div>
          <div class="image-section">
            <img :src="slides[currentSlide - 1].image" :alt="slides[currentSlide - 1].title"
                 @error="handleImageError($event, 'public/placeholder.jpg')">
          </div>
        </div>

        <!-- SLIDE PROFILE -->
        <div v-else-if="slides[currentSlide - 1].type === 'profile'" class="slide-content">
          <h1>Presentation</h1>
          <div class="profile-card">
            <p><strong>Andriantsoa A. Manjaka</strong></p>
            <p>3eme annee en informatique</p>
            <p>developper Freelance chez I-Tsika</p>
          </div>
        </div>

        <!-- SLIDE IMAGE-TEXT-SUBTITLE -->
        <div v-else-if="slides[currentSlide - 1].type === 'image-text-subtitle'" class="slide-content slide-with-image">
          <div class="text-section">
            <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
            <h1>{{ slides[currentSlide - 1].title }}</h1>
            <h2>{{ slides[currentSlide - 1].subtitle }}</h2>
            <p v-html="slides[currentSlide - 1].text"></p>
          </div>
          <div class="image-section">
            <img :src="slides[currentSlide - 1].image" :alt="slides[currentSlide - 1].title"
                 @error="handleImageError($event, 'public/placeholder.jpg')">
          </div>
        </div>

        <!-- SLIDE PUNCHLINE -->
        <div v-else-if="slides[currentSlide - 1].type === 'punchline'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <div class="punchline-box" v-html="slides[currentSlide - 1].punchline"></div>
        </div>

        <!-- SLIDE DEMO -->
        <div v-else-if="slides[currentSlide - 1].type === 'demo'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <div class="demo-screen">{{ slides[currentSlide - 1].code }}</div>
        </div>

        <!-- SLIDE SKILLS -->
        <div v-else-if="slides[currentSlide - 1].type === 'skills'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <div class="grid-3">
            <div v-for="item in slides[currentSlide - 1].items" :key="item.title" class="skill-card">
              <strong>{{ item.title }}</strong>
              <p>{{ item.text }}</p>
            </div>
          </div>
        </div>

        <!-- SLIDE LIST -->
        <div v-else-if="slides[currentSlide - 1].type === 'list'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <ul class="list-items">
            <li v-for="item in slides[currentSlide - 1].items" :key="item">{{ item }}</li>
          </ul>
        </div>

        <!-- SLIDE START -->
        <div v-else-if="slides[currentSlide - 1].type === 'start'" class="slide-content slide-with-image">
          <div class="text-section">
            <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
            <h1>{{ slides[currentSlide - 1].title }}</h1>
            <div class="links-box">
              <a href="https://code.visualstudio.com/" target="_blank">📝 Visual Studio Code</a>
              <a href="https://github.com/" target="_blank">🐙 GitHub</a>
            </div>
          </div>
          <div class="image-section">
            <img :src="slides[currentSlide - 1].image" :alt="slides[currentSlide - 1].title"
                 @error="handleImageError($event, 'public/placeholder.jpg')">
          </div>
        </div>

        <!-- SLIDE CONCLUSION -->
        <div v-else-if="slides[currentSlide - 1].type === 'conclusion'" class="slide-content">
          <div class="icon-wrapper" v-html="icons[slides[currentSlide - 1].icon]"></div>
          <h1>{{ slides[currentSlide - 1].title }}</h1>
          <div class="punchline-box conclusion-box" v-html="slides[currentSlide - 1].punchline"></div>
        </div>
      </div>

      <div class="controls">
        <button :disabled="currentSlide === 1" @click="changeSlide(-1)" class="btn-nav">← Précédent</button>
        <div class="progress-wrapper">
          <div class="progress-bar">
            <div class="progress-fill" :style="{ width: progress + '%' }"></div>
          </div>
        </div>
        <span class="counter">{{ currentSlide }} / {{ totalSlides }}</span>
        <button @click="toggleFullscreen" class="btn-nav btn-fullscreen" title="Appuyez sur F pour fullscreen">⛶</button>
        <button :disabled="currentSlide === totalSlides" @click="changeSlide(1)" class="btn-nav">Suivant →</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.slides-outer {
  width: 100vw;
  height: 100vh;
  background: #3B82F6;
  overflow: hidden;
  display: flex;
}

.slides-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  background: #f8f9fa;
}

.slide-main {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: clamp(20px, 8vw, 60px) clamp(15px, 10vw, 80px);
  overflow-y: auto;
  overflow-x: hidden;
  background: linear-gradient(to bottom, #ffffff, #f5f7fa);
}

.slide-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  max-width: clamp(280px, 90vw, 900px);
  width: 100%;
  animation: slideIn 0.4s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.slide-with-image {
  flex-direction: row !important;
  gap: 50px;
  align-items: flex-start !important;
  text-align: left;
}

.text-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  text-align: left;
}

.image-section {
  flex: 0 1 400px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.image-section img {
  width: 100%;
  max-height: 400px;
  border-radius: 15px;
  box-shadow: 0 10px 40px rgba(0,0,0,0.15);
  object-fit: cover;
}

.icon-wrapper {
  width: 100px;
  height: 100px;
  background: #3B82F6;
  border-radius: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 30px;
  color: white;
  box-shadow: 0 10px 30px rgba(59, 130, 246, 0.4);
  flex-shrink: 0;
}

.icon-wrapper svg {
  width: 60px;
  height: 60px;
  stroke-width: 1.5;
}

h1 {
  font-size: 3rem;
  color: #1a1a1a;
  margin-bottom: 15px;
  font-weight: 800;
  line-height: 1.2;
}

h2 {
  font-size: 1.8rem;
  color: #3B82F6;
  margin-bottom: 20px;
  font-weight: 600;
}

p {
  font-size: 1.3rem;
  color: #4a5568;
  line-height: 1.8;
  margin-bottom: 15px;
}

.subtitle {
  font-size: 1.4rem !important;
  color: #3B82F6;
  font-weight: 600;
}

.logos-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-top: 40px;
  width: 100%;
  max-width: 500px;
}

.logo-box {
  padding: 20px;
  border-radius: 12px;
  color: white;
  font-weight: 600;
  font-size: 1.1rem;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  box-shadow: 0 5px 20px rgba(0,0,0,0.2);
  transition: transform 0.3s ease;
}

.logo-box:hover {
  transform: translateY(-5px);
}

.logo-box span {
  font-size: 1.5rem;
}

.punchline-box {
  background: rgba(59, 130, 246, 0.1);
  border-left: 5px solid #3B82F6;
  padding: 30px;
  border-radius: 10px;
  font-size: 1.2rem;
  line-height: 2;
  margin-top: 30px;
  color: #2d2d2d;
  font-weight: 500;
}

.profile-card {
  width: min(760px, 100%);
  margin-top: 12px;
  padding: 28px 30px;
  border-radius: 16px;
  background: #eef5ff;
  border: 1px solid #d7e7ff;
  box-shadow: 0 10px 24px rgba(59, 130, 246, 0.08);
  text-align: left;
}

.profile-card p {
  margin: 10px 0;
  font-size: 1.08rem;
  color: #24406b;
}

.conclusion-box {
  border-left-color: #3B82F6;
}

.demo-screen {
  background: #1a1a2e;
  color: #00ff00;
  padding: 25px;
  border-radius: 10px;
  font-family: 'Monaco', 'Courier New', monospace;
  font-size: 0.95rem;
  line-height: 1.8;
  margin-top: 30px;
  white-space: pre-wrap;
  text-align: left;
  min-height: 150px;
  width: 100%;
  max-width: 600px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
}

.grid-3 {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin-top: 40px;
  width: 100%;
}

.skill-card {
  background: #3B82F6;
  color: white;
  padding: 25px;
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(59, 130, 246, 0.1);
  transition: transform 0.3s ease;
}

.skill-card:hover {
  transform: translateY(-10px);
}

.skill-card strong {
  display: block;
  font-size: 1.3rem;
  margin-bottom: 10px;
}

.skill-card p {
  font-size: 1rem;
  color: rgba(255,255,255,0.9);
  margin: 0;
}

.list-items {
  list-style: none;
  text-align: left;
  display: inline-block;
  margin-top: 30px;
}

.list-items li {
  margin: 20px 0;
  padding-left: 40px;
  position: relative;
  font-size: 1.2rem;
  color: #2d2d2d;
  line-height: 1.8;
}

.list-items li:before {
  content: "→";
  position: absolute;
  left: 0;
  color: #3B82F6;
  font-weight: bold;
  font-size: 1.3rem;
}

.links-box {
  margin-top: 30px;
  display: flex;
  flex-direction: column;
  gap: 15px;
  width: 100%;
  max-width: 400px;
}

.links-box a {
  display: inline-block;
  padding: 15px 30px;
  background: #3B82F6;
  color: white;
  text-decoration: none;
  border-radius: 10px;
  font-weight: 600;
  transition: all 0.3s ease;
  box-shadow: 0 5px 15px rgba(59, 130, 246, 0.3);
}

.links-box a:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(59, 130, 246, 0.5);
}

.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 25px 50px;
  background: white;
  border-top: 2px solid #e2e8f0;
  gap: 30px;
  box-shadow: 0 -5px 20px rgba(0,0,0,0.05);
}

.btn-nav {
  padding: 12px 30px;
  background: #3B82F6;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 600;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(59, 130, 246, 0.3);
  white-space: nowrap;
}

.btn-nav:hover:not(:disabled) {
  transform: translateY(-3px);
  box-shadow: 0 6px 25px rgba(59, 130, 246, 0.5);
}

.btn-fullscreen {
  padding: 10px 12px !important;
  font-size: 1.2rem !important;
  min-width: auto;
}

.progress-wrapper {
  flex: 1;
  min-width: 200px;
}

.progress-bar {
  width: 100%;
  height: 6px;
  background: #e2e8f0;
  border-radius: 3px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: #3B82F6;
  transition: width 0.4s ease;
}

.counter {
  font-size: 1.1rem;
  font-weight: 600;
  color: #2d2d2d;
  min-width: 100px;
  text-align: center;
  white-space: nowrap;
}

/* TABLET */
@media (max-width: 1024px) {
  .slide-main {
    padding: 40px 50px;
  }

  h1 {
    font-size: 2.2rem;
  }

  h2 {
    font-size: 1.5rem;
  }

  p {
    font-size: 1.1rem;
  }

  .icon-wrapper {
    width: 80px;
    height: 80px;
  }

  .icon-wrapper svg {
    width: 50px;
    height: 50px;
  }

  .slide-with-image {
    gap: 30px;
  }

  .image-section img {
    max-height: 300px;
  }
}

/* SMALL TABLET / LANDSCAPE PHONE */
@media (max-width: 768px) {
  .slide-main {
    padding: 30px 25px;
    padding-bottom: 140px;
  }

  .slide-content {
    max-width: 95vw;
  }

  .slide-with-image {
    flex-direction: column !important;
    gap: 25px;
    align-items: center !important;
    text-align: center !important;
  }

  .text-section {
    width: 100%;
    align-items: center !important;
    text-align: center !important;
  }

  .image-section {
    width: 100%;
    flex: 0 1 auto;
  }

  .image-section img {
    max-height: 250px;
    max-width: 100%;
  }

  h1 {
    font-size: 1.8rem;
  }

  h2 {
    font-size: 1.3rem;
  }

  p {
    font-size: 1rem;
  }

  .subtitle {
    font-size: 1.1rem !important;
  }

  .icon-wrapper {
    width: 70px;
    height: 70px;
    margin-bottom: 20px;
  }

  .icon-wrapper svg {
    width: 40px;
    height: 40px;
  }

  .logos-grid {
    grid-template-columns: 1fr;
    gap: 15px;
    max-width: 100%;
  }

  .grid-3 {
    grid-template-columns: 1fr;
    gap: 15px;
  }

  .skill-card {
    padding: 20px;
  }

  .punchline-box {
    padding: 20px;
    font-size: 1rem;
    line-height: 1.8;
  }

  .demo-screen {
    font-size: 0.85rem;
    padding: 15px;
    min-height: 120px;
  }

  .profile-card {
    padding: 22px;
  }

  .profile-card p {
    font-size: 0.98rem;
  }

  .controls {
    padding: 15px 15px;
    gap: 10px;
    flex-wrap: wrap;
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 1000;
  }

  .btn-nav {
    padding: 10px 15px;
    font-size: 0.9rem;
    flex: 1;
    min-width: 80px;
  }

  .counter {
    font-size: 0.95rem;
    min-width: 70px;
  }

  .progress-wrapper {
    min-width: 100px;
    order: -1;
    flex: 1 1 100%;
  }

  .list-items li {
    font-size: 1rem;
    margin: 15px 0;
    padding-left: 30px;
  }

  .links-box {
    max-width: 100%;
  }

  .links-box a {
    padding: 12px 20px;
    font-size: 0.95rem;
  }
}

/* MOBILE PORTRAIT (480px and below) */
@media (max-width: 480px) {
  .slides-outer {
    height: 100vh;
  }

  .slide-main {
    padding: 20px 15px;
    padding-bottom: 130px;
    min-height: calc(100vh - 100px);
  }

  .slide-content {
    max-width: 100%;
  }

  h1 {
    font-size: 1.5rem;
    margin-bottom: 12px;
  }

  h2 {
    font-size: 1.1rem;
  }

  p {
    font-size: 0.95rem;
    line-height: 1.6;
  }

  .icon-wrapper {
    width: 60px;
    height: 60px;
    margin-bottom: 15px;
  }

  .icon-wrapper svg {
    width: 35px;
    height: 35px;
  }

  .logos-grid {
    grid-template-columns: 1fr;
    max-width: 100%;
    gap: 10px;
  }

  .logo-box {
    padding: 15px;
    font-size: 0.95rem;
  }

  .punchline-box {
    padding: 15px;
    font-size: 0.9rem;
    line-height: 1.6;
    border-left: 3px solid;
  }

  .demo-screen {
    font-size: 0.75rem;
    padding: 12px;
    min-height: 100px;
    max-width: 100%;
  }

  .profile-card {
    padding: 18px;
  }

  .profile-card p {
    font-size: 0.92rem;
  }

  .grid-3 {
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .skill-card {
    padding: 15px;
  }

  .skill-card strong {
    font-size: 1.1rem;
  }

  .skill-card p {
    font-size: 0.9rem;
  }

  .list-items li {
    font-size: 0.95rem;
    margin: 12px 0;
    padding-left: 25px;
  }

  .controls {
    padding: 12px 10px;
    gap: 8px;
    flex-direction: column;
  }

  .btn-nav {
    padding: 8px 12px;
    font-size: 0.8rem;
    min-width: 60px;
    width: 100%;
  }

  .counter {
    font-size: 0.85rem;
    min-width: auto;
    order: 3;
    width: 100%;
  }

  .progress-wrapper {
    min-width: auto;
    order: 2;
    width: 100%;
  }

  .subtitle {
    font-size: 1rem !important;
  }

  .links-box {
    gap: 10px;
  }

  .links-box a {
    padding: 10px 15px;
    font-size: 0.9rem;
    border-radius: 8px;
  }
}
</style>

