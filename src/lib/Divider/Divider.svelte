<script lang="ts">
  import { Separator } from 'bits-ui';
  import './Divider.css';
  import type { DividerProps } from './Divider.types';

  let { color, inverted = false }: DividerProps = $props();

  const lineColor = $derived(
    inverted ? 'var(--color-white)' : (color ?? 'var(--color-slate-300)'),
  );
</script>

<div class={['divider', inverted && 'divider--inverted'].filter(Boolean).join(' ')}>
  <Separator.Root decorative={false}>
    {#snippet child({ props })}
      <hr {...props} style:background-color={lineColor} />
    {/snippet}
  </Separator.Root>
</div>
