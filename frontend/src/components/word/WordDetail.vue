<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import VocabService from '../../services/vocabService'
import { useVocabStore } from '../../stores/vocabStore'
import { useAuthStore } from '../../stores/authStore'

const props = defineProps({
  slug: { type: String, required: true },
})

const router = useRouter()
const vocabStore = useVocabStore()
const authStore = useAuthStore()

const vocab = ref(null)
const isLoading = ref(false)
const error = ref(null)

onMounted(async () => {
  isLoading.value = true
  error.value = null
  try {
    vocab.value = await VocabService.getBySlug(props.slug)
  } catch (err) {
    error.value = err.message || 'Không tải được từ này.'
  } finally {
    isLoading.value = false
  }
})

</script>

<template>
  <div class="container mt-4" style="max-width: 500px;">
    <!-- Loading -->
    <div v-if="isLoading" class="text-center text-muted">
      <div class="spinner-border spinner-border-sm me-2" role="status"></div> Đang tải...
    </div>

    <!-- Error -->
    <p v-else-if="error" class="alert alert-danger">{{ error }}</p>

    <!-- Content -->
    <div v-else-if="vocab" class="card shadow-sm border-0">
      <div class="card-body">
        <h2 class="text-primary mb-1">{{ vocab.firstLanguage }}</h2>
        <h5 class="text-muted mb-3">{{ vocab.secondLanguage }}</h5>
        
        <p class="bg-light p-3 rounded">
          <strong>Definition:</strong> {{ vocab.definition }}
        </p>
        
        <div class="text-end mt-3">
          <button class="btn btn-outline-secondary px-4" @click="router.back()">Back</button>
        </div>
      </div>
    </div>
  </div>
</template>