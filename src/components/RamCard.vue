<script setup>
import { defineProps } from "vue";
const props = defineProps({
  character: Object,
});
function statusColor() {
  switch (props.character.status) {
    case "Alive":
      return "green";
    case "Dead":
      return "red";
    default:
      return "gray";
  }
}
</script>

<template>
  <article class="card">
    <div class="img-wrapper">
      <img class="img" :src="props.character.image" />
    </div>
    <div class="character__info">
      <div class="character__section">
        <h2>{{ character.name }}</h2>
        <span class="status">
          <span
            class="status__icon"
            :style="{ backgroundColor: statusColor() }"
          ></span>
          <span>{{ character.status }} - {{ character.species }}</span>
        </span>
      </div>
      <div class="character__section">
        <span class="text-gray">Last known location:</span>
        <span class="text-big">{{ character.location.name }}</span>
      </div>
      <div class="character__section">
        <span class="text-gray">First seen in:</span>
        <span class="text-big">{{ character.firstEpisode }}</span>
      </div>
    </div>
  </article>
</template>

<style scoped>
h2 {
	font-size: 27px; 
}
.card {
  width: 600px;
  height: 220px;
  display: flex;
  overflow: hidden;
  background: rgb(60, 62, 68);
  border-radius: 0.5rem;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 6px -1px,
    rgba(0, 0, 0, 0.06) 0px 2px 4px -1px;
}
.img-wrapper {
  flex: 2 1 0%;
  width: 100%;
}

.img {
  width: 100%;
  height: 100%;
  object-position: center center;
  object-fit: cover;
}

.character__info {
  flex: 3 1 0%;
  position: relative;
  padding: 0.75rem;
  display: flex;
  flex-direction: column;
  color: white;
}

.character__section {
  flex: 1 1 0%;
  display: flex;
  flex-direction: column;
  text-transform: capitalize;
}

.status__icon {
  display: inline-block;
  min-height: 0.5rem;
  min-width: 0.5rem;
  border-radius: 50%;
  margin-right: 0.5rem;
}
.text-gray {
	color: rgb(158, 158, 158);
}

.text-big {
	font-size: 18px
}
</style>
