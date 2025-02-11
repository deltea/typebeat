<script lang="ts">

</script>

<main class="flex flex-col justify-center items-center h-full gap-8">
  <div class="space-y-2 text-center">
    <h1 class="underline font-bold text-lg">typebeat</h1>
    <h2 class="text-stone-500">generate videos with images on beat</h2>
  </div>

  <div class="flex items-center gap-8">
    <div>
      <label for="bpm">bpm: </label>
      <input type="number" id="bpm" value="100" class="outline-none bg-stone-700 rounded-md p-2 w-20" />
    </div>

    <button class="bg-stone-700 rounded-md py-2 px-4 cursor-pointer hover:bg-stone-200 hover:text-stone-800">
      generate!
    </button>
  </div>

  <div class="bg-stone-700 rounded-md aspect-square w-80">

  </div>
</main>
