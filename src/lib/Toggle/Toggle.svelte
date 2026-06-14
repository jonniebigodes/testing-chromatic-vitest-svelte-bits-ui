<script lang="ts">
  import { Toggle as TogglePrimitive } from 'bits-ui';
  import './Toggle.css';
  import type { ToggleProps } from './Toggle.types';

  let {
    onPressedChange,
    pressed,
    disabled = false,
    name,
    label,
    children,
  }: ToggleProps = $props();
</script>

<div class="toggle">
  <TogglePrimitive.Root
    pressed={pressed}
    onPressedChange={(next) => onPressedChange?.(next)}
    {disabled}
    aria-label={label ?? 'Toggle'}
  >
    {#snippet child({ props })}
      <button
        {...props}
        type="button"
        {name}
      >
        <div class="toggle__dot"></div>
      </button>
    {/snippet}
  </TogglePrimitive.Root>
  {#if children || label}
    <span
      class={[
        'toggle__label',
        disabled ? 'toggle__label--disabled' : 'toggle__label--enabled',
      ].join(' ')}
    >
      {#if children}
        {@render children()}
      {:else}
        {label}
      {/if}
    </span>
  {/if}
</div>
