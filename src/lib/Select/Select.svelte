<script lang="ts">
  import { Select as SelectPrimitive } from 'bits-ui';
  import './Select.css';
  import type { SelectProps } from './Select.types';

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
    items,
    children,
  }: SelectProps = $props();

  const isMultiple = $derived(type === 'multiple');
  const bitsValue = $derived(
    isMultiple ? (value ?? []) : (value?.[0] ?? undefined),
  );

  function handleValueChange(next: string | string[]) {
    const adapted = Array.isArray(next) ? next : [next];
    onValueChange?.({ value: adapted });
  }

  function handleOpenChange(next: boolean) {
    onOpenChange?.({ open: next });
  }

  const selectedItems = $derived(
    items.filter((item) => (value ?? []).includes(item.value)),
  );

  const valueText = $derived(
    selectedItems.length > 0
      ? selectedItems.map((item) => item.label).join(', ')
      : '',
  );
</script>

<div class="select">
{#if isMultiple}
  <SelectPrimitive.Root
    type="multiple"
    value={bitsValue as string[]}
    onValueChange={handleValueChange}
    open={open}
    onOpenChange={handleOpenChange}
    {disabled}
    {name}
    {required}
    items={items}
  >
    {#if children}
      <span class="select__label">{@render children()}</span>
    {/if}
    <div>
      <SelectPrimitive.Trigger {disabled}>
        <span
          class={['select__value', !valueText && 'select__value--placeholder']
            .filter(Boolean)
            .join(' ')}
        >
          {valueText || placeholder}
        </span>
        <span class="select__chevron">
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
              d="M4 6L8 10L12 6"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </span>
      </SelectPrimitive.Trigger>
    </div>
    <SelectPrimitive.ContentStatic>
        <SelectPrimitive.Viewport>
          <ul class="select__listbox" role="listbox" aria-multiselectable={true}>
            {#each items as item (item.value)}
              {@const isSelected = (value ?? []).includes(item.value)}
              <SelectPrimitive.Item
                value={item.value}
                label={item.label}
                disabled={item.disabled}
              >
                {#snippet child({ props })}
                  <li
                    {...props}
                    role="option"
                    onmousedown={(e) => e.preventDefault()}
                  >
                    <span>{item.label}</span>
                    {#if props['data-selected']}
                      <span class="select__check">✓</span>
                    {/if}
                  </li>
                {/snippet}
              </SelectPrimitive.Item>
            {/each}
          </ul>
        </SelectPrimitive.Viewport>
    </SelectPrimitive.ContentStatic>
  </SelectPrimitive.Root>
{:else}
  <SelectPrimitive.Root
    type="single"
    value={bitsValue as string | undefined}
    onValueChange={handleValueChange}
    open={open}
    onOpenChange={handleOpenChange}
    {disabled}
    {name}
    {required}
    items={items}
  >
    {#if children}
      <span class="select__label">{@render children()}</span>
    {/if}
    <div>
      <SelectPrimitive.Trigger {disabled}>
        <span
          class={['select__value', !valueText && 'select__value--placeholder']
            .filter(Boolean)
            .join(' ')}
        >
          {valueText || placeholder}
        </span>
        <span class="select__chevron">
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path
              d="M4 6L8 10L12 6"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
        </span>
      </SelectPrimitive.Trigger>
    </div>
    <SelectPrimitive.ContentStatic>
        <SelectPrimitive.Viewport>
          <ul class="select__listbox" role="listbox">
            {#each items as item (item.value)}
              {@const isSelected = (value ?? []).includes(item.value)}
              <SelectPrimitive.Item
                value={item.value}
                label={item.label}
                disabled={item.disabled}
              >
                {#snippet child({ props })}
                  <li
                    {...props}
                    role="option"
                    onmousedown={(e) => e.preventDefault()}
                  >
                    <span>{item.label}</span>
                    {#if props['data-selected']}
                      <span class="select__check">✓</span>
                    {/if}
                  </li>
                {/snippet}
              </SelectPrimitive.Item>
            {/each}
          </ul>
        </SelectPrimitive.Viewport>
    </SelectPrimitive.ContentStatic>
  </SelectPrimitive.Root>
{/if}
</div>
