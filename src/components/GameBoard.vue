<template>
  <div class="board" @click="handleClick">

    <!-- Player character -->
    <div
      class="player"
      :style="{ left: player.x + 'px', top: player.y + 'px' }"
    >
      <div class="player__inner"></div>
    </div>

    <!-- Hazards -->
    <div
      v-for="hazard in hazards"
      :key="hazard.id"
      class="hazard"
      :style="{ left: hazard.x + 'px', top: hazard.y + 'px' }"
    ></div>

    <!-- Grid overlay for visual style -->
    <div class="board__grid"></div>

  </div>
</template>

<script setup>
defineProps(['player', 'hazards'])
const emit = defineEmits(['click'])

function handleClick(e) {
  const rect = e.currentTarget.getBoundingClientRect()
  emit('click', {
    x: e.clientX - rect.left,
    y: e.clientY - rect.top
  })
}
</script>

<style lang="scss" scoped>
@mixin glow($color, $size: 8px) {
  box-shadow: 0 0 $size $color, 0 0 ($size * 2) $color;
}

$board-bg:     #1a1a2e;
$player-color: #00d4ff;
$hazard-color: #ff4757;
$grid-color:   rgba(255, 255, 255, 0.03);

.board {
  position: relative;
  width: 100%;
  height: 500px;
  background: $board-bg;
  overflow: hidden;
  cursor: crosshair;

  &__grid {
    position: absolute;
    inset: 0;
    background-image:
      linear-gradient($grid-color 1px, transparent 1px),
      linear-gradient(90deg, $grid-color 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
  }
}

.player {
  position: absolute;
  width: 32px;
  height: 32px;
  transform: translate(-50%, -50%);
  transition: left 0.08s ease, top 0.08s ease;
  z-index: 2;

  &__inner {
    width: 100%;
    height: 100%;
    background: $player-color;
    border-radius: 4px;
    @include glow($player-color);
  }
}

.hazard {
  position: absolute;
  width: 24px;
  height: 24px;
  background: $hazard-color;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
  @include glow($hazard-color, 6px);
}
</style>
