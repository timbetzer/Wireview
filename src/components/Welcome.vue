<script setup>
import { computed, reactive } from "vue";
import PcapFileInput from "../PcapFileInput.vue";
import { manager } from "../globals";

const state = reactive({
  downloadingExample: false,

  // computed
  statusText: null,
});

const loadDemo = (event) => {
  if (state.downloadingExample) return;
  state.downloadingExample = true;
  fetch(event.target.href)
    .then((response) => response.arrayBuffer())
    .then((buffer) => {
      state.downloadingExample = false;
      const file = new File([buffer], "shark1.pcapng");
      manager.openFile(file);
    });
};

state.statusText = computed(() => {
  if (state.downloadingExample) return "Please wait: downloading example...";
  if (manager.activeFile) return "Please wait: opening file...";

  if (manager.lastFileOpenError) {
    const { error, filename } = manager.lastFileOpenError;
    return `Error loading file ${filename}: ${error}`;
  }

  return null;
});
</script>

<template>
  <div class="welcome-container-wrap">
    <div class="welcome-container">
      <div class="welcome-bubble">Willkommen bei Wireview</div>

      <section>
        <h2>Über</h2>
        <p>
          Verwende Wireview, um Paketaufzeichnungsdateien (.pcap, .pcapng, etc.) direkt im Browser zu öffnen und anzusehen.
          Wireview wurde mit Vue.js entwickelt und nutzt Wireshark, das in WebAssembly kompiliert wurde.
        </p>
        <p>
          Alle Vorgänge werden im Browser ausgeführt; es werden keine Daten an einen Server hochgeladen.
        </p>
        <p>
          <a
            href="https://github.com/timbetzer/Wireview#"
            target="_blank"
            class="muted"
            >Projektquellcode auf GitHub ansehen</a
          >.
          <a
            href="https://github.com/radiantly/Wireview"
            target="_blank"
            class="muted"
            >Originalprojektcode auf Github ansehen</a
          >.
          Ermöglicht durch die
          <a
            href="https://github.com/good-tools/wiregasm"
            target="_blank"
            class="muted"
            >Wiregasm</a
          >
          und
          <a href="https://www.wireshark.org/" target="_blank" class="muted"
            >Wireshark</a
          >
          Projekte.
        </p>
        <p>
        <a
            href="https://net.in.tum.de/homepage/legal.html"
            target="_blank"
            class="muted"
            >Impressum</a
          >
        </p>
      </section>
      <section>
        <h2>Öffnen</h2>
        <p v-if="manager.initialized">
          Laden erfolgreich. Wähle eine Datei aus oder
          <a href="/shark1.pcapng" @click.prevent="loadDemo"
            >teste ein Beispiel</a
          >.
        </p>
        <p v-else>Bitte warten, WASM-Binärdatei wird geladen...</p>
        <label>
          <PcapFileInput />
        </label>
        <p>
          {{ state.statusText }}
        </p>
      </section>
    </div>
  </div>
</template>

<style scoped>
p {
  max-width: 80ch;
}
h2 {
  color: #888;
  margin: 0;
}
section {
  display: flex;
  flex-direction: column;
}
a.muted {
  color: inherit;
  text-decoration: underline dotted;
}
a.muted:hover {
  opacity: 0.75;
  text-decoration: underline solid;
}
.welcome-container-wrap {
  flex-grow: 1;

  display: flex;
  justify-content: center;
  background-color: white;
  line-height: 1.5;
  color: #111;
}
.welcome-container {
  width: 80vw;
  padding: 4vh 0;

  display: flex;
  flex-direction: column;
  gap: 4vh;
}
.welcome-bubble {
  align-self: flex-start;
  background-color: var(--ws-nice-blue);
  padding: 6px 9px;
  border-radius: 5px;
}
</style>
