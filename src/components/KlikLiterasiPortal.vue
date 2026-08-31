<template>
  <div class="literacy-portal font-sans bg-amber-50/20 min-h-screen text-slate-800">
    
    <!-- DASHBOARD LOGIN -->
    <div 
      v-if="!currentStudent" 
      class="min-h-screen w-full relative flex flex-col items-center justify-center bg-cover bg-center bg-no-repeat overflow-x-hidden p-4 sm:p-6"
      style="background-image: url('/bg-login.jpg');"
    >
      <div class="absolute inset-0 bg-black/40 backdrop-blur-[1px]"></div>

      <div class="relative z-10 text-center mb-6">
        <div class="w-14 h-14 bg-indigo-600 rounded-2xl flex items-center justify-center text-white font-black text-xl shadow-xl shadow-indigo-900/40 mx-auto mb-2 border border-white/20">
          SD
        </div>
        <h1 class="text-2xl font-black text-white tracking-wide drop-shadow-md">SD NEGERI PUCUNG</h1>
        <p class="text-[11px] font-bold text-amber-300 tracking-widest uppercase mt-0.5">Portal Klik Literasi</p>
      </div>

      <div class="relative z-10 w-full max-w-md p-6 sm:p-8 bg-white/95 backdrop-blur-md rounded-3xl shadow-2xl border border-white/40">
        <div class="text-center mb-5">
          <h2 class="text-xl font-extrabold text-slate-800 flex items-center justify-center gap-1.5">
            Selamat Datang! <span class="text-xl">👋</span>
          </h2>
          <p class="text-xs text-slate-500 mt-1">
            Pilih kelas, nama lengkap, dan masukkan PIN/Password Anda.
          </p>
        </div>

        <form @submit.prevent="handleLogin" class="space-y-4">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5">
              Pilih Kelas / Peran
            </label>
            <select
              v-model="loginForm.classLevel"
              required
              class="w-full bg-slate-50/90 border border-slate-200 rounded-xl px-4 py-3 text-xs font-semibold text-slate-800 focus:outline-none focus:border-indigo-500 focus:bg-white transition cursor-pointer"
            >
              <option :value="null" disabled>-- Pilih Kelas / Peran Anda --</option>
              <option :value="0">👨‍🏫 Guru / Admin</option>
              <option v-for="c in 6" :key="c" :value="c">Kelas {{ c }}</option>
            </select>
          </div>

          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5">
              {{ Number(loginForm.classLevel) === 0 ? 'Nama Lengkap Guru' : 'Nama Lengkap' }}
            </label>
            <input
              v-model="loginForm.full_name"
              type="text"
              list="user-suggestions"
              :placeholder="Number(loginForm.classLevel) === 0 ? 'Ketik / Pilih Nama Guru...' : 'Masukkan atau pilih nama siswa...'"
              required
              class="w-full bg-slate-50/90 border border-slate-200 rounded-xl px-4 py-3 text-xs font-semibold text-slate-800 focus:outline-none focus:border-indigo-500 focus:bg-white transition"
            />

            <datalist id="user-suggestions">
              <option 
                v-for="user in filteredUserOptions" 
                :key="user.id" 
                :value="user.full_name"
              />
            </datalist>
          </div>

          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1.5">
              {{ Number(loginForm.classLevel) === 0 ? 'Password Guru / Admin' : 'PIN Siswa (4-6 Digit)' }}
            </label>
            <input
              v-model="loginForm.password"
              type="password"
              :placeholder="Number(loginForm.classLevel) === 0 ? 'Masukkan password admin...' : 'Masukkan PIN angka...'"
              required
              class="w-full bg-slate-50/90 border border-slate-200 rounded-xl px-4 py-3 text-xs font-semibold text-slate-800 focus:outline-none focus:border-indigo-500 focus:bg-white transition"
            />
          </div>

          <div v-if="loginError" class="p-3 bg-rose-50 border border-rose-200 rounded-xl text-xs text-rose-600 font-medium">
            ⚠️ {{ loginError }}
          </div>

          <button 
            type="submit" 
            :disabled="isLoggingIn"
            class="w-full py-3.5 bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-300 text-white font-bold text-xs rounded-xl shadow-lg shadow-indigo-600/30 transition duration-200 cursor-pointer flex items-center justify-center space-x-2 mt-2"
          >
            <span v-if="isLoggingIn">⏳ Memeriksa Data...</span>
            <span v-else>Masuk ke Portal Literasi 🚀</span>
          </button>
        </form>

        <footer class="text-center text-[10px] text-slate-400 mt-6 pt-4 border-t border-slate-100">
          <p>© {{ new Date().getFullYear() }} SD Negeri Pucung. Domain: <span class="font-semibold text-slate-500">sdnpucung.my.id</span></p>
        </footer>
      </div>
    </div>

    <!-- DASHBOARD UTAMA -->
    <div v-else class="pb-12">
      <!-- HEADER / NAVBAR -->
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

          <!-- NAVIGASI DESKTOP -->
          <nav class="hidden md:flex items-center space-x-8 font-medium text-slate-600 text-sm">
            <a href="#beranda" class="hover:text-indigo-600 transition font-semibold">Beranda</a>
            <a href="#katalog" class="hover:text-indigo-600 transition">Katalog Buku</a>
            <a href="#jurnal" class="hover:text-indigo-600 transition">Jurnal Membaca</a>
            <a href="#jadwal" class="hover:text-indigo-600 transition">Jadwal Akses</a>
          </nav>

          <div class="flex items-center space-x-3">
            <div class="flex items-center space-x-1.5 bg-slate-100 p-1.5 rounded-xl border border-slate-200 text-xs">
              <span class="font-bold text-slate-700 pl-1">👤 {{ currentStudent.full_name }}</span>
              <span class="bg-indigo-100 text-indigo-700 px-2 py-0.5 rounded-md font-extrabold">
                {{ Number(currentStudent.class_level) === 0 ? 'Guru / Admin' : `Kelas ${currentStudent.class_level}` }}
              </span>
            </div>

            <button 
              @click="handleLogout" 
              class="text-xs font-bold bg-rose-50 text-rose-600 hover:bg-rose-100 px-3 py-1.5 rounded-lg border border-rose-200 transition cursor-pointer"
            >
              Keluar
            </button>

            <button @click="isMenuOpen = !isMenuOpen" class="md:hidden text-2xl p-1 text-slate-600 focus:outline-none">
              {{ isMenuOpen ? '✖' : '☰' }}
            </button>
          </div>
        </div>

        <!-- NAVIGASI MOBILE -->
        <div v-if="isMenuOpen" class="md:hidden mt-4 pt-3 border-t border-slate-100 flex flex-col space-y-3 font-medium text-sm text-slate-600">
          <a href="#beranda" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Beranda</a>
          <a href="#katalog" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Katalog Buku</a>
          <a href="#jurnal" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Jurnal Membaca</a>
          <a href="#jadwal" @click="isMenuOpen = false" class="hover:text-indigo-600 transition py-1">Jadwal Akses</a>
        </div>
      </header>

      <!-- SECTION 1: BERANDA (HERO SECTION) -->
      <section id="beranda" class="max-w-6xl mx-auto px-6 pt-10 pb-8 grid md:grid-cols-2 gap-8 items-center">
        <div>
          <span class="text-xs font-bold text-indigo-600 bg-indigo-50 px-3 py-1.5 rounded-full border border-indigo-100 inline-block mb-3">
            📖 Portal Literasi SD Negeri Pucung
          </span>
          <h1 class="text-3xl md:text-5xl font-black text-slate-800 leading-tight">
            Jelajahi Dunia Lewat <span class="text-indigo-600">Buku Digital</span>
          </h1>
          <p class="text-slate-600 mt-4 text-sm md:text-base leading-relaxed">
            Baca buku cerita seru, tulis rangkumanmu di Lembar Kerja, dan bagikan kesan membacamu bersama teman-teman!
          </p>
          <div class="mt-6 flex flex-wrap gap-3">
            <a 
              href="#katalog" 
              class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-xs px-6 py-3.5 rounded-2xl shadow-lg shadow-indigo-600/30 transition transform hover:-translate-y-0.5 flex items-center gap-2"
            >
              Mulai Membaca 📖
            </a>
            <a 
              href="#jurnal" 
              class="bg-white hover:bg-slate-50 text-slate-700 font-bold text-xs px-6 py-3.5 rounded-2xl border border-slate-200 transition flex items-center gap-2"
            >
              Tulis Rangkuman ✍️
            </a>
          </div>
        </div>

        <div class="relative flex justify-center">
          <div class="w-full max-w-sm bg-white p-6 rounded-3xl shadow-xl border border-slate-100 relative z-10 text-center">
            <div class="w-20 h-20 bg-gradient-to-tr from-amber-400 to-indigo-500 rounded-3xl flex items-center justify-center text-white text-4xl mx-auto shadow-lg mb-4">
              📚
            </div>
            <h3 class="font-extrabold text-slate-800 text-lg">Gerakan Literasi Sekolah</h3>
            <p class="text-xs text-slate-500 mt-2 leading-relaxed">
              Membangun minat baca siswa SD Negeri Pucung secara digital, interaktif, dan menyenangkan.
            </p>
          </div>
          <div class="absolute inset-0 bg-indigo-400/20 blur-3xl rounded-full transform scale-90"></div>
        </div>
      </section>

      <!-- SECTION 2: BANNER JADWAL AKSES -->
      <section id="jadwal" class="max-w-6xl mx-auto px-6 mt-4 mb-8">
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
            <p class="text-xs text-indigo-100 font-medium">Status Anda ({{ Number(selectedClass) === 0 ? 'Guru' : 'Kelas ' + selectedClass }}):</p>
            <span 
              :class="isBolehInteraksi ? 'bg-emerald-400 text-emerald-950' : 'bg-rose-400 text-rose-950'"
              class="inline-block mt-2 px-4 py-1.5 rounded-full text-xs font-extrabold shadow-sm"
            >
              {{ isBolehInteraksi ? '✨ BERHAK INTERAKSI' : '🔒 KHUSUS MEMBACA' }}
            </span>
          </div>
        </div>
      </section>

      <!-- SECTION 3: KATALOG BUKU BACAAN -->
      <section id="katalog" class="max-w-6xl mx-auto px-6 py-8">
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

        <div v-if="!isBolehInteraksi" class="bg-amber-50 border border-amber-200 text-amber-800 p-4 rounded-2xl mb-6 text-sm flex items-center space-x-3">
          <span class="text-xl">ℹ️</span>
          <p>
            <strong>Informasi:</strong> Fitur interaksi untuk <strong>Kelas {{ selectedClass }}</strong> terkunci hari ini. Siswa tetap bebas membaca buku, namun pengisian jurnal & komentar dibuka sesuai jadwal harian.
          </p>
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
                class="bg-amber-500 hover:bg-amber-600 text-white text-xs font-bold px-4 py-2 rounded-xl shadow-md shadow-amber-500/20 transition flex items-center gap-1"
              >
                Baca Buku 📖
              </a>
            </div>
          </div>
        </div>
      </section>

      <!-- SECTION 4: JURNAL MEMBACA -->
      <section id="jurnal" class="max-w-4xl mx-auto px-6 py-12">
        <div class="text-center mb-8">
          <h2 class="text-3xl font-black text-slate-800">Jurnal Membaca Siswa</h2>
          <p class="text-sm text-slate-500 mt-1">Tulis rangkuman ceritamu dan berikan apresiasi kepada teman-teman!</p>
        </div>

        <div class="bg-white p-6 rounded-3xl shadow-lg border border-slate-100 mb-10">
          <h3 class="font-extrabold text-slate-800 text-base mb-4 flex items-center space-x-2">
            <span>✍️</span> <span>Buat Lembar Kerja Baru</span>
          </h3>
          
          <form v-if="isBolehInteraksi" @submit.prevent="submitWorksheet" class="space-y-4">
            <div class="grid sm:grid-cols-2 gap-4">
              <div>
                <input 
                  v-model="studentName" 
                  type="text" 
                  disabled
                  class="w-full bg-slate-100 border border-slate-200 rounded-xl px-4 py-2.5 text-xs font-bold text-slate-600 cursor-not-allowed"
                />
              </div>

              <div>
                <select 
                  v-model="newEntry.bookTitle" 
                  required
                  class="w-full bg-slate-50 border border-slate-200 rounded-xl px-4 py-2.5 text-xs font-medium focus:outline-none focus:border-indigo-500 cursor-pointer"
                >
                  <option value="" disabled selected>Pilih Buku yang Dibaca</option>
                  <option v-for="b in books" :key="b.id" :value="b.title">{{ b.title }}</option>
                </select>
              </div>
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
              Mohon maaf, fitur menulis jurnal untuk <b>Kelas {{ selectedClass }}</b> baru dibuka pada hari jadwal kelasmu. Hari ini dikhususkan untuk <b>{{ activeScheduleText }}</b>.
            </p>
          </div>
        </div>

        <div class="space-y-6">
          <div v-if="isLoadingPosts" class="text-center py-8 text-slate-400 text-sm">
            ⏳ Memuat jurnal siswa...
          </div>

          <div v-else-if="journalPosts.length === 0" class="text-center py-8 text-slate-400 text-sm bg-white rounded-3xl border border-slate-100">
            Belum ada rangkuman yang dikirim. Jadilah yang pertama mengisi!
          </div>

          <div v-for="post in journalPosts" :key="post.id" class="bg-white rounded-3xl border border-slate-100 shadow-sm overflow-hidden">
            <div class="p-4 border-b border-slate-50 flex items-center justify-between"
                 :class="{ 'bg-indigo-50/40': Number(post.studentClass) === 0 }">
              
              <div class="flex items-center space-x-3">
                <div class="w-9 h-9 rounded-full flex items-center justify-center text-white font-bold text-xs shadow-sm"
                     :class="Number(post.studentClass) === 0 
                       ? 'bg-gradient-to-tr from-indigo-600 to-purple-600 ring-2 ring-indigo-300' 
                       : 'bg-gradient-to-tr from-amber-400 to-indigo-500'">
                  {{ Number(post.studentClass) === 0 ? '👨‍🏫' : getInitials(post.studentName) }}
                </div>

                <div>
                  <div class="flex items-center space-x-1.5">
                    <h4 class="font-extrabold text-slate-800 text-xs">{{ post.studentName }}</h4>
                    
                    <span v-if="Number(post.studentClass) === 0" 
                          class="bg-indigo-600 text-white text-[9px] font-black px-1.5 py-0.5 rounded-md shadow-sm">
                      👨‍🏫 GURU
                    </span>
                  </div>
                  
                  <p class="text-[10px]" :class="Number(post.studentClass) === 0 ? 'text-indigo-600 font-bold' : 'text-slate-400'">
                    {{ Number(post.studentClass) === 0 ? 'Tenaga Pendidik / Admin' : `Kelas ${post.studentClass}` }}
                  </p>
                </div>
              </div>

              <div class="flex items-center space-x-2">
                <span class="bg-indigo-50 text-indigo-600 text-[10px] font-bold px-2.5 py-1 rounded-md">
                  📖 {{ post.bookTitle }}
                </span>
                
                <button 
                  v-if="post.studentName.toLowerCase() === studentName.trim().toLowerCase() && studentName !== ''" 
                  @click="deletePost(post.id, post.studentName)"
                  class="text-xs text-rose-500 hover:text-rose-700 bg-rose-50 p-1 rounded-md transition"
                  title="Hapus Postingan"
                >
                  🗑️
                </button>
              </div>
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
                <span v-if="!isBolehInteraksi" class="text-[10px] font-normal text-slate-400 ml-1">(Terkunci)</span>
              </button>

              <div class="flex items-center space-x-1.5 font-bold text-slate-400">
                <span class="text-base">💬</span>
                <span>{{ post.comments ? post.comments.length : 0 }} Komentar</span>
              </div>
            </div>

            <div class="px-5 py-3 bg-slate-50/80 border-t border-slate-100 space-y-2">
              <div v-for="comment in post.comments" :key="comment.id" class="text-xs flex items-center justify-between">
                <div class="flex items-center flex-wrap gap-1">
                  <span class="font-bold text-slate-800">
                    {{ comment.author }}
                  </span>

                  <span v-if="comment.isTeacher || comment.author.toLowerCase().includes('guru') || Number(comment.studentClass) === 0" 
                        class="bg-indigo-600 text-white text-[8px] font-extrabold px-1.5 py-0.5 rounded uppercase tracking-wider shadow-sm">
                    👨‍🏫 GURU
                  </span>:

                  <span class="text-slate-700 ml-1" :class="{ 'font-semibold text-indigo-950': comment.isTeacher || Number(comment.studentClass) === 0 }">
                    {{ comment.text }}
                  </span>
                </div>

                <button 
                  v-if="comment.author === studentName || (selectedClass === 0 && comment.author.toLowerCase().includes('guru'))" 
                  @click="deleteComment(comment.id, comment.author)"
                  class="text-[10px] text-slate-400 hover:text-rose-500 ml-2"
                  title="Hapus Komentar"
                >
                  ✖
                </button>
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
              
              <p v-else class="text-[10px] text-slate-400 italic pt-1">
                🔒 Komentar terkunci untuk {{ Number(selectedClass) === 0 ? 'Guru' : 'Kelas ' + selectedClass }} hari ini.
              </p>
            </div>
          </div>
        </div>
      </section>

      <footer class="text-center text-xs text-slate-400 mt-12">
        <p>© {{ new Date().getFullYear() }} SD Negeri Pucung. Domain: <span class="font-semibold text-slate-600">sdnpucung.my.id</span></p>
      </footer>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

// --- CONSTANTS & CONFIG ---
const API_URL = 'https://api-literasi.sdnpucung.my.id/api.php'

// --- STATE NAVIGASI & USER / LOGIN ---
const isMenuOpen = ref(false)
const currentStudent = ref(null)

const loginForm = ref({
  classLevel: null,
  full_name: '',
  password: ''
})
const isLoggingIn = ref(false)
const loginError = ref('')

const selectedClass = ref(0)
const studentName = ref('')
const studentList = ref([])

// --- STATE PERPUSTAKAAN & JURNAL ---
const isSubmitting = ref(false)
const isLoadingPosts = ref(false)
const selectedCategory = ref('Semua')

const newEntry = ref({
  bookTitle: '',
  summary: ''
})

const journalPosts = ref([])

// --- MASTER DATA BUKU ---
const books = ref([
  {
    id: 1,
    title: 'Apa Itu',
    category: 'Kelas 1-3',
    targetClass: 'Kelas 1-3',
    synopsis: 'Buku cerita bergambar interaktif dengan fitur bahasa isyarat.',
    coverIcon: '🦊',
    url: 'https://buku.kemdikbud.go.id'
  },
  {
    id: 2,
    title: 'Ini atau Itu',
    category: 'Kelas 4-6',
    targetClass: 'Kelas 4-6',
    synopsis: 'Membantu memahami konsep pilihan dan konsekuensi melalui cerita interaktif.',
    coverIcon: '🌳',
    url: 'https://buku.kemdikbud.go.id'
  }
])

// --- LIFECYCLE HOOKS & WATCHERS ---
onMounted(() => {
  const savedUser = localStorage.getItem('user_session')
  if (savedUser) {
    try {
      currentStudent.value = JSON.parse(savedUser)
      selectedClass.value = Number(currentStudent.value.class_level)
      studentName.value = currentStudent.value.full_name
    } catch (e) {
      localStorage.removeItem('user_session')
    }
  }

  const savedClass = localStorage.getItem('user_selected_class')
  if (savedClass !== null && !currentStudent.value) {
    selectedClass.value = Number(savedClass)
  }

  fetchJournalPosts()
  fetchStudents()
})

watch(selectedClass, (newClass) => {
  localStorage.setItem('user_selected_class', newClass)
  if (!currentStudent.value) {
    studentName.value = ''
  }
})

// --- AUTENTIKASI ---
const handleLogin = async () => {
  if (!loginForm.value.full_name || !loginForm.value.password) {
    loginError.value = 'Silakan isi nama dan PIN/Password!'
    return
  }

  isLoggingIn.value = true
  loginError.value = ''

  try {
    const user = {
      full_name: loginForm.value.full_name,
      class_level: Number(loginForm.value.classLevel)
    }
    
    currentStudent.value = user
    selectedClass.value = user.class_level
    studentName.value = user.full_name

    localStorage.setItem('user_session', JSON.stringify(user))
  } catch (err) {
    loginError.value = 'Login gagal. Periksa kembali nama dan PIN/Password.'
  } finally {
    isLoggingIn.value = false
  }
}

const handleLogout = () => {
  currentStudent.value = null
  studentName.value = ''
  localStorage.removeItem('user_session')
}

// --- LOGIKA JADWAL & ATURAN INTERAKSI ---
const currentDayName = new Date().toLocaleDateString('id-ID', { weekday: 'long' })

const scheduleMatrix = {
  'Senin': 1,
  'Selasa': 2,
  'Rabu': 3,
  'Kamis': 4,
  'Jumat': 5,
  'Sabtu': 6
}

const activeScheduleText = computed(() => {
  if (Number(selectedClass.value) === 0) return 'Akses Guru (Bebas Interaksi Kapan Saja)'
  if (currentDayName === 'Minggu') return 'Khusus Membaca (Kecuali Kelas 6 & Guru)'
  
  const allowedClass = scheduleMatrix[currentDayName]
  return allowedClass ? `Siswa Kelas ${allowedClass} (+ Kelas 6 & Guru)` : 'Semua Kelas'
})

const isBolehInteraksi = computed(() => {
  const userClass = Number(selectedClass.value)
  if (userClass === 0 || userClass === 6) return true
  if (currentDayName === 'Minggu') return false
  return scheduleMatrix[currentDayName] === userClass
})

// --- COMPUTED PROPERTIES ---
const filteredUserOptions = computed(() => {
  return studentList.value.filter(user => 
    Number(user.class_level) === Number(selectedClass.value)
  )
})

const categories = computed(() => {
  const cats = new Set(books.value.map(b => b.category))
  return ['Semua', ...Array.from(cats)]
})

const filteredBooks = computed(() => {
  if (selectedCategory.value === 'Semua') return books.value
  return books.value.filter(b => b.category === selectedCategory.value || b.targetClass === selectedCategory.value)
})

// --- API METHODS ---
const fetchStudents = async () => {
  try {
    const response = await fetch(`${API_URL}?action=get_students`)
    if (response.ok) {
      const resData = await response.json()
      if (resData.status === 'success') {
        studentList.value = resData.data
      }
    }
  } catch (error) {
    console.error('Gagal mengambil daftar pengguna:', error)
  }
}

const fetchJournalPosts = async () => {
  isLoadingPosts.value = true
  try {
    const response = await fetch(API_URL)
    if (response.ok) {
      const resData = await response.json()
      if (resData.status === 'success') {
        journalPosts.value = resData.data.map(item => ({
          ...item,
          comments: item.comments || [],
          newCommentText: ''
        }))
      }
    }
  } catch (error) {
    console.error('Gagal mengambil data dari server:', error)
  } finally {
    isLoadingPosts.value = false
  }
}

const submitWorksheet = async () => {
  if (!isBolehInteraksi.value) return
  if (!studentName.value || !newEntry.value.summary) return

  isSubmitting.value = true

  const payload = {
    action: 'add_post',
    studentName: studentName.value,
    studentClass: selectedClass.value,
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

    if (response.ok && result.status === 'success') {
      await fetchJournalPosts()
      alert('✨ Rangkumanmu berhasil tersimpan!')
      newEntry.value.summary = ''
      newEntry.value.bookTitle = ''
    } else {
      alert(`⚠️ ${result.message || 'Gagal menyimpan rangkuman.'}`)
    }
  } catch (error) {
    console.error('Error submit:', error)
    alert('⚠️ Gagal terhubung ke server. Periksa jaringan Anda!')
  } finally {
    isSubmitting.value = false
  }
}

const toggleLike = async (post) => {
  if (!isBolehInteraksi.value) return

  post.isLiked = !post.isLiked
  post.likes += post.isLiked ? 1 : -1

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        action: 'toggle_like',
        postId: post.id,
        studentClass: selectedClass.value
      })
    })

    const result = await response.json()
    if (result.status !== 'success') {
      console.warn('Gagal memproses like:', result.message)
    }
  } catch (err) {
    console.error('Error liking post:', err)
  }
}

const addComment = async (post) => {
  if (!isBolehInteraksi.value) return
  if (!post.newCommentText || !post.newCommentText.trim()) return

  const commentText = post.newCommentText.trim()
  const isTeacherUser = Number(selectedClass.value) === 0
  
  const authorName = isTeacherUser 
    ? (studentName.value || 'Guru SD') 
    : (studentName.value || `Siswa Kelas ${selectedClass.value}`)

  post.newCommentText = ''

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        action: 'add_comment',
        postId: post.id,
        author: authorName,
        text: commentText,
        isTeacher: isTeacherUser
      })
    })

    const result = await response.json()
    if (result.status === 'success') {
      await fetchJournalPosts()
    } else {
      alert(`⚠️ ${result.message}`)
    }
  } catch (err) {
    console.error('Error comment:', err)
  }
}

const deletePost = async (postId, postStudentName) => {
  if (!confirm('Apakah Anda yakin ingin menghapus rangkuman ini?')) return

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        action: 'delete_post',
        postId: postId,
        studentName: postStudentName
      })
    })

    const result = await response.json()
    if (result.status === 'success') {
      await fetchJournalPosts()
    } else {
      alert(`⚠️ ${result.message}`)
    }
  } catch (error) {
    console.error('Error delete post:', error)
  }
}

const deleteComment = async (commentId, authorName) => {
  if (!confirm('Hapus komentar ini?')) return

  try {
    const response = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        action: 'delete_comment',
        commentId: commentId,
        author: authorName
      })
    })

    const result = await response.json()
    if (result.status === 'success') {
      await fetchJournalPosts()
    } else {
      alert(`⚠️ ${result.message}`)
    }
  } catch (error) {
    console.error('Error delete comment:', error)
  }
}

const getInitials = (name) => {
  if (!name) return 'SD'
  return name.split(' ').map(n => n[0]).join('').toUpperCase().slice(0, 2)
}
</script>