<template>
  <v-app>
    <v-main>
      <v-container class="py-5">
        <v-row>
          <v-col cols="12">
            <h1 class="text-h4 mb-4">Image Gallery (Unsplash)</h1>
          </v-col>

          <v-col
            v-for="(image, index) in images"
            :key="image.id"
            cols="12"
            sm="6"
            md="4"
            lg="3"
          >
            <v-card
              @click="openModal(index)"
              class="hoverable"
            >
              <v-img :src="image.urls.small" height="200px" cover />
              <v-card-title class="text-truncate">
                {{ image.alt_description || 'No Title' }}
              </v-card-title>
            </v-card>
          </v-col>
        </v-row>

        <!-- Skeleton loader -->
        <v-row v-if="loading">
          <v-col v-for="n in 12" :key="n" cols="12" sm="6" md="4" lg="3">
            <v-skeleton-loader type="image" />
          </v-col>
        </v-row>

        <!-- Modal -->
        <v-dialog v-model="dialog" max-width="90%" @click:outside="dialog = false">
          <v-card class="modal-card red-outline">
            <!-- Top-right close button (X) - Made more visible -->
            <v-btn
              icon
              class="close-icon"
              @click="dialog = false"
              size="large"
              color="black"
            >
              <v-icon>mdi-close</v-icon>
            </v-btn>

            <!-- Image slider with visible controls -->
            <v-carousel
              v-model="selectedIndex"
              hide-delimiters
              show-arrows="hover"
              height="600"
              class="image-slider"
            >
              <v-carousel-item
                v-for="(image, index) in images"
                :key="index"
              >
                <div class="image-container">
                  <img
                    :src="image.urls.regular"
                    :alt="image.alt_description"
                    class="modal-image"
                  />
                </div>
              </v-carousel-item>
            </v-carousel>

            <!-- Caption -->
            <v-card-text class="text-center">
              <p class="text-subtitle-1">
                {{ images[selectedIndex]?.alt_description || 'No Description' }}
              </p>
            </v-card-text>

            <!-- Bottom controls - Made more visible -->
            <v-card-actions class="justify-space-between px-4 pb-4">
              <div class="d-flex align-center">
                <v-btn
                  icon
                  :href="images[selectedIndex]?.links?.download || images[selectedIndex]?.urls?.full"
                  target="_blank"
                  download
                  size="large"
                  color="primary"
                  class="mr-2"
                >
                  <v-icon>mdi-download</v-icon>
                  <v-tooltip activator="parent" location="bottom">Download</v-tooltip>
                </v-btn>
              </div>

              <!-- Bottom-right red outline close -->
              <v-btn
                color="red"
                variant="outlined"
                @click="dialog = false"
                size="large"
              >
                Close
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const images = ref([])
const loading = ref(true)
const dialog = ref(false)
const selectedIndex = ref(0)

const ACCESS_KEY = 'ZbQJc5soK7RrK1jqiHjGLspB3K9toeK-j6RN4ngA8-E'

const openModal = (index) => {
  selectedIndex.value = index
  dialog.value = true
}

const fetchImages = async () => {
  loading.value = true
  try {
    const res = await fetch(`https://api.unsplash.com/photos/random?count=12&client_id=${ACCESS_KEY}`)
    const data = await res.json()
    images.value = data
  } catch (err) {
    console.error('Failed to load Unsplash images:', err)
  } finally {
    loading.value = false
  }
}

onMounted(fetchImages)
</script>

<style scoped>
.hoverable {
  cursor: pointer;
  transition: transform 0.2s ease;
}
.hoverable:hover {
  transform: scale(1.03);
}

.modal-card {
  position: relative;
  padding: 16px;
}



.close-icon {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: white;
  border: 2px solid black;
  z-index: 10;
}

.image-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  background-color: #f5f5f5;
}

.modal-image {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

.image-slider {
  background-color: #f5f5f5;
  position: relative;
}

/* Custom carousel arrows */
:deep(.v-carousel__controls) {
  background: transparent;
}

:deep(.v-btn--icon.v-carousel__controls) {
  background-color: rgba(255, 255, 255, 0.7);
  color: black !important;
  border: 1px solid rgba(0, 0, 0, 0.2);
  width: 48px;
  height: 48px;
}

:deep(.v-carousel__controls .v-btn:not(:hover)) {
  opacity: 0.7;
}

:deep(.v-carousel__controls .v-btn:hover) {
  opacity: 1;
  background-color: rgba(255, 255, 255, 0.9);
}
</style>