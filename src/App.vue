<!-- <template>
  <div class="app-container">
    <header class="header">
      <h1 class="main-title">Student Gallery</h1>
      <nav class="tabs">
        <button v-for="assignment in assignmentList" :key="assignment"
          :class="['tab-btn', { active: currentAssignment === assignment }]" @click="currentAssignment = assignment">
          {{ assignment }}
        </button>
      </nav>
    </header>

    <div class="main-content">
      <aside class="sidebar">
        <div v-if="currentAssignmentData" class="student-list">
          <div v-for="(images, studentID) in currentAssignmentData" :key="studentID" class="student-row">
            <h3 class="student-id">{{ studentID }}</h3>
            <div class="thumbnails">
              <img v-for="url in images" :key="url" :src="url" :class="['thumb', { selected: selectedImg === url }]"
                @click="selectedImg = url" loading="lazy" />
            </div>
          </div>
        </div>
        <div v-else class="no-data">No data found for this assignment.</div>
      </aside>

      <main class="viewer">
        <div v-if="selectedImg" class="image-wrapper">
          <img :src="selectedImg" class="full-image" />
        </div>
        <div v-else class="placeholder">
          <p>Select a thumbnail to preview work</p>
        </div>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// 1. Auto-scan the directory structure
const modules = import.meta.glob('@/assets/assignments/**/*.{jpg,jpeg,png,webp,webp}', {
  eager: true,
  import: 'default'
});

// 2. Parse paths into nested structure: { Assignment: { StudentID: [URLs] } }
const galleryData = computed(() => {
  const data = {};
  for (const path in modules) {
    const parts = path.split('/');
    // Expected path: /src/assets/assignments/AssignmentName/StudentID/image.jpg
    const assignmentName = parts[parts.length - 3];
    const studentID = parts[parts.length - 2];
    const imgUrl = modules[path];

    if (!data[assignmentName]) data[assignmentName] = {};
    if (!data[assignmentName][studentID]) data[assignmentName][studentID] = [];

    data[assignmentName][studentID].push(imgUrl);
  }
  return data;
});

// 3. States for UI
const assignmentList = computed(() => Object.keys(galleryData.value).sort());
const currentAssignment = ref(assignmentList.value[0] || '');
const selectedImg = ref('');

// Filtered data for the active tab
const currentAssignmentData = computed(() => galleryData.value[currentAssignment.value]);
</script>

<style scoped>
/* Basic Layout */
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

/* Tabs Header */
.header {
  background: #2c3e50;
  color: white;
  padding: 1rem 2rem;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.main-title {
  margin: 0 0 1rem 0;
  font-size: 1.5rem;
}

.tabs {
  display: flex;
  gap: 10px;
}

.tab-btn {
  padding: 8px 20px;
  background: #34495e;
  border: none;
  color: #bdc3c7;
  cursor: pointer;
  border-radius: 4px;
}

.tab-btn.active {
  background: #3498db;
  color: white;
}

/* Main Content Split */
.main-content {
  display: flex;
  flex: 1;
  overflow: hidden;
}

/* Sidebar (Rows) */
.sidebar {
  width: 450px;
  border-right: 1px solid #ddd;
  overflow-y: auto;
  padding: 20px;
  background: #f8f9fa;
}

.student-row {
  margin-bottom: 25px;
  border-bottom: 1px solid #eee;
  padding-bottom: 15px;
}

.student-id {
  font-size: 0.9rem;
  color: #7f8c8d;
  margin-bottom: 10px;
}

.thumbnails {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.thumb {
  width: 70px;
  height: 70px;
  object-fit: cover;
  border-radius: 4px;
  cursor: pointer;
  border: 2px solid transparent;
}

.thumb.selected {
  border-color: #3498db;
  transform: scale(1.05);
}

/* Viewer */
.viewer {
  flex: 1;
  background: #e9ecef;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.full-image {
  max-width: 100%;
  max-height: 85vh;
  box-shadow: 0 5px 25px rgba(0, 0, 0, 0.2);
  border-radius: 4px;
}

.placeholder {
  color: #95a5a6;
  font-size: 1.2rem;
}
</style> -->

<template>
  <div class="app-container">
    <header class="header">
      <h1 class="main-title">Student Gallery</h1>
      <nav class="tabs">
        <button v-for="assignment in assignmentList" :key="assignment"
          :class="['tab-btn', { active: currentAssignment === assignment }]" @click="currentAssignment = assignment">
          {{ assignment }}
        </button>
      </nav>
    </header>

    <div class="main-content">
      <aside class="sidebar">
        <div v-if="currentAssignmentData" class="student-list">
          <div v-for="(images, studentID) in currentAssignmentData" :key="studentID" class="student-row">
            <h3 class="student-id">{{ studentID }}</h3>
            <div class="thumbnails">
              <img v-for="url in images" :key="url" :src="url" :class="['thumb', { selected: selectedImg === url }]"
                @click="handleImageClick(url)" loading="lazy" />
            </div>
          </div>
        </div>
        <div v-else class="no-data">No data found for this assignment.</div>
      </aside>

      <main class="viewer desktop-only">
        <div v-if="selectedImg" class="image-wrapper">
          <img :src="selectedImg" class="full-image" />
        </div>
        <div v-else class="placeholder">
          <p>Select a thumbnail to preview work</p>
        </div>
      </main>
    </div>

    <dialog ref="mobileDialog" class="mobile-modal" @click.self="closeModal">
      <div v-if="selectedImg" class="modal-content">
        <img :src="selectedImg" class="modal-image" />
        <button class="close-btn" @click="closeModal">Close</button>
      </div>
    </dialog>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 1. Directory Scanning Logic
const modules = import.meta.glob('@/assets/assignments/**/*.{jpg,jpeg,png,webp}', {
  eager: true,
  import: 'default',
})

const galleryData = computed(() => {
  const data = {}
  for (const path in modules) {
    const parts = path.split('/')
    const assignmentName = parts[parts.length - 3]
    const studentID = parts[parts.length - 2]
    if (!data[assignmentName]) data[assignmentName] = {}
    if (!data[assignmentName][studentID]) data[assignmentName][studentID] = []
    data[assignmentName][studentID].push(modules[path])
  }
  return data
})

// 2. State Management
const assignmentList = computed(() => Object.keys(galleryData.value).sort())
const currentAssignment = ref(assignmentList.value[0] || '')
const selectedImg = ref('')
const currentAssignmentData = computed(() => galleryData.value[currentAssignment.value])
const mobileDialog = ref(null)

// 3. Interaction Logic
const handleImageClick = (url) => {
  selectedImg.value = url
  // If screen width is less than 768px, open the dialog
  if (window.innerWidth < 768) {
    mobileDialog.value.showModal()
  }
}

const closeModal = () => {
  mobileDialog.value.close()
}
</script>

<style scoped>
/* Existing Layout Base */
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  font-family: sans-serif;
}

.header {
  background: #2c3e50;
  color: white;
  padding: 1rem 2rem;
}

.main-title {
  margin: 0 0 1rem 0;
  font-size: 1.5rem;
}

.tabs {
  display: flex;
  gap: 10px;
  overflow-x: auto;
}

.tab-btn {
  padding: 8px 15px;
  background: #34495e;
  border: none;
  color: white;
  cursor: pointer;
  border-radius: 4px;
  white-space: nowrap;
}

.tab-btn.active {
  background: #3498db;
}

.main-content {
  display: flex;
  flex: 1;
  overflow: hidden;
}

/* Sidebar Adjustment */
.sidebar {
  width: 400px;
  border-right: 1px solid #ddd;
  overflow-y: auto;
  padding: 20px;
  background: #f8f9fa;
}

.student-row {
  margin-bottom: 20px;
}

.student-id {
  font-size: 0.8rem;
  color: #7f8c8d;
}

.thumbnails {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

.thumb {
  width: 80px;
  height: 80px;
  object-fit: cover;
  cursor: pointer;
  border-radius: 4px;
  border: 2px solid transparent;
}

.thumb.selected {
  border-color: #3498db;
}

/* Desktop Viewer */
.viewer {
  flex: 1;
  background: #e9ecef;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.full-image {
  max-width: 100%;
  max-height: 85vh;
  border-radius: 4px;
}

/* Mobile Dialog Styles */
.mobile-modal {
  border: none;
  border-radius: 8px;
  padding: 0;
  max-width: 95vw;
  max-height: 90vh;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
}

.mobile-modal::backdrop {
  background: rgba(0, 0, 0, 0.8);
}

.modal-content {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.modal-image {
  max-width: 100%;
  max-height: 80vh;
  display: block;
}

.close-btn {
  margin: 15px;
  padding: 10px 20px;
  background: #e74c3c;
  color: white;
  border: none;
  border-radius: 4px;
  width: 100%;
}

/* --- Responsive Media Queries --- */
@media (max-width: 768px) {
  .sidebar {
    width: 100%;
    border-right: none;
  }

  .desktop-only {
    display: none;
  }

  /* Hide the right preview on mobile */
  .thumbnails {
    justify-content: space-between;
  }

  .thumb {
    width: 30%;
    height: auto;
    aspect-ratio: 1;
  }
}
</style>
