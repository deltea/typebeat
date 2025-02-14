<script lang="ts">
  import { analyzeFullBuffer } from "realtime-bpm-analyzer";
	import FileDrop from "$components/FileDrop.svelte";

  let { bpm = $bindable(), audio = $bindable(), onAudioUpload }: {
    bpm: number;
    audio: File | null;
    onAudioUpload: (audio: File) => void;
  } = $props();

  async function onFileLoaded(files: File[]) {
    const reader = new FileReader();

    onAudioUpload(files[0]);
    audio = files[0];

    reader.addEventListener("load", () => {
      const audioContext = new AudioContext();
      audioContext.decodeAudioData(reader.result as ArrayBuffer, async (buffer) => {
        const candidates = await analyzeFullBuffer(buffer);
        bpm = candidates[0].tempo;
        console.log(`bpm: ${bpm}`);
      });
    });

    reader.readAsArrayBuffer(files[0]);
  }
</script>

<FileDrop
  validFileTypes={["audio/mpeg", "audio/mp3"]}
  {onFileLoaded}
>
  <p>drop an audio file here</p>
</FileDrop>
