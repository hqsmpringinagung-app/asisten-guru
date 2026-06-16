<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GuruAsisten - Persiapan & Rekap Mengajar</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts (Inter) -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
  <!-- Lucide Icons CDN -->
  <script src="https://unpkg.com/lucide@latest"></script>
  <style>
    body {
      font-family: 'Inter', sans-serif;
    }
    /* Kustom scrollbar untuk estetika */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #f1f5f9;
    }
    ::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col md:flex-row">

  <!-- TOAST NOTIFICATION CONTAINER -->
  <div id="toast-container" class="fixed top-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

  <!-- CUSTOM MODAL DIALOG (Pengganti Confirm) -->
  <div id="custom-confirm-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-sm hidden transition-all duration-300">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-xl border border-slate-150 transform transition-all scale-95">
      <div class="flex items-center space-x-3 text-rose-600 mb-4">
        <div class="bg-rose-50 p-2 rounded-lg">
          <i data-lucide="alert-triangle" class="w-6 h-6"></i>
        </div>
        <h4 class="text-lg font-bold text-slate-800" id="confirm-title">Konfirmasi Hapus</h4>
      </div>
      <p class="text-sm text-slate-500 mb-6" id="confirm-message">Apakah Anda yakin ingin menghapus data ini? Tindakan ini tidak dapat dibatalkan.</p>
      <div class="flex justify-end space-x-3">
        <button id="confirm-btn-cancel" class="px-4 py-2 text-sm font-semibold text-slate-500 hover:bg-slate-100 rounded-xl transition-all">Batal</button>
        <button id="confirm-btn-ok" class="px-5 py-2 text-sm font-semibold text-white bg-rose-600 hover:bg-rose-700 rounded-xl shadow-sm transition-all">Hapus</button>
      </div>
    </div>
  </div>

  <!-- -->
  <!-- CUSTOM ATTENDANCE DETAIL MODAL (Fitur Baru: Lihat Detail Kehadiran Kelas) -->
  <div id="attendance-detail-modal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-sm hidden transition-all duration-300">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-xl border border-slate-150 transform transition-all scale-95 flex flex-col max-h-[85vh]">
      <div class="flex items-center justify-between border-b border-slate-100 pb-4 mb-4">
        <div class="flex items-center space-x-2.5">
          <div class="bg-indigo-50 p-2 rounded-lg text-indigo-600">
            <i data-lucide="eye" class="w-5 h-5"></i>
          </div>
          <div>
            <h4 class="text-md font-bold text-slate-800" id="att-detail-title">Detail Kehadiran</h4>
            <p class="text-xs text-slate-400" id="att-detail-subtitle">Kelas & Tanggal</p>
          </div>
        </div>
        <button onclick="closeAttendanceDetailModal()" class="text-slate-400 hover:text-slate-600 p-1 rounded-lg hover:bg-slate-50 transition-colors">
          <i data-lucide="x" class="w-5 h-5"></i>
        </button>
      </div>
      
      <!-- Detail Siswa Container -->
      <div class="overflow-y-auto flex-1 pr-1 space-y-2.5" id="att-detail-list">
        <!-- Generated dinamis via JS -->
      </div>
      
      <!-- Footer Modal -->
      <div class="flex justify-end pt-4 border-t border-slate-100 mt-4 space-x-2">
        <button onclick="loadRecordToFormFromModal()" id="btn-load-to-form" class="px-4 py-2 text-xs font-bold text-white bg-indigo-600 hover:bg-indigo-700 rounded-xl shadow-sm transition-all flex items-center space-x-1.5">
          <i data-lucide="edit-3" class="w-3.5 h-3.5"></i>
          <span>Edit Presensi Ini</span>
        </button>
        <button onclick="closeAttendanceDetailModal()" class="px-4 py-2 text-xs font-semibold text-slate-500 hover:bg-slate-100 rounded-xl transition-all">Tutup</button>
      </div>
    </div>
  </div>

  <!-- SIDEBAR -->
  <aside class="w-full md:w-64 bg-slate-900 text-white shrink-0 flex flex-col border-r border-slate-800">
    <!-- Header Sidebar -->
    <div class="p-6 border-b border-slate-800 flex items-center space-x-3">
      <div class="bg-indigo-600 p-2 rounded-lg text-white">
        <i data-lucide="book-open" class="w-6 h-6"></i>
      </div>
      <div>
        <h1 class="font-bold text-lg tracking-tight">GuruAsisten</h1>
        <p class="text-xs text-slate-400">Persiapan & Rekap Ajar</p>
      </div>
    </div>

    <!-- Navigasi Menu -->
    <nav class="flex-1 p-4 space-y-1.5 overflow-y-auto">
      <button onclick="switchTab('dashboard')" id="nav-dashboard" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all bg-indigo-600 text-white shadow-md">
        <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
        <span>Beranda & Ringkasan</span>
      </button>

      <button onclick="switchTab('jurnal')" id="nav-jurnal" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:bg-slate-800 hover:text-slate-200">
        <i data-lucide="file-text" class="w-5 h-5"></i>
        <span>Jurnal Harian</span>
      </button>

      <button onclick="switchTab('kehadiran')" id="nav-kehadiran" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:bg-slate-800 hover:text-slate-200">
        <i data-lucide="calendar" class="w-5 h-5"></i>
        <span>Presensi / Kehadiran</span>
      </button>

      <button onclick="switchTab('materi')" id="nav-materi" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:bg-slate-800 hover:text-slate-200">
        <i data-lucide="book-marked" class="w-5 h-5"></i>
        <span>Materi & BAB</span>
      </button>

      <button onclick="switchTab('nilai')" id="nav-nilai" class="w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:bg-slate-800 hover:text-slate-200">
        <i data-lucide="award" class="w-5 h-5"></i>
        <span>Daftar Nilai Siswa</span>
      </button>
    </nav>

    <!-- Footer Sidebar -->
    <div class="p-4 border-t border-slate-800 bg-slate-950/40 flex flex-col items-center gap-1.5">
      <div class="text-xs text-slate-500 text-center">
        Sistem Asisten Mengajar v3.0 (HTML/JS)
      </div>
      <button onclick="triggerFullReset()" class="text-[10px] text-rose-400 hover:text-rose-300 font-semibold transition-colors flex items-center gap-1 mt-1">
        <i data-lucide="rotate-ccw" class="w-3.5 h-3.5"></i> Reset Semua Data
      </button>
    </div>
  </aside>

  <!-- MAIN CONTENT AREA -->
  <main class="flex-1 flex flex-col min-w-0 overflow-y-auto">
    <!-- Header Top -->
    <header class="bg-white border-b border-slate-200 py-4 px-6 md:px-8 flex flex-col sm:flex-row sm:items-center sm:justify-between space-y-2 sm:space-y-0 shadow-sm">
      <div>
        <h2 id="header-title" class="text-xl font-bold text-slate-800">Dashboard Overview</h2>
        <p id="header-desc" class="text-sm text-slate-500">Rangkuman aktivitas akademik dan performa mengajar.</p>
      </div>
      <div class="flex items-center space-x-2.5">
        <select
          id="global-ta-select"
          class="text-xs font-bold px-3 py-1.5 bg-indigo-50 text-indigo-700 rounded-full border border-indigo-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 cursor-pointer transition-all"
        >
          <option value="2025/2026">TA: 2025/2026</option>
          <option value="2026/2027" selected>TA: 2026/2027</option>
          <option value="2027/2028">TA: 2027/2028</option>
        </select>
        <select
          id="global-semester-select"
          class="text-xs font-bold px-3 py-1.5 bg-violet-50 text-violet-700 rounded-full border border-violet-200 focus:outline-none focus:ring-2 focus:ring-violet-500 cursor-pointer transition-all"
        >
          <option value="Ganjil" selected>Semester: Ganjil</option>
          <option value="Genap">Semester: Genap</option>
        </select>
      </div>
    </header>

    <!-- Content Sections Wrapper -->
    <div class="p-6 md:p-8 flex-1 max-w-7xl w-full mx-auto">
      
      <!-- ================= TAB: DASHBOARD ================= -->
      <section id="tab-dashboard" class="space-y-6">
        <!-- Rangkuman Metrik -->
        <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
          <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 flex items-center space-x-4">
            <div class="p-3 bg-indigo-50 text-indigo-600 rounded-xl">
              <i data-lucide="users" class="w-6 h-6"></i>
            </div>
            <div>
              <p class="text-[10px] font-bold text-slate-400 uppercase tracking-wider">Total Siswa Aktif</p>
              <h3 id="stat-students" class="text-2xl font-bold text-slate-800">0 Siswa</h3>
            </div>
          </div>

          <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 flex items-center space-x-4">
            <div class="p-3 bg-emerald-50 text-emerald-600 rounded-xl">
              <i data-lucide="book-marked" class="w-6 h-6"></i>
            </div>
            <div>
              <p class="text-[10px] font-bold text-slate-400 uppercase tracking-wider">Total Materi Pembelajaran</p>
              <h3 id="stat-chapters" class="text-2xl font-bold text-slate-800">0 BAB</h3>
            </div>
          </div>

          <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 flex items-center space-x-4">
            <div class="p-3 bg-amber-50 text-amber-600 rounded-xl">
              <i data-lucide="file-text" class="w-6 h-6"></i>
            </div>
            <div>
              <p class="text-[10px] font-bold text-slate-400 uppercase tracking-wider">Total Jurnal Diajarkan</p>
              <h3 id="stat-journals" class="text-2xl font-bold text-slate-800">0 Sesi</h3>
            </div>
          </div>

          <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 flex items-center space-x-4">
            <div class="p-3 bg-rose-50 text-rose-600 rounded-xl">
              <i data-lucide="award" class="w-6 h-6"></i>
            </div>
            <div>
              <p class="text-[10px] font-bold text-slate-400 uppercase tracking-wider">Rata-Rata Nilai Akhir</p>
              <h3 id="stat-grades" class="text-2xl font-bold text-slate-800">0 / 100</h3>
            </div>
          </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <!-- Kolom Kiri: Form Cepat -->
          <div class="lg:col-span-1 space-y-6">
            <!-- Form Tambah Kelas -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
              <h3 class="text-md font-bold text-slate-800 mb-3 flex items-center">
                <i data-lucide="plus" class="w-4 h-4 mr-1 text-indigo-600"></i> Daftarkan Kelas Baru
              </h3>
              <form id="form-add-class" class="space-y-3">
                <div>
                  <label class="block text-xs text-slate-500 font-medium mb-1">Nama Kelas</label>
                  <input
                    type="text"
                    id="input-class-name"
                    placeholder="Contoh: 10 MIPA 3, 11 IPS 2"
                    class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                    required
                  />
                </div>
                <button
                  type="submit"
                  class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-medium text-xs px-4 py-2.5 rounded-lg transition-colors flex justify-center items-center"
                >
                  Simpan Kelas
                </button>
              </form>
            </div>

            <!-- Form Tambah Siswa -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
              <h3 class="text-md font-bold text-slate-800 mb-3 flex items-center">
                <i data-lucide="plus" class="w-4 h-4 mr-1 text-indigo-600"></i> Tambah Siswa Baru
              </h3>
              <form id="form-add-student" class="space-y-3">
                <div>
                  <label class="block text-xs text-slate-500 font-medium mb-1">Nama Lengkap Siswa</label>
                  <input
                    type="text"
                    id="input-student-name"
                    placeholder="Contoh: Budi Wibowo"
                    class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                    required
                  />
                </div>

                <div>
                  <label class="block text-xs text-slate-500 font-medium mb-1">Pilih Kelas</label>
                  <select
                    id="select-student-class"
                    class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                    required
                  >
                    <!-- Ditampilkan dinamis via JS -->
                  </select>
                </div>

                <button
                  type="submit"
                  id="btn-submit-student"
                  class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-medium text-xs px-4 py-2.5 rounded-lg transition-colors flex justify-center items-center"
                >
                  Simpan Data Siswa
                </button>
              </form>
            </div>
          </div>

          <!-- Kolom Kanan: Panduan & Sesi Terbaru -->
          <div class="lg:col-span-2 space-y-6">
            <div class="bg-gradient-to-r from-indigo-500 to-violet-600 p-6 rounded-2xl text-white shadow-sm relative overflow-hidden">
              <div class="absolute top-0 right-0 transform translate-x-12 -translate-y-12 opacity-10">
                <i data-lucide="book-open" class="w-64 h-64"></i>
              </div>
              <div class="relative z-10 max-w-lg">
                <h3 class="text-lg font-bold mb-2">Selamat Mengajar, Guru Hebat! 👋</h3>
                <p class="text-sm text-indigo-100 leading-relaxed mb-4">
                  Gunakan menu sebelah kiri untuk mengorganisasikan kegiatan belajar mengajar Anda secara terstruktur. Mulailah dengan mendaftarkan modul materi di menu <strong>Materi & BAB</strong>, lakukan presensi, lalu tulis catatan Anda di <strong>Jurnal Pembelajaran</strong>.
                </p>
                <div class="inline-flex space-x-4">
                  <span id="badge-classes-count" class="text-xs bg-white/20 px-3 py-1.5 rounded-full font-medium">
                    0 Kelas Terdaftar
                  </span>
                  <span id="badge-students-count" class="text-xs bg-white/20 px-3 py-1.5 rounded-full font-medium">
                    0 Total Siswa
                  </span>
                </div>
              </div>
            </div>

            <!-- Jurnal Terbaru Dashboard -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
              <h3 class="text-md font-bold text-slate-800 mb-4 flex items-center">
                <i data-lucide="file-text" class="w-5 h-5 text-indigo-600 mr-2"></i> Jurnal Mengajar Terbaru
              </h3>
              <div id="latest-journals-container" class="space-y-3">
                <!-- Diisi via JS -->
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- ================= TAB: JURNAL ================= -->
      <section id="tab-jurnal" class="space-y-8 hidden">
        <!-- Form Jurnal -->
        <div class="bg-white p-6 md:p-8 rounded-2xl border border-slate-200 shadow-sm">
          <h3 class="text-lg font-bold text-slate-800 mb-6 flex items-center">
            <i data-lucide="file-text" class="w-5 h-5 text-indigo-600 mr-2"></i>
            Form Jurnal Harian Persiapan & Pelaksanaan Mengajar
          </h3>

          <div id="jurnal-warning" class="bg-amber-50 border border-amber-200 rounded-xl p-4 flex items-start space-x-3 hidden">
            <i data-lucide="info" class="w-5 h-5 text-amber-600 shrink-0 mt-0.5"></i>
            <div>
              <p class="text-sm font-semibold text-amber-800">Materi Pembelajaran Belum Siap</p>
              <p class="text-xs text-amber-600 mt-1">
                Silakan tambahkan data BAB dan topik materi di tab <strong>Materi & BAB</strong> terlebih dahulu untuk dapat mengisi jurnal dengan rincian materi yang terstruktur.
              </p>
            </div>
          </div>

          <form id="form-save-journal" class="space-y-6">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
              <!-- Tanggal Sesi -->
              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-2">Tanggal Sesi</label>
                <input
                  type="date"
                  id="jurnal-input-date"
                  class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  required
                />
              </div>

              <!-- Pilih Kelas -->
              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-2">Pilih Kelas</label>
                <select
                  id="jurnal-select-class"
                  class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  required
                >
                  <!-- Diisi dinamis -->
                </select>
              </div>

              <!-- Pilih BAB -->
              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-2">Pilih BAB</label>
                <select
                  id="jurnal-select-chapter"
                  class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  required
                >
                  <!-- Diisi dinamis -->
                </select>
              </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <!-- Pilih Topik -->
              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-2">Pilih Sub-Materi / Topik</label>
                <select
                  id="jurnal-select-topic"
                  class="w-full px-3.5 py-2.5 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  required
                >
                  <!-- Diisi dinamis -->
                </select>
              </div>

              <!-- Preview Isi Materi -->
              <div class="p-4 bg-slate-50 border border-slate-200 rounded-xl flex flex-col justify-center">
                <span class="text-[10px] uppercase font-bold tracking-wider text-indigo-600 block mb-1">Preview Rincian Materi</span>
                <p id="jurnal-topic-preview" class="text-xs text-slate-600 italic">"Pilih topik untuk melihat deskripsi."</p>
              </div>
            </div>

            <!-- Detail Mengajar -->
            <div class="space-y-4">
              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-2">Catatan Kegiatan Mengajar & Persiapan</label>
                <textarea
                  id="jurnal-input-notes"
                  rows="3"
                  placeholder="Ceritakan proses kegiatan mengajar, tujuan tercapai, atau persiapan khusus hari ini..."
                  class="w-full px-4 py-3 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  required
                ></textarea>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-xs font-semibold uppercase tracking-wider text-rose-500 mb-2">Hambatan / Kendala Kelas (Opsional)</label>
                  <textarea
                    id="jurnal-input-impediment"
                    rows="2"
                    placeholder="Sebutkan kendala siswa atau kendala teknis yang dihadapi..."
                    class="w-full px-4 py-3 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  ></textarea>
                </div>

                <div>
                  <label class="block text-xs font-semibold uppercase tracking-wider text-emerald-600 mb-2">Solusi / Tindak Lanjut (Opsional)</label>
                  <textarea
                    id="jurnal-input-solution"
                    rows="2"
                    placeholder="Tuliskan aksi perbaikan atau penyesuaian untuk pertemuan berikutnya..."
                    class="w-full px-4 py-3 text-sm border border-slate-300 rounded-xl focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  ></textarea>
                </div>
              </div>
            </div>

            <button
              type="submit"
              class="px-6 py-3 bg-indigo-600 hover:bg-indigo-700 text-white font-semibold text-sm rounded-xl transition-colors shadow-sm flex items-center justify-center space-x-2"
            >
              <i data-lucide="save" class="w-4 h-4"></i>
              <span>Simpan Jurnal Baru</span>
            </button>
          </form>
        </div>

        <!-- Riwayat Jurnal -->
        <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
          <div class="p-6 border-b border-slate-200">
            <h3 class="text-lg font-bold text-slate-800">Riwayat Jurnal Mengajar</h3>
          </div>
          <div id="journal-list-container" class="divide-y divide-slate-200">
            <!-- Diisi via JS -->
          </div>
        </div>
      </section>

      <!-- ================= TAB: KEHADIRAN ================= -->
      <section id="tab-kehadiran" class="space-y-8 hidden">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
          
          <!-- Panel Utama: Pengisian Absen -->
          <div class="lg:col-span-2 space-y-6">
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
              <h3 class="text-md font-bold text-slate-800 mb-6 flex items-center">
                <i data-lucide="users" class="w-5 h-5 text-indigo-600 mr-2"></i> Lembar Pengisian Absensi Siswa
              </h3>

              <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
                <div>
                  <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-1.5">Pilih Kelas</label>
                  <select
                    id="attendance-select-class"
                    class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  >
                    <!-- Dinamis -->
                  </select>
                </div>

                <div>
                  <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-1.5">Tanggal Absen</label>
                  <input
                    type="date"
                    id="attendance-input-date"
                    class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:outline-none"
                  />
                </div>
              </div>

              <!-- Daftar Siswa untuk Absensi -->
              <div id="attendance-students-wrapper" class="space-y-4">
                <div class="hidden sm:grid grid-cols-12 gap-2 pb-2 border-b border-slate-100 text-xs font-bold text-slate-400 uppercase tracking-wider">
                  <div class="col-span-6">Nama Siswa</div>
                  <div class="col-span-6 text-center">Opsi Kehadiran</div>
                </div>

                <div id="attendance-students-list" class="divide-y divide-slate-100 max-h-96 overflow-y-auto pr-2">
                  <!-- Generated dinamis -->
                </div>

                <div class="pt-4 border-t border-slate-100 flex justify-end">
                  <button
                    onclick="saveAttendanceRecord()"
                    class="bg-indigo-600 hover:bg-indigo-700 text-white text-sm font-bold px-6 py-2.5 rounded-xl transition-all shadow-sm flex items-center space-x-2"
                  >
                    <i data-lucide="check" class="w-4 h-4"></i>
                    <span>Simpan Lembar Kehadiran</span>
                  </button>
                </div>
              </div>
            </div>
          </div>

          <!-- Panel Kanan: Log Histori Absen -->
          <div class="lg:col-span-1 space-y-6">
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
              <h3 class="text-md font-bold text-slate-800 mb-4 flex items-center">
                <i data-lucide="calendar" class="w-5 h-5 text-indigo-600 mr-2"></i> Log Absensi Tersimpan
              </h3>
              <div id="attendance-history-list" class="space-y-4 max-h-[420px] overflow-y-auto pr-1">
                <!-- Dinamis -->
              </div>
            </div>
          </div>

        </div>
      </section>

      <!-- ================= TAB: MATERI & BAB ================= -->
      <section id="tab-materi" class="grid grid-cols-1 lg:grid-cols-12 gap-6 hidden">
        <!-- Form Tambah BAB -->
        <div class="lg:col-span-4 space-y-6">
          <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm sticky top-6">
            <h3 class="text-md font-bold text-slate-800 mb-4 flex items-center">
              <i data-lucide="plus" class="w-5 h-5 text-indigo-600 mr-1.5"></i> Tambah BAB Baru
            </h3>
            <form id="form-save-chapter" class="space-y-4">
              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-1">Judul BAB</label>
                <input
                  type="text"
                  id="materi-input-chapter-title"
                  placeholder="Contoh: BAB 3: Logika Gerbang"
                  class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                  required
                />
              </div>

              <div>
                <label class="block text-xs font-semibold uppercase tracking-wider text-slate-500 mb-1">Deskripsi Umum BAB</label>
                <textarea
                  id="materi-input-chapter-desc"
                  rows="3"
                  placeholder="Deskripsi singkat target kompetensi dasar pembelajaran..."
                  class="w-full px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
                ></textarea>
              </div>

              <button
                type="submit"
                class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-xs px-4 py-2.5 rounded-lg transition-all"
              >
                Simpan BAB
              </button>
            </form>
          </div>
        </div>

        <!-- Daftar BAB & Sub-materi Accordion -->
        <div class="lg:col-span-8 space-y-6">
          <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
            <h3 class="text-md font-bold text-slate-800 mb-6 flex items-center">
              <i data-lucide="book-marked" class="w-5 h-5 text-indigo-600 mr-2"></i>
              Daftar Silabus & Detail Materi Persiapan Mengajar
            </h3>
            <div id="materi-chapters-list" class="space-y-4">
              <!-- Dinamis -->
            </div>
          </div>
        </div>
      </section>

      <!-- ================= TAB: DAFTAR NILAI ================= -->
      <section id="tab-nilai" class="bg-white p-6 md:p-8 rounded-2xl border border-slate-200 shadow-sm space-y-6 hidden">
        <!-- Pencarian & Filter Kelas -->
        <div class="flex flex-col md:flex-row gap-4 justify-between items-start md:items-center pb-4 border-b border-slate-200">
          <div class="flex flex-col sm:flex-row gap-3 w-full md:w-auto">
            <div>
              <label class="block text-[10px] font-bold text-slate-400 uppercase mb-1">Pilih Kelas</label>
              <select
                id="nilai-select-class"
                class="px-3 py-2 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 w-full sm:w-48"
              >
                <!-- Dinamis -->
              </select>
            </div>

            <div>
              <label class="block text-[10px] font-bold text-slate-400 uppercase mb-1">Cari Nama Siswa</label>
              <div class="relative">
                <i data-lucide="search" class="w-4 h-4 text-slate-400 absolute left-3 top-2.5"></i>
                <input
                  type="text"
                  id="nilai-input-search"
                  placeholder="Masukkan nama..."
                  class="pl-9 pr-3 py-1.5 text-sm border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 w-full sm:w-64"
                />
              </div>
            </div>
          </div>

          <!-- Informasi Rumus -->
          <div class="bg-indigo-50/50 p-3 rounded-xl border border-indigo-100 text-xs text-indigo-800 space-y-1 w-full md:w-auto">
            <p class="font-bold flex items-center">
              <i data-lucide="info" class="w-3.5 h-3.5 mr-1"></i> Rumus Nilai Akhir Kelas:
            </p>
            <p class="text-slate-600">
              Harian (40%) + UTS (30%) + UAS (30%). <span class="font-bold">KKM: 75</span>
            </p>
          </div>
        </div>

        <!-- Tabel Nilai -->
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse min-w-[700px]">
            <thead>
              <tr class="border-b border-slate-200 text-xs font-bold text-slate-400 uppercase tracking-wider">
                <th class="py-3 px-4">Nama Siswa</th>
                <th class="py-3 px-4 text-center">Nilai Harian (40%)</th>
                <th class="py-3 px-4 text-center">Nilai UTS (30%)</th>
                <th class="py-3 px-4 text-center">Nilai UAS (30%)</th>
                <th class="py-3 px-4 text-center">Nilai Akhir</th>
                <th class="py-3 px-4 text-center">Status Kelulusan</th>
                <th class="py-3 px-4 text-center">Aksi</th>
              </tr>
            </thead>
            <tbody id="nilai-table-body" class="divide-y divide-slate-100 text-sm">
              <!-- Dinamis -->
            </tbody>
          </table>
        </div>
      </section>

    </div>
  </main>

  <script>
    // ================= DATA DEFAULT & STATE =================
    const DEFAULT_CLASSES = [];

    const DEFAULT_STUDENTS = [];

    const DEFAULT_CHAPTERS = [];

    const DEFAULT_JOURNALS = [];

    const DEFAULT_ATTENDANCE = [];

    // State utama aplikasi
    let state = {
      classes: [],
      students: [],
      chapters: [],
      journals: [],
      attendance: [],
      ta: '2026/2027',
      semester: 'Ganjil'
    };

    // State bantuan UI
    let activeTab = 'dashboard';
    let expandedChapters = {}; // Map of chapterId -> boolean
    let editingStudentId = null;

    // State penampung kustom modal konfirmasi
    let confirmCallback = null;

    // ================= INITIALIZATION & STORAGE =================
    window.onload = function() {
      // Deteksi jika masih menyimpan data demo bawaan sebelumnya di localStorage, kosongkan agar bersih
      if (localStorage.getItem('guru_students') && localStorage.getItem('guru_students').includes('Andi Pratama')) {
        localStorage.clear();
      }

      // Muat data dari localStorage atau pasang default yang sudah kosong
      state.classes = JSON.parse(localStorage.getItem('guru_classes')) || DEFAULT_CLASSES;
      state.students = JSON.parse(localStorage.getItem('guru_students')) || DEFAULT_STUDENTS;
      state.chapters = JSON.parse(localStorage.getItem('guru_chapters')) || DEFAULT_CHAPTERS;
      state.journals = JSON.parse(localStorage.getItem('guru_journals')) || DEFAULT_JOURNALS;
      state.attendance = JSON.parse(localStorage.getItem('guru_attendance')) || DEFAULT_ATTENDANCE;
      
      // Muat pengaturan Semester dan TA aktif
      state.ta = localStorage.getItem('guru_ta') || '2026/2027';
      state.semester = localStorage.getItem('guru_semester') || 'Ganjil';
      document.getElementById('global-ta-select').value = state.ta;
      document.getElementById('global-semester-select').value = state.semester;

      // Inisialisasi accordion untuk chapter awal
      state.chapters.forEach(c => {
        expandedChapters[c.id] = false;
      });

      // Atur form tanggal default ke hari ini
      const todayStr = new Date().toISOString().split('T')[0];
      document.getElementById('jurnal-input-date').value = todayStr;
      document.getElementById('attendance-input-date').value = todayStr;

      // Event listener pencarian nilai & filter
      document.getElementById('nilai-select-class').addEventListener('change', renderGrades);
      document.getElementById('nilai-input-search').addEventListener('input', renderGrades);

      // Event listener input absen
      document.getElementById('attendance-select-class').addEventListener('change', loadAttendanceRecord);
      document.getElementById('attendance-input-date').addEventListener('change', loadAttendanceRecord);

      // Sinkronisasi dropdown Jurnal berdasarkan BAB yang dipilih
      document.getElementById('jurnal-select-chapter').addEventListener('change', handleJournalChapterChange);
      document.getElementById('jurnal-select-topic').addEventListener('change', handleJournalTopicChange);

      // Event listener selektor global di Header
      document.getElementById('global-ta-select').addEventListener('change', handleGlobalTaChange);
      document.getElementById('global-semester-select').addEventListener('change', handleGlobalSemesterChange);

      // Daftarkan listener submit form
      setupFormListeners();

      // Render awal semua komponen
      renderAll();
    };

    function saveState() {
      localStorage.setItem('guru_classes', JSON.stringify(state.classes));
      localStorage.setItem('guru_students', JSON.stringify(state.students));
      localStorage.setItem('guru_chapters', JSON.stringify(state.chapters));
      localStorage.setItem('guru_journals', JSON.stringify(state.journals));
      localStorage.setItem('guru_attendance', JSON.stringify(state.attendance));
      localStorage.setItem('guru_ta', state.ta);
      localStorage.setItem('guru_semester', state.semester);
      
      // Update data di semua tab
      renderAll();
    }

    function handleGlobalTaChange(e) {
      state.ta = e.target.value;
      localStorage.setItem('guru_ta', state.ta);
      showToast(`Tahun Ajaran diubah ke ${state.ta}`);
      saveState();
    }

    function handleGlobalSemesterChange(e) {
      state.semester = e.target.value;
      localStorage.setItem('guru_semester', state.semester);
      showToast(`Semester aktif diubah ke ${state.semester}`);
      saveState();
    }

    function triggerFullReset() {
      openConfirmModal(
        "Reset Semua Data?",
        "Apakah Anda benar-benar yakin ingin menghapus seluruh data kelas, siswa, materi, jurnal, dan nilai? Tindakan ini akan mengosongkan aplikasi sepenuhnya.",
        () => {
          localStorage.clear();
          state.classes = [];
          state.students = [];
          state.chapters = [];
          state.journals = [];
          state.attendance = [];
          expandedChapters = {};
          editingStudentId = null;
          showToast("Seluruh database berhasil dibersihkan!", "error");
          saveState();
        }
      );
    }

    // ================= TOAST NOTIFICATION SYSTEM =================
    function showToast(message, type = 'success') {
      const container = document.getElementById('toast-container');
      const toast = document.createElement('div');
      
      const bgClass = type === 'success' ? 'bg-emerald-600 border-emerald-500' : 'bg-rose-600 border-rose-500';
      const iconName = type === 'success' ? 'check-circle' : 'alert-circle';
      
      toast.className = `flex items-center p-4 rounded-lg shadow-lg border text-white transition-all duration-300 transform translate-y-2 opacity-0 ${bgClass}`;
      toast.innerHTML = `
        <i data-lucide="${iconName}" class="w-5 h-5 mr-2 shrink-0"></i>
        <span class="font-medium text-sm">${message}</span>
      `;
      
      container.appendChild(toast);
      lucide.createIcons();

      // Trigger animation
      setTimeout(() => {
        toast.classList.remove('translate-y-2', 'opacity-0');
      }, 10);

      // Sembunyikan dan hapus setelah 3 detik
      setTimeout(() => {
        toast.classList.add('translate-y-2', 'opacity-0');
        setTimeout(() => toast.remove(), 300);
      }, 3000);
    }

    // ================= CUSTOM CONFIRM SYSTEM (NO BROWSER DIALOGS) =================
    function openConfirmModal(title, message, callback) {
      document.getElementById('confirm-title').innerText = title;
      document.getElementById('confirm-message').innerText = message;
      
      const modal = document.getElementById('custom-confirm-modal');
      modal.classList.remove('hidden');
      setTimeout(() => {
        modal.firstElementChild.classList.remove('scale-95');
      }, 10);

      confirmCallback = callback;
    }

    function closeConfirmModal() {
      const modal = document.getElementById('custom-confirm-modal');
      modal.firstElementChild.classList.add('scale-95');
      setTimeout(() => {
        modal.classList.add('hidden');
        confirmCallback = null;
      }, 150);
    }

    document.getElementById('confirm-btn-cancel').onclick = closeConfirmModal;
    document.getElementById('confirm-btn-ok').onclick = function() {
      if (confirmCallback) confirmCallback();
      closeConfirmModal();
    };

    // ================= TAB SWITCHING =================
    function switchTab(tabId) {
      activeTab = tabId;
      
      // Update navigasi aktf
      const navIds = ['dashboard', 'jurnal', 'kehadiran', 'materi', 'nilai'];
      navIds.forEach(id => {
        const btn = document.getElementById(`nav-${id}`);
        if (id === tabId) {
          btn.className = "w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all bg-indigo-600 text-white shadow-md";
        } else {
          btn.className = "w-full flex items-center space-x-3 px-4 py-3 rounded-xl text-sm font-medium transition-all text-slate-400 hover:bg-slate-800 hover:text-slate-200";
        }
      });

      // Update Tampilan Tab
      navIds.forEach(id => {
        const section = document.getElementById(`tab-${id}`);
        if (id === tabId) {
          section.classList.remove('hidden');
        } else {
          section.classList.add('hidden');
        }
      });

      // Header text mapping
      const headerTitles = {
        dashboard: "Dashboard Overview",
        jurnal: "Jurnal Pembelajaran",
        kehadiran: "Pencatatan Kehadiran Siswa",
        materi: "Persiapan Materi Pembelajaran",
        nilai: "Rekapitulasi Nilai Siswa"
      };

      const headerDescs = {
        dashboard: "Rangkuman aktivitas akademik dan performa mengajar.",
        jurnal: "Catat kejadian, progres, hambatan, dan solusi selama mengajar.",
        kehadiran: "Kelola daftar absensi siswa per kelas hari ini.",
        materi: "Buat silabus, BAB, dan rincian materi pendukung persiapan ajar.",
        nilai: "Pantau dan edit Nilai Harian, UTS, dan UAS siswa secara real-time."
      };

      document.getElementById('header-title').innerText = headerTitles[tabId];
      document.getElementById('header-desc').innerText = headerDescs[tabId];

      renderAll();
    }

    // ================= FORM SUBMISSION HANDLERS =================
    function setupFormListeners() {
      // Form Tambah Kelas
      document.getElementById('form-add-class').onsubmit = function(e) {
        e.preventDefault();
        const inputName = document.getElementById('input-class-name');
        const nameVal = inputName.value.trim();
        if (!nameVal) return;

        if (state.classes.includes(nameVal)) {
          showToast("Gagal! Kelas sudah terdaftar sebelumnya.", "error");
          return;
        }

        state.classes.push(nameVal);
        inputName.value = '';
        showToast("Kelas baru berhasil didaftarkan.");
        saveState();
      };

      // Form Tambah Siswa
      document.getElementById('form-add-student').onsubmit = function(e) {
        e.preventDefault();
        const nameVal = document.getElementById('input-student-name').value.trim();
        const classVal = document.getElementById('select-student-class').value;

        if (!nameVal || !classVal) return;

        const newStudent = {
          id: 's_' + Date.now(),
          name: nameVal,
          class: classVal,
          grades: { harian: 0, uts: 0, uas: 0 }
        };

        state.students.push(newStudent);
        document.getElementById('input-student-name').value = '';
        showToast(`Siswa ${nameVal} berhasil ditambahkan ke kelas ${classVal}.`);
        saveState();
      };

      // Form Simpan Jurnal Baru
      document.getElementById('form-save-journal').onsubmit = function(e) {
        e.preventDefault();
        const dateVal = document.getElementById('jurnal-input-date').value;
        const classVal = document.getElementById('jurnal-select-class').value;
        const chapIndex = document.getElementById('jurnal-select-chapter').value;
        const topicIndex = document.getElementById('jurnal-select-topic').value;
        const notesVal = document.getElementById('jurnal-input-notes').value.trim();
        const impedimentVal = document.getElementById('jurnal-input-impediment').value.trim();
        const solutionVal = document.getElementById('jurnal-input-solution').value.trim();

        const chapterObj = state.chapters[chapIndex];
        const topicObj = chapterObj?.topics[topicIndex];

        if (!classVal || !chapterObj || !topicObj) {
          showToast("Gagal! Silakan pastikan data BAB dan Topik sudah terdaftar.", "error");
          return;
        }

        const newJournal = {
          id: 'j_' + Date.now(),
          date: dateVal,
          class: classVal,
          chapter: chapterObj.title,
          topic: topicObj.title,
          notes: notesVal,
          impediment: impedimentVal,
          solution: solutionVal,
          ta: state.ta,
          semester: state.semester
        };

        state.journals.unshift(newJournal); // Masukkan di baris teratas
        
        // Reset form input deskripsi
        document.getElementById('jurnal-input-notes').value = '';
        document.getElementById('jurnal-input-impediment').value = '';
        document.getElementById('jurnal-input-solution').value = '';
        
        showToast("Catatan Jurnal Mengajar berhasil ditambahkan.");
        saveState();
      };

      // Form Tambah BAB Baru
      document.getElementById('form-save-chapter').onsubmit = function(e) {
        e.preventDefault();
        const titleVal = document.getElementById('materi-input-chapter-title').value.trim();
        const descVal = document.getElementById('materi-input-chapter-desc').value.trim();

        if (!titleVal) return;

        const newChapter = {
          id: 'c_' + Date.now(),
          title: titleVal,
          description: descVal || 'Tidak ada deskripsi BAB.',
          topics: []
        };

        state.chapters.push(newChapter);
        expandedChapters[newChapter.id] = true; // Langsung ekspansi setelah dibuat

        document.getElementById('materi-input-chapter-title').value = '';
        document.getElementById('materi-input-chapter-desc').value = '';
        
        showToast(`BAB baru: "${titleVal}" berhasil disimpan.`);
        saveState();
      };
    }

    // ================= GLOBAL RENDERING SYSTEM =================
    function renderAll() {
      // 1. Sinkronisasi Data Dropdowns
      syncDropdownOptions();

      // 2. Render view spesifik berdasarkan tab aktif
      if (activeTab === 'dashboard') {
        renderDashboardStats();
      } else if (activeTab === 'jurnal') {
        renderJournalsView();
      } else if (activeTab === 'kehadiran') {
        renderAttendanceView();
      } else if (activeTab === 'materi') {
        renderChaptersView();
      } else if (activeTab === 'nilai') {
        renderGrades();
      }

      // Re-trigger visualisasi ikon Lucide
      lucide.createIcons();
    }

    // Singkronkan semua dropdown pilihan kelas/materi ke state terkini
    function syncDropdownOptions() {
      // Dropdown Kelas di Dashboard (Form Siswa)
      const selectStudentClass = document.getElementById('select-student-class');
      const btnSubmitStudent = document.getElementById('btn-submit-student');
      selectStudentClass.innerHTML = '';
      if (state.classes.length === 0) {
        selectStudentClass.innerHTML = `<option value="">-- Buat Kelas Terlebih Dahulu --</option>`;
        btnSubmitStudent.disabled = true;
      } else {
        btnSubmitStudent.disabled = false;
        state.classes.forEach(c => {
          selectStudentClass.innerHTML += `<option value="${c}">${c}</option>`;
        });
      }

      // Dropdown Kelas & BAB di Tab Jurnal
      const jurnalSelectClass = document.getElementById('jurnal-select-class');
      jurnalSelectClass.innerHTML = '';
      state.classes.forEach(c => {
        jurnalSelectClass.innerHTML += `<option value="${c}">${c}</option>`;
      });

      const jurnalSelectChapter = document.getElementById('jurnal-select-chapter');
      jurnalSelectChapter.innerHTML = '';
      state.chapters.forEach((ch, idx) => {
        jurnalSelectChapter.innerHTML += `<option value="${idx}">${ch.title}</option>`;
      });

      // Dropdown Kelas di Tab Kehadiran
      const attSelectClass = document.getElementById('attendance-select-class');
      const savedSelAttClass = attSelectClass.value;
      attSelectClass.innerHTML = '';
      state.classes.forEach(c => {
        attSelectClass.innerHTML += `<option value="${c}" ${c === savedSelAttClass ? 'selected' : ''}>${c}</option>`;
      });

      // Dropdown Kelas di Tab Nilai
      const nilaiSelectClass = document.getElementById('nilai-select-class');
      const savedSelNilClass = nilaiSelectClass.value;
      nilaiSelectClass.innerHTML = '';
      state.classes.forEach(c => {
        nilaiSelectClass.innerHTML += `<option value="${c}" ${c === savedSelNilClass ? 'selected' : ''}>${c}</option>`;
      });
    }

    // ================= RENDERING: TAB DASHBOARD =================
    function renderDashboardStats() {
      // Hitung metrik
      const totalStudents = state.students.length;
      const totalChapters = state.chapters.length;
      const totalJournals = state.journals.length;

      let totalGradeSum = 0;
      state.students.forEach(s => {
        const finalGrade = (s.grades.harian * 0.4) + (s.grades.uts * 0.3) + (s.grades.uas * 0.3);
        totalGradeSum += finalGrade;
      });
      const avgGrade = totalStudents > 0 ? (totalGradeSum / totalStudents).toFixed(1) : 0;

      // Update UI Metrik
      document.getElementById('stat-students').innerText = `${totalStudents} Siswa`;
      document.getElementById('stat-chapters').innerText = `${totalChapters} BAB`;
      document.getElementById('stat-journals').innerText = `${totalJournals} Sesi`;
      document.getElementById('stat-grades').innerText = `${avgGrade} / 100`;

      // Badge Counts
      document.getElementById('badge-classes-count').innerText = `${state.classes.length} Kelas Terdaftar`;
      document.getElementById('badge-students-count').innerText = `${totalStudents} Total Siswa`;

      // Render Sesi Jurnal Terbaru (3 entri terakhir)
      const container = document.getElementById('latest-journals-container');
      container.innerHTML = '';

      if (state.journals.length === 0) {
        container.innerHTML = `
          <div class="text-center py-6 text-slate-400 text-sm">
            Belum ada jurnal mengajar yang dibuat. Silakan menuju menu Jurnal Harian.
          </div>
        `;
      } else {
        const slicedJournals = state.journals.slice(0, 3);
        slicedJournals.forEach(j => {
          container.innerHTML += `
            <div class="p-4 rounded-xl bg-slate-50 border border-slate-200 flex flex-col sm:flex-row justify-between sm:items-center gap-3">
              <div>
                <div class="flex items-center space-x-2">
                  <span class="text-xs bg-indigo-50 text-indigo-700 font-bold px-2 py-0.5 rounded border border-indigo-150">
                    ${j.class}
                  </span>
                  <span class="text-xs text-slate-400 font-medium">
                    ${j.date}
                  </span>
                </div>
                <h4 class="font-semibold text-sm text-slate-700 mt-1">${j.chapter}</h4>
                <p class="text-xs text-indigo-600 font-medium mt-0.5">${j.topic}</p>
                <p class="text-xs text-slate-500 italic mt-2 line-clamp-1">"${j.notes}"</p>
              </div>
              <div class="shrink-0">
                <span class="text-[10px] uppercase font-bold px-2.5 py-1 bg-emerald-50 text-emerald-700 rounded-full border border-emerald-150">
                  Tercatat
                </span>
              </div>
            </div>
          `;
        });
      }
    }

    // ================= RENDERING: TAB JURNAL =================
    function renderJournalsView() {
      const formEl = document.getElementById('form-save-journal');
      const warningEl = document.getElementById('jurnal-warning');

      if (state.chapters.length === 0) {
        formEl.classList.add('hidden');
        warningEl.classList.remove('hidden');
      } else {
        formEl.classList.remove('hidden');
        warningEl.classList.add('hidden');
        
        // Pemicu pemutakhiran dropdown subtopic pertama kali
        handleJournalChapterChange();
      }

      // Render Riwayat Jurnal
      const listContainer = document.getElementById('journal-list-container');
      listContainer.innerHTML = '';

      if (state.journals.length === 0) {
        listContainer.innerHTML = `
          <div class="p-12 text-center text-slate-400 text-sm">
            Belum ada entri riwayat jurnal mengajar.
          </div>
        `;
      } else {
        state.journals.forEach(j => {
          let extraDetails = '';
          if (j.impediment || j.solution) {
            extraDetails = `
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 pt-3 border-t border-slate-100">
                ${j.impediment ? `
                  <div>
                    <span class="text-[10px] uppercase tracking-wider font-extrabold text-rose-500 block">Hambatan:</span>
                    <p class="text-xs text-slate-600 mt-0.5">${j.impediment}</p>
                  </div>
                ` : ''}
                ${j.solution ? `
                  <div>
                    <span class="text-[10px] uppercase tracking-wider font-extrabold text-emerald-600 block">Solusi Guru:</span>
                    <p class="text-xs text-slate-600 mt-0.5">${j.solution}</p>
                  </div>
                ` : ''}
              </div>
            `;
          }

          listContainer.innerHTML += `
            <div class="p-6 hover:bg-slate-50/50 transition-colors">
              <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-4">
                <div>
                  <div class="flex flex-wrap items-center gap-2">
                    <span class="text-xs font-extrabold bg-indigo-100 text-indigo-700 px-2.5 py-1 rounded border border-indigo-200">
                      ${j.class}
                    </span>
                    <span class="text-xs bg-slate-100 text-slate-600 px-2.5 py-1 rounded font-medium border border-slate-200">
                      ${j.date}
                    </span>
                    ${j.semester ? `
                      <span class="text-xs bg-violet-50 text-violet-700 px-2.5 py-1 rounded font-bold border border-violet-150">
                        Semester: ${j.semester}
                      </span>
                    ` : ''}
                    ${j.ta ? `
                      <span class="text-xs bg-amber-50 text-amber-700 px-2.5 py-1 rounded font-semibold border border-amber-150">
                        TA: ${j.ta}
                      </span>
                    ` : ''}
                  </div>
                  <h4 class="text-md font-bold text-slate-800 mt-2">${j.chapter}</h4>
                  <p class="text-xs text-indigo-600 font-semibold">${j.topic}</p>
                </div>
                
                <button
                  onclick="deleteJournal('${j.id}')"
                  class="self-start md:self-center text-rose-500 hover:bg-rose-50 p-2 rounded-lg transition-colors border border-transparent hover:border-rose-100"
                  title="Hapus Jurnal"
                >
                  <i data-lucide="trash-2" class="w-5 h-5"></i>
                </button>
              </div>

              <div class="space-y-3 bg-white p-4 rounded-xl border border-slate-100 shadow-inner">
                <div>
                  <span class="text-[10px] uppercase tracking-wider font-extrabold text-slate-400 block">Isi Catatan Kelas:</span>
                  <p class="text-sm text-slate-700 mt-0.5 whitespace-pre-wrap leading-relaxed">${j.notes}</p>
                </div>
                ${extraDetails}
              </div>
            </div>
          `;
        });
      }
    }

    function handleJournalChapterChange() {
      const chapIndex = document.getElementById('jurnal-select-chapter').value;
      const selectTopic = document.getElementById('jurnal-select-topic');
      selectTopic.innerHTML = '';

      const chapter = state.chapters[chapIndex];
      if (chapter && chapter.topics && chapter.topics.length > 0) {
        chapter.topics.forEach((t, i) => {
          selectTopic.innerHTML += `<option value="${i}">${t.title}</option>`;
        });
      } else {
        selectTopic.innerHTML = `<option value="">-- Tidak ada sub-materi --</option>`;
      }

      handleJournalTopicChange();
    }

    function handleJournalTopicChange() {
      const chapIndex = document.getElementById('jurnal-select-chapter').value;
      const topicIndex = document.getElementById('jurnal-select-topic').value;
      const previewEl = document.getElementById('jurnal-topic-preview');

      const chapter = state.chapters[chapIndex];
      const topic = chapter?.topics[topicIndex];

      if (topic) {
        previewEl.innerText = `"${topic.content}"`;
      } else {
        previewEl.innerText = "Belum ada detail materi untuk topik terpilih.";
      }
    }

    function deleteJournal(id) {
      openConfirmModal(
        "Konfirmasi Hapus Jurnal",
        "Apakah Anda benar-benar yakin ingin menghapus catatan jurnal ini?",
        () => {
          state.journals = state.journals.filter(j => j.id !== id);
          showToast("Jurnal berhasil dihapus.", "error");
          saveState();
        }
      );
    }

    // ================= RENDERING: TAB KEHADIRAN =================
    let attendanceRecordsTemp = {}; // Menyimpan perubahan sementara sebelum tombol simpan ditekan

    function renderAttendanceView() {
      const selectClass = document.getElementById('attendance-select-class').value;
      const inputDate = document.getElementById('attendance-input-date').value;

      // Filter siswa berdasarkan kelas terpilih
      const classStudents = state.students.filter(s => s.class === selectClass);
      const containerList = document.getElementById('attendance-students-list');
      containerList.innerHTML = '';

      if (classStudents.length === 0) {
        containerList.innerHTML = `
          <div class="text-center py-12 text-slate-400 text-sm">
            Belum ada data siswa di kelas ini. Daftarkan siswa baru di Dashboard terlebih dahulu.
          </div>
        `;
      } else {
        // Tarik data presensi tersimpan atau inisialisasi default "Hadir"
        const existing = state.attendance.find(a => a.class === selectClass && a.date === inputDate);
        attendanceRecordsTemp = existing ? { ...existing.records } : {};

        classStudents.forEach(student => {
          // Jika belum ada record sementara, atur default 'Hadir'
          if (!attendanceRecordsTemp[student.id]) {
            attendanceRecordsTemp[student.id] = "Hadir";
          }

          const currentStatus = attendanceRecordsTemp[student.id];

          // Buat baris status tombol kehadiran
          containerList.innerHTML += `
            <div class="grid grid-cols-1 sm:grid-cols-12 gap-2 py-3.5 items-center">
              <div class="col-span-6">
                <p class="text-sm font-semibold text-slate-700">${student.name}</p>
                <p class="text-xs text-slate-400">ID: ${student.id}</p>
              </div>

              <div class="col-span-6 flex justify-between sm:justify-around bg-slate-50 p-1.5 rounded-lg border border-slate-200">
                ${['Hadir', 'Sakit', 'Izin', 'Alpa'].map(status => {
                  const isActive = currentStatus === status;
                  let activeClass = '';
                  if (isActive) {
                    if (status === 'Hadir') activeClass = 'bg-emerald-600 text-white shadow-sm';
                    else if (status === 'Sakit') activeClass = 'bg-amber-500 text-white shadow-sm';
                    else if (status === 'Izin') activeClass = 'bg-blue-500 text-white shadow-sm';
                    else activeClass = 'bg-rose-500 text-white shadow-sm';
                  } else {
                    activeClass = 'text-slate-500 hover:text-slate-800';
                  }

                  return `
                    <button
                      onclick="changeTempAttendance('${student.id}', '${status}')"
                      class="px-3 py-1 rounded text-xs font-bold transition-all ${activeClass}"
                    >
                      ${status}
                    </button>
                  `;
                }).join('')}
              </div>
            </div>
          `;
        });
      }

      // Render histori rekap absensi di panel kanan
      renderAttendanceHistory();
    }

    function changeTempAttendance(studentId, status) {
      attendanceRecordsTemp[studentId] = status;
      // Re-render daftar absensi saja agar tombol berkedip aktif
      const selectClass = document.getElementById('attendance-select-class').value;
      const classStudents = state.students.filter(s => s.class === selectClass);
      const containerList = document.getElementById('attendance-students-list');
      
      // Update instan visual tanpa render total
      classStudents.forEach(student => {
        const studentRow = document.querySelector(`[onclick="changeTempAttendance('${student.id}', 'Hadir')"]`)?.parentElement;
        if (studentRow) {
          const currentStatus = attendanceRecordsTemp[student.id];
          const buttons = studentRow.querySelectorAll('button');
          buttons.forEach(btn => {
            const btnText = btn.innerText.trim();
            btn.className = "px-3 py-1 rounded text-xs font-bold transition-all text-slate-500 hover:text-slate-800";
            if (btnText === currentStatus) {
              if (currentStatus === 'Hadir') btn.classList.add('bg-emerald-600', 'text-white', 'shadow-sm');
              else if (currentStatus === 'Sakit') btn.classList.add('bg-amber-500', 'text-white', 'shadow-sm');
              else if (currentStatus === 'Izin') btn.classList.add('bg-blue-500', 'text-white', 'shadow-sm');
              else btn.classList.add('bg-rose-500', 'text-white', 'shadow-sm');
            }
          });
        }
      });
    }

    function loadAttendanceRecord() {
      // Dipanggil saat filter kelas atau tanggal pada tab kehadiran diubah
      renderAttendanceView();
    }

    function saveAttendanceRecord() {
      const selectClass = document.getElementById('attendance-select-class').value;
      const inputDate = document.getElementById('attendance-input-date').value;

      if (!selectClass || !inputDate) return;

      const existingIndex = state.attendance.findIndex(a => a.class === selectClass && a.date === inputDate);
      const newRecord = {
        id: existingIndex !== -1 ? state.attendance[existingIndex].id : 'att_' + Date.now(),
        date: inputDate,
        class: selectClass,
        records: { ...attendanceRecordsTemp },
        ta: state.ta,
        semester: state.semester
      };

      if (existingIndex !== -1) {
        state.attendance[existingIndex] = newRecord;
      } else {
        state.attendance.unshift(newRecord);
      }

      showToast(`Presensi kelas ${selectClass} tanggal ${inputDate} berhasil disimpan.`);
      saveState();
    }

    function renderAttendanceHistory() {
      const container = document.getElementById('attendance-history-list');
      container.innerHTML = '';

      if (state.attendance.length === 0) {
        container.innerHTML = `
          <div class="text-center py-6 text-slate-400 text-sm">
            Belum ada rekap kehadiran tersimpan.
          </div>
        `;
      } else {
        // Urutkan secara kronologis (terlama ke terbaru) untuk menghitung Pertemuan Ke-X per Kelas
        const sortedAttendance = [...state.attendance].sort((a, b) => new Date(a.date) - new Date(b.date));
        const classCounters = {};
        const meetingNumbers = {};

        sortedAttendance.forEach(att => {
          if (!classCounters[att.class]) {
            classCounters[att.class] = 0;
          }
          classCounters[att.class] += 1;
          meetingNumbers[att.id] = classCounters[att.class];
        });

        state.attendance.forEach(att => {
          const meetingNum = meetingNumbers[att.id] || 1;
          const vals = Object.values(att.records);
          const hadir = vals.filter(v => v === 'Hadir').length;
          const sakit = vals.filter(v => v === 'Sakit').length;
          const izin = vals.filter(v => v === 'Izin').length;
          const alpa = vals.filter(v => v === 'Alpa').length;

          container.innerHTML += `
            <div class="p-4 rounded-xl border border-slate-150 bg-slate-50/50 flex flex-col justify-between">
              <div>
                <div class="flex justify-between items-center mb-1">
                  <span class="text-xs font-extrabold bg-indigo-50 text-indigo-600 px-2 py-0.5 rounded border border-indigo-150">
                    ${att.class}
                  </span>
                  <span class="text-xs text-slate-500 font-medium">${att.date}</span>
                </div>

                <!-- TAMPILAN NOMOR PERTEMUAN KELAS -->
                <div class="text-xs font-extrabold text-indigo-600 mt-1 mb-1">
                  Pertemuan ke-${meetingNum}
                </div>
                
                ${att.semester && att.ta ? `
                  <div class="flex space-x-1.5 text-[9px] font-bold text-slate-400 mb-2">
                    <span>Semester: ${att.semester}</span>
                    <span>•</span>
                    <span>TA: ${att.ta}</span>
                  </div>
                ` : ''}
                
                <div class="grid grid-cols-4 gap-1 text-center text-[10px] font-bold mt-2">
                  <div class="bg-emerald-50 text-emerald-700 py-1 px-1 rounded border border-emerald-100">
                    H: ${hadir}
                  </div>
                  <div class="bg-amber-50 text-amber-700 py-1 px-1 rounded border border-amber-100">
                    S: ${sakit}
                  </div>
                  <div class="bg-blue-50 text-blue-700 py-1 px-1 rounded border border-blue-100">
                    I: ${izin}
                  </div>
                  <div class="bg-rose-50 text-rose-700 py-1 px-1 rounded border border-rose-100">
                    A: ${alpa}
                  </div>
                </div>
              </div>

              <!-- Button Actions (Lihat Detail & Hapus) -->
              <div class="flex items-center justify-between pt-2.5 mt-3 border-t border-slate-200/60">
                <button
                  onclick="showAttendanceDetail('${att.id}')"
                  class="text-xs font-bold text-indigo-600 hover:text-indigo-800 transition-colors flex items-center space-x-1"
                >
                  <i data-lucide="eye" class="w-3.5 h-3.5"></i>
                  <span>Lihat Detail</span>
                </button>
                <button
                  onclick="deleteAttendanceRecord('${att.id}')"
                  class="text-rose-400 hover:text-rose-600 transition-colors"
                  title="Hapus Log"
                >
                  <i data-lucide="trash-2" class="w-3.5 h-3.5"></i>
                </button>
              </div>
            </div>
          `;
        });
      }
    }

    let selectedAttRecordIdForLoad = null;

    function showAttendanceDetail(id) {
      const att = state.attendance.find(a => a.id === id);
      if (!att) return;

      selectedAttRecordIdForLoad = id;

      // Cari nomor pertemuan kronologis khusus kelas ini untuk judul pop-up
      const classSorted = state.attendance
        .filter(a => a.class === att.class)
        .sort((a, b) => new Date(a.date) - new Date(b.date));
      const meetingIndex = classSorted.findIndex(a => a.id === id);
      const meetingNum = meetingIndex !== -1 ? meetingIndex + 1 : 1;

      document.getElementById('att-detail-title').innerText = `Kehadiran Kelas ${att.class} (Pertemuan ke-${meetingNum})`;
      document.getElementById('att-detail-subtitle').innerText = `Tanggal Pelaksanaan: ${att.date}`;

      const listContainer = document.getElementById('att-detail-list');
      listContainer.innerHTML = '';

      // Ambil siswa terdaftar di kelas bersangkutan
      const classStudents = state.students.filter(s => s.class === att.class);
      
      if (classStudents.length === 0) {
        listContainer.innerHTML = `<p class="text-xs text-slate-400 italic text-center py-4">Tidak ada data siswa terdaftar di kelas ini.</p>`;
      } else {
        classStudents.forEach(student => {
          const status = att.records[student.id] || "Belum Absen";
          let badgeColor = "bg-slate-100 text-slate-600 border-slate-200";
          if (status === "Hadir") badgeColor = "bg-emerald-50 text-emerald-700 border-emerald-150";
          else if (status === "Sakit") badgeColor = "bg-amber-50 text-amber-700 border-amber-150";
          else if (status === "Izin") badgeColor = "bg-blue-50 text-blue-700 border-blue-150";
          else if (status === "Alpa") badgeColor = "bg-rose-50 text-rose-700 border-rose-150";

          listContainer.innerHTML += `
            <div class="flex items-center justify-between p-3 rounded-xl bg-slate-50 border border-slate-150">
              <div>
                <p class="text-sm font-bold text-slate-700">${student.name}</p>
                <p class="text-[10px] text-slate-400">ID: ${student.id}</p>
              </div>
              <span class="text-[11px] font-bold px-2.5 py-1 rounded-full border ${badgeColor}">
                ${status}
              </span>
            </div>
          `;
        });
      }

      const modal = document.getElementById('attendance-detail-modal');
      modal.classList.remove('hidden');
      setTimeout(() => {
        modal.firstElementChild.classList.remove('scale-95');
      }, 10);

      lucide.createIcons();
    }

    function closeAttendanceDetailModal() {
      const modal = document.getElementById('attendance-detail-modal');
      modal.firstElementChild.classList.add('scale-95');
      setTimeout(() => {
        modal.classList.add('hidden');
      }, 150);
    }

    function loadRecordToFormFromModal() {
      if (!selectedAttRecordIdForLoad) return;
      const att = state.attendance.find(a => a.id === selectedAttRecordIdForLoad);
      if (!att) return;

      // Set input filter form ke log yang dipilih
      document.getElementById('attendance-select-class').value = att.class;
      document.getElementById('attendance-input-date').value = att.date;

      // Render ulang tampilan form presensi
      renderAttendanceView();
      
      closeAttendanceDetailModal();
      showToast(`Memuat log ${att.class} (${att.date}) ke formulir.`);
    }

    function deleteAttendanceRecord(id) {
      openConfirmModal(
        "Hapus Log Kehadiran",
        "Apakah Anda yakin ingin menghapus catatan rekap kehadiran ini dari daftar log?",
        () => {
          state.attendance = state.attendance.filter(a => a.id !== id);
          showToast("Log kehadiran berhasil dihapus.", "error");
          saveState();
        }
      );
    }

    // ================= RENDERING: TAB MATERI & BAB =================
    function renderChaptersView() {
      const listContainer = document.getElementById('materi-chapters-list');
      listContainer.innerHTML = '';

      if (state.chapters.length === 0) {
        listContainer.innerHTML = `
          <div class="text-center py-12 text-slate-400 text-sm">
            Belum ada materi terdaftar. Silakan buat BAB pertama Anda menggunakan form sebelah kiri.
          </div>
        `;
      } else {
        state.chapters.forEach((chapter, index) => {
          const isExpanded = expandedChapters[chapter.id];
          
          // Generate sub-topics list
          let topicsHtml = '';
          if (chapter.topics.length === 0) {
            topicsHtml = `<p class="text-xs text-slate-400 italic">Belum ada sub-materi untuk BAB ini.</p>`;
          } else {
            topicsHtml = `
              <div class="space-y-3">
                ${chapter.topics.map(t => `
                  <div class="p-3.5 bg-slate-50 rounded-xl border border-slate-150 flex justify-between items-start gap-3">
                    <div class="flex-1">
                      <h6 class="text-sm font-bold text-slate-700">${t.title}</h6>
                      <p class="text-xs text-slate-500 mt-1 whitespace-pre-wrap leading-relaxed">${t.content}</p>
                    </div>
                    <button
                      onclick="deleteTopic('${chapter.id}', '${t.id}')"
                      class="text-rose-400 hover:text-rose-600 p-1 rounded hover:bg-rose-50"
                      title="Hapus Sub-materi"
                    >
                      <i data-lucide="trash-2" class="w-4 h-4"></i>
                    </button>
                  </div>
                `).join('')}
              </div>
            `;
          }

          // Build Chapter Card
          listContainer.innerHTML += `
            <div class="border border-slate-200 rounded-xl overflow-hidden shadow-sm bg-white">
              <!-- Chapter Header (Accordion Toggle) -->
              <div 
                onclick="toggleChapterAccordion('${chapter.id}')"
                class="bg-slate-50/70 p-4 flex justify-between items-center cursor-pointer hover:bg-slate-50 transition-colors"
              >
                <div class="flex-1 min-w-0 pr-4">
                  <h4 class="font-bold text-slate-800 truncate">${chapter.title}</h4>
                  <p class="text-xs text-slate-500 line-clamp-1 mt-0.5">${chapter.description}</p>
                </div>
                <div class="flex items-center space-x-2">
                  <span class="text-[10px] font-bold bg-indigo-50 text-indigo-700 px-2.5 py-1 rounded-full shrink-0 border border-indigo-150">
                    ${chapter.topics.length} Sub-Materi
                  </span>
                  <i data-lucide="chevron-right" class="w-5 h-5 text-slate-400 transform transition-transform ${isExpanded ? 'rotate-90' : ''}"></i>
                </div>
              </div>

              <!-- Chapter Body (Accordion Content) -->
              <div id="body-${chapter.id}" class="${isExpanded ? 'p-4 border-t border-slate-200 space-y-6' : 'hidden'}">
                <!-- List Subtopics -->
                <div>
                  <h5 class="text-xs font-bold text-indigo-600 uppercase tracking-widest mb-3">Sub-Materi & Rincian</h5>
                  ${topicsHtml}
                </div>

                <!-- Form Tambah Subtopic Baru -->
                <div class="p-4 rounded-xl bg-slate-50 border border-slate-200 border-dashed space-y-3">
                  <h5 class="text-xs font-bold text-slate-700 flex items-center">
                    <i data-lucide="plus" class="w-4 h-4 mr-1 text-indigo-600"></i> Tambah Sub-Materi
                  </h5>
                  
                  <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                    <input
                      type="text"
                      id="input-topic-title-${chapter.id}"
                      placeholder="Judul Sub-Materi (Contoh: Pseudocode Sederhana)"
                      class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-1 focus:ring-indigo-500 bg-white"
                    />
                    
                    <textarea
                      id="input-topic-content-${chapter.id}"
                      rows="1"
                      placeholder="Rincian / Detail materi penjelasan untuk diajarkan..."
                      class="w-full px-3 py-2 text-xs border border-slate-300 rounded-lg focus:outline-none focus:ring-1 focus:ring-indigo-500 bg-white resize-none"
                    ></textarea>
                  </div>

                  <button
                    type="button"
                    onclick="addTopicToChapter('${chapter.id}')"
                    class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-[11px] px-3.5 py-1.5 rounded-lg transition-all flex items-center justify-center space-x-1"
                  >
                    <i data-lucide="plus" class="w-3.5 h-3.5"></i>
                    <span>Simpan Sub-Materi</span>
                  </button>
                </div>

                <!-- Tombol Hapus Seluruh BAB -->
                <div class="pt-4 border-t border-slate-100 flex justify-end">
                  <button
                    type="button"
                    onclick="deleteChapter('${chapter.id}')"
                    class="text-xs text-rose-500 hover:text-rose-700 font-bold flex items-center space-x-1"
                  >
                    <i data-lucide="trash-2" class="w-3.5 h-3.5"></i>
                    <span>Hapus Seluruh BAB</span>
                  </button>
                </div>
              </div>
            </div>
          `;
        });
      }
    }

    function toggleChapterAccordion(id) {
      expandedChapters[id] = !expandedChapters[id];
      renderChaptersView();
      lucide.createIcons();
    }

    function addTopicToChapter(chapterId) {
      const titleEl = document.getElementById(`input-topic-title-${chapterId}`);
      const contentEl = document.getElementById(`input-topic-content-${chapterId}`);

      const titleVal = titleEl.value.trim();
      const contentVal = contentEl.value.trim();

      if (!titleVal) {
        showToast("Judul Sub-materi tidak boleh kosong!", "error");
        return;
      }

      state.chapters = state.chapters.map(c => {
        if (c.id === chapterId) {
          return {
            ...c,
            topics: [
              ...c.topics,
              {
                id: 't_' + Date.now(),
                title: titleVal,
                content: contentVal || 'Materi detail belum diisi.'
              }
            ]
          };
        }
        return c;
      });

      showToast("Sub-Materi / Topik berhasil ditambahkan ke BAB.");
      saveState();
    }

    function deleteChapter(chapterId) {
      openConfirmModal(
        "Konfirmasi Hapus BAB",
        "Apakah Anda yakin ingin menghapus BAB ini beserta semua sub-materinya? Tindakan ini permanen.",
        () => {
          state.chapters = state.chapters.filter(c => c.id !== chapterId);
          showToast("BAB berhasil dihapus.", "error");
          saveState();
        }
      );
    }

    function deleteTopic(chapterId, topicId) {
      openConfirmModal(
        "Konfirmasi Hapus Sub-Materi",
        "Apakah Anda yakin ingin menghapus sub-materi ini?",
        () => {
          state.chapters = state.chapters.map(c => {
            if (c.id === chapterId) {
              return {
                ...c,
                topics: c.topics.filter(t => t.id !== topicId)
              };
            }
            return c;
          });
          showToast("Sub-materi dihapus.", "error");
          saveState();
        }
      );
    }

    // ================= RENDERING: TAB DAFTAR NILAI =================
    function renderGrades() {
      const selectedClass = document.getElementById('nilai-select-class').value;
      const searchQuery = document.getElementById('nilai-input-search').value.toLowerCase();

      // Filter siswa
      const filteredStudents = state.students.filter(s => {
        const matchClass = s.class === selectedClass;
        const matchSearch = s.name.toLowerCase().includes(searchQuery);
        return matchClass && matchSearch;
      });

      const tbody = document.getElementById('nilai-table-body');
      tbody.innerHTML = '';

      if (filteredStudents.length === 0) {
        tbody.innerHTML = `
          <tr>
            <td colspan="7" class="text-center py-12 text-slate-400 text-sm">
              Tidak ditemukan data siswa untuk kriteria pencarian ini.
            </td>
          </tr>
        `;
      } else {
        filteredStudents.forEach(student => {
          const isEditing = editingStudentId === student.id;
          const finalGrade = (student.grades.harian * 0.4) + (student.grades.uts * 0.3) + (student.grades.uas * 0.3);
          
          // Penentuan Kelulusan (KKM: 75)
          const passed = finalGrade >= 75;
          const statusText = passed ? "Lulus (Tuntas)" : "Remedial";
          const statusClass = passed 
            ? "bg-emerald-50 text-emerald-700 border-emerald-100" 
            : "bg-rose-50 text-rose-700 border-rose-100";

          let scoreCells = '';
          let actionCell = '';

          if (isEditing) {
            scoreCells = `
              <td class="py-4 px-4 text-center">
                <input
                  type="number"
                  id="edit-harian-${student.id}"
                  value="${student.grades.harian}"
                  class="w-16 px-2 py-1 text-center border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 text-sm"
                  min="0" max="100"
                />
              </td>
              <td class="py-4 px-4 text-center">
                <input
                  type="number"
                  id="edit-uts-${student.id}"
                  value="${student.grades.uts}"
                  class="w-16 px-2 py-1 text-center border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 text-sm"
                  min="0" max="100"
                />
              </td>
              <td class="py-4 px-4 text-center">
                <input
                  type="number"
                  id="edit-uas-${student.id}"
                  value="${student.grades.uas}"
                  class="w-16 px-2 py-1 text-center border border-slate-300 rounded focus:ring-2 focus:ring-indigo-500 text-sm"
                  min="0" max="100"
                />
              </td>
            `;

            actionCell = `
              <button
                onclick="saveStudentGrades('${student.id}')"
                class="text-emerald-600 hover:bg-emerald-50 px-3 py-1 rounded-lg border border-emerald-200 text-xs font-bold transition-all"
              >
                Simpan
              </button>
            `;
          } else {
            scoreCells = `
              <td class="py-4 px-4 text-center">
                <span class="font-medium text-slate-700">${student.grades.harian}</span>
              </td>
              <td class="py-4 px-4 text-center">
                <span class="font-medium text-slate-700">${student.grades.uts}</span>
              </td>
              <td class="py-4 px-4 text-center">
                <span class="font-medium text-slate-700">${student.grades.uas}</span>
              </td>
            `;

            actionCell = `
              <button
                onclick="startEditGrades('${student.id}')"
                class="text-indigo-600 hover:bg-indigo-50 px-3 py-1 rounded-lg border border-indigo-200 text-xs font-bold transition-all flex items-center justify-center mx-auto space-x-1"
              >
                <i data-lucide="edit-3" class="w-3.5 h-3.5"></i>
                <span>Input</span>
              </button>
            `;
          }

          tbody.innerHTML += `
            <tr class="hover:bg-slate-50/50 transition-colors">
              <td class="py-4 px-4 font-semibold text-slate-800">
                <div>
                  ${student.name}
                  <p class="text-[10px] text-slate-400 font-normal">ID: ${student.id}</p>
                </div>
              </td>
              ${scoreCells}
              <td class="py-4 px-4 text-center font-bold text-slate-900">
                ${finalGrade.toFixed(1)}
              </td>
              <td class="py-4 px-4 text-center">
                <span class="inline-block px-2.5 py-1 text-xs font-bold rounded-full border ${statusClass}">
                  ${statusText}
                </span>
              </td>
              <td class="py-4 px-4 text-center">
                ${actionCell}
              </td>
            </tr>
          `;
        });
      }
      
      lucide.createIcons();
    }

    function startEditGrades(studentId) {
      editingStudentId = studentId;
      renderGrades();
    }

    function saveStudentGrades(studentId) {
      const harianVal = parseFloat(document.getElementById(`edit-harian-${studentId}`).value);
      const utsVal = parseFloat(document.getElementById(`edit-uts-${studentId}`).value);
      const uasVal = parseFloat(document.getElementById(`edit-uas-${studentId}`).value);

      const cleanGrade = (val) => {
        if (isNaN(val)) return 0;
        if (val < 0) return 0;
        if (val > 100) return 100;
        return val;
      };

      state.students = state.students.map(s => {
        if (s.id === studentId) {
          return {
            ...s,
            grades: {
              harian: cleanGrade(harianVal),
              uts: cleanGrade(utsVal),
              uas: cleanGrade(uasVal)
            }
          };
        }
        return s;
      });

      editingStudentId = null;
      showToast("Nilai siswa berhasil diperbarui.");
      saveState();
    }

  </script>
</body>
</html>
