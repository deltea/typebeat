<script lang="ts">
	import FileDrop from "$components/FileDrop.svelte";

  let { images = $bindable(), onImageUpload }: {
    images?: HTMLImageElement[] | null;
    onImageUpload: (images: HTMLImageElement[]) => void;
  } = $props();

  async function onFileLoaded(files: File[]) {
    images = [];
    for (let i = 0; i < files.length; i++) {
      const file = files[i];
      const image = new Image();
      image.src = URL.createObjectURL(file);
      images.push(image);
    }

    onImageUpload(images);
  }
</script>

<FileDrop
  validFileTypes={["image/png", "image/jpeg", "image/webp"]}
  {onFileLoaded}
  multiple={true}
>
  <p>drop one or more<br> image files here</p>
</FileDrop>
