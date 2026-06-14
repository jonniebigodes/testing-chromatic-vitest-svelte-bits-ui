<script lang="ts">
  import { Combobox as ComboboxPrimitive } from 'bits-ui';
  import './Combobox.css';
  import type { ComboboxProps } from './Combobox.types';

  let {
    type = 'single',
    value,
    onValueChange,
    open,
    onOpenChange,
    disabled = false,
    placeholder = 'Select an option',
    name,
    required = false,
    items = [],
    label,
  }: ComboboxProps = $props();

  const isMultiple = $derived(type === 'multiple');
  const bitsValue = $derived(
    isMultiple ? (value ?? []) : (value?.[0] ?? undefined),
  );
  const comboboxItems = $derived(items.map((item) => ({ value: item, label: item })));

  function handleValueChange(next: string | string[]) {
    const adapted = Array.isArray(next) ? next : [next];
    onValueChange?.({ value: adapted });
  }

  function handleOpenChange(next: boolean) {
    onOpenChange?.({ open: next });
  }
</script>

<div class="combobox">
{#if isMultiple}
  <ComboboxPrimitive.Root
    type="multiple"
    value={bitsValue as string[]}
    onValueChange={handleValueChange}
    open={open}
    onOpenChange={handleOpenChange}
    {disabled}
    {name}
    {required}
    items={comboboxItems}
  >
    {#if label}
      <span class="combobox__label">{label}</span>
    {/if}
    <div class="combobox__input-wrapper">
      <ComboboxPrimitive.Input
        {disabled}
        {placeholder}
        {required}
      />
      <div class="combobox__actions">
        <button type="button" class="combobox__icon-btn" aria-label="Clear" {disabled}>
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
              d="M12 4L4 12M4 4L12 12"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
            />
          </svg>
        </button>
        <ComboboxPrimitive.Trigger aria-label="Toggle" {disabled}>
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
              d="M4 6L8 10L12 6"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </ComboboxPrimitive.Trigger>
      </div>
    </div>
    <ComboboxPrimitive.ContentStatic>
        <ComboboxPrimitive.Viewport>
          <ul class="combobox__listbox" role="listbox">
            {#each items as item (item)}
              <ComboboxPrimitive.Item value={item} label={item}>
                {#snippet child({ props })}
                  <li
                    {...props}
                    role="option"
                    onmousedown={(e) => e.preventDefault()}
                  >
                    <span>{item}</span>
                    {#if props['aria-selected']}
                      <span class="combobox__check">✓</span>
                    {/if}
                  </li>
                {/snippet}
              </ComboboxPrimitive.Item>
            {/each}
          </ul>
        </ComboboxPrimitive.Viewport>
    </ComboboxPrimitive.ContentStatic>
  </ComboboxPrimitive.Root>
{:else}
  <ComboboxPrimitive.Root
    type="single"
    value={bitsValue as string | undefined}
    onValueChange={handleValueChange}
    open={open}
    onOpenChange={handleOpenChange}
    {disabled}
    {name}
    {required}
    items={comboboxItems}
  >
    {#if label}
      <span class="combobox__label">{label}</span>
    {/if}
    <div class="combobox__input-wrapper">
      <ComboboxPrimitive.Input
        {disabled}
        {placeholder}
        {required}
      />
      <div class="combobox__actions">
        <button type="button" class="combobox__icon-btn" aria-label="Clear" {disabled}>
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
              d="M12 4L4 12M4 4L12 12"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
            />
          </svg>
        </button>
        <ComboboxPrimitive.Trigger aria-label="Toggle" {disabled}>
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
              d="M4 6L8 10L12 6"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </ComboboxPrimitive.Trigger>
      </div>
    </div>
    <ComboboxPrimitive.ContentStatic>
        <ComboboxPrimitive.Viewport>
          <ul class="combobox__listbox" role="listbox">
            {#each items as item (item)}
              <ComboboxPrimitive.Item value={item} label={item}>
                {#snippet child({ props })}
                  <li
                    {...props}
                    role="option"
                    onmousedown={(e) => e.preventDefault()}
                  >
                    <span>{item}</span>
                    {#if props['aria-selected']}
                      <span class="combobox__check">✓</span>
                    {/if}
                  </li>
                {/snippet}
              </ComboboxPrimitive.Item>
            {/each}
          </ul>
        </ComboboxPrimitive.Viewport>
    </ComboboxPrimitive.ContentStatic>
  </ComboboxPrimitive.Root>
{/if}
</div>
