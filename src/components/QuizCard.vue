<script>
export default {
  props: {
    quiz: {
      type: Object,
      required: true
    }
  },
  methods: {
    goToQuestions() {
      this.$router.push({
        name: 'questions',
        params: {
          quizId: this.quiz._id,
          quizTitle: this.quiz.title
        }
      })
    }
  }
}
</script>

<template>
  <div
    :id="`quiz-card-${quiz._id}`"
    class="relative rounded-2xl overflow-hidden shadow-sm bg-white cursor-pointer image-card"
    @click="goToQuestions"
  >
    <div class="image-container">
      <img class="w-full" :src="`https://quiz-trove.onrender.com/${quiz.image}`" alt="Quiz image" />
    </div>
    <div class="overlay">
      <div class="text-content">
        <p class="text-lg font-semibold text-white">View Questions</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(19, 21, 26, 0.7);
  color: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  border-radius: 1rem;
  text-align: center;
}

.text-content {
  padding: 1rem;
}

.image-card .image-container {
  transition: transform 0.3s ease-in-out;
}

.image-card:hover .image-container {
  transform: scale(1.1);
  box-shadow: 0 10px 20px rgba(19, 21, 26, 0.6);
}

.image-card:hover .overlay {
  opacity: 1;
}
</style>
