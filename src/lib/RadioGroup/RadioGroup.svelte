<script lang="ts">
  import { untrack } from 'svelte';
  import { RadioGroup as RadioGroupPrimitive } from 'bits-ui';
  import './RadioGroup.css';
  import type { RadioGroupProps } from './RadioGroup.types';

  let {
    onValueChange,
    disabled = false,
    required = false,
    name,
    orientation = 'vertical',
    readOnly = false,
    label,
    children,
    value,
    defaultValue,
    options,
  }: RadioGroupProps = $props();

  let internalValue = $state<string | null>(untrack(() => defaultValue ?? null));
  const isControlled = $derived(value !== undefined);
  const selectedValue = $derived(isControlled ? value : internalValue);
</script>

<RadioGroupPrimitive.Root
  value={selectedValue ?? ''}
  onValueChange={(next) => {
    if (!isControlled) internalValue = next;
    onValueChange?.({ value: next });
  }}
  {disabled}
  {required}
  {name}
  readonly={readOnly}
  {orientation}
>
  {#if children || label}
    <span class="radio-group__label">
      {#if children}
        {@render children()}
      {:else}
        {label}
      {/if}
      {#if required}<span class="radio-group__required">*</span>{/if}
    </span>
  {/if}
  <div
    class={[
      'radio-group__options',
      orientation === 'horizontal'
        ? 'radio-group__options--horizontal'
        : 'radio-group__options--vertical',
    ].join(' ')}
  >
    {#each options as option (option.value)}
      {@const isItemDisabled = disabled || option.disabled || false}
      {@const checked = selectedValue === option.value}
      <label
        class={[
          'radio-group__item',
          disabled || readOnly || option.disabled
            ? 'radio-group__item--disabled'
            : 'radio-group__item--interactive',
        ].join(' ')}
      >
        <RadioGroupPrimitive.Item
          value={option.value}
          disabled={isItemDisabled}
        />
        <span
          aria-hidden="true"
          class="radio-group__indicator"
          data-state={checked ? 'checked' : 'unchecked'}
          data-disabled={isItemDisabled || undefined}
        ></span>
        <span
          class={[
            'radio-group__option-label',
            disabled || option.disabled ? 'radio-group__option-label--disabled' : '',
          ]
            .filter(Boolean)
            .join(' ')}
        >
          {option.label}
        </span>
      </label>
    {/each}
  </div>
</RadioGroupPrimitive.Root>
