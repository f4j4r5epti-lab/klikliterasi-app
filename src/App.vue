<template>
  <div class="literacy-portal font-sans bg-amber-50/20 min-h-screen text-slate-800">
    
    <!-- ========================================== -->
    <!-- HALAMAN 1: HALAMAN LOGIN AWAL (BELUM LOGIN) -->
    <!-- ========================================== -->
    <div v-if="!currentStudent" class="min-h-screen flex flex-col justify-between p-6 bg-gradient-to-br from-indigo-50 via-amber-50/30 to-purple-50">
      <header class="max-w-md mx-auto w-full text-center pt-8">
        <div class="w-16 h-16 bg-indigo-600 rounded-2xl flex items-center justify-center text-white font-black text-2xl shadow-xl shadow-indigo-200 mx-auto mb-3">
          SD
        </div>
        <h1 class="text-2xl font-black text-slate-800 tracking-tight">SD NEGERI PUCUNG</h1>
        <p class="text-xs font-bold text-indigo-600 tracking-widest uppercase mt-1">Portal Klik Literasi</p>
      </header>

      <main class="max-w-md mx-auto w-full my-auto py-8">
        <div class="bg-white p-8 rounded-3xl shadow-2xl border border-slate-100">
          <div class="text-center mb-6">
            <h2 class="text-xl font-extrabold text-slate-800">Selamat Datang! 👋</h2>
            <p class="text-xs text-slate-500 mt-1">Pilih kelas, nama lengkap, dan masukkan PIN/Password Anda.</p>
          </div>

          <form @submit.prevent="handleLogin" class="space-y-4">
            <!-- 1. Form Input Pilihan Kelas / Peran -->
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">
                Pilih Kelas / Peran
              </label>
              <select
                v-model="loginForm.classLevel"
                required
                class="w-full p-3.5 bg-slate-50 border border-slate-200 rounded-xl text-xs font-semibold focus:outline-none focus:border-indigo-600 focus:bg-white transition"
              >
                <option :value="null" disabled>-- Pilih Kelas / Peran Anda --</option>
                <!-- OPSI GURU / ADMIN DENGAN VALUE 0 -->
                <option :value="0">Guru / Admin</option>
                <!-- OPSI KELAS SISWA 1-6 -->
                <option :value="1">Kelas 1</option>
                <option :value="2">Kelas 2</option>
                <option :value="3">Kelas 3</option>
                <option :value="4">Kelas 4</option>
                <option :value="5">Kelas 5</option>
                <option :value="6">Kelas 6</option>
              </select>
            </div>

            <!-- 2. Auto-complete Nama (Menggunakan filteredUserOptions) -->
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">
                Nama Lengkap
              </label>
              <input
                v-model="loginForm.full_name"
                type="text"
                list="user-suggestions"
                placeholder="Masukkan atau pilih nama..."
                required
                class="w-full p-3.5 bg-slate-50 border border-slate-200 rounded-xl text-xs font-semibold focus:outline-none focus:border-indigo-600 focus:bg-white transition"
              />

              <!-- Datalist rekomendasi nama sesuai kelas/peran yang dipilih -->
              <datalist id="user-suggestions">
                <option 
                  v-for="user in filteredUserOptions" 
                  :key="user.id" 
                  :value="user.full_name"
                >
                  {{ formatClassDisplay(user.class_level) }}
                </option>
              </datalist>
            </div>

            <!-- 3. PIN / Password (Dinamis sesuai Peran) -->
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">
                {{ loginForm.classLevel === 0 ? 'Password Guru / Admin' : 'PIN Siswa (4-6 Digit)' }}
              </label>
              <input
                v-model="loginForm.password"
                :type="loginForm.classLevel === 0 ? 'password' : 'password'"
                :placeholder="loginForm.classLevel === 0 ? 'Masukkan password admin...' : 'Masukkan PIN angka...'"
                required
                class="w-full p-3.5 bg-slate-50 border border-slate-200 rounded-xl text-xs font-semibold focus:outline-none focus:border-indigo-600 focus:bg-white transition"
              />
            </div>

            <!-- Error Message -->
            <div v-if="loginError" class="p-3 bg-rose-50 border border-rose-200 rounded-xl text-xs text-rose-600 font-medium">
              ⚠️ {{ loginError }}
            </div>

            <button 
              type="submit" 
              :disabled="isLoggingIn"
              class="w-full py-3.5 bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-300 text-white font-bold text-xs rounded-xl shadow-lg shadow-indigo-600/30 transition duration-200 cursor-pointer"
            >
              <span v-if="isLoggingIn">Memeriksa Data...</span>
              <span v-else>Masuk ke Portal Literasi 🚀</span>
            </button>
          </form>
        </div>
      </main>

      <footer class="text-center text-xs text-slate-400 pb-4">
        <p>© {{ new Date().getFullYear() }} SD Negeri Pucung. Domain: <span class="font-semibold text-slate-600">sdnpucung.my.id</span></p>
      </footer>
    </div>


    <!-- ========================================== -->
    <!-- HALAMAN 2: PORTAL LITERASI (SUDAH LOGIN)   -->
    <!-- ========================================== -->
    <div v-else class="pb-12">
      <!-- NAVBAR -->
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

          <nav class="hidden md:flex items-center space-x-8 font-medium text-slate-600">
            <a href="#beranda" class="hover:text-indigo-600 transition">Beranda</a>
            <a href="#katalog" class="hover:text-indigo-600 transition">Katalog Buku</a>
            <a href="#jurnal" class="hover:text-indigo-600 transition">Jurnal Membaca</a>
            <a href="#jadwal" class="hover:text-indigo-600 transition">Jadwal Akses</a>
          </nav>

          <!-- AKUN & LOGOUT -->
          <div class="flex items-center gap-3">
            <div class="flex items-center gap-2 bg-emerald-50 border border-emerald-200 px-3 py-1.5 rounded-xl">
              <span class="text-xs font-bold text-emerald-800">
                👤 {{ currentStudent.full_name }} ({{ formatClassDisplay(currentStudent.class_level) }})
              </span>
              <button @click="handleLogout" class="px-2.5 py-1 bg-red-500 hover:bg-red-600 text-white rounded-lg text-xs font-bold transition cursor-pointer">
                Keluar
              </button>
            </div>

            <button @click="isMenuOpen = !isMenuOpen" class="md:hidden text-2xl p-1 text-slate-600 focus:outline-none">
              {{ isMenuOpen ? '✖' : '☰' }}
            </button>
          </div>
        </div>

        <div v-if="isMenuOpen" class="md:hidden mt-4 pt-3 border-t border-slate-100 flex flex-col space-y-3 font-medium text-sm text-slate-600">
          <a href="#beranda" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Beranda</a>
          <a href="#katalog" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Katalog Buku</a>
          <a href="#jurnal" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Jurnal Membaca</a>
          <a href="#jadwal" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Jadwal Akses</a>
        </div>
      </header>

      <!-- BANNER HARI & PENJADWALAN -->
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
            <p class="text-xs text-indigo-100 font-medium">
              Status Anda ({{ formatClassDisplay(currentStudent.class_level) }}):
            </p>
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
        <div v-if="!isBolehInteraksi" class="bg-amber-50 border-l-4 border-amber-400 p-4 mb-6 rounded-r-2xl text-amber-800 text-xs md:text-sm">
          🔒 <strong>Akses Interaksi Terkunci:</strong> Anda berada di {{ formatClassDisplay(currentStudent.class_level) }}. Hari ini jadwal interaksi khusus <strong>{{ activeScheduleText }}</strong>. Anda tetap bebas membaca buku di bawah ini.
        </div>

        <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-6">
          <div>
            <h2 class="text-2xl font-black text-slate-800">Daftar Buku Bacaan</h2>
            <p class="text-xs text-slate-500">Semua pengguna terdaftar dapat membaca buku di bawah ini kapan saja</p>
          </div>

          <div class="flex flex-wrap gap-2">
            <button 
              v-for="cat in categories" 
              :key="cat"
              @click="selectedCategory = cat"
              :class="selectedCategory === cat ? 'bg-indigo-600 text-white shadow-md shadow-indigo-200' : 'bg-white text-slate-600 border border-slate-200 hover:bg-slate-50'"
              class="px-4 py-2 rounded-xl text-xs font-bold transition cursor-pointer"
            >
              {{ cat }}
            </button>
          </div>
        </div>

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

        <!-- FORM INPUT MERANGKUM -->
        <div class="bg-white p-6 rounded-3xl shadow-lg border border-slate-100 mb-10">
          <h3 class="font-extrabold text-slate-800 text-base mb-4 flex items-center space-x-2">
            <span>✍️</span> <span>Buat Lembar Kerja Baru</span>
          </h3>
          
          <form v-if="isBolehInteraksi" @submit.prevent="submitWorksheet" class="space-y-4">
            <div class="grid sm:grid-cols-2 gap-4">
              <input 
                v-model="newEntry.studentName" 
                type="text" 
                placeholder="Nama Lengkap" 
                required
                readonly
                class="w-full bg-slate-100 border border-slate-200 rounded-xl px-4 py-2.5 text-xs font-bold text-slate-700 cursor-not-allowed focus:outline-none"
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

          <div v-else class="p-8 text-center bg-slate-50 border border-dashed border-slate-300 rounded-2xl">
            <span class="text-4xl block mb-2">🔒</span>
            <h4 class="font-bold text-slate-800 text-sm">Pengisian Rangkuman Terkunci</h4>
            <p class="text-xs text-slate-500 max-w-md mx-auto mt-1 leading-relaxed">
              Mohon maaf, fitur menulis jurnal untuk <b>{{ formatClassDisplay(currentStudent.class_level) }}</b> baru dibuka pada hari jadwal kelasmu. Hari ini dikhususkan untuk <b>{{ activeScheduleText }}</b>.
            </p>
          </div>
        </div>

        <!-- FEED JURNAL PERMANEN -->
        <div class="space-y-6">
          <div v-if="isLoadingPosts" class="text-center py-8 text-slate-400 text-xs">
            ⏳ Memuat rangkuman dari database...
          </div>

          <div v-else-if="journalPosts.length === 0" class="text-center py-8 text-slate-400 text-xs bg-white rounded-3xl p-6 border border-slate-100">
            Belum ada rangkuman yang dikirim. Jadilah yang pertama mengisi! 📝
          </div>

          <div v-for="post in journalPosts" :key="post.id" class="bg-white rounded-3xl border border-slate-100 shadow-sm overflow-hidden">
            <div class="p-4 border-b border-slate-50 flex items-center justify-between">
              <div class="flex items-center space-x-3">
                <div class="w-9 h-9 rounded-full bg-gradient-to-tr from-amber-400 to-indigo-500 flex items-center justify-center text-white font-bold text-xs">
                  {{ getInitials(post.studentName) }}
                </div>
                <div>
                  <h4 class="font-extrabold text-slate-800 text-xs">{{ post.studentName }}</h4>
                  <p class="text-[10px] text-slate-400">{{ post.studentClass }}</p>
                </div>
              </div>
              <span class="bg-indigo-50 text-indigo-600 text-[10px] font-bold px-2.5 py-1 rounded-md">
                📖 {{ post.bookTitle }}
              </span>
            </div>

            <div class="p-5 text-xs text-slate-700 leading-relaxed bg-slate-50/50">
              <p class="font-semibold text-slate-500 text-[10px] uppercase tracking-wider mb-1">Rangkuman / Catatan:</p>
              "{{ post.summary }}"
            </div>

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
              </button>

              <div class="flex items-center space-x-1.5 font-bold text-slate-400">
                <span class="text-base">💬</span>
                <span>{{ post.comments ? post.comments.length : 0 }} Komentar</span>
              </div>
            </div>

            <div class="px-5 py-3 bg-slate-50/80 border-t border-slate-100 space-y-2">
              <div v-for="(comment, cIndex) in post.comments" :key="cIndex" class="text-xs">
                <span class="font-bold text-slate-800" :class="{'text-indigo-600': comment.isTeacher}">
                  {{ comment.author }}:
                </span>
                <span class="text-slate-600 ml-1">{{ comment.text }}</span>
              </div>

              <form v-if="isBolehInteraksi" @submit.prevent="addComment(post)" class="mt-3 flex gap-2 pt-2">
                <input 
                  v-model="post.newCommentText" 
                  type="text" 
                  placeholder="Tulis komentar atau pujian..." 
                  class="flex-1 bg-white border border-slate-200 rounded-xl px-3 py-1.5 text-xs focus:outline-none focus:border-indigo-500"
                />
                <button 
                  type="submit" 
                  class="bg-indigo-600 hover:bg-indigo-700 text-white px-3 py-1.5 rounded-xl font-bold text-xs transition cursor-pointer"
                >
                  Kirim
                </button>
              </form>
            </div>
          </div>
        </div>
      </section>

      <!-- FOOTER -->
      <footer class="text-center text-xs text-slate-400 mt-12">
        <p>© {{ new Date().getFullYear() }} SD Negeri Pucung. Domain: <span class="font-semibold text-slate-600">sdnpucung.my.id</span></p>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const isMenuOpen = ref(false)

// URL API ke subdomain api.klikliterasi
const API_URL = 'https://api-literasi.sdnpucung.my.id/api.php'

const isSubmitting = ref(false)
const isLoadingPosts = ref(false)
const isLoadingStudents = ref(false)

// --- STATE LOGIN & USER ---
const isLoggingIn = ref(false)
const loginError = ref('')
const currentStudent = ref(null)

// Form Login dengan input Password/PIN
const loginForm = ref({ 
  full_name: '', 
  classLevel: null,
  password: '' 
})
const studentList = ref([]) // Master data siswa & guru dari database

// --- COMPUTED OPTIONS NAMA UNTUK FORM LOGIN ---
const filteredUserOptions = computed(() => {
  if (loginForm.value.classLevel === '' || loginForm.value.classLevel === null) {
    return studentList.value
  }
  return studentList.value.filter(
    user => Number(user.class_level) === Number(loginForm.value.classLevel)
  )
})

// --- HELPER FORMAT & EKSTRAKSI ---
const formatClassDisplay = (val) => {
  if (val === null || val === undefined || val === '') return ''
  const num = Number(val)
  if (num === 0) return 'Guru / Admin'
  const str = String(val)
  return str.startsWith('Kelas') ? str : `Kelas ${str}`
}

const getNumericClassLevel = (val) => {
  if (val === null || val === undefined || val === '') return 0
  const match = String(val).match(/\d+/)
  return match ? parseInt(match[0], 10) : 0
}

// --- FETCH DATA MASTER & SESSION ---
const fetchStudentList = async () => {
  isLoadingStudents.value = true
  try {
    const response = await fetch(`${API_URL}?action=get_students`)
    const result = await response.json()
    if (result.status === 'success') {
      studentList.value = result.data
    }
  } catch (error) {
    console.error('Error fetching student list:', error)
  } finally {
    isLoadingStudents.value = false
  }
}

onMounted(() => {
  // 1. Ambil data master siswa & guru dari database untuk autocomplete
  fetchStudentList()

  // 2. Cek sesi login tersimpan
  const savedUser = localStorage.getItem('logged_student')
  if (savedUser) {
    currentStudent.value = JSON.parse(savedUser)
    newEntry.value.studentName = currentStudent.value.full_name
    fetchJournalPosts()
  }
})

// --- AKSI LOGIN & LOGOUT ---
const handleLogin = async () => {
  isLoggingIn.value = true
  loginError.value = ''

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        action: 'login',
        full_name: loginForm.value.full_name.trim(),
        class_level: loginForm.value.classLevel,
        password: loginForm.value.password // Dikirim ke PHP untuk verifikasi
      })
    })

    const result = await response.json()
    if (result.status === 'success') {
      currentStudent.value = result.student
      localStorage.setItem('logged_student', JSON.stringify(result.student))
      newEntry.value.studentName = result.student.full_name
      loginForm.value = { full_name: '', classLevel: null, password: '' }
      
      // Ambil data jurnal setelah berhasil masuk
      fetchJournalPosts()
    } else {
      loginError.value = result.message || 'Nama, Kelas, atau PIN/Password tidak cocok dengan database.'
    }
  } catch (error) {
    loginError.value = 'Terjadi kesalahan koneksi ke server.'
  } finally {
    isLoggingIn.value = false
  }
}

const handleLogout = () => {
  currentStudent.value = null
  localStorage.removeItem('logged_student')
  newEntry.value.studentName = ''
}

// --- LOGIKA HARI DAN JADWAL AKSES ---
const todayIndex = new Date().getDay()
const daysNameList = ['Minggu', 'Senin', 'Selasa', 'Rabu', 'Kamis', 'Jumat', 'Sabtu']
const currentDayName = daysNameList[todayIndex]

const scheduleMatrix = {
  'Senin': 1,
  'Selasa': 2,
  'Rabu': 3,
  'Kamis': 4,
  'Jumat': 5,
  'Sabtu': 6
}

const activeScheduleText = computed(() => {
  const allowedClass = scheduleMatrix[currentDayName]
  return allowedClass ? `Siswa Kelas ${allowedClass}` : 'Semua Kelas (Bebas / Minggu)'
})

const isBolehInteraksi = computed(() => {
  if (!currentStudent.value) return false
  
  const userClassNum = getNumericClassLevel(currentStudent.value.class_level)
  
  // 1. GURU / ADMIN (Level 0) BISA INTERAKSI SETIAP HARI
  if (userClassNum === 0) return true 
  
  // 2. HARI MINGGU SEMUA KELAS BEBAS INTERAKSI
  if (currentDayName === 'Minggu') return true 
  
  // 3. SISWA SESUAI JADWAL HARI BERJALAN
  return scheduleMatrix[currentDayName] === userClassNum
})

// --- DATA KATALOG BUKU & JURNAL ---
const selectedCategory = ref('Semua')
const categories = ['Semua', 'Kemdikdasmen', 'Dongeng Nusantara', 'Kelas 1-3', 'Kelas 4-6']

const books = ref([
  { id: 1, title: 'Apa Itu', category: 'Kemdikdasmen', targetClass: 'Kelas 1-3', coverIcon: '🦊', url: 'https://buku.kemendikdasmen.go.id/katalog/apa-itu-edisi-buku-bahasa-isyarat', synopsis: 'Buku cerita bergambar interaktif dengan fitur bahasa isyarat.' },
  { id: 2, title: 'Ini atau Itu', category: 'Kelas 4-6', targetClass: 'Kelas 4-6', coverIcon: '🌳', url: 'https://buku.kemendikdasmen.go.id/katalog/ini-atau-itu-edisi-braille', synopsis: 'Membantu memahami konsep pilihan dan konsekuensi melalui cerita interaktif.' }
])

const filteredBooks = computed(() => {
  if (selectedCategory.value === 'Semua') return books.value
  return books.value.filter(b => b.category === selectedCategory.value || b.targetClass === selectedCategory.value)
})

const newEntry = ref({ studentName: '', bookTitle: '', summary: '' })
const journalPosts = ref([])

const fetchJournalPosts = async () => {
  isLoadingPosts.value = true
  try {
    const response = await fetch(API_URL)
    const result = await response.json()
    if (result.status === 'success') {
      journalPosts.value = result.data.map(post => ({
        ...post,
        comments: post.comments || [],
        newCommentText: ''
      }))
    }
  } catch (error) {
    console.error('Error fetching posts:', error)
  } finally {
    isLoadingPosts.value = false
  }
}

const submitWorksheet = async () => {
  if (!isBolehInteraksi.value || !currentStudent.value) return
  if (!newEntry.value.summary) return

  isSubmitting.value = true

  const payload = {
    action: 'add_post',
    studentName: currentStudent.value.full_name,
    studentClass: formatClassDisplay(currentStudent.value.class_level),
    bookTitle: newEntry.value.bookTitle || 'Buku Cerita',
    summary: newEntry.value.summary
  }

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })
    const result = await response.json()

    if (result.status === 'success') {
      alert('✨ Rangkumanmu berhasil terkirim!')
      newEntry.value.bookTitle = ''
      newEntry.value.summary = ''
      fetchJournalPosts()
    } else {
      alert('⚠️ Gagal mengirim: ' + (result.message || 'Terjadi kesalahan'))
    }
  } catch (error) {
    console.error('Error:', error)
    alert('⚠️ Gagal mengirim rangkuman. Periksa koneksi internetmu!')
  } finally {
    isSubmitting.value = false
  }
}

const toggleLike = async (post) => {
  if (!isBolehInteraksi.value || !currentStudent.value) return
  
  const currentLikes = Number(post.likes) || 0

  post.isLiked = !post.isLiked
  post.likes = post.isLiked ? currentLikes + 1 : Math.max(0, currentLikes - 1)

  const payload = {
    action: 'toggle_like',
    postId: post.id,
    studentClass: formatClassDisplay(currentStudent.value.class_level)
  }

  try {
    await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })
  } catch (error) {
    console.error('Error sending like:', error)
  }
}

const addComment = async (post) => {
  if (!isBolehInteraksi.value || !currentStudent.value) return
  if (!post.newCommentText?.trim()) return

  const commentText = post.newCommentText.trim()
  const authorName = `${currentStudent.value.full_name} (${formatClassDisplay(currentStudent.value.class_level)})`

  if (!post.comments) post.comments = []

  post.comments.push({
    author: authorName,
    text: commentText,
    isTeacher: Number(currentStudent.value.class_level) === 0
  })
  
  post.newCommentText = ''

  const payload = {
    action: 'add_comment',
    postId: post.id,
    author: authorName,
    text: commentText
  }

  try {
    await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })
  } catch (error) {
    console.error('Error sending comment:', error)
  }
}

const getInitials = (name) => {
  if (!name) return 'SS'
  return name.split(' ').map(n => n[0]).join('').toUpperCase().slice(0, 2)
}
</script>