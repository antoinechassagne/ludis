<template>
  <svg
    role="img"
    xmlns="http://www.w3.org/2000/svg"
    :width="`${width}px`"
    :height="`${height}px`"
    viewBox="0 0 24 24"
    stroke-width="2"
    stroke-linecap="round"
    stroke-linejoin="miter"
    fill="none"
    :class="`icon icon--${color}`"
  >
    <path d="M17 12L7 12M12 17L12 7" />
    <circle cx="12" cy="12" r="10" />
  </svg>
</template>

<script>
import Icon from "./Icon";

export default {
  name: "IconAdd",
  mixins: [Icon],
};
</script>
