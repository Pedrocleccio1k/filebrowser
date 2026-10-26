<template>
  <!-- EARTHROPY: suite wordmark + A Drive app header (Figma "A Drive" sidebar).
       Clicking the app header opens the app switcher with every EARTHROPY app. -->
  <div class="earthropy-brand">
    <img class="earthropy-brand__wordmark" :src="wordmark" alt="EARTHROPY" />
    <button
      type="button"
      class="earthropy-brand__app"
      aria-haspopup="dialog"
      :aria-expanded="open ? 'true' : 'false'"
      aria-label="Switch app"
      @click="toggle"
    >
      <span class="earthropy-brand__icon" aria-hidden="true">A</span>
      <span class="earthropy-brand__text">
        <span class="earthropy-brand__name">A Drive</span>
        <span class="earthropy-brand__tagline">files &amp; sync</span>
      </span>
      <span class="earthropy-brand__chevron" aria-hidden="true">▾</span>
    </button>

    <div v-if="open" class="earthropy-switcher" role="dialog" aria-label="EARTHROPY apps">
      <div class="earthropy-switcher__title">EARTHROPY apps</div>
      <div class="earthropy-switcher__grid">
        <template v-for="app in apps" :key="app.id">
          <a
            v-if="app.url"
            class="earthropy-switcher__app"
            :href="app.url"
            :aria-current="app.id === 'drive' ? 'page' : null"
            :title="app.description"
          >
            <span class="earthropy-switcher__icon" :style="iconStyle(app)" aria-hidden="true">{{ app.letter }}</span>
            <span>{{ app.name }}</span>
          </a>
          <span
            v-else
            class="earthropy-switcher__app earthropy-switcher__app--off"
            title="Not installed yet"
            aria-disabled="true"
          >
            <span class="earthropy-switcher__icon" :style="iconStyle(app)" aria-hidden="true">{{ app.letter }}</span>
            <span>{{ app.name }}</span>
          </span>
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import wordmark from "@/css/earthropy/wordmark.png";

// Used when the central list (/earthropy/apps.json, served by the EARTHROPY doorman)
// is not reachable, e.g. when A Drive runs on its own.
const fallbackApps = [
  { id: "dashboard", name: "Dashboard", description: "Home of the suite", letter: "E", from: "#0b1b3f", to: "#4f7cff", url: "/" },
  { id: "drive", name: "A Drive", description: "Files & sync", letter: "A", from: "#1b6bff", to: "#19d3c5", url: null },
];

export default {
  name: "EarthropyBrand",
  data() {
    return { wordmark, open: false, apps: fallbackApps, loaded: false };
  },
  mounted() {
    document.addEventListener("keydown", this.onKeydown);
    document.addEventListener("pointerdown", this.onPointerDown);
  },
  unmounted() {
    document.removeEventListener("keydown", this.onKeydown);
    document.removeEventListener("pointerdown", this.onPointerDown);
  },
  methods: {
    iconStyle(app) {
      return { background: `linear-gradient(90deg, ${app.from}, ${app.to})` };
    },
    onPointerDown(event) {
      // close when clicking anywhere outside the brand block
      if (this.open && !this.$el.contains(event.target)) this.open = false;
    },
    onKeydown(event) {
      if (event.key === "Escape") this.open = false;
    },
    async toggle() {
      this.open = !this.open;
      if (this.open && !this.loaded) await this.loadApps();
    },
    async loadApps() {
      this.loaded = true;
      try {
        const res = await fetch("/earthropy/apps.json", { cache: "no-store" });
        if (!res.ok) return;
        const data = await res.json();
        if (Array.isArray(data.apps) && data.apps.length) this.apps = data.apps;
      } catch (e) {
        // keep the fallback list
      }
      // this app is always reachable from itself
      this.apps = this.apps.map((app) => (app.id === "drive" && !app.url ? { ...app, url: window.location.pathname } : app));
    },
  },
};
</script>
