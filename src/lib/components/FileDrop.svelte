<script lang="ts">
  import type { Snippet } from "svelte";

  let element: HTMLButtonElement;
  let { children, onFileLoaded, validFileTypes, multiple = false }: {
    children: Snippet<[]>;
    onFileLoaded: (files: File[]) => void;
    validFileTypes: string[];
    multiple?: boolean;
  } = $props();

  function onDrop(e: DragEvent) {
    onDragEnd(e);

    let files: (File | null)[] = [];
    if (e.dataTransfer?.items) {
      if (multiple) {
        for (let i = 0; i < e.dataTransfer.items.length; i++) {
          const item = e.dataTransfer.items[i];
          if (item.kind === "file") {
            files?.push(item.getAsFile());
          }
        }
      } else{
        const item = e.dataTransfer.items[0];
        if (item.kind === "file") {
          files?.push(item.getAsFile());
        }
      }
    } else {
      if (multiple) {
        if (!e.dataTransfer?.files) {
          return;
        }

        for (let i = 0; i < e.dataTransfer?.files.length; i++) {
          files.push(e.dataTransfer?.files[i] ?? null);
        }
      } else {
        files.push(e.dataTransfer?.files[0] ?? null);
      }
    }

    if (multiple) {
      files = files.filter((file) => file !== null && validFileTypes.includes(file?.type!));
      if (files.length > 0) {
        onFileLoaded(files as File[]);
      }
    } else {
      if (files.length > 0 && validFileTypes.includes(files[0]?.type!)) {
        onFileLoaded(files as File[]);
      }
    }
  }

  const onDragOver = (_: DragEvent) => element.classList.add("is-over");
  const onDragEnd = (_: DragEvent) => element.classList.remove("is-over");
</script>

<button
  class="rounded-md border-4 border-stone-700 [.is-over]:border-stone-500 text-stone-500 aspect-square w-80 flex justify-center items-center border-dashed duration-200 hover:cursor-pointer hover:border-stone-500"
  on:drop|preventDefault={onDrop}
  on:dragover|preventDefault={onDragOver}
  on:dragleave|preventDefault={onDragEnd}
  on:click={() => {}}
  bind:this={element}
  aria-label="File drop zone"
>
  {@render children()}
</button>
