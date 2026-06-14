<script lang="ts">
  import { untrack } from 'svelte';
  import { RatingGroup as RatingGroupPrimitive } from 'bits-ui';
  import './RatingGroup.css';
  import type { RatingGroupProps } from './RatingGroup.types';

  let {
    onValueChange,
    disabled = false,
    required = false,
    name,
    min = 1,
    max = 5,
    readOnly = false,
    orientation = 'horizontal',
    label,
    children: labelSnippet,
    value,
    defaultValue,
  }: RatingGroupProps = $props();

  const isVertical = $derived(orientation === 'vertical');

  let internalValue = $state(untrack(() => defaultValue ?? 0));
  const isControlled = $derived(value !== undefined);
  const currentValue = $derived(isControlled ? value : internalValue);
</script>

<RatingGroupPrimitive.Root
  value={currentValue}
  onValueChange={(next) => {
    if (!isControlled) internalValue = next;
    onValueChange?.({ value: next });
  }}
  {min}
  {max}
  {disabled}
  {required}
  {name}
  readonly={readOnly}
  {orientation}
  hoverPreview={!disabled && !readOnly}
>
  {#snippet children({ items })}
    {#if labelSnippet || label}
      <span class="rating-group__label">
        {#if labelSnippet}
          {@render labelSnippet()}
        {:else}
          {label}
        {/if}
      </span>
    {/if}
    <div
      class={[
        'rating-group__stars',
        isVertical ? 'rating-group__stars--vertical' : 'rating-group__stars--horizontal',
      ].join(' ')}
    >
      {#each items as item (item.index)}
        <RatingGroupPrimitive.Item
          index={item.index}
          disabled={disabled || readOnly}
        >
          {#snippet children({ state })}
            <svg
              class="rating-group__star-icon"
              width="24"
              height="24"
              viewBox="0 0 24 24"
              fill={state === 'active' || state === 'partial' ? 'currentColor' : 'none'}
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <polygon
                points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"
              />
            </svg>
          {/snippet}
        </RatingGroupPrimitive.Item>
      {/each}
    </div>
  {/snippet}
</RatingGroupPrimitive.Root>
