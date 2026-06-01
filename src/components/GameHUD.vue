<template>
  <div class="hud">
    <div class="hud__score">Score: {{ score }}</div>

    <div class="hud__health">
      <span>Health:</span>
      <div class="hud__bar">
        <div
          class="hud__fill"
          :style="{ width: health + '%' }"
          :class="{ low: health <= 30 }"
        ></div>
      </div>
      <span class="hud__num">{{ health }}%</span>
    </div>

    <div class="hud__timer">Time: {{ timer }}s</div>
  </div>
</template>

<script setup>
defineProps(['score', 'health', 'timer'])
</script>

<style lang="scss" scoped>
$hud-bg:      rgba(0, 0, 0, 0.7);
$green:       #2ecc71;
$red:         #e74c3c;
$text:        #ffffff;

.hud {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 20px;
  background: $hud-bg;
  color: $text;
  font-size: 0.95rem;
  border-bottom: 1px solid rgba(255,255,255,0.1);

  &__health {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  &__bar {
    width: 130px;
    height: 14px;
    background: #333;
    border-radius: 7px;
    overflow: hidden;
  }

  &__fill {
    height: 100%;
    background: $green;
    border-radius: 7px;
    transition: width 0.3s ease, background 0.3s ease;

    &.low {
      background: $red;
    }
  }

  &__num {
    font-size: 0.8rem;
    color: #aaa;
    min-width: 36px;
  }
}
</style>
