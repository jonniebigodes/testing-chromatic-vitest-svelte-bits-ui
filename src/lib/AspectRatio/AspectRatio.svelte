<script lang="ts">
  import { AspectRatio as AspectRatioPrimitive } from 'bits-ui';
  import './AspectRatio.css';
  import type { AspectRatioProps } from './AspectRatio.types';
  import type { Snippet } from 'svelte';

  let { ratio, children }: AspectRatioProps & { children?: Snippet } = $props();
</script>

<div class="aspect-ratio">
  <AspectRatioPrimitive.Root {ratio}>
    {#snippet child({ props })}
      <div {...props}>
        {#if children}
          {@render children()}
        {/if}
      </div>
    {/snippet}
  </AspectRatioPrimitive.Root>
</div>
