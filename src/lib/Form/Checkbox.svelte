<script lang="ts">
  import { Checkbox as CheckboxPrimitive } from 'bits-ui';
  import './Checkbox.css';
  import type { CheckboxProps } from './Checkbox.types';

  let {
    checked,
    onCheckedChange,
    disabled = false,
    required = false,
    name,
    value = 'on',
    readOnly = false,
    children,
  }: CheckboxProps = $props();

  let isChecked = $state(false);

  $effect(() => {
    if (checked !== undefined) {
      isChecked = checked;
    }
  });

  function handleCheckedChange(next: boolean) {
    if (readOnly) return;
    if (checked === undefined) {
      isChecked = next;
    }
    onCheckedChange?.({ checked: next });
  }
</script>

<label class={['checkbox', disabled && 'checkbox--disabled'].filter(Boolean).join(' ')}>
  <CheckboxPrimitive.Root
    checked={checked !== undefined ? checked : isChecked}
    onCheckedChange={handleCheckedChange}
    {disabled}
    {required}
    {name}
    {value}
    readonly={readOnly}
    class="checkbox__input"
  />
  <span aria-hidden="true" class="checkbox__box">
    <span class="checkbox__check">
      <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path
          d="M10 3L4.5 8.5L2 6"
          stroke="white"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        />
      </svg>
    </span>
  </span>
  {#if children}
    <span class="checkbox__label">{@render children()}</span>
  {/if}
</label>
