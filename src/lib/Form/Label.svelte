<script lang="ts">
  import { Label as LabelPrimitive } from 'bits-ui';
  import './Label.css';
  import type { LabelProps } from './Label.types';

  let { htmlFor, inverted = false, children }: LabelProps = $props();
</script>

<LabelPrimitive.Root
  for={htmlFor}
  class={[
    'label',
    htmlFor && 'label--linked',
    inverted && 'label--inverted',
  ]
    .filter(Boolean)
    .join(' ')}
>
  {@render children()}
</LabelPrimitive.Root>
