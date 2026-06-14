<script lang="ts">
  import {
    PinInput as PinInputPrimitive,
    REGEXP_ONLY_DIGITS,
    REGEXP_ONLY_CHARS,
    REGEXP_ONLY_DIGITS_AND_CHARS,
  } from 'bits-ui';
  import './PinInput.css';
  import type { PinInputProps } from './PinInput.types';

  let {
    value,
    onValueChange,
    disabled = false,
    maxLength = 4,
    label,
    children,
    required = false,
    name,
    type = 'numeric',
    mask = false,
    placeholder = '○',
    otp = false,
  }: PinInputProps = $props();

  const pattern = $derived(
    type === 'numeric'
      ? REGEXP_ONLY_DIGITS
      : type === 'alphabetic'
        ? REGEXP_ONLY_CHARS
        : REGEXP_ONLY_DIGITS_AND_CHARS,
  );

  const stringValue = $derived((value ?? []).join(''));

  let hiddenInput = $state<HTMLInputElement | null>(null);

  $effect(() => {
    if (hiddenInput) {
      hiddenInput.setAttribute('aria-hidden', 'true');
      hiddenInput.tabIndex = -1;
    }
  });

  function handleValueChange(next: string) {
    const chars = next.split('').slice(0, maxLength);
    onValueChange?.({
      value: chars,
      valueAsString: next,
    });
  }
</script>

<div class="pin-input">
  {#if children || label}
    <label class="pin-input__label">
      {#if children}
        {@render children()}
      {:else}
        {label}
      {/if}
    </label>
  {/if}

  <PinInputPrimitive.Root
    value={stringValue}
    onValueChange={handleValueChange}
    maxlength={maxLength}
    {disabled}
    {required}
    {name}
    {pattern}
    autocomplete={otp ? 'one-time-code' : 'off'}
    pushPasswordManagerStrategy="none"
    bind:inputRef={hiddenInput}
  >
    {#snippet children({ cells })}
      {#each cells as cell, index (index)}
        <PinInputPrimitive.Cell {cell}>
          {#snippet child({ props })}
            <input
              {...props}
              type={mask ? 'password' : 'text'}
              {disabled}
              {required}
              {placeholder}
              inputmode={type === 'numeric' ? 'numeric' : 'text'}
              aria-label={`Digit ${index + 1}`}
              value={cell.char ?? ''}
              readonly
              tabindex={-1}
            />
          {/snippet}
        </PinInputPrimitive.Cell>
      {/each}
    {/snippet}
  </PinInputPrimitive.Root>
</div>

<style>
  :global(.pin-input [data-pin-input-input-wrapper]) {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
  }

  :global(.pin-input [data-pin-input-input-wrapper] input) {
    position: absolute;
    opacity: 0;
    pointer-events: none;
    width: 0;
    height: 0;
  }
</style>
