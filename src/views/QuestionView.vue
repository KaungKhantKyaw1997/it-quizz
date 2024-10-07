<script>
import axios from 'axios'
import { BASE_URL } from '@/config.js'
import BackArrowIcon from '@/components/icons/BackArrowIcon.vue'

export default {
  components: {
    BackArrowIcon
  },
  props: {
    quizId: {
      type: String,
      required: true
    },
    quizTitle: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      questions: [],
      totalQuestions: 0,
      currentPage: 1,
      totalPages: 1,
      selectedAnswers: {},
      error: null
    }
  },
  mounted() {
    this.fetchQuestions()
  },
  methods: {
    async fetchQuestions(page = 1) {
      try {
        const response = await axios.get(
          `${BASE_URL}/api/v1/quizzes/${this.quizId}/questions?page=${page}&limit=5`
        )
        if (response.status === 200) {
          this.questions = response.data.questions
          this.totalQuestions = response.data.totalQuestions
          this.currentPage = response.data.currentPage
          this.totalPages = response.data.totalPages
        }
      } catch (error) {
        this.error = 'Error fetching questions. Please try again.'
      }
    },
    changePage(page) {
      this.fetchQuestions(page)
    },
    selectAnswer(questionId, optionIndex) {
      this.selectedAnswers = { ...this.selectedAnswers, [questionId]: optionIndex }
    },
    isCorrect(question) {
      return this.selectedAnswers[question._id] === question.correctAnswer
    },
    getCorrectAnswer(question) {
      return question.correctAnswer !== undefined ? question.correctAnswer + 1 : null
    },
    goHome() {
      this.$router.push({ name: 'home' })
    }
  }
}
</script>

<template>
  <div class="container mx-auto p-6">
    <h1 class="flex justify-between items-center text-2xl font-bold mb-6">
      <span>{{ quizTitle }}</span>
      <button @click="goHome" class="bg-gray-100 rounded-xl p-2" id="back-button">
        <BackArrowIcon alt="Back Icon" class="h-5 w-5" />
      </button>
    </h1>

    <div v-if="questions.length" class="space-y-6">
      <div v-for="(question, index) in questions" :key="question._id" class="border-b pb-4">
        <h2 class="text-lg font-semibold mb-2">
          Question {{ index + 1 }}: {{ question.questionText }}
        </h2>
        <ul class="list-none space-y-2">
          <li v-for="(option, optionIndex) in question.options" :key="optionIndex">
            <label
              :class="[
                'block p-2 rounded-lg',
                selectedAnswers[question._id] !== undefined
                  ? isCorrect(question) && selectedAnswers[question._id] === optionIndex
                    ? 'bg-green-100'
                    : selectedAnswers[question._id] === optionIndex
                      ? 'bg-red-100'
                      : 'bg-gray-100'
                  : 'bg-gray-100'
              ]"
            >
              <input
                type="radio"
                :name="`question-${question._id}`"
                :value="optionIndex"
                v-model="selectedAnswers[question._id]"
                @change="selectAnswer(question._id, optionIndex)"
                class="mr-2"
                :id="`option-${question._id}-${optionIndex}`"
              />
              <span class="font-semibold">{{ optionIndex + 1 }}.</span> {{ option }}
            </label>
          </li>
        </ul>
        <p
          v-if="selectedAnswers[question._id] !== undefined"
          class="mt-2 font-medium"
          :class="isCorrect(question) ? 'text-green-700' : 'text-red-700'"
        >
          Correct answer: {{ question.options[getCorrectAnswer(question) - 1] }} (Option
          {{ getCorrectAnswer(question) }})
        </p>
      </div>
    </div>

    <div v-else class="flex justify-center items-center text-center">
      <p class="text-lg font-semibold text-gray-700">No questions found.</p>
    </div>

    <div v-if="totalPages > 1" class="flex justify-between items-center mt-8">
      <button
        @click="changePage(currentPage - 1)"
        :disabled="currentPage === 1"
        class="w-24 px-4 py-2 bg-gray-300 text-gray-700 font-semibold rounded-lg disabled:opacity-30"
        id="prev-page-button"
      >
        Previous
      </button>
      <span class="px-4 py-2 text-sm font-semibold text-gray-700">
        {{ currentPage }} / {{ totalPages }}
      </span>
      <button
        @click="changePage(currentPage + 1)"
        :disabled="currentPage === totalPages"
        class="w-24 px-4 py-2 bg-gray-300 text-gray-700 font-semibold rounded-lg disabled:opacity-30"
        id="next-page-button"
      >
        Next
      </button>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 800px;
}
</style>
