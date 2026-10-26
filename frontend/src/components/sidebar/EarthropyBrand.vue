<template>
  <!-- EARTHROPY: suite wordmark + A Drive app header (Figma "A Drive" sidebar) -->
  <div class="earthropy-brand">
    <img class="earthropy-brand__wordmark" :src="wordmark" alt="EARTHROPY" />
    <div class="earthropy-brand__app">
      <span class="earthropy-brand__icon" aria-hidden="true">A</span>
      <span class="earthropy-brand__text">
        <span class="earthropy-brand__name">A Drive</span>
        <span class="earthropy-brand__tagline">files &amp; sync</span>
      </span>
    </div>
  </div>
</template>

<script>
import wordmark from "@/css/earthropy/wordmark.png";

export default {
  name: "EarthropyBrand",
  data() {
    return { wordmark };
  },
};
</script>
