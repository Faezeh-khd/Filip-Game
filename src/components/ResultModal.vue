<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal">
      <h2 v-if="status === 'win'" class="success">🎉 تبریک! همه محصولات را پیدا کردی</h2>
      <h2 v-if="status === 'lose'" class="fail">😔 متاسفم، بازی تمام شد</h2>

      <div class="record" v-if="status === 'win'">
        <p v-if="record.bestMoves">بهترین رکورد تعداد حرکت: {{ record.bestMoves }}</p>
        <p v-if="record.bestTime">بهترین رکورد زمان: {{ record.bestTime }} ثانیه</p>
      </div>

      <button class="close-btn" @click="$emit('close')">بستن</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ResultModal',
  props: {
    status: {
      type: String,
      required: true,
    },
    record: {
      type: Object,
      required: true,
    },
  },
}
</script>
