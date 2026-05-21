<script setup>
import { ref } from 'vue'

import heroFoto from './assets/hero-foto.jpeg'
import is1 from './assets/is-1.jpg.jpeg'
import is2 from './assets/is-2.jpg.jpeg'
import is3 from './assets/is-3.jpg.jpeg'

const selectedImage = ref(null)

const name = ref('')
const phone = ref('')
const note = ref('')
const botcheck = ref('') // SPAM ENGELLEME HILESI
const isSubmitted = ref(false)
const isLoading = ref(false) // YÜKLEME DURUMU

const handleFormSubmit = async () => {
  if (botcheck.value) {
    isSubmitted.value = true
    return
  }

  if (!name.value || !phone.value) return
  
  isLoading.value = true // BUTONU GÖNDERİLİYOR YAP

  try {
    const response = await fetch('https://api.web3forms.com/submit', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
      },
      body: JSON.stringify({
        access_key: '2d42f4b9-d839-403c-ae63-a5dabcc26796',
        name: name.value,
        phone: phone.value,
        message: note.value || 'Müşteri ekstra not bırakmadı.',
        subject: 'Özgehan Otomotiv - Siteden Yeni Araç Talebi'
      })
    });

    const result = await response.json();
    
    if (result.success) {
      isSubmitted.value = true
      setTimeout(() => {
        name.value = ''
        phone.value = ''
        note.value = ''
        isSubmitted.value = false
      }, 5000)
    } else {
      alert('Gönderim başarısız oldu, lütfen geçici bir süre için WhatsApp üzerinden ulaşın.');
    }
  } catch (error) {
     console.error(error);
     alert('Bir bağlantı hatası oluştu!');
  } finally {
     isLoading.value = false // İŞLEM BİTİNCE BUTONU NORMALE DÖNDÜR
  }
}
</script>

<template>
  <div class="min-h-screen w-full bg-slate-100 text-slate-900 font-sans relative overflow-x-hidden pb-16 md:pb-0">
    
    <header class="bg-slate-950 text-white p-4 md:p-5 shadow-2xl sticky top-0 z-50 border-b border-slate-800">
      <div class="max-w-7xl mx-auto flex justify-between items-center">
        
        <div class="flex items-center gap-2">
          <span class="text-2xl md:text-3xl text-slate-400">🔧</span>
          <div class="text-xl sm:text-2xl md:text-3xl font-serif font-bold uppercase tracking-wide">
            <span class="text-white">Özgehan</span>
            <span class="text-slate-400 ml-1 font-medium">Otomotiv</span>
          </div>
        </div>
        
        <div class="flex items-center gap-4">
          <span class="text-slate-400 hidden lg:flex items-center gap-2 font-medium">
            <svg class="w-5 h-5 text-yellow-500" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M10 2a.75.75 0 01.67.4l2.13 4.3 4.75.69a.75.75 0 01.41 1.28l-3.44 3.35.81 4.73c.06.35-.3.65-.62.47L10 14.98l-4.25 2.24a.75.75 0 01-1.09-.79l.81-4.73-3.44-3.35a.75.75 0 01.41-1.28l4.75-.69 2.13-4.3A.75.75 0 0110 2z" clip-rule="evenodd" /></svg>
            40 Yıllık Sanayi Tecrübesi
          </span>
          <a href="tel:05326213429" class="font-bold text-sm sm:text-base bg-red-700 px-4 sm:px-5 py-2.5 rounded-full hover:bg-red-800 transition-colors flex items-center gap-2 shadow-inner whitespace-nowrap">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.94.725l.548 2.2a1 1 0 01-.321.988l-1.305.98a10.582 10.582 0 004.872 4.872l.98-1.305a1 1 0 01.988-.321l2.2.548a1 1 0 01.725.94V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"/></svg>
            <span class="hidden sm:block">0532 621 34 29</span>
            <span class="block sm:hidden tracking-wide">Ara</span>
          </a>
        </div>
      </div>
    </header>

    <main class="bg-white">
      <div class="max-w-7xl mx-auto px-4 py-12 md:py-24 grid grid-cols-1 md:grid-cols-2 gap-10 md:gap-16 items-center">
        <div class="text-center md:text-left flex flex-col items-center md:items-start">
          
          <div class="inline-flex lg:hidden items-center gap-2 px-4 py-2 rounded-full bg-slate-50 border border-slate-200 text-sm font-bold text-slate-700 mb-6 shadow-sm">
            <svg class="w-4 h-4 text-yellow-500" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M10 2a.75.75 0 01.67.4l2.13 4.3 4.75.69a.75.75 0 01.41 1.28l-3.44 3.35.81 4.73c.06.35-.3.65-.62.47L10 14.98l-4.25 2.24a.75.75 0 01-1.09-.79l.81-4.73-3.44-3.35a.75.75 0 01.41-1.28l4.75-.69 2.13-4.3A.75.75 0 0110 2z" clip-rule="evenodd" /></svg>
            40 Yıllık Sanayi Tecrübesi
          </div>

          <h1 class="text-4xl sm:text-5xl md:text-7xl font-black text-slate-950 leading-tight mb-4 md:mb-6">
            Aracınız<br/> <span class="text-red-700">Emin Ellerde</span>
          </h1>
          <p class="text-lg md:text-xl text-slate-700 mb-8 md:mb-10 leading-relaxed max-w-lg text-center md:text-left font-medium">
            Profesyonel motor mekanik, periyodik bakım ve arıza tespit işlemlerinizde yılların sanayi tecrübesiyle dürüst ve garantili hizmet.
          </p>
          <div class="flex flex-col sm:flex-row gap-4 w-full sm:w-auto">
            <a href="https://wa.me/905326213429?text=Merhaba,%20sitenizden%20ulaşıyorum." 
               target="_blank" 
               class="inline-flex items-center justify-center gap-3 px-8 py-4 text-lg font-bold text-white transition-all bg-emerald-600 rounded-xl shadow-lg hover:bg-emerald-700 hover:-translate-y-1">
              💬 WhatsApp'tan Ulaşın
            </a>
            <a href="https://www.google.com/maps/search/?api=1&query=Şaşmaz+Oto+Sanayi+Sitesi+Ankara" 
               target="_blank" 
               class="inline-flex items-center justify-center gap-3 px-8 py-4 text-lg font-bold text-slate-700 transition-all bg-slate-100 rounded-xl border-2 border-slate-200 hover:bg-slate-200 hover:-translate-y-1">
              📍 Yol Tarifi Al
            </a>
          </div>
        </div>
        <div class="w-full h-[250px] sm:h-[350px] md:h-[450px] bg-slate-900 rounded-3xl shadow-2xl overflow-hidden border-4 md:border-8 border-slate-100 mt-4 md:mt-0">
          <img :src="heroFoto" alt="Özgehan Otomotiv Servis Mekanik" loading="lazy" class="w-full h-full object-cover"/>
        </div>
      </div>
    </main>

    <section class="py-16 bg-slate-900 border-y border-slate-800">
      <div class="max-w-7xl mx-auto px-4">
        <div class="text-center mb-12">
          <h2 class="text-3xl md:text-4xl font-bold text-white mb-4">Neden Bizi Tercih Etmelisiniz?</h2>
          <p class="text-slate-400 max-w-2xl mx-auto">Aracınızı güvenle teslim edebilmeniz için kaliteden ve şeffaflıktan ödün vermiyoruz.</p>
        </div>
        
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-6">
          <div class="bg-slate-950 p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 transition-colors">
            <div class="w-12 h-12 bg-slate-900 rounded-full flex items-center justify-center mx-auto mb-4 border border-slate-700">
              <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
            </div>
            <h3 class="text-white font-bold text-lg mb-2">Dürüst Fiyatlandırma</h3>
            <p class="text-slate-400 text-sm">Aracınıza yalnızca gerçekten ihtiyaç duyulan işlemler uygulanır.</p>
          </div>
          
          <div class="bg-slate-950 p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 transition-colors">
            <div class="w-12 h-12 bg-slate-900 rounded-full flex items-center justify-center mx-auto mb-4 border border-slate-700">
              <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"></path></svg>
            </div>
            <h3 class="text-white font-bold text-lg mb-2">Kaliteli Parça</h3>
            <p class="text-slate-400 text-sm">Aracınızın ömrünü uzatan orijinal ve oem onaylı yedek parçalar kullanılır.</p>
          </div>

          <div class="bg-slate-950 p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 transition-colors">
            <div class="w-12 h-12 bg-slate-900 rounded-full flex items-center justify-center mx-auto mb-4 border border-slate-700">
              <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z"></path></svg>
            </div>
            <h3 class="text-white font-bold text-lg mb-2">Garantili İşçilik</h3>
            <p class="text-slate-400 text-sm">Yaptığımız her mekanik işlem ve parça değişimi usta garantimiz altındadır.</p>
          </div>

          <div class="bg-slate-950 p-6 rounded-2xl border border-slate-800 text-center hover:border-red-600 transition-colors">
            <div class="w-12 h-12 bg-slate-900 rounded-full flex items-center justify-center mx-auto mb-4 border border-slate-700">
              <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
            </div>
            <h3 class="text-white font-bold text-lg mb-2">Hızlı Teslimat</h3>
            <p class="text-slate-400 text-sm">Zamanınızın değerli olduğunu biliyor, aracınızı söz verdiğimiz sürede teslim ediyoruz.</p>
          </div>
        </div>
      </div>
    </section>

    <section class="py-16 md:py-24 bg-white border-b border-slate-200">
      <div class="max-w-4xl mx-auto px-4 text-center md:text-left">
        <div class="flex flex-col md:flex-row items-center gap-8 md:gap-12">
          <div class="w-24 h-24 md:w-32 md:h-32 bg-slate-900 text-white rounded-full flex items-center justify-center border border-slate-800 shadow-md shrink-0">
            <svg class="w-12 md:w-16 h-12 md:h-16 text-red-600" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"/></svg>
          </div>
          <div>
            <h2 class="text-3xl md:text-4xl font-bold text-slate-950 mb-4">Hakkımızda</h2>
            <p class="text-lg text-slate-700 leading-relaxed font-medium mb-3">Özgehan Otomotiv, Ankara Şaşmaz Oto Sanayi Sitesi'nde uzun yıllara dayanan sektörel tecrübe ve çekirdekten yetişme profesyonel usta kadrosuyla hizmet vermektedir. Kurulduğumuz günden bu yana temel ilkemiz dürüstlük, şeffaf fiyatlandırma ve garantili usta işçiliğidir.</p>
          </div>
        </div>
      </div>
    </section>

    <section class="py-16 md:py-24 bg-slate-100">
      <div class="max-w-7xl mx-auto px-4">
        <h2 class="text-3xl md:text-5xl font-bold text-center mb-12 text-slate-950">Hizmetlerimiz</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 md:gap-10">
          
          <div class="p-8 bg-white rounded-3xl shadow-md border border-slate-200 hover:border-red-500 transition-all text-center md:text-left group">
            <div class="w-14 h-14 bg-slate-100 rounded-2xl flex items-center justify-center mb-6 border border-slate-200 group-hover:bg-red-50 mx-auto md:mx-0 shadow-inner">
              <svg class="w-8 h-8 text-slate-700 group-hover:text-red-700" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M11.423 3.001c.101-.06.216-.092.333-.092.118 0 .233.031.334.092l7.33 4.364c.224.133.36.37.36.627v8.018c0 .257-.136.494-.36.627l-7.33 4.364a.669.669 0 01-.668 0l-7.33-4.364a.668.668 0 01-.36-.627V8.928c0-.257.136-.494.36-.627l7.33-4.364z"/></svg>
            </div>
            <h3 class="text-xl md:text-2xl font-bold mb-3 text-slate-950">Motor Mekanik</h3>
            <p class="text-slate-600 leading-relaxed font-medium">Motor rektefiye, şanzıman onarımı, alt takım, süspansiyon ve ağır bakım işlemleri titizlikle yürütülür.</p>
          </div>

          <div class="p-8 bg-white rounded-3xl shadow-md border border-slate-200 hover:border-red-500 transition-all text-center md:text-left group">
            <div class="w-14 h-14 bg-slate-100 rounded-2xl flex items-center justify-center mb-6 border border-slate-200 group-hover:bg-red-50 mx-auto md:mx-0 shadow-inner">
              <svg class="w-8 h-8 text-slate-700 group-hover:text-red-700" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 2.25c-1.43 2.861-6 8.586-6 11.25a6 6 0 1012 0c0-2.664-4.57-8.389-6-11.25z" />
              </svg>
            </div>
            <h3 class="text-xl md:text-2xl font-bold mb-3 text-slate-950">Periyodik Bakım</h3>
            <p class="text-slate-600 leading-relaxed font-medium">Yüksek kaliteli yağ filtre grupları ile periyodik bakım işlemlerinizi eksiksiz yerine getirmekteyiz.</p>
          </div>

          <div class="p-8 bg-white rounded-3xl shadow-md border border-slate-200 hover:border-red-500 transition-all text-center md:text-left group">
            <div class="w-14 h-14 bg-slate-100 rounded-2xl flex items-center justify-center mb-6 border border-slate-200 group-hover:bg-red-50 mx-auto md:mx-0 shadow-inner">
              <svg class="w-8 h-8 text-slate-700 group-hover:text-red-700" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/></svg>
            </div>
            <h3 class="text-xl md:text-2xl font-bold mb-3 text-slate-950">Arıza Tespiti</h3>
            <p class="text-slate-600 leading-relaxed font-medium">Gelişmiş arıza tespit bilgisayarları ile aracın beyni taranarak nokta atışı onarım yapılır.</p>
          </div>

        </div>
      </div>
    </section>

    <section class="py-16 bg-white border-t border-slate-200">
      <div class="max-w-7xl mx-auto px-4">
        <h2 class="text-3xl md:text-4xl font-bold text-center mb-10 text-slate-950">Ustalığımız ve Atölyemiz</h2>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4 md:gap-6">
          <div class="aspect-video bg-slate-900 rounded-2xl overflow-hidden shadow-lg border-2 border-slate-100 hover:border-red-500 transition-all cursor-pointer" @click="selectedImage = is1"><img :src="is1" loading="lazy" class="w-full h-full object-cover hover:scale-105 transition-transform duration-300"/></div>
          <div class="aspect-video bg-slate-900 rounded-2xl overflow-hidden shadow-lg border-2 border-slate-100 hover:border-red-500 transition-all cursor-pointer" @click="selectedImage = is2"><img :src="is2" loading="lazy" class="w-full h-full object-cover hover:scale-105 transition-transform duration-300"/></div>
          <div class="aspect-video bg-slate-900 rounded-2xl overflow-hidden shadow-lg border-2 border-slate-100 hover:border-red-500 transition-all cursor-pointer" @click="selectedImage = is3"><img :src="is3" loading="lazy" class="w-full h-full object-cover hover:scale-105 transition-transform duration-300"/></div>
        </div>
      </div>
    </section>

    <section class="py-16 bg-slate-900 text-white border-t border-slate-800">
      <div class="max-w-xl mx-auto px-4">
        <div class="text-center mb-8">
          <h2 class="text-3xl md:text-4xl font-bold text-white mb-2">Sizi Arayalım</h2>
          <p class="text-slate-400">Numaranızı bırakın, ustanız en kısa sürede dönüş sağlasın.</p>
        </div>
        <form @submit.prevent="handleFormSubmit" class="bg-slate-950 p-6 md:p-8 rounded-3xl border border-slate-800 shadow-xl space-y-4">
          
          <input v-model="botcheck" type="text" name="botcheck" class="hidden" style="display:none !important;" autocomplete="off" />

          <div v-if="!isSubmitted" class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-slate-300 mb-1">Adınız Soyadınız *</label>
              <input v-model="name" type="text" required placeholder="Ahmet Yılmaz" class="w-full bg-slate-900 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-red-700" />
            </div>
            <div>
              <label class="block text-sm font-medium text-slate-300 mb-1">Telefon Numaranız *</label>
              <input v-model="phone" type="tel" required placeholder="05XX XXX XX XX" class="w-full bg-slate-900 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-red-700" />
            </div>
            <div>
              <label class="block text-sm font-medium text-slate-300 mb-1">Araç Modeli / Şikayetiniz (Opsiyonel)</label>
              <textarea v-model="note" rows="3" placeholder="Örn: 2016 Passat Yağ Bakımı ve Ön Takım Sesi" class="w-full bg-slate-900 border border-slate-800 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-red-700 resize-none"></textarea>
            </div>
            
            <button 
              type="submit" 
              :disabled="isLoading"
              class="w-full bg-red-700 hover:bg-red-800 disabled:opacity-70 disabled:cursor-not-allowed text-white font-bold py-3.5 rounded-xl transition-colors shadow-lg flex items-center justify-center gap-2">
              <span v-if="isLoading" class="flex items-center gap-2">
                <svg class="animate-spin -ml-1 mr-2 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                Gönderiliyor...
              </span>
              <span v-else>
                📞 Beni Arayın
              </span>
            </button>
          </div>
          <div v-else class="text-center py-8 bg-emerald-950/30 border border-emerald-800 rounded-2xl">
            <span class="text-3xl text-emerald-500 block mb-2">✓</span>
            <h3 class="text-xl font-bold">Talebiniz Ustaya İletildi</h3>
            <p class="text-emerald-300/80 text-sm mt-1">En kısa sürede arayıp detaylı bilgi vereceğiz.</p>
          </div>
        </form>
      </div>
    </section>

    <section class="py-16 bg-slate-50 border-t border-slate-200">
      <div class="max-w-7xl mx-auto px-4">
        <h2 class="text-3xl font-bold text-center mb-10 text-slate-950">Müşteri Deneyimleri</h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="p-6 bg-white rounded-2xl shadow-sm border border-slate-200">
            <div class="flex text-yellow-400 mb-2">★★★★★</div>
            <p class="text-slate-700 italic text-sm mb-4">"Yıllardır çözülemeyen tekleme arızasını yarım günde nokta atışı tespit edip çözdüler. Şaşmaz'da dürüst usta bulmak zor, Ali Usta sağ olsun."</p>
            <div class="font-bold text-slate-900 text-sm">Hakan Ç. <span class="text-slate-500 font-normal text-xs">- 2015 VW Golf (Ağır Mekanik)</span></div>
          </div>
          <div class="p-6 bg-white rounded-2xl shadow-sm border border-slate-200">
            <div class="flex text-yellow-400 mb-2">★★★★★</div>
            <p class="text-slate-700 italic text-sm mb-4">"Periyodik bakım için gittim. Malzemeleri gözümün önünde açıp tek tek taktılar. Şeffaf ve temiz esnaflık, kesinlikle tavsiye ederim."</p>
            <div class="font-bold text-slate-900 text-sm">Murat K. <span class="text-slate-500 font-normal text-xs">- 2018 BMW 320d (Sıvı Bakımları)</span></div>
          </div>
          <div class="p-6 bg-white rounded-2xl shadow-sm border border-slate-200">
            <div class="flex text-yellow-400 mb-2">★★★★★</div>
            <p class="text-slate-700 italic text-sm mb-4">"Alt takımdan gelen o sinir bozucu sesi burası kesti. Gereksiz parça değiştirmeye kalkmıyorlar, neyse onu tamir ediyorlar."</p>
            <div class="font-bold text-slate-900 text-sm">Yasin B. <span class="text-slate-500 font-normal text-xs">- 2014 Ford Focus (Alt Takım Onarımı)</span></div>
          </div>
        </div>
      </div>
    </section>

    <section class="bg-slate-900 text-white py-16 border-t-8 border-red-700">
      <div class="max-w-7xl mx-auto px-4 grid grid-cols-1 md:grid-cols-2 gap-10 items-center">
        <div>
          <h2 class="text-4xl font-bold mb-6">İletişim Bilgileri</h2>
          <div class="space-y-4 text-slate-300">
            <p><strong class="text-white">📍 Adres:</strong><br/> Şaşmaz Oto Sanayi Sitesi, Etimesgut / Ankara</p>
            <p><strong class="text-white">⏰ Çalışma Saatleri:</strong><br/> Pazartesi - Cumartesi (08:30 - 19:00)</p>
          </div>
        </div>
        <div class="w-full h-64 bg-slate-800 rounded-3xl overflow-hidden shadow-2xl border-4 border-slate-700">
          <iframe src="https://maps.google.com/maps?q=Şaşmaz%20Oto%20Sanayi%20Sitesi,%20Ankara&t=&z=14&ie=UTF8&iwloc=&output=embed" loading="lazy" width="100%" height="100%" style="border:0;" allowfullscreen=""></iframe>
        </div>
      </div>
    </section>

    <footer class="bg-slate-950 text-slate-500 py-8 text-center border-t border-slate-800 text-sm w-full">
      <div class="space-y-2">
        <div class="text-white font-bold text-lg">
          Özgehan Otomotiv
        </div>
        <p>
          Şaşmaz Oto Sanayi Sitesi / Ankara
        </p>
        <p>
          📞 0532 621 34 29
        </p>
        <p class="text-xs opacity-60 pt-2">
          © 2026 Özgehan Otomotiv. Tüm hakları saklıdır.
        </p>
      </div>
    </footer>

    <div class="md:hidden fixed bottom-0 left-0 w-full bg-slate-950/95 backdrop-blur border-t border-slate-800 grid grid-cols-3 text-center text-white font-bold text-xs shadow-2xl z-50">
      <a href="tel:05326213429" class="py-3 border-r border-slate-800 flex flex-col items-center justify-center gap-1 active:bg-slate-900">
        <span>📞</span> Hemen Ara
      </a>
      <a href="https://wa.me/905326213429?text=Merhaba,%20sitenizden%20ulaşıyorum." target="_blank" class="py-3 border-r border-slate-800 bg-emerald-950/30 text-emerald-400 flex flex-col items-center justify-center gap-1 active:bg-emerald-900">
        <span>💬</span> WhatsApp
      </a>
      <a href="https://www.google.com/maps/search/?api=1&query=Şaşmaz+Oto+Sanayi+Sitesi+Ankara" target="_blank" class="py-3 flex flex-col items-center justify-center gap-1 text-slate-300 active:bg-slate-900">
        <span>📍</span> Yol Tarifi
      </a>
    </div>

    <div v-if="selectedImage" class="fixed inset-0 z-[100] flex items-center justify-center bg-black/95 p-4 backdrop-blur-sm cursor-pointer" @click="selectedImage = null">
      <button class="absolute top-4 right-6 text-white text-4xl hover:text-red-500">×</button>
      <img :src="selectedImage" class="max-w-full max-h-[90vh] object-contain rounded-xl shadow-2xl cursor-default" @click.stop />
    </div>

  </div>
</template>

