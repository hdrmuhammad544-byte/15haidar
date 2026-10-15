
<script setup>
import { onMounted, ref } from 'vue'
import api from '../src/services/api'

const users = ref([])
const isLoading = ref(true)
const errorMessage = ref('')

async function loadUsers() {
  isLoading.value = true
  errorMessage.value = ''

  try {
    const response = await api.get('/api/users')
    users.value = response.data.data ?? []
  } catch (error) {
    errorMessage.value =
      error instanceof Error
        ? error.message
        : 'Terjadi kesalahan saat mengambil data.'
  } finally {
    isLoading.value = false
  }
}

onMounted(loadUsers)
</script>

<template>
  <main class="page-wrapper">
    <div class="container">
      <!-- Header -->
      <header class="page-header">
        <div>
          <span class="eyebrow">USER MANAGEMENT</span>
          <h1>Daftar Pengguna</h1>
          <p class="subtitle">
            Informasi pengguna yang terdaftar di sistem.
          </p>
        </div>

        <button
          class="refresh-button"
          type="button"
          :disabled="isLoading"
          @click="loadUsers"
        >
          <svg
            class="refresh-icon"
            :class="{ spinning: isLoading }"
            viewBox="0 0 24 24"
            fill="none"
            aria-hidden="true"
          >
            <path
              d="M20 7v5h-5M4 17v-5h5"
              stroke="currentColor"
              stroke-width="1.8"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
            <path
              d="M5.6 9A7 7 0 0 1 18 6l2 6M4 12l2 6a7 7 0 0 0 12.4-3"
              stroke="currentColor"
              stroke-width="1.8"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
          <span>Refresh</span>
        </button>
      </header>

      <!-- Summary -->
      <section class="summary-card" aria-label="Ringkasan pengguna">
        <div class="summary-icon" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none">
            <path
              d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2m7-10a4 4 0 1 0 0-8 4 4 0 0 0 0 8Zm7-5a4 4 0 0 1 0 8m2 3h2a3 3 0 0 1 3 3v1"
              stroke="currentColor"
              stroke-width="1.7"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </div>

        <div>
          <p class="summary-label">Total pengguna</p>
          <p class="summary-number">
            {{ isLoading ? '—' : users.length }}
          </p>
        </div>

        <span class="summary-badge">Data pengguna</span>
      </section>

      <!-- Content -->
      <section class="content-section">
        <div class="section-heading">
          <div>
            <h2>Semua pengguna</h2>
            <p>Daftar akun yang tersedia.</p>
          </div>

          <span v-if="!isLoading && !errorMessage" class="count-badge">
            {{ users.length }} akun
          </span>
        </div>

        <!-- Loading -->
        <div v-if="isLoading" class="state-card" role="status">
          <span class="loader"></span>
          <h3>Memuat data pengguna</h3>
          <p>Tunggu sebentar, data sedang diambil.</p>
        </div>

        <!-- Error -->
        <div v-else-if="errorMessage" class="state-card" role="alert">
          <div class="state-icon error-icon" aria-hidden="true">!</div>
          <h3>Data gagal dimuat</h3>
          <p>{{ errorMessage }}</p>
          <button
            class="primary-button"
            type="button"
            @click="loadUsers"
          >
            Coba lagi
          </button>
        </div>

        <!-- Empty -->
        <div v-else-if="users.length === 0" class="state-card">
          <div class="state-icon" aria-hidden="true">
            <svg viewBox="0 0 24 24" fill="none">
              <path
                d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2m7-10a4 4 0 1 0 0-8 4 4 0 0 0 0 8Z"
                stroke="currentColor"
                stroke-width="1.7"
                stroke-linecap="round"
              />
            </svg>
          </div>
          <h3>Belum ada pengguna</h3>
          <p>Data pengguna akan ditampilkan di sini.</p>
        </div>

        <!-- User list -->
        <ul v-else class="user-list">
          <li
            v-for="(user, index) in users"
            :key="user.id"
            class="user-card"
          >
            <div class="avatar" aria-hidden="true">
              {{ (user.name || '?').trim().charAt(0).toUpperCase() }}
            </div>

            <div class="user-info">
              <h3>{{ user.name || 'Tanpa nama' }}</h3>
              <p>{{ user.email || 'Email tidak tersedia' }}</p>
            </div>

            <span class="user-number">
              {{ String(index + 1).padStart(2, '0') }}
            </span>
          </li>
        </ul>
      </section>

      <footer class="page-footer">
        <span>Manajemen Pengguna</span>
        <span>Data ditampilkan secara otomatis</span>
      </footer>
    </div>
  </main>
</template>

<style>
/* Reset dasar */
*,
*::before,
*::after {
  box-sizing: border-box;
}

.page-wrapper {
  min-height: 100vh;
  min-height: 100dvh;
  padding: 40px 24px;
  background: #f5f7fb;
  color: #172033;
  font-family:
    Inter, -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
}

.page-wrapper button {
  font: inherit;
}

.container {
  width: 100%;
  max-width: 1000px;
  margin-inline: auto;
}

.page-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 28px;
}

.eyebrow {
  display: inline-block;
  margin-bottom: 10px;
  color: #4169e1;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 1.5px;
}

.page-header h1 {
  margin: 0;
  font-size: clamp(25px, 4vw, 34px);
  line-height: 1.2;
  letter-spacing: -1px;
}

.subtitle {
  margin: 10px 0 0;
  color: #758096;
  font-size: 14px;
  line-height: 1.7;
}

.refresh-button {
  display: inline-flex;
  min-height: 42px;
  align-items: center;
  justify-content: center;
  gap: 9px;
  padding: 0 15px;
  flex-shrink: 0;
  border: 1px solid #e1e7f0;
  border-radius: 10px;
  background: #fff;
  color: #34415a;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s, border-color 0.2s;
}

.refresh-button:hover:not(:disabled) {
  background: #edf3ff;
  border-color: #b9ccff;
}

.refresh-button:disabled {
  cursor: wait;
  opacity: 0.7;
}

.refresh-icon {
  width: 17px;
  height: 17px;
}

.spinning {
  animation: spin 1s linear infinite;
}

.summary-card {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 23px;
  border: 1px solid #e9edf5;
  border-radius: 16px;
  background: #fff;
  box-shadow: 0 4px 18px rgb(31 45 75 / 3%);
}

.summary-icon,
.avatar {
  display: grid;
  flex-shrink: 0;
  place-items: center;
  background: #edf2ff;
  color: #4169e1;
}

.summary-icon {
  width: 52px;
  height: 52px;
  border-radius: 13px;
  padding: 13px;
}

.summary-icon svg {
  width: 100%;
  height: 100%;
}

.summary-label {
  margin: 0 0 5px;
  color: #758096;
  font-size: 13px;
}

.summary-number {
  margin: 0;
  font-size: 27px;
  font-weight: 750;
  letter-spacing: -0.7px;
}

.summary-badge,
.count-badge {
  margin-left: auto;
  border-radius: 8px;
  background: #f0f4ff;
  color: #4169e1;
  font-size: 12px;
  font-weight: 650;
  white-space: nowrap;
}

.summary-badge {
  padding: 8px 11px;
}

.content-section {
  margin-top: 32px;
}

.section-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 17px;
}

.section-heading h2 {
  margin: 0;
  font-size: 18px;
  letter-spacing: -0.3px;
}

.section-heading p {
  margin: 6px 0 0;
  color: #8791a5;
  font-size: 13px;
  line-height: 1.5;
}

.count-badge {
  padding: 7px 10px;
}

.user-list {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 14px;
  margin: 0;
  padding: 0;
  list-style: none;
}

.user-card {
  display: flex;
  min-width: 0;
  align-items: center;
  gap: 14px;
  padding: 19px;
  border: 1px solid #e9edf5;
  border-radius: 14px;
  background: #fff;
  box-shadow: 0 3px 12px rgb(31 45 75 / 2%);
  transition:
    transform 0.2s ease,
    border-color 0.2s ease,
    box-shadow 0.2s ease;
}

.user-card:hover {
  transform: translateY(-2px);
  border-color: #d6e0ff;
  box-shadow: 0 8px 22px rgb(31 45 75 / 6%);
}

.avatar {
  width: 46px;
  height: 46px;
  border-radius: 12px;
  font-size: 17px;
  font-weight: 750;
}

.user-info {
  min-width: 0;
  flex: 1;
}

.user-info h3 {
  overflow-wrap: anywhere;
  margin: 0 0 6px;
  font-size: 14px;
  font-weight: 700;
  line-height: 1.5;
}

.user-info p {
  overflow-wrap: anywhere;
  margin: 0;
  color: #8791a5;
  font-size: 12px;
  line-height: 1.6;
}

.user-number {
  align-self: flex-start;
  color: #a3acc0;
  font-size: 11px;
  font-weight: 700;
}

.state-card {
  display: flex;
  min-height: 240px;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 28px 20px;
  border: 1px solid #e9edf5;
  border-radius: 14px;
  background: #fff;
  text-align: center;
}

.state-card h3 {
  margin: 15px 0 7px;
  font-size: 16px;
}

.state-card p {
  max-width: 100%;
  margin: 0;
  color: #8791a5;
  font-size: 13px;
  line-height: 1.7;
  overflow-wrap: anywhere;
}

.loader {
  width: 32px;
  height: 32px;
  border: 3px solid #e3eaff;
  border-top-color: #4169e1;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}

.state-icon {
  display: grid;
  width: 48px;
  height: 48px;
  place-items: center;
  border-radius: 14px;
  background: #edf2ff;
  color: #4169e1;
  font-size: 22px;
  font-weight: 700;
}

.state-icon svg {
  width: 23px;
  height: 23px;
}

.error-icon {
  background: #fff0f0;
  color: #d14343;
}

.primary-button {
  margin-top: 18px;
  padding: 10px 16px;
  border: 0;
  border-radius: 9px;
  background: #4169e1;
  color: #fff;
  font-size: 13px;
  font-weight: 650;
  cursor: pointer;
}

.primary-button:hover {
  background: #3155c5;
}

.page-footer {
  display: flex;
  justify-content: space-between;
  gap: 14px;
  margin-top: 36px;
  padding-top: 18px;
  border-top: 1px solid #e5eaf2;
  color: #9aa3b5;
  font-size: 11px;
  line-height: 1.6;
}

/* Tablet */
@media (max-width: 760px) {
  .page-wrapper {
    padding: 30px 20px;
  }

  .user-list {
    grid-template-columns: minmax(0, 1fr);
  }

  .user-card {
    padding: 17px;
  }
}

/* Mobile */
@media (max-width: 480px) {
  .page-wrapper {
    padding: 24px 15px;
  }

  .page-header {
    align-items: flex-start;
    gap: 12px;
    margin-bottom: 22px;
  }

  .page-header h1 {
    font-size: 25px;
  }

  .subtitle {
    font-size: 13px;
  }

  .refresh-button {
    min-height: 39px;
    gap: 6px;
    padding: 0 10px;
    font-size: 12px;
  }

  .summary-card {
    gap: 12px;
    padding: 17px;
  }

  .summary-icon {
    width: 44px;
    height: 44px;
    padding: 11px;
  }

  .summary-number {
    font-size: 24px;
  }

  .summary-badge {
    padding: 6px 8px;
    font-size: 10px;
  }

  .content-section {
    margin-top: 27px;
  }

  .section-heading h2 {
    font-size: 16px;
  }

  .user-card {
    gap: 12px;
    padding: 15px;
  }

  .avatar {
    width: 42px;
    height: 42px;
  }

  .page-footer {
    flex-direction: column;
    gap: 5px;
    margin-top: 28px;
  }
}

/* Aksesibilitas */
button:focus-visible {
  outline: 3px solid #9db5ff;
  outline-offset: 3px;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    scroll-behavior: auto !important;
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
</style>
