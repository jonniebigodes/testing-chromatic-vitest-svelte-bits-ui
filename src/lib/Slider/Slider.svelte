<script lang="ts">
  import { Slider as SliderPrimitive } from 'bits-ui';
  import './Slider.css';
  import type { SliderProps } from './Slider.types';

  let {
    value,
    onValueChange,
    disabled = false,
    min = 0,
    max = 100,
    step = 1,
    orientation = 'horizontal',
    label,
    children,
  }: SliderProps = $props();

  const isVertical = $derived(orientation === 'vertical');
  const currentValue = $derived(value?.[0] ?? min);
</script>

<div
  class={[
    'slider',
    isVertical ? 'slider--vertical' : 'slider--horizontal',
    disabled ? 'slider--disabled' : '',
  ]
    .filter(Boolean)
    .join(' ')}
>
  {#if children || label}
    <span class="slider__label">
      {#if children}
        {@render children()}
      {:else}
        {label}
      {/if}
    </span>
  {/if}
  <span class="slider__value">{currentValue}</span>
  <SliderPrimitive.Root
    type="single"
    value={currentValue}
    onValueChange={(next) => onValueChange?.({ value: [next] })}
    {disabled}
    {min}
    {max}
    {step}
    {orientation}
  >
    {#snippet children({ thumbItems })}
      <div
        class={['slider__control', disabled ? 'slider__control--disabled' : ''].filter(Boolean).join(' ')}
      >
        <div
          class={[
            'slider__track',
            isVertical ? 'slider__track--vertical' : 'slider__track--horizontal',
          ].join(' ')}
        >
          <SliderPrimitive.Range />
        </div>
        {#each thumbItems as { index } (index)}
          <SliderPrimitive.Thumb
            {index}
          />
        {/each}
      </div>
    {/snippet}
  </SliderPrimitive.Root>
</div>
