<script lang="ts">
  import { Avatar as AvatarPrimitive } from 'bits-ui';
  import './Avatar.css';
  import type { AvatarProps } from './Avatar.types';

  let {
    src,
    alt,
    fallback,
    onStatusChange,
    ids,
    rootStyle,
    rootClass,
  }: AvatarProps = $props();

  function handleStatusChange(status: 'loading' | 'loaded' | 'error') {
    onStatusChange?.({ status });
  }
</script>

<AvatarPrimitive.Root
  id={ids?.root}
  class={rootClass}
  style={rootStyle}
  onLoadingStatusChange={(status) => handleStatusChange(status)}
>
  <AvatarPrimitive.Fallback id={ids?.fallback}>
    {fallback}
  </AvatarPrimitive.Fallback>
  {#if src}
    <AvatarPrimitive.Image id={ids?.image} {src} {alt} />
  {/if}
</AvatarPrimitive.Root>
