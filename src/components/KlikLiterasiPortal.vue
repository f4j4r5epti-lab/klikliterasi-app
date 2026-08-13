<template>
  <div class="literacy-portal font-sans bg-amber-50/20 min-h-screen text-slate-800 pb-12">
    <!-- NAVBAR (Responsif dengan Hamburger Menu di HP) -->
    <header class="bg-white/80 backdrop-blur-md sticky top-0 z-50 border-b border-slate-100 px-6 py-4 shadow-sm">
      <div class="flex items-center justify-between">
        <div class="flex items-center space-x-3">
          <div class="w-10 h-10 bg-indigo-600 rounded-xl flex items-center justify-center text-white font-bold text-xl shadow-md shadow-indigo-200">
            SD
          </div>
          <div>
            <h1 class="font-extrabold text-slate-800 text-lg leading-tight">SDN PUCUNG</h1>
            <p class="text-xs text-indigo-600 font-semibold tracking-wide">PORTAL KLIK LITERASI</p>
          </div>
        </div>

        <!-- Menu Desktop -->
        <nav class="hidden md:flex items-center space-x-8 font-medium text-slate-600">
          <a href="#beranda" class="hover:text-indigo-600 transition">Beranda</a>
          <a href="#katalog" class="hover:text-indigo-600 transition">Katalog Buku</a>
          <a href="#jurnal" class="hover:text-indigo-600 transition">Jurnal Membaca</a>
          <a href="#jadwal" class="hover:text-indigo-600 transition">Jadwal Akses</a>
        </nav>

        <div class="flex items-center space-x-3">
          <!-- Selector Kelas -->
          <div class="flex items-center space-x-1.5 bg-slate-100 p-1.5 rounded-xl border border-slate-200">
            <span class="text-xs font-semibold text-slate-500 pl-1">Saya:</span>
            <select v-model="selectedClass" class="bg-white text-xs font-bold text-indigo-600 px-2 py-1 rounded-lg border-none focus:outline-none cursor-pointer">
              <option v-for="c in 6" :key="c" :value="c">Kelas {{ c }}</option>
            </select>
          </div>

          <!-- Tombol Hamburger (Khusus Layar HP) -->
          <button @click="isMenuOpen = !isMenuOpen" class="md:hidden text-2xl p-1 text-slate-600 focus:outline-none">
            {{ isMenuOpen ? '✖' : '☰' }}
          </button>
        </div>
      </div>

      <!-- Dropdown Menu HP saat tombol ☰ diklik -->
      <div v-if="isMenuOpen" class="md:hidden mt-4 pt-3 border-t border-slate-100 flex flex-col space-y-3 font-medium text-sm text-slate-600">
        <a href="#beranda" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Beranda</a>
        <a href="#katalog" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Katalog Buku</a>
        <a href="#jurnal" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Jurnal Membaca</a>
        <a href="#jadwal" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Jadwal Akses</a>
      </div>
    </header>

    <!-- BANNER HARI & PENJADWALAN (Traffic Control) -->
    <section id="jadwal" class="max-w-6xl mx-auto px-6 mt-6">
      <div class="bg-gradient-to-r from-indigo-600 to-purple-600 rounded-3xl p-6 text-white shadow-xl flex flex-col md:flex-row items-center justify-between gap-4">
        <div>
          <span class="bg-white/20 text-xs px-3 py-1 rounded-full font-semibold uppercase tracking-wider">
            Hari Ini: {{ currentDayName }}
          </span>
          <h2 class="text-xl md:text-2xl font-bold mt-2">
            Jadwal Akses Interaksi: <span class="text-amber-300">{{ activeScheduleText }}</span>
          </h2>
          <p class="text-indigo-100 text-sm mt-1">
            Membaca buku terbuka untuk semua kelas. Pengisian ringkasan, komentar, dan love dikhususkan sesuai jadwal harian.
          </p>
        </div>

        <div class="bg-white/10 backdrop-blur-md p-4 rounded-2xl border border-white/20 text-center min-w-[220px]">
          <p class="text-xs text-indigo-100 font-medium">Status Interaksi (Kelas {{ selectedClass }}):</p>
          <span 
            :class="isBolehInteraksi ? 'bg-emerald-400 text-emerald-950' : 'bg-rose-400 text-rose-950'"
            class="inline-block mt-2 px-4 py-1.5 rounded-full text-xs font-extrabold shadow-sm"
          >
            {{ isBolehInteraksi ? '✨ BERHAK INTERAKSI' : '🔒 KHUSUS MEMBACA' }}
          </span>
        </div>
      </div>
    </section>

    <!-- HERO SECTION -->
    <section id="beranda" class="max-w-6xl mx-auto px-6 py-12 grid md:grid-cols-2 gap-8 items-center">
      <div>
        <span class="text-xs font-bold text-indigo-600 bg-indigo-50 px-3 py-1 rounded-full border border-indigo-100">
          #1 Portal Literasi SDN Pucung
        </span>
        <h1 class="text-4xl md:text-5xl font-black text-slate-800 mt-4 leading-tight">
          Jelajahi Dunia Lewat <span class="text-indigo-600">Buku Digital</span>
        </h1>
        <p class="text-slate-600 mt-4 text-base leading-relaxed">
          Baca buku cerita seru dari Kemdikdasmen, tulis rangkumanmu di Lembar Kerja, dan bagikan kesan membacamu bersama teman-teman!
        </p>
        <a href="#katalog" class="inline-block mt-6 bg-amber-500 hover:bg-amber-600 text-white font-bold px-8 py-3.5 rounded-2xl shadow-lg shadow-amber-500/30 transition-all transform hover:-translate-y-0.5">
          Mulai Membaca Sekarang
        </a>
      </div>

      <div class="relative flex justify-center">
        <div class="w-full max-w-sm bg-white p-5 rounded-3xl shadow-2xl border border-slate-100 relative z-10">
          <div class="bg-gradient-to-tr from-purple-500 to-indigo-500 h-48 rounded-2xl flex items-center justify-center text-white text-5xl font-extrabold shadow-inner">
            📚
          </div>
          <div class="mt-4">
            <span class="text-xs font-bold text-amber-500 bg-amber-50 px-2.5 py-0.5 rounded-md">Rekomendasi Minggu Ini</span>
            <h3 class="font-extrabold text-slate-800 text-lg mt-1">Petualangan Si Kancil Cerdik</h3>
            <p class="text-xs text-slate-500 mt-1">Sumber: Kemdikdasmen • Untuk Kelas 1-3</p>
          </div>
        </div>
        <div class="absolute inset-0 bg-indigo-400/20 blur-3xl rounded-full transform scale-90"></div>
      </div>
    </section>

    <!-- KATALOG BUKU BACAAN -->
    <section id="katalog" class="max-w-6xl mx-auto px-6 py-8">
      <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-6">
        <div>
          <h2 class="text-2xl font-black text-slate-800">Daftar Buku Bacaan</h2>
          <p class="text-xs text-slate-500">Semua siswa dari kelas manapun dapat membaca buku di bawah ini kapan saja</p>
        </div>

        <div class="flex flex-wrap gap-2">
          <button 
            v-for="cat in categories" 
            :key="cat"
            @click="selectedCategory = cat"
            :class="selectedCategory === cat ? 'bg-indigo-600 text-white shadow-md shadow-indigo-200' : 'bg-white text-slate-600 border border-slate-200 hover:bg-slate-50'"
            class="px-4 py-2 rounded-xl text-xs font-bold transition"
          >
            {{ cat }}
          </button>
        </div>
      </div>

      <!-- Warning/Notifikasi Jika Di Luar Jadwal Interaksi -->
      <div v-if="!isBolehInteraksi" class="bg-amber-50 border border-amber-200 text-amber-800 p-4 rounded-2xl mb-6 text-sm flex items-center space-x-3">
        <span class="text-xl">ℹ️</span>
        <p>
          <strong>Informasi:</strong> Hari ini jadwal interaksi khusus <strong>{{ activeScheduleText }}</strong>. Siswa Kelas {{ selectedClass }} tetap bebas membaca buku, namun fitur pengisian jurnal & komentar terkunci hari ini.
        </p>
      </div>

      <!-- Grid Buku -->
      <div class="grid sm:grid-cols-2 md:grid-cols-3 gap-6">
        <div v-for="book in filteredBooks" :key="book.id" class="bg-white rounded-3xl p-5 border border-slate-100 shadow-sm hover:shadow-xl transition flex flex-col justify-between">
          <div>
            <div class="bg-slate-100 h-40 rounded-2xl mb-4 flex items-center justify-center text-4xl">
              {{ book.coverIcon }}
            </div>
            <span class="text-[10px] font-extrabold uppercase tracking-wider text-indigo-600 bg-indigo-50 px-2 py-1 rounded-md">
              {{ book.category }}
            </span>
            <h3 class="font-extrabold text-slate-800 text-base mt-2 line-clamp-1">{{ book.title }}</h3>
            <p class="text-xs text-slate-500 mt-1 line-clamp-2 leading-relaxed">{{ book.synopsis }}</p>
          </div>

          <div class="mt-6 pt-4 border-t border-slate-100 flex items-center justify-between">
            <span class="text-[11px] font-semibold text-slate-400">Target: {{ book.targetClass }}</span>
            <a 
              :href="book.url" 
              target="_blank"
              class="bg-amber-500 hover:bg-amber-600 text-white text-xs font-bold px-4 py-2 rounded-xl shadow-md shadow-amber-500/20 transition"
            >
              Baca Buku 📖
            </a>
          </div>
        </div>
      </div>
    </section>

    <!-- LEMBAR KERJA & JURNAL INTERAKTIF -->
    <section id="jurnal" class="max-w-4xl mx-auto px-6 py-12">
      <div class="text-center mb-8">
        <h2 class="text-3xl font-black text-slate-800">Jurnal Membaca Siswa</h2>
        <p class="text-sm text-slate-500 mt-1">Tulis rangkuman ceritamu dan berikan apresiasi kepada teman-teman!</p>
      </div>

      <!-- FORM INPUT MERANGKUM (Terkunci jika bukan jadwal kelasnya) -->
      <div class="bg-white p-6 rounded-3xl shadow-lg border border-slate-100 mb-10">
        <h3 class="font-extrabold text-slate-800 text-base mb-4 flex items-center space-x-2">
          <span>✍️</span> <span>Buat Lembar Kerja Baru</span>
        </h3>
        
        <!-- OPSI A: Jika Boleh Interaksi -->
        <form v-if="isBolehInteraksi" @submit.prevent="submitWorksheet" class="space-y-4">
          <div class="grid sm:grid-cols-2 gap-4">
            <input 
              v-model="newEntry.studentName" 
              type="text" 
              placeholder="Nama Lengkap Siswa" 
              required
              class="w-full bg-slate-50 border border-slate-200 rounded-xl px-4 py-2.5 text-xs font-medium focus:outline-none focus:border-indigo-500"
            />
            <select 
              v-model="newEntry.bookTitle" 
              required
              class="w-full bg-slate-50 border border-slate-200 rounded-xl px-4 py-2.5 text-xs font-medium focus:outline-none focus:border-indigo-500"
            >
              <option value="" disabled selected>Pilih Buku yang Dibaca</option>
              <option v-for="b in books" :key="b.id" :value="b.title">{{ b.title }}</option>
            </select>
          </div>

          <textarea 
            v-model="newEntry.summary" 
            rows="3" 
            placeholder="Tuliskan rangkuman cerita atau hal-hal penting yang kamu pelajari..." 
            required
            class="w-full bg-slate-50 border border-slate-200 rounded-xl p-4 text-xs font-medium focus:outline-none focus:border-indigo-500"
          ></textarea>

          <button 
            type="submit" 
            :disabled="isSubmitting"
            class="w-full bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-300 text-white font-bold py-3 rounded-xl shadow-lg transition text-xs cursor-pointer flex items-center justify-center space-x-2"
          >
            <span v-if="isSubmitting">⏳ Mengirim Rangkuman...</span>
            <span v-else>Kirim Hasil Rangkuman 🚀</span>
          </button>
        </form>

        <!-- OPSI B: Tampilan Terkunci Jika Bukan Jadwalnya -->
        <div v-else class="p-8 text-center bg-slate-50 border border-dashed border-slate-300 rounded-2xl">
          <span class="text-4xl block mb-2">🔒</span>
          <h4 class="font-bold text-slate-800 text-sm">Pengisian Rangkuman Terkunci</h4>
          <p class="text-xs text-slate-500 max-w-md mx-auto mt-1 leading-relaxed">
            Mohon maaf, fitur menulis jurnal untuk <b>Kelas {{ selectedClass }}</b> baru dibuka pada hari jadwal kelasmu. Hari ini dikhususkan untuk <b>{{ activeScheduleText }}</b>.
          </p>
        </div>
      </div>

      <!-- FEED JURNAL SISWA (Instagram Style Card) -->
      <div class="space-y-6">
        <div v-for="post in journalPosts" :key="post.id" class="bg-white rounded-3xl border border-slate-100 shadow-sm overflow-hidden">
          
          <!-- Header Card -->
          <div class="p-4 border-b border-slate-50 flex items-center justify-between">
            <div class="flex items-center space-x-3">
              <div class="w-9 h-9 rounded-full bg-gradient-to-tr from-amber-400 to-indigo-500 flex items-center justify-center text-white font-bold text-xs">
                {{ getInitials(post.studentName) }}
              </div>
              <div>
                <h4 class="font-extrabold text-slate-800 text-xs">{{ post.studentName }}</h4>
                <p class="text-[10px] text-slate-400">Kelas {{ post.studentClass }} • {{ post.timeAgo }}</p>
              </div>
            </div>
            <span class="bg-indigo-50 text-indigo-600 text-[10px] font-bold px-2.5 py-1 rounded-md">
              📖 {{ post.bookTitle }}
            </span>
          </div>

          <!-- Content Rangkuman -->
          <div class="p-5 text-xs text-slate-700 leading-relaxed bg-slate-50/50">
            <p class="font-semibold text-slate-500 text-[10px] uppercase tracking-wider mb-1">Rangkuman / Catatan:</p>
            "{{ post.summary }}"
          </div>

          <!-- Interaction Bar (Love & Count) -->
          <div class="px-5 py-3 border-t border-slate-50 flex items-center space-x-6 text-xs">
            <button 
              @click="toggleLike(post)" 
              :disabled="!isBolehInteraksi"
              class="flex items-center space-x-1.5 font-bold transition"
              :class="[
                !isBolehInteraksi 
                  ? 'text-slate-300 cursor-not-allowed' 
                  : post.isLiked 
                    ? 'text-rose-500 cursor-pointer' 
                    : 'text-slate-400 hover:text-rose-500 cursor-pointer'
              ]"
            >
              <span class="text-base">{{ post.isLiked ? '❤️' : '🤍' }}</span>
              <span>{{ post.likes }} Suka</span>
              <span v-if="!isBolehInteraksi" class="text-[10px] font-normal text-slate-400 ml-1">(Terkunci)</span>
            </button>

            <div class="flex items-center space-x-1.5 font-bold text-slate-400">
              <span class="text-base">💬</span>
              <span>{{ post.comments.length }} Komentar</span>
            </div>
          </div>

          <!-- Comment Section -->
          <div class="px-5 py-3 bg-slate-50/80 border-t border-slate-100 space-y-2">
            <!-- Comment List -->
            <div v-for="(comment, cIndex) in post.comments" :key="cIndex" class="text-xs">
              <span class="font-bold text-slate-800" :class="{'text-indigo-600': comment.isTeacher}">
                {{ comment.author }} <span v-if="comment.isTeacher" class="text-[9px] bg-indigo-100 text-indigo-700 px-1 rounded">GURU</span>:
              </span>
              <span class="text-slate-600 ml-1">{{ comment.text }}</span>
            </div>

            <!-- Comment Input (Aktif jika sesuai jadwal) -->
            <form v-if="isBolehInteraksi" @submit.prevent="addComment(post)" class="mt-3 flex gap-2 pt-2">
              <input 
                v-model="post.newCommentText" 
                type="text" 
                placeholder="Tulis komentar atau pujian guru/teman..." 
                class="flex-1 bg-white border border-slate-200 rounded-xl px-3 py-1.5 text-xs focus:outline-none focus:border-indigo-500"
              />
              <button 
                type="submit" 
                class="bg-indigo-600 hover:bg-indigo-700 text-white px-3 py-1.5 rounded-xl font-bold text-xs transition cursor-pointer"
              >
                Kirim
              </button>
            </form>
            
            <p v-else class="text-[10px] text-slate-400 italic pt-1">
              🔒 Komentar terkunci untuk Kelas {{ selectedClass }} hari ini.
            </p>
          </div>

        </div>
      </div>
    </section>

    <!-- FOOTER -->
    <footer class="text-center text-xs text-slate-400 mt-12">
      <p>© {{ new Date().getFullYear() }} SD Negeri Pucung. Domain: <span class="font-semibold text-slate-600">sdnpucung.my.id</span></p>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

// --- STATE TOGGLE MENU HP ---
const isMenuOpen = ref(false)

// --- KONFIGURASI REST API CPANEL ---
const API_URL = 'https://api-literasi.sdnpucung.my.id';

// State untuk status pengiriman & loading
const isSubmitting = ref(false)
const isLoadingPosts = ref(false)

// --- STATE PENJADWALAN & PILIHAN KELAS (DENGAN LOCALSTORAGE) ---
const selectedClass = ref(1) // Default awal jika belum pernah memilih

// Mengambil data kelas & data rangkuman dari MySQL saat komponen dimuat
onMounted(() => {
  const savedClass = localStorage.getItem('user_selected_class')
  if (savedClass) {
    selectedClass.value = Number(savedClass)
  }
  fetchJournalPosts()
})

// Menyimpan otomatis ke localStorage setiap kali siswa mengganti kelas
watch(selectedClass, (newClass) => {
  localStorage.setItem('user_selected_class', newClass)
})

// Ambil Nama Hari Saat Ini (Sistem Lokal HP/Laptop Siswa)
const currentDayName = new Date().toLocaleDateString('id-ID', { weekday: 'long' })

// Pemetaan Jadwal Akses Interaksi
const scheduleMatrix = {
  'Senin': 1,
  'Selasa': 2,
  'Rabu': 3,
  'Kamis': 4,
  'Jumat': 5,
  'Sabtu': 6
}

// Teks Keterangan Jadwal Hari Ini
const activeScheduleText = computed(() => {
  const allowedClass = scheduleMatrix[currentDayName]
  return allowedClass ? `Siswa Kelas ${allowedClass}` : 'Semua Kelas (Libur Hari Minggu)'
})

// Cek Apakah Kelas Siswa Berhak Berinteraksi Hari Ini
const isBolehInteraksi = computed(() => {
  if (currentDayName === 'Minggu') return true // Bebas akses di hari Minggu
  return scheduleMatrix[currentDayName] === Number(selectedClass.value)
})

// --- KATALOG BUKU ---
const selectedCategory = ref('Semua')
const categories = ['Semua', 'Kemdikdasmen', 'Dongeng Nusantara', 'Kelas 1-3', 'Kelas 4-6']

const books = ref([
  {
    id: 1,
    title: 'Kancil & Buaya Cerdik',
    category: 'Kemdikdasmen',
    targetClass: 'Kelas 1-3',
    synopsis: 'Kisah kancil yang cerdik menyeberangi sungai dengan menghitung buaya.',
    coverIcon: '🦊',
    url: 'https://buku.kemdikbud.go.id'
  },
  {
    id: 2,
    title: 'Misteri Rumah Pohon',
    category: 'Kelas 4-6',
    targetClass: 'Kelas 4-6',
    synopsis: 'Petualangan 3 sahabat menemukan rahasia di dalam hutan desa.',
    coverIcon: '🌳',
    url: 'https://buku.kemdikbud.go.id'
  },
  {
    id: 3,
    title: 'Legenda Malin Kundang',
    category: 'Dongeng Nusantara',
    targetClass: 'Kelas 4-6',
    synopsis: 'Cerita rakyat tentang keutamaan berbakti kepada orang tua.',
    coverIcon: '⛵',
    url: 'https://buku.kemdikbud.go.id'
  }
])

const filteredBooks = computed(() => {
  if (selectedCategory.value === 'Semua') return books.value
  return books.value.filter(b => b.category === selectedCategory.value || b.targetClass === selectedCategory.value)
})

// --- JURNAL MEMBACA / LEMBAR KERJA ---
const newEntry = ref({
  studentName: '',
  bookTitle: '',
  summary: ''
})

const journalPosts = ref([])

// AMBIL DATA RANGKUMAN DARI BACKEND CPANEL (MYSQL)
const fetchJournalPosts = async () => {
  isLoadingPosts.value = true
  try {
    const response = await fetch(`${API_URL}/get_journals.php`)
    if (response.ok) {
      const data = await response.json()
      journalPosts.value = data.map(item => ({
        id: item.id,
        studentName: item.student_name,
        studentClass: item.student_class,
        timeAgo: item.created_at || 'Baru saja',
        bookTitle: item.book_title,
        summary: item.summary,
        likes: item.likes || 0,
        isLiked: false,
        newCommentText: '',
        comments: item.comments || []
      }))
    }
  } catch (error) {
    console.error('Gagal mengambil data dari MySQL:', error)
  } finally {
    isLoadingPosts.value = false
  }
}

// SUBMIT LEMBAR KERJA KE BACKEND CPANEL (MYSQL)
const submitWorksheet = async () => {
  if (!isBolehInteraksi.value) return
  if (!newEntry.value.studentName || !newEntry.value.summary) return

  isSubmitting.value = true

  const payload = {
    studentName: newEntry.value.studentName,
    studentClass: selectedClass.value,
    bookTitle: newEntry.value.bookTitle || 'Buku Cerita',
    summary: newEntry.value.summary
  }

  try {
    const response = await fetch(`${API_URL}/save_journal.php`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(payload)
    })

    const result = await response.json()

    if (response.ok && result.success) {
      // Refresh feed agar data paling baru dari MySQL tampil
      await fetchJournalPosts()

      alert('✨ Rangkumanmu berhasil terkirim dan tersimpan aman di server!')
      newEntry.value.studentName = ''
      newEntry.value.bookTitle = ''
      newEntry.value.summary = ''
    } else {
      throw new Error(result.message || 'Gagal menyimpan ke database')
    }

  } catch (error) {
    console.error('Error:', error)
    alert('⚠️ Gagal mengirim rangkuman. Periksa koneksi internetmu ya!')
  } finally {
    isSubmitting.value = false
  }
}

const toggleLike = (post) => {
  if (!isBolehInteraksi.value) return
  post.isLiked = !post.isLiked
  post.likes += post.isLiked ? 1 : -1
}

const addComment = (post) => {
  if (!isBolehInteraksi.value) return
  if (!post.newCommentText.trim()) return

  post.comments.push({
    author: 'Siswa / Guru',
    text: post.newCommentText,
    isTeacher: false
  })
  post.newCommentText = ''
}

const getInitials = (name) => {
  if (!name) return 'SD'
  return name.split(' ').map(n => n[0]).join('').toUpperCase().slice(0, 2)
}
</script>