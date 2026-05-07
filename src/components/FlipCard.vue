<template>
  <div
    class="flip-card"
    :class="{ 'is-flipped': card.flipped || card.matched, 'is-matched': card.matched }"
  >
    <div class="flip-card__inner">
      <div class="flip-card__front">{{ card.id + 1 }}</div>

      <div class="flip-card__back">
        <div class="image-wrapper">
          <img :src="card.image" :alt="`image ${card.id + 1}`" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FlipCard',
  props: {
    card: {
      type: Object,
      required: true,
    },
  },
}
</script>
