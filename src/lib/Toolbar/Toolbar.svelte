<script lang="ts">
  import { Toolbar as ToolbarPrimitive } from 'bits-ui';
  import './Toolbar.css';
  import type { ToolbarProps } from './Toolbar.types';
  import type { Snippet } from 'svelte';

  let { orientation = 'horizontal', children }: ToolbarProps & { children?: Snippet } = $props();

</script>

<ToolbarPrimitive.Root
  {orientation}
>
  {#if children}
    {@render children()}
  {/if}
</ToolbarPrimitive.Root>
