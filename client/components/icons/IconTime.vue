<template>
  <svg
    role="img"
    xmlns="http://www.w3.org/2000/svg"
    :width="`${width}px`"
    :height="`${height}px`"
    viewBox="0 0 24 24"
    stroke-width="2"
    stroke-linecap="square"
    stroke-linejoin="miter"
    fill="none"
    :class="`icon icon--${color}`"
  >
    <circle cx="12" cy="12" r="10" />
    <polyline points="12 5 12 12 16 16" />
  </svg>
</template>

<script>
import Icon from "./Icon";

export default {
  name: "IconTime",
  mixins: [Icon],
};
</script>
