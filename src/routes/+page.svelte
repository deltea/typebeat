<script lang="ts">
  import { FFmpeg } from "@ffmpeg/ffmpeg";
  import { fetchFile } from "@ffmpeg/util";
  import { onMount } from "svelte";

  const ffmpeg = new FFmpeg();

  // ffmpeg.on("log", ({ message }) => console.log(message));
  ffmpeg.on("progress", ({ progress }) => console.log(progress));

  const src = ["/test-1.png", "/test-2.png"];
  let bpm = 120;
  let duration = 10;

  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;

  const images = src.map((src) => {
    const img = new Image();
    img.src = src;
    return img;
  });

  onMount(async () => {
    canvas = document.getElementById("output") as HTMLCanvasElement;
    ctx = canvas.getContext("2d")!;
    canvas.width = 1280;
    canvas.height = 1280;

    await ffmpeg.load();
  });

  function renderFrames(images: HTMLImageElement[]) {
    const fps = 60;
    const totalFrames = duration * fps;
    const framesPerBeat = (60 / bpm) * fps;
    let nextBeatFrmae = framesPerBeat;
    let currentImageIndex = 0;

    const frames = [];

    if (!ctx) return [];

    for (let i = 0; i < totalFrames; i++) {
      if (i >= nextBeatFrmae) {
        console.log(currentImageIndex);
        currentImageIndex = (currentImageIndex + 1) % images.length;
        nextBeatFrmae += framesPerBeat;
      }

      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.drawImage(images[currentImageIndex], 0, 0);

      frames.push(canvas.toDataURL("image/png"));
    }

    return frames;
  }

  async function generateVideo(frames: string[]) {
    console.log("generating video...");

    for (let i = 0; i < frames.length; i++) {
      const file = await fetchFile(frames[i]);
      await ffmpeg.writeFile(`frame-${i}.png`, file);
    }

    await ffmpeg.writeFile("audio.mp3", await fetchFile("/audio-2.mp3"));

    await ffmpeg.exec([
      "-framerate", "30",
      "-i", "frame-%d.png",
      "-i", "audio.mp3",
      "-c:v", "libx264",
      "-c:a", "aac",
      "-filter:a", "volume=0.2",
      "-pix_fmt", "yuv420p",
      "-shortest",
      "output.mp4"
    ]);

    const data = await ffmpeg.readFile("output.mp4");
    const videoBlob = new Blob([data], { type: "video/mp4" });

    console.log("video generated!");
    return URL.createObjectURL(videoBlob);
  }

  async function getVideo() {
    const frames = renderFrames(images);
    const videoURL = await generateVideo(frames);
    console.log(videoURL);
    const a = document.getElementById("download") as HTMLAnchorElement;
    a.href = videoURL;
    a.download = "output.mp4";
  }
</script>

<main class="flex flex-col justify-center items-center h-full gap-8">
  <div class="space-y-2 text-center">
    <h1 class="underline font-bold text-lg">typebeat</h1>
    <h2 class="text-stone-500">generate videos with images on beat</h2>
  </div>

  <div class="flex items-center gap-8">
    <div>
      <label for="bpm">bpm: </label>
      <input
        type="number"
        id="bpm"
        class="outline-none bg-stone-700 rounded-md p-2 w-20"
        bind:value={bpm}
      />
    </div>

    <div>
      <label for="duration">duration: </label>
      <input
        type="number"
        id="duration"
        class="outline-none bg-stone-700 rounded-md p-2 w-20"
        bind:value={duration}
      />
    </div>

    <button
      class="bg-stone-700 rounded-md py-2 px-4 cursor-pointer hover:bg-stone-200 hover:text-stone-800"
      on:click={getVideo}
    >
      generate!
    </button>
  </div>

  <canvas id="output" class="bg-stone-700 rounded-md aspect-square w-80"></canvas>

  <a
    id="download"
    href="/"
    class="bg-stone-700 rounded-md py-2 px-4 cursor-pointer hover:bg-stone-200 hover:text-stone-800"
  >
    download
  </a>
</main>
