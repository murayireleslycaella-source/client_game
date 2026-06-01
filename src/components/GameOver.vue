<template>
  <div class="gameover">
    <div class="gameover__box">

      <h2 class="gameover__title">Game Over</h2>

      <div class="gameover__scores">
        <div class="gameover__stat">
          <span class="gameover__label">Your Score</span>
          <span class="gameover__value">{{ score }}</span>
        </div>
        <div class="gameover__stat">
          <span class="gameover__label">Best Score</span>
          <span class="gameover__value highlight">{{ highScore }}</span>
        </div>
      </div>

      <p v-if="score >= highScore && score > 0" class="gameover__record">
        New High Score!
      </p>

      <button class="gameover__btn" @click="$emit('restart')">
        Play Again
      </button>

    </div>
  </div>
</template>

<script setup>
defineProps(['score', 'highScore'])
defineEmits(['restart'])
</script>

<style lang="scss" scoped>
@mixin glow-border($color) {
  border: 2px solid $color;
  box-shadow: 0 0 20px rgba($color, 0.4);
}

$overlay:  rgba(0, 0, 0, 0.85);
$box-bg:   #16213e;
$accent:   #e94560;
$gold:     #f9c74f;
$text:     #eaeaea;
$muted:    #888;

.gameover {
  position: absolute;
  inset: 0;
  background: $overlay;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;

  &__box {
    background: $box-bg;
    padding: 44px 60px;
    border-radius: 14px;
    text-align: center;
    color: $text;
    min-width: 300px;
    @include glow-border($accent);
  }

  &__title {
    font-size: 2rem;
    color: $accent;
    margin-bottom: 28px;
    letter-spacing: 2px;
  }

  &__scores {
    display: flex;
    justify-content: center;
    gap: 40px;
    margin-bottom: 20px;
  }

  &__stat {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  &__label {
    font-size: 0.75rem;
    color: $muted;
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  &__value {
    font-size: 2.2rem;
    font-weight: bold;

    &.highlight {
      color: $gold;
    }
  }

  &__record {
    color: $gold;
    font-size: 0.9rem;
    margin-bottom: 8px;
    letter-spacing: 1px;
  }

  &__btn {
    margin-top: 24px;
    padding: 14px 40px;
    background: $accent;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    font-family: 'Courier New', monospace;
    cursor: pointer;
    letter-spacing: 1px;
    transition: opacity 0.2s, transform 0.1s;

    &:hover  { opacity: 0.85; }
    &:active { transform: scale(0.97); }
  }
}
</style>
