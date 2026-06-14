<script lang="ts">
  import { Button as ButtonPrimitive } from 'bits-ui';
  import './Button.css';
  import type { ButtonProps } from './Button.types';

  let {
    backgroundColor,
    size = 'medium',
    label,
    onclick,
  }: ButtonProps = $props();

  const bgStyle = $derived(
    backgroundColor
      ? `background-color: ${backgroundColor}`
      : 'background-color: var(--color-blue-500)',
  );
</script>

<ButtonPrimitive.Root
  type="button"
  class={`button--${size}`}
  style={bgStyle}
  {onclick}
>
  {label}
</ButtonPrimitive.Root>
