<script>
import axios from 'axios'
import { BASE_URL } from '@/config.js'
import QuizCard from '@/components/QuizCard.vue'
import SearchIcon from '@/components/icons/SearchIcon.vue'

export default {
  components: {
    QuizCard,
    SearchIcon
  },
  data() {
    return {
      quizzes: [],
      searchTerm: '',
      searchTimeout: null
    }
  },
  mounted() {
    this.fetchQuizzes()
  },
  methods: {
    async fetchQuizzes() {
      try {
        const response = await axios.get(`${BASE_URL}/api/v1/quizzes?search=${this.searchTerm}`)
        if (response.status == 200) {
          this.quizzes = response.data
        }
      } catch (error) {
        this.error = 'Error fetching quizzes. Please try again.'
      }
    },
    handleSearch() {
      if (this.searchTimeout) {
        clearTimeout(this.searchTimeout)
      }
      this.searchTimeout = setTimeout(() => {
        this.fetchQuizzes()
      }, 500)
    }
  }
}
</script>

<template>
  <main class="container mx-auto p-6">
    <h1
      id="home-title"
      class="text-6xl md:text-6xl font-bold text-[color:var(--primary-black)] custom-text text-center mb-8"
    >
      <span
        class="bg-[color:var(--primary-black)] text-[color:var(--background-white)] ps-4 pe-3 pb-0.5 pt-1 rounded-xl custom-text"
        >IT</span
      >
      Quizz
    </h1>

    <div class="flex justify-center mb-6">
      <div class="relative w-full sm:w-1/3 md:w-1/4">
        <SearchIcon class="absolute left-3 top-3 h-5 w-5" />
        <input
          id="search-input"
          v-model="searchTerm"
          @input="handleSearch"
          @keyup.enter="handleSearch"
          type="text"
          placeholder="Search quizzes..."
          class="border border-gray-300 rounded-xl p-2 pl-10 w-full focus:outline-none focus:ring-1 focus:ring-[color:var(--secondary-black)]"
        />
      </div>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6 mb-6">
      <QuizCard v-for="(quiz, index) in quizzes" :key="index" :quiz="quiz" />
    </div>
  </main>
</template>

<style scoped>
@font-face {
  font-family: 'And Then It Ends';
  src: url('../assets/fonts/And Then It Ends.otf') format('opentype');
  font-weight: normal;
  font-style: normal;
}

.custom-text {
  font-family: 'And Then It Ends', sans-serif;
}
</style>
