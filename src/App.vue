<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import is1 from './assets/is-1.jpeg'
import is2 from './assets/is-2.jpeg'
import is3 from './assets/is-3.jpeg'
import is4 from './assets/is-4.jpeg'
if ('scrollRestoration' in window.history) {
  window.history.scrollRestoration = 'manual'
}
const scrollToSection = (id) => {
  document.querySelector(id)?.scrollIntoView({
    behavior: 'smooth',
    block: 'start'
  })

  window.history.replaceState(null, '', window.location.pathname)
}
const navScrollRef = ref(null)
const showNavLeft = ref(false)
const showNavRight = ref(true)

const updateNavArrows = () => {
  const el = navScrollRef.value
  if (!el) return

  showNavLeft.value = el.scrollLeft > 8
  showNavRight.value = el.scrollLeft + el.clientWidth < el.scrollWidth - 8
}
const showScrollTop = ref(false)

const handleScrollTopButton = () => {
  showScrollTop.value = window.scrollY > 250
}

const scrollToTop = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth'
  })
}
const scrollNav = (direction) => {
  const el = navScrollRef.value
  if (!el) return

  el.scrollBy({
    left: direction * 160,
    behavior: 'smooth'
  })

  setTimeout(updateNavArrows, 250)
}
// Lightbox
const galleryImages = [is1, is2, is3, is4]
const selectedImage = ref(null)
const selectedImageIndex = ref(0)
const lightboxTouchStartX = ref(null)

const openLightbox = (index) => {
  selectedImageIndex.value = index
  selectedImage.value = galleryImages[index]
}

const closeLightbox = () => {
  selectedImage.value = null
}

const nextImage = () => {
  selectedImageIndex.value = (selectedImageIndex.value + 1) % galleryImages.length
  selectedImage.value = galleryImages[selectedImageIndex.value]
}

const prevImage = () => {
  selectedImageIndex.value = (selectedImageIndex.value - 1 + galleryImages.length) % galleryImages.length
  selectedImage.value = galleryImages[selectedImageIndex.value]
}
const handleLightboxKeydown = (event) => {
  if (!selectedImage.value) return

  if (event.key === 'Escape') {
    closeLightbox()
  }

  if (event.key === 'ArrowRight') {
    nextImage()
  }

  if (event.key === 'ArrowLeft') {
    prevImage()
  }
}

const handleLightboxTouchStart = (event) => {
  lightboxTouchStartX.value = event.changedTouches[0].clientX
}

const handleLightboxTouchEnd = (event) => {
  if (lightboxTouchStartX.value === null) return

  const touchEndX = event.changedTouches[0].clientX
  const swipeDistance = lightboxTouchStartX.value - touchEndX

  if (Math.abs(swipeDistance) > 50) {
    if (swipeDistance > 0) {
      nextImage()
    } else {
      prevImage()
    }
  }

  lightboxTouchStartX.value = null
}
// Form
const name = ref('')
const phone = ref('')
const note = ref('')
const botcheck = ref('')
const isSubmitted = ref(false)
const isLoading = ref(false)
const phoneError = ref('')

// TELEFON NUMARASI MASKESİ 
const handlePhoneInput = (e) => {
  let val = e.target.value.replace(/\D/g, ''); 
  if (val.length > 0 && val[0] !== '0') val = '0' + val; 
  if (val.length > 11) val = val.slice(0, 11); 
  phone.value = val;
  if (phoneError.value) phoneError.value = '';
}

const validatePhone = () => {
  const cleaned = phone.value.replace(/\s/g, '')
  if (cleaned && !/^(05|5)\d{9}$/.test(cleaned)) {
    phoneError.value = 'Geçerli bir Türkiye telefon numarası giriniz (Örn: 05XX).'
  } else {
    phoneError.value = ''
  }
}

// Yorumlar Carousel
const currentReview = ref(0)
const reviews = [
  {
    text: "Yıllardır çözülemeyen tekleme arızasını yarım günde nokta atışı tespit edip çözdüler. Şaşmaz'da dürüst usta bulmak zor, Ali usta sağ olsun.",
    author: "Hakan Ç.",
    vehicle: "2015 VW Golf (Ağır Mekanik)"
  },
  {
    text: "Periyodik bakım için gittim. Malzemeleri gözümün önünde açıp tek tek taktılar. Şeffaf ve temiz esnaflık, kesinlikle tavsiye ederim.",
    author: "Murat K.",
    vehicle: "2018 BMW 320d (Sıvı Bakımları)"
  },
  {
    text: "Alt takımdan gelen o sinir bozucu sesi burası kesti. Gereksiz parça değiştirmeye kalkmıyorlar, neyse onu tamir ediyorlar.",
    author: "Yasin B.",
    vehicle: "2014 Ford Focus (Alt Takım Onarımı)"
  },
  {
    text: "Motorda ciddi bir sorun vardı, başka yerler çok yüksek fiyat kesti. Burası hem daha uygun hem de daha hızlı teslim etti. Teşekkürler.",
    author: "Emre S.",
    vehicle: "2017 Renault Megane (Motor Onarımı)"
  }
]

let reviewTimer = null

const startReviewTimer = () => {
  if (reviewTimer) clearInterval(reviewTimer)

  reviewTimer = setInterval(() => {
    currentReview.value = (currentReview.value + 1) % reviews.length
  }, 4500)
}

const goToReview = (index) => {
  currentReview.value = index
  startReviewTimer()
}

const nextReview = () => {
  currentReview.value = (currentReview.value + 1) % reviews.length
  startReviewTimer()
}

const prevReview = () => {
  currentReview.value = (currentReview.value - 1 + reviews.length) % reviews.length
  startReviewTimer()
}
// İşletme Saatleri ve Açık/Kapalı Kontrolü
const isOpen = ref(false)

const checkIsOpen = () => {
  const now = new Date()

  const turkeyTime = new Date(
    now.toLocaleString('en-US', { timeZone: 'Europe/Istanbul' })
  )

  const day = turkeyTime.getDay()
  const hours = turkeyTime.getHours()
  const minutes = turkeyTime.getMinutes()
  const timeInMinutes = hours * 60 + minutes

  const openTime = 8 * 60 + 30
  const closeTime = 19 * 60

  isOpen.value = day !== 0 && timeInMinutes >= openTime && timeInMinutes < closeTime
}
// Scroll reveal observer
let observer = null
let timeCheckInterval = null

const handleFormSubmit = async () => {
  validatePhone()
  if (phoneError.value) return

  if (botcheck.value) {
    isSubmitted.value = true
    return
  }
  if (!name.value || !phone.value) return

  isLoading.value = true

  try {
    const response = await fetch('https://api.web3forms.com/submit', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
      body: JSON.stringify({
        access_key: '2d42f4b9-d839-403c-ae63-a5dabcc26796',
        name: name.value,
        phone: phone.value,
        message: note.value || 'Müşteri ekstra not bırakmadı.',
        subject: 'Özgehan Otomotiv - Siteden Yeni Araç Talebi'
      })
    })
    const result = await response.json()
    if (result.success) {
      isSubmitted.value = true
      setTimeout(() => {
        name.value = ''
        phone.value = ''
        note.value = ''
        isSubmitted.value = false
      }, 5000)
    } else {
      alert('Gönderim başarısız oldu, lütfen geçici bir süre için WhatsApp üzerinden ulaşın.')
    }
  } catch (error) {
    console.error(error)
    alert('Bir bağlantı hatası oluştu!')
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  startReviewTimer()
  checkIsOpen()
  timeCheckInterval = setInterval(checkIsOpen, 60000)
  window.addEventListener('keydown', handleLightboxKeydown)
    window.addEventListener('scroll', handleScrollTopButton)
    setTimeout(updateNavArrows, 100)
  window.addEventListener('resize', updateNavArrows)

  window.history.replaceState(null, '', window.location.pathname)
  window.scrollTo(0, 0)

  // Scroll reveal
  const els = document.querySelectorAll('.reveal')
  observer = new IntersectionObserver((entries) => {
    entries.forEach(el => {
      if (el.isIntersecting) {
        el.target.classList.add('revealed')
        observer.unobserve(el.target)
      }
    })
  }, { threshold: 0 })
  els.forEach(el => observer.observe(el))
})

onUnmounted(() => {
  if (reviewTimer) clearInterval(reviewTimer)
  if (timeCheckInterval) clearInterval(timeCheckInterval)
  window.removeEventListener('keydown', handleLightboxKeydown)
window.removeEventListener('scroll', handleScrollTopButton)
window.removeEventListener('resize', updateNavArrows)
  if (observer) observer.disconnect()
})
</script>

<template>
  <div class="min-h-screen w-full bg-slate-100 text-slate-900 font-sans relative overflow-x-hidden pb-16 md:pb-0">

    <div class="sticky top-0 z-50 bg-slate-950">
      <header class="bg-slate-950 text-white px-3 py-3 md:px-5 md:py-5 shadow-2xl">
        <div class="max-w-7xl mx-auto flex justify-center md:justify-between items-center">
    
    <div class="flex flex-col md:flex-row md:items-center justify-center gap-0.5 md:gap-3">
      <div class="flex items-center justify-center gap-2">
        <div class="hidden md:flex md:w-14 md:h-14 items-center justify-center shrink-0">
  <img
    src="/logo.png"
    alt="Özgehan Otomotiv Logo"
    class="w-full h-full object-contain scale-150"
  />
</div>

        <div class="font-display text-[23px] sm:text-2xl md:text-3xl font-black uppercase tracking-tight whitespace-nowrap leading-none md:scale-y-125 origin-left">
          <span class="text-white">Özgehan</span>
          <span class="text-slate-400 ml-1 font-black">Otomotiv</span>
        </div>
      </div>

      <div class="text-[10px] md:text-xs text-amber-400 font-bold uppercase tracking-[0.22em] text-center md:text-left mt-0.5 md:mt-0 md:ml-1 md:pt-1">
        1974’TEN BERİ
      </div>
    </div>

    <div class="hidden md:flex items-center gap-4">
      <a href="tel:05326213429" class="font-bold text-base bg-red-700 px-5 py-2.5 rounded-full hover:bg-red-600 transition-all flex items-center gap-2 shadow-inner whitespace-nowrap hover:scale-105 active:scale-95">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.94.725l.548 2.2a1 1 0 01-.321.988l-1.305.98a10.582 10.582 0 004.872 4.872l.98-1.305a1 1 0 01.988-.321l2.2.548a1 1 0 01.725.94V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"/></svg>
        0532 621 34 29
      </a>
    </div>

        </div>
      </header>
      <nav class="bg-slate-900">
        <div class="max-w-7xl mx-auto px-3 md:px-5 py-2 relative">
          <div class="relative mx-auto md:w-fit">
            <div
              ref="navScrollRef"
              @scroll="updateNavArrows"
              class="flex items-center gap-2 md:justify-center overflow-x-auto no-scrollbar rounded-xl md:rounded-full bg-slate-950/70 border border-slate-700 px-2 py-1.5 pl-2 pr-8 md:px-3"
            >
              <button type="button" @click="scrollToSection('#anasayfa')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Anasayfa
              </button>

              <button type="button" @click="scrollToSection('#hizmetler')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Hizmetler
              </button>

              <button type="button" @click="scrollToSection('#farkimiz')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Farkımız
              </button>

              <button type="button" @click="scrollToSection('#hakkimizda')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Hakkımızda
              </button>

              <button type="button" @click="scrollToSection('#atolye')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Atölye
              </button>

              <button type="button" @click="scrollToSection('#yorumlar')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Yorumlar
              </button>

              <button type="button" @click="scrollToSection('#randevu')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                Randevu
              </button>

              <button type="button" @click="scrollToSection('#iletisim')" class="shrink-0 px-3 py-2 rounded-full text-[11px] md:text-sm font-bold text-slate-300 hover:text-white hover:bg-slate-800 transition-colors cursor-pointer">
                İletişim
              </button>
            </div>

            <button
              v-show="showNavRight"
              type="button"
              @click="scrollNav(1)"
              aria-label="Menüyü sağa kaydır"
              class="md:hidden absolute top-0 right-0 h-full w-10 z-20 bg-gradient-to-l from-slate-950 via-slate-950/90 to-transparent flex items-center justify-end pr-2 text-white text-2xl font-black">
              ›
            </button>

            <button
              v-show="showNavLeft"
              type="button"
              @click="scrollNav(-1)"
              aria-label="Menüyü sola kaydır"
              class="md:hidden absolute top-0 left-0 h-full w-10 z-20 bg-gradient-to-r from-slate-950 via-slate-950/90 to-transparent flex items-center justify-start pl-2 text-white text-2xl font-black">
              ‹
            </button>
          </div>
        </div>
      </nav>
    </div>

<main id="anasayfa" class="bg-white relative overflow-hidden scroll-mt-32 md:scroll-mt-40">
  <div class="absolute top-0 right-0 w-1/2 h-full bg-slate-50 clip-diagonal hidden md:block pointer-events-none" style="clip-path: polygon(15% 0, 100% 0, 100% 100%, 0% 100%)"></div>

  <div class="max-w-7xl mx-auto px-4 py-12 md:py-24 grid grid-cols-1 md:grid-cols-2 gap-10 md:gap-16 items-center relative z-10">
    <div class="text-center md:text-left flex flex-col items-center md:items-start">
      
      <h1 class="soft-fade-up text-4xl sm:text-5xl md:text-6xl lg:text-7xl font-extrabold text-slate-950 leading-[1.08] mb-5 md:mb-6 tracking-tight">
        Aracınız<br/>
        <span class="text-red-700 relative inline-block font-bold">
          Emin Ellerde
          <span class="absolute -bottom-1 left-0 w-full h-1 bg-red-100 rounded-full"></span>
        </span>
      </h1>

          <p class="soft-fade-up-delay text-lg md:text-xl text-slate-600 mb-8 md:mb-10 leading-relaxed max-w-lg font-medium">
            Profesyonel motor mekanik, periyodik bakım ve arıza tespitinde yılların sanayi tecrübesiyle <strong class="text-slate-900">dürüst ve garantili hizmet.</strong>
          </p>

          <div class="flex flex-col sm:flex-row gap-4 w-full sm:w-auto">
            <a href="https://wa.me/905326213429?text=Merhaba,%20%C3%96zgehan%20Otomotiv%20sitenizden%20ula%C5%9F%C4%B1yorum."
               target="_blank"
               class="inline-flex items-center justify-center gap-3 px-7 py-4 text-base font-bold text-white transition-all bg-emerald-600 rounded-xl shadow-lg hover:bg-emerald-700 hover:-translate-y-1 active:translate-y-0">
              <svg class="w-5 h-5" viewBox="0 0 24 24" fill="currentColor"><path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413Z"/></svg>
              WhatsApp'tan Ulaşın
            </a>
            <a href="https://maps.app.goo.gl/hC9RY8HX29PepBwY6"
               target="_blank"
               class="inline-flex items-center justify-center gap-3 px-7 py-4 text-base font-bold text-slate-700 transition-all bg-white rounded-xl border-2 border-slate-200 hover:border-slate-400 hover:bg-slate-50 hover:-translate-y-1 active:translate-y-0">
              📍 Yol Tarifi Al
            </a>
          </div>
        </div>

        <div class="w-full h-[260px] sm:h-[360px] md:h-[460px] bg-slate-200 rounded-3xl shadow-2xl overflow-hidden border-4 md:border-8 border-slate-100 mt-4 md:mt-0 relative group">
          <img
  src="/logo-full.png"
  alt="Özgehan Otomotiv"
  loading="eager"
  fetchpriority="high"
  class="w-full h-full object-contain p-6 md:p-10 bg-white"
/>
          <div
  class="absolute top-4 right-4 inline-flex items-center gap-2 text-xs font-bold rounded-full px-3 py-2 shadow-lg backdrop-blur border"
  :class="isOpen ? 'text-emerald-700 bg-white/90 border-emerald-200' : 'text-red-700 bg-white/90 border-red-200'"
>
  <span
    :class="isOpen ? 'w-2 h-2 bg-emerald-500 rounded-full animate-pulse' : 'w-2 h-2 bg-red-500 rounded-full'"
  ></span>
  {{ isOpen ? 'Şu an açık' : 'Şu an kapalı' }}
</div>
        </div>
      </div>
    </main>
<section class="bg-slate-950 border-y border-slate-800 py-4 md:py-5">
  <div class="max-w-7xl mx-auto px-4">
    <div class="grid grid-cols-3 gap-3 text-center">
      <div>
        <div class="text-amber-400 font-black text-sm md:text-xl tracking-tight">1974’ten Beri</div>
        <div class="text-slate-500 text-[10px] md:text-xs font-bold uppercase tracking-widest mt-1">Sanayi Tecrübesi</div>
      </div>

      <div class="border-x border-slate-800">
        <div class="text-white font-black text-sm md:text-xl tracking-tight">3 Uzman Usta</div>
        <div class="text-slate-500 text-[10px] md:text-xs font-bold uppercase tracking-widest mt-1">Profesyonel Ekip</div>
      </div>

      <div>
        <div class="text-white font-black text-sm md:text-xl tracking-tight">Tüm Markalar</div>
        <div class="text-slate-500 text-[10px] md:text-xs font-bold uppercase tracking-widest mt-1">Motor Mekanik</div>
      </div>
    </div>
  </div>
</section>
 <section id="hizmetler" class="py-12 md:py-24 bg-slate-100 border-b border-slate-200 scroll-mt-32 md:scroll-mt-40">
  <div class="max-w-7xl mx-auto px-4">
    <div class="text-center mb-8 md:mb-12 reveal">
      <span class="text-xs font-bold tracking-widest uppercase text-red-600 mb-3 block">Uzman Kadro</span>
      <h2 class="font-display text-2xl md:text-5xl font-bold text-slate-950 mb-3">Hizmetlerimiz</h2>
      <p class="text-slate-500 max-w-xl mx-auto text-sm md:text-base">Tüm araç markaları ve modellerinde profesyonel servis hizmeti sunuyoruz.</p>
    </div>

    <div class="grid grid-cols-2 md:grid-cols-4 gap-3 md:gap-6">
      <div class="reveal reveal-delay-1 p-3 md:p-6 bg-white rounded-2xl md:rounded-3xl shadow-sm border border-slate-200 hover:border-red-400 hover:shadow-lg transition-all duration-300 group">
        <div class="w-9 h-9 md:w-12 md:h-12 bg-slate-50 rounded-2xl flex items-center justify-center mb-4 md:mb-5 border border-slate-200 group-hover:bg-red-50 group-hover:border-red-200 transition-colors shadow-inner">
          <svg class="w-5 h-5 md:w-6 md:h-6 text-slate-600 group-hover:text-red-700 transition-colors" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M11.42 15.17L17.25 21A2.652 2.652 0 0021 17.25l-5.877-5.877M11.42 15.17l2.496-3.03c.317-.384.74-.626 1.208-.766M11.42 15.17l-4.655 5.653a2.548 2.548 0 11-3.586-3.586l6.837-5.63m5.108-.233c.55-.164 1.163-.188 1.743-.14a4.5 4.5 0 004.486-6.336l-3.276 3.277a3.004 3.004 0 01-2.25-2.25l3.276-3.276a4.5 4.5 0 00-6.336 4.486c.091 1.076-.071 2.264-.904 2.95l-.102.085m-1.745 1.437L5.909 7.5H4.5L2.25 3.75l1.5-1.5L7.5 4.5v1.409l4.26 4.26m-1.745 1.437l1.745-1.437m6.615 8.206L15.75 15.75M4.867 19.125h.008v.008h-.008v-.008z"/></svg>
        </div>

        <div class="mb-2 md:mb-3">
          <h3 class="text-[15px] md:text-lg font-bold text-slate-950">Motor Mekanik</h3>
          <span class="inline-block mt-1 text-[10px] md:text-xs font-semibold text-red-600 bg-red-50 border border-red-100 px-2 py-1 rounded-full">Ağır Servis</span>
        </div>

        <p class="text-slate-500 leading-snug md:leading-relaxed mb-2 md:mb-4 text-[11px] md:text-sm">Motor, şanzıman, alt takım ve ağır bakım işlemleri titizlikle yürütülür.</p>

        <ul class="space-y-1 md:space-y-1.5 text-[10px] md:text-xs text-slate-600">
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-red-500 rounded-full shrink-0"></span>Motor Revizyonu</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-red-500 rounded-full shrink-0"></span>Şanzıman Onarımı</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-red-500 rounded-full shrink-0"></span>Alt Takım</li>
        </ul>
      </div>

      <div class="reveal reveal-delay-2 p-3 md:p-6 bg-white rounded-2xl md:rounded-3xl shadow-sm border border-slate-200 hover:border-red-400 hover:shadow-lg transition-all duration-300 group">
        <div class="w-9 h-9 md:w-12 md:h-12 bg-slate-50 rounded-2xl flex items-center justify-center mb-4 md:mb-5 border border-slate-200 group-hover:bg-red-50 group-hover:border-red-200 transition-colors shadow-inner">
          <svg class="w-5 h-5 md:w-6 md:h-6 text-slate-600 group-hover:text-red-700 transition-colors" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 2.25c-1.43 2.861-6 8.586-6 11.25a6 6 0 1012 0c0-2.664-4.57-8.389-6-11.25z"/></svg>
        </div>

        <div class="mb-2 md:mb-3">
          <h3 class="text-[15px] md:text-lg font-bold text-slate-950">Periyodik Bakım</h3>
          <span class="inline-block mt-1 text-[10px] md:text-xs font-semibold text-emerald-600 bg-emerald-50 border border-emerald-100 px-2 py-1 rounded-full">Aynı Gün</span>
        </div>

        <p class="text-slate-500 leading-snug md:leading-relaxed mb-2 md:mb-4 text-[11px] md:text-sm">Yağ, filtre ve sıvı bakımları eksiksiz şekilde yapılır.</p>

        <ul class="space-y-1 md:space-y-1.5 text-[10px] md:text-xs text-slate-600">
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-emerald-500 rounded-full shrink-0"></span>Motor Yağı</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-emerald-500 rounded-full shrink-0"></span>Filtre Değişimi</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-emerald-500 rounded-full shrink-0"></span>Triger Seti</li>
        </ul>
      </div>

      <div class="reveal reveal-delay-3 p-3 md:p-6 bg-white rounded-2xl md:rounded-3xl shadow-sm border border-slate-200 hover:border-red-400 hover:shadow-lg transition-all duration-300 group">
        <div class="w-9 h-9 md:w-12 md:h-12 bg-slate-50 rounded-2xl flex items-center justify-center mb-4 md:mb-5 border border-slate-200 group-hover:bg-red-50 group-hover:border-red-200 transition-colors shadow-inner">
          <svg class="w-5 h-5 md:w-6 md:h-6 text-slate-600 group-hover:text-red-700 transition-colors" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/></svg>
        </div>

        <div class="mb-2 md:mb-3">
          <h3 class="text-[15px] md:text-lg font-bold text-slate-950">Arıza Tespiti</h3>
          <span class="inline-block mt-1 text-[10px] md:text-xs font-semibold text-blue-600 bg-blue-50 border border-blue-100 px-2 py-1 rounded-full">Dijital Tarama</span>
        </div>

        <p class="text-slate-500 leading-snug md:leading-relaxed mb-2 md:mb-4 text-[11px] md:text-sm">Aracın beyni taranarak nokta atışı onarım yapılır.</p>

        <ul class="space-y-1 md:space-y-1.5 text-[10px] md:text-xs text-slate-600">
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-blue-500 rounded-full shrink-0"></span>Motor Lambası</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-blue-500 rounded-full shrink-0"></span>Elektronik Tarama</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-blue-500 rounded-full shrink-0"></span>Arıza Analizi</li>
        </ul>
      </div>

      <div class="reveal reveal-delay-4 p-3 md:p-6 bg-white rounded-2xl md:rounded-3xl shadow-sm border border-slate-200 hover:border-red-400 hover:shadow-lg transition-all duration-300 group">
        <div class="w-9 h-9 md:w-12 md:h-12 bg-slate-50 rounded-2xl flex items-center justify-center mb-4 md:mb-5 border border-slate-200 group-hover:bg-red-50 group-hover:border-red-200 transition-colors shadow-inner">
          <svg class="w-5 h-5 md:w-6 md:h-6 text-slate-600 group-hover:text-red-700 transition-colors" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" d="M3 13h2l2-7h10l2 7h2M5 13v5a1 1 0 001 1h1a1 1 0 001-1v-1h8v1a1 1 0 001 1h1a1 1 0 001-1v-5M7 13h10M8 9h8" />
          </svg>
        </div>

        <div class="mb-2 md:mb-3">
          <h3 class="text-[15px] md:text-lg font-bold text-slate-950">Fren & Süspansiyon</h3>
          <span class="inline-block mt-1 text-[10px] md:text-xs font-semibold text-red-600 bg-red-50 border border-red-100 px-2 py-1 rounded-full">Güvenlik</span>
        </div>

        <p class="text-slate-500 leading-snug md:leading-relaxed mb-2 md:mb-4 text-[11px] md:text-sm">Fren, amortisör ve süspansiyon kontrolleri yapılır.</p>

        <ul class="space-y-1 md:space-y-1.5 text-[10px] md:text-xs text-slate-600">
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-red-500 rounded-full shrink-0"></span>Fren Balata</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-red-500 rounded-full shrink-0"></span>Amortisör</li>
          <li class="flex items-center gap-2"><span class="w-1.5 h-1.5 bg-red-500 rounded-full shrink-0"></span>Süspansiyon</li>
        </ul>
      </div>
    </div>
  </div>
</section>np
    <section id="farkimiz" class="py-12 md:py-20 bg-slate-900 border-y border-slate-800 scroll-mt-32 md:scroll-mt-40">
      <div class="max-w-7xl mx-auto px-4">
        <div class="text-center mb-8 md:mb-12 reveal">
          <span class="text-xs font-bold tracking-widest uppercase text-red-500 mb-3 block">Farkımız</span>
          <h2 class="font-display text-2xl md:text-4xl font-bold text-white mb-3 md:mb-4">Neden Bizi Tercih Etmelisiniz?</h2>
          <p class="text-slate-400 max-w-2xl mx-auto text-sm md:text-base">Aracınızı güvenle teslim edebilmeniz için kaliteden ve şeffaflıktan ödün vermiyoruz.</p>
        </div>

        <div class="grid grid-cols-2 md:grid-cols-4 gap-3 md:gap-5">
          <div class="reveal reveal-delay-1 bg-slate-950 p-4 md:p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 hover:-translate-y-1 transition-all duration-300">
            <div class="w-10 h-10 md:w-12 md:h-12 bg-red-700/10 border border-red-700/30 rounded-2xl flex items-center justify-center mx-auto mb-3 md:mb-4">
              <svg class="w-5 h-5 md:w-6 md:h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
            </div>
            <h3 class="text-white font-bold text-sm md:text-base mb-1.5 md:mb-2">Dürüst Fiyatlandırma</h3>
            <p class="text-slate-400 text-xs md:text-sm leading-relaxed">Aracınıza yalnızca gerçekten ihtiyaç duyulan işlemler uygulanır.</p>
          </div>

          <div class="reveal reveal-delay-2 bg-slate-950 p-4 md:p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 hover:-translate-y-1 transition-all duration-300">
            <div class="w-10 h-10 md:w-12 md:h-12 bg-red-700/10 border border-red-700/30 rounded-2xl flex items-center justify-center mx-auto mb-3 md:mb-4">
              <svg class="w-5 h-5 md:w-6 md:h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"/></svg>
            </div>
            <h3 class="text-white font-bold text-sm md:text-base mb-1.5 md:mb-2">Kaliteli Parça</h3>
            <p class="text-slate-400 text-xs md:text-sm leading-relaxed">Orijinal ve OEM onaylı yedek parçalarla aracınızın ömrü uzatılır.</p>
          </div>

          <div class="reveal reveal-delay-3 bg-slate-950 p-4 md:p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 hover:-translate-y-1 transition-all duration-300">
            <div class="w-10 h-10 md:w-12 md:h-12 bg-red-700/10 border border-red-700/30 rounded-2xl flex items-center justify-center mx-auto mb-3 md:mb-4">
              <svg class="w-5 h-5 md:w-6 md:h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z"/></svg>
            </div>
            <h3 class="text-white font-bold text-sm md:text-base mb-1.5 md:mb-2">Garantili İşçilik</h3>
            <p class="text-slate-400 text-xs md:text-sm leading-relaxed">Her mekanik işlem ve parça değişimi usta garantimiz altındadır.</p>
          </div>

          <div class="reveal reveal-delay-4 bg-slate-950 p-4 md:p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 hover:-translate-y-1 transition-all duration-300">
            <div class="w-10 h-10 md:w-12 md:h-12 bg-red-700/10 border border-red-700/30 rounded-2xl flex items-center justify-center mx-auto mb-3 md:mb-4">
              <svg class="w-5 h-5 md:w-6 md:h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
            </div>
            <h3 class="text-white font-bold text-sm md:text-base mb-1.5 md:mb-2">Hızlı Teslimat</h3>
            <p class="text-slate-400 text-xs md:text-sm leading-relaxed">Zamanınızın değerini biliyor, aracınızı söz verilen sürede teslim ediyoruz.</p>
          </div>
        </div>
      </div>
    </section>

   

    <section id="hakkimizda" class="py-16 md:py-20 bg-white border-b border-slate-200 scroll-mt-32 md:scroll-mt-40">
      <div class="max-w-4xl mx-auto px-4">
        <div class="flex flex-col md:flex-row items-center gap-8 md:gap-12 reveal">
          <div class="w-24 h-24 md:w-28 md:h-28 bg-slate-900 text-white rounded-full flex items-center justify-center border-4 border-slate-100 shadow-xl shrink-0">
            <svg class="w-12 md:w-14 h-12 md:h-14 text-red-600" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"/></svg>
          </div>
          <div class="text-center md:text-left">
            <span class="text-xs font-bold tracking-widest uppercase text-red-600 mb-2 block">Biz Kimiz?</span>
            <h2 class="font-display text-3xl md:text-4xl font-bold text-slate-950 mb-4">Hakkımızda</h2>
            <p class="text-lg text-slate-600 leading-relaxed">Özgehan Otomotiv, Ankara <strong class="text-slate-900">Mega Şaşmaz'da</strong> uzun yıllara dayanan sektörel tecrübe ve çekirdekten yetişme profesyonel usta kadrosuyla hizmet vermektedir. Kurulduğumuz günden bu yana temel ilkemiz <strong class="text-slate-900">müşteri memnuniyetidir</strong>.</p>
          </div>
        </div>
      </div>
    </section>

    <section id="atolye" class="py-16 bg-slate-100 border-b border-slate-200 scroll-mt-32 md:scroll-mt-40">
      <div class="max-w-7xl mx-auto px-4">
        <div class="text-center mb-10 reveal">
          <span class="text-xs font-bold tracking-widest uppercase text-red-600 mb-3 block">İş Kalitemiz</span>
          <h2 class="font-display text-3xl md:text-4xl font-bold text-slate-950">Ustalığımız ve Atölyemiz</h2>
        </div>
        <div class="grid grid-cols-2 md:grid-cols-4 gap-3 md:gap-6">
          <div v-for="(img, i) in galleryImages" :key="i"
     class="reveal aspect-video bg-slate-900 rounded-2xl overflow-hidden shadow-lg border-2 border-white hover:border-red-400 hover:shadow-xl transition-all cursor-pointer group"
     :class="`reveal-delay-${Math.min(i + 1, 4)}`"
     @click="openLightbox(i)">
            <div class="relative w-full h-full overflow-hidden">
              <img :src="img" loading="lazy" :alt="`Atölye Fotoğrafı ${i+1}`" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500"/>
              <div class="absolute inset-0 bg-slate-950/0 group-hover:bg-slate-950/20 transition-colors flex items-center justify-center">
                <div class="opacity-0 group-hover:opacity-100 transition-opacity bg-white/90 rounded-full px-4 py-2 text-slate-900 text-sm font-bold">
                  🔍 Büyüt
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section id="yorumlar" class="py-16 bg-white border-b border-slate-100 scroll-mt-32 md:scroll-mt-40">
      <div class="max-w-4xl mx-auto px-4">
        <div class="text-center mb-10 reveal">
          <span class="text-xs font-bold tracking-widest uppercase text-red-600 mb-3 block">Referanslar</span>
          <h2 class="font-display text-3xl md:text-4xl font-bold text-slate-950">Müşteri Deneyimleri</h2>
        </div>

        <div class="relative bg-slate-50 rounded-3xl p-8 md:p-12 border border-slate-200 shadow-sm overflow-hidden min-h-[240px] md:min-h-[220px] reveal">
          <div class="absolute top-6 left-8 text-red-100 font-serif text-8xl leading-none select-none pointer-events-none font-black">"</div>
          <button
  type="button"
  @click="prevReview"
  class="absolute left-3 md:left-4 top-1/2 -translate-y-1/2 w-9 h-9 md:w-10 md:h-10 rounded-full bg-white border border-slate-200 text-slate-700 shadow hover:bg-slate-100 hover:text-red-700 transition-all z-20 flex items-center justify-center text-2xl leading-none cursor-pointer">
  ‹
</button>
<button
  type="button"
  @click="nextReview"
  class="absolute right-3 md:right-4 top-1/2 -translate-y-1/2 w-9 h-9 md:w-10 md:h-10 rounded-full bg-white border border-slate-200 text-slate-700 shadow hover:bg-slate-100 hover:text-red-700 transition-all z-20 flex items-center justify-center text-2xl leading-none cursor-pointer">
  ›
</button>

          <Transition name="review" mode="out-in">
            <div :key="currentReview">
              <p class="text-base md:text-xl text-slate-700 leading-relaxed italic mb-6 relative z-10">
                "{{ reviews[currentReview].text }}"
              </p>
              <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-slate-200 rounded-full flex items-center justify-center text-slate-600 font-bold text-sm">
                  {{ reviews[currentReview].author.charAt(0) }}
                </div>
                <div>
                  <div class="font-bold text-slate-900">{{ reviews[currentReview].author }}</div>
                  <div class="text-slate-500 text-sm">{{ reviews[currentReview].vehicle }}</div>
                </div>
                <div class="ml-auto text-yellow-400 text-lg">★★★★★</div>
              </div>
            </div>
          </Transition>
        </div>

        <div class="flex justify-center gap-2 mt-5">
          <button v-for="(_, i) in reviews" :key="i"
        @click="goToReview(i)"
        class="w-2.5 h-2.5 rounded-full transition-all"
        :class="i === currentReview ? 'bg-red-600 w-6' : 'bg-slate-300 hover:bg-slate-400'"></button>
        </div>
      </div>
    </section>

    <section id="randevu" class="py-16 bg-slate-900 text-white border-t border-slate-800 scroll-mt-32 md:scroll-mt-40">
      <div class="max-w-xl mx-auto px-4">
        <div class="text-center mb-8 reveal">
          <span class="text-xs font-bold tracking-widest uppercase text-red-400 mb-3 block">Randevu & Bilgi</span>
          <h2 class="font-display text-3xl md:text-4xl font-bold text-white mb-2">Sizi Arayalım</h2>
          <p class="text-slate-400">Numaranızı bırakın, ustanız en kısa sürede dönüş sağlasın.</p>
        </div>

        <div class="bg-slate-950 p-6 md:p-8 rounded-3xl border border-slate-800 shadow-xl reveal">
          <input v-model="botcheck" type="text" name="botcheck" class="hidden" style="display:none !important;" autocomplete="off"/>

          <div v-if="!isSubmitted" class="space-y-4">
            <div>
              <label class="block text-sm font-semibold text-slate-300 mb-1.5">Adınız Soyadınız <span class="text-red-500">*</span></label>
              <input v-model="name" type="text" required placeholder="Ahmet Yılmaz"
                     class="w-full bg-slate-900 border border-slate-700 rounded-xl px-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-red-600 transition-colors text-sm"/>
            </div>
            <div>
              <label class="block text-sm font-semibold text-slate-300 mb-1.5">Telefon Numaranız <span class="text-red-500">*</span></label>
              <input v-model="phone" @input="handlePhoneInput" @blur="validatePhone" type="tel" required placeholder="05XX XXX XX XX"
                     class="w-full bg-slate-900 border rounded-xl px-4 py-3 text-white placeholder-slate-500 focus:outline-none transition-colors text-sm"
                     :class="phoneError ? 'border-red-500 focus:border-red-500' : 'border-slate-700 focus:border-red-600'"/>
              <p v-if="phoneError" class="mt-1.5 text-xs text-red-400 flex items-center gap-1">
                <svg class="w-3.5 h-3.5 shrink-0" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z" clip-rule="evenodd"/></svg>
                {{ phoneError }}
              </p>
            </div>
            <div>
              <label class="block text-sm font-semibold text-slate-300 mb-1.5">Araç Modeli / Şikayetiniz <span class="text-slate-500 font-normal">(Opsiyonel)</span></label>
              <textarea v-model="note" rows="3" placeholder="Örn: 2016 Passat Yağ Bakımı ve Ön Takım Sesi"
                        class="w-full bg-slate-900 border border-slate-700 rounded-xl px-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-red-600 transition-colors resize-none text-sm"></textarea>
            </div>
            <button
              type="button"
              @click="handleFormSubmit"
              :disabled="isLoading"
              class="w-full bg-red-700 hover:bg-red-600 active:bg-red-800 disabled:opacity-60 disabled:cursor-not-allowed text-white font-bold py-3.5 rounded-xl transition-all shadow-lg flex items-center justify-center gap-2 hover:scale-[1.02] active:scale-100">
              <span v-if="isLoading" class="flex items-center gap-2">
                <svg class="animate-spin h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"/>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"/>
                </svg>
                Gönderiliyor...
              </span>
              <span v-else class="flex items-center gap-2">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.94.725l.548 2.2a1 1 0 01-.321.988l-1.305.98a10.582 10.582 0 004.872 4.872l.98-1.305a1 1 0 01.988-.321l2.2.548a1 1 0 01.725.94V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"/></svg>
                Beni Arayın
              </span>
            </button>
          </div>

          <div v-else class="text-center py-10 bg-emerald-950/20 border border-emerald-800/50 rounded-2xl">
            <div class="w-14 h-14 bg-emerald-500/10 border border-emerald-500/30 rounded-full flex items-center justify-center mx-auto mb-3">
              <svg class="w-7 h-7 text-emerald-500" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5"/></svg>
            </div>
            <h3 class="text-xl font-bold text-white mb-1">Talebiniz Ustaya İletildi</h3>
            <p class="text-emerald-300/70 text-sm">En kısa sürede arayıp detaylı bilgi vereceğiz.</p>
          </div>
        </div>
      </div>
    </section>
<section id="iletisim" class="bg-slate-950 text-white py-14 md:py-20 border-t border-slate-800 relative overflow-hidden scroll-mt-32 md:scroll-mt-40">
  <div class="absolute inset-0 bg-[radial-gradient(circle_at_top_left,rgba(185,28,28,0.16),transparent_35%)] pointer-events-none"></div>

  <div class="max-w-7xl mx-auto px-4 relative z-10">
    <div class="grid grid-cols-1 md:grid-cols-2 gap-8 md:gap-12 items-stretch">
      
      <div class="reveal">
        <span class="text-xs font-bold tracking-widest uppercase text-amber-400 mb-3 block">
          Ulaşın
        </span>

        <h2 class="font-display text-3xl md:text-5xl font-black tracking-tight mb-4">
          İletişim Bilgileri
        </h2>

        <p class="text-slate-400 text-sm md:text-base leading-relaxed mb-7 max-w-xl">
          Randevu, yol tarifi veya araç arızası hakkında bilgi almak için bize doğrudan ulaşabilirsiniz.
        </p>

        <div class="space-y-4">
          
          <div class="group flex gap-4 p-5 bg-slate-900/80 border border-slate-800 rounded-3xl hover:border-red-800/70 transition-all">
            <div class="w-12 h-12 rounded-2xl bg-slate-800 border border-slate-700 flex items-center justify-center shrink-0 text-xl group-hover:bg-red-900/30 transition-colors">
              📍
            </div>
            <div>
              <div class="text-white font-bold mb-1">Adres</div>
              <a
                href="https://maps.app.goo.gl/hC9RY8HX29PepBwY6"
                target="_blank"
                class="text-slate-400 hover:text-amber-300 transition-colors text-sm md:text-base leading-relaxed">
                Mega Şaşmaz Oto Sanayi Sitesi, Etimesgut / Ankara
              </a>
            </div>
          </div>

          <div class="group flex gap-4 p-5 bg-slate-900/80 border border-slate-800 rounded-3xl hover:border-red-800/70 transition-all">
            <div class="w-12 h-12 rounded-2xl bg-slate-800 border border-slate-700 flex items-center justify-center shrink-0 text-xl group-hover:bg-red-900/30 transition-colors">
              ⏰
            </div>
            <div>
              <div class="text-white font-bold mb-1">Çalışma Saatleri</div>
              <div class="text-slate-400 text-sm md:text-base">
                Pazartesi – Cumartesi · 08:30 – 19:00
              </div>
              <div class="inline-flex items-center gap-2 mt-2 text-xs font-semibold text-red-300 bg-red-950/40 border border-red-900/60 px-2.5 py-1 rounded-full">
                <span class="w-1.5 h-1.5 rounded-full bg-red-400"></span>
                Pazar günleri kapalı
              </div>
            </div>
          </div>

          <div class="group p-5 bg-slate-900/80 border border-slate-800 rounded-3xl hover:border-red-800/70 transition-all">
            <div class="flex gap-4">
              <div class="w-12 h-12 rounded-2xl bg-slate-800 border border-slate-700 flex items-center justify-center shrink-0 text-xl group-hover:bg-red-900/30 transition-colors">
                📞
              </div>

              <div class="flex-1 min-w-0">
                <div class="text-white font-bold mb-3">Telefon Numaraları</div>

                <div class="divide-y divide-slate-800/80">
                  <a href="tel:05326213429" class="flex items-center justify-between gap-4 py-2.5 first:pt-0 group/phone">
                    <span class="text-slate-400 text-sm">Ali Kala</span>
                    <span class="text-slate-100 text-sm md:text-base font-semibold tabular-nums group-hover/phone:text-amber-300 transition-colors">
                      0532 621 34 29
                    </span>
                  </a>

                  <a href="tel:05058586591" class="flex items-center justify-between gap-4 py-2.5 group/phone">
                    <span class="text-slate-400 text-sm">Bünyamin Kala</span>
                    <span class="text-slate-100 text-sm md:text-base font-semibold tabular-nums group-hover/phone:text-amber-300 transition-colors">
                      0505 858 65 91
                    </span>
                  </a>

                  <a href="tel:05077276716" class="flex items-center justify-between gap-4 py-2.5 last:pb-0 group/phone">
                    <span class="text-slate-400 text-sm">Yusuf Kala</span>
                    <span class="text-slate-100 text-sm md:text-base font-semibold tabular-nums group-hover/phone:text-amber-300 transition-colors">
                      0507 727 67 16
                    </span>
                  </a>
                </div>
              </div>
            </div>
          </div>

        </div>
      </div>

      <div class="reveal">
        <div class="h-full min-h-[320px] md:min-h-[520px] bg-slate-900 rounded-[2rem] overflow-hidden border border-slate-800 shadow-2xl relative">
          <iframe
            src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3059.570177466949!2d32.719895799999996!3d39.9286341!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x14d347ee37df85e5%3A0x41d15ac7bb58e9b!2zTWVnYSDFnmHFn21heg!5e0!3m2!1str!2str!4v1779471948767!5m2!1str!2str"
            width="100%"
            height="100%"
            style="border:0;"
            allowfullscreen=""
            loading="lazy"
            referrerpolicy="no-referrer-when-downgrade">
          </iframe>

          <a
            href="https://maps.app.goo.gl/hC9RY8HX29PepBwY6"
            target="_blank"
            class="absolute bottom-4 left-4 right-4 md:left-5 md:right-auto inline-flex items-center justify-center gap-2 bg-slate-950/90 backdrop-blur border border-slate-700 text-white px-5 py-3 rounded-2xl text-sm font-bold hover:bg-red-700 hover:border-red-600 transition-all">
            📍 Google Haritalar’da Aç
          </a>
        </div>
      </div>

    </div>
  </div>
</section>

    <footer class="bg-slate-950 text-slate-500 py-8 text-center border-t border-slate-800 text-sm">
      <div class="space-y-2">
        <div class="font-display text-white font-bold text-xl tracking-wide">Özgehan Otomotiv</div>
        <p>Mega Şaşmaz Oto Sanayi, Etimesgut / Ankara</p>
        <p class="text-xs opacity-50 pt-3">© 2026 Özgehan Otomotiv. Tüm hakları saklıdır.</p>
      </div>
    </footer>
    <div class="md:hidden fixed bottom-0 left-0 w-full bg-slate-950/95 backdrop-blur-md border-t border-slate-800 grid grid-cols-3 text-center text-white font-bold text-xs shadow-2xl z-50 pb-safe">
      <a href="tel:05326213429" class="py-3.5 border-r border-slate-800 flex flex-col items-center justify-center gap-1 active:bg-slate-900 transition-colors">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.94.725l.548 2.2a1 1 0 01-.321.988l-1.305.98a10.582 10.582 0 004.872 4.872l.98-1.305a1 1 0 01.988-.321l2.2.548a1 1 0 01.725.94V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"/></svg>
        Hemen Ara
      </a>
      <a href="https://wa.me/905326213429?text=Merhaba,%20%C3%96zgehan%20Otomotiv%20sitenizden%20ula%C5%9F%C4%B1yorum." target="_blank"
         class="py-3.5 border-r border-slate-800 bg-emerald-950/30 text-emerald-400 flex flex-col items-center justify-center gap-1 active:bg-emerald-900 transition-colors">
        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413Z"/></svg>
        WhatsApp
      </a>
      <a href="https://maps.app.goo.gl/hC9RY8HX29PepBwY6" target="_blank"
         class="py-3.5 flex flex-col items-center justify-center gap-1 text-slate-300 active:bg-slate-900 transition-colors">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15 10.5a3 3 0 11-6 0 3 3 0 016 0z"/><path stroke-linecap="round" stroke-linejoin="round" d="M19.5 10.5c0 7.142-7.5 11.25-7.5 11.25S4.5 17.642 4.5 10.5a7.5 7.5 0 1115 0z"/></svg>
        Yol Tarifi
      </a>
    </div>

    <Teleport to="body">
      <button
  v-show="showScrollTop"
  type="button"
  @click="scrollToTop"
  aria-label="En üste dön"
  class="fixed right-4 bottom-24 md:bottom-6 z-40 w-11 h-11 rounded-full bg-slate-950 text-white border border-slate-700 shadow-2xl flex items-center justify-center hover:bg-red-700 hover:border-red-600 active:scale-95 transition-all cursor-pointer">
  ↑
</button>
  <div v-if="selectedImage"
     class="fixed inset-0 z-[100] flex items-center justify-center bg-black/95 p-4 backdrop-blur-sm cursor-pointer"
     @click="closeLightbox"
     @touchstart="handleLightboxTouchStart"
     @touchend="handleLightboxTouchEnd">

    <button
      class="absolute top-4 right-6 text-white/70 hover:text-white text-4xl hover:scale-110 transition-all leading-none z-20"
      @click.stop="closeLightbox">
      ×
    </button>

    <button
      class="absolute left-3 md:left-8 top-1/2 -translate-y-1/2 w-11 h-11 md:w-14 md:h-14 rounded-full bg-white/10 hover:bg-white/20 text-white text-3xl md:text-4xl flex items-center justify-center backdrop-blur border border-white/20 transition-all z-20 cursor-pointer"
      @click.stop="prevImage">
      ‹
    </button>

    <img
      :src="selectedImage"
      class="max-w-full max-h-[90vh] object-contain rounded-2xl shadow-2xl cursor-default ring-2 ring-white/10"
      @click.stop
    />

    <button
      class="absolute right-3 md:right-8 top-1/2 -translate-y-1/2 w-11 h-11 md:w-14 md:h-14 rounded-full bg-white/10 hover:bg-white/20 text-white text-3xl md:text-4xl flex items-center justify-center backdrop-blur border border-white/20 transition-all z-20 cursor-pointer"
      @click.stop="nextImage">
      ›
    </button>

  </div>
</Teleport>

  </div>
</template>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Syne:wght@700;800;900&family=DM+Sans:ital,wght@0,400;0,500;0,600;1,400&display=swap');

  html,
body {
  background: #020617;
}

body {
  font-family: 'DM Sans', sans-serif;
}
  .font-display { font-family: 'Syne', sans-serif; }

  /* Scroll reveal */
/* Scroll reveal - güvenli hafif animasyon */
.reveal {
  opacity: 1;
  transform: translateY(14px);
  transition: transform 0.5s ease;
}

.reveal.revealed {
  opacity: 1;
  transform: translateY(0);
}

.reveal-delay-1 { transition-delay: 0.05s; }
.reveal-delay-2 { transition-delay: 0.1s; }
.reveal-delay-3 { transition-delay: 0.15s; }
.reveal-delay-4 { transition-delay: 0.2s; }

  /* Review carousel transition */
  .review-enter-active, .review-leave-active { transition: opacity 0.4s ease, transform 0.4s ease; }
  .review-enter-from { opacity: 0; transform: translateY(10px); }
  .review-leave-to { opacity: 0; transform: translateY(-10px); }

  /* Noise texture overlay */
  .noise::before {
    content: '';
    position: absolute;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 0;
  }
  .pb-safe {
  padding-bottom: env(safe-area-inset-bottom);
}
@keyframes softFadeUp {
  from {
    opacity: 0;
    transform: translateY(14px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.soft-fade-up {
  animation: softFadeUp 0.7s ease-out both;
}

.soft-fade-up-delay {
  animation: softFadeUp 0.8s ease-out 0.15s both;
}
html {
  scroll-behavior: smooth;
}

.no-scrollbar::-webkit-scrollbar {
  display: none;
}

.no-scrollbar {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
</style>