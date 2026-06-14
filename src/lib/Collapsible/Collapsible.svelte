<script lang="ts">
  import { Collapsible as CollapsiblePrimitive } from 'bits-ui';
  import './Collapsible.css';
  import type { CollapsibleProps } from './Collapsible.types';

  let {
    open,
    onOpenChange,
    disabled = false,
    label = 'Toggle',
    children,
    labelContent,
  }: CollapsibleProps = $props();

  let internalOpen = $state(false);
  const isControlled = $derived(open !== undefined);
  const isOpen = $derived(isControlled ? open! : internalOpen);

  function handleOpenChange(next: boolean) {
    if (!isControlled) internalOpen = next;
    onOpenChange?.({ open: next });
  }
</script>

<CollapsiblePrimitive.Root
  open={isOpen}
  onOpenChange={handleOpenChange}
  {disabled}
>
  <CollapsiblePrimitive.Trigger {disabled}>
    {#if labelContent}
      {@render labelContent()}
    {:else}
      <span>{label}</span>
    {/if}
    <span class="collapsible__chevron">
      <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path
          d="M6 4L10 8L6 12"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        />
      </svg>
    </span>
  </CollapsiblePrimitive.Trigger>
  <CollapsiblePrimitive.Content role="region">
    {#if typeof children === 'string'}
      {children}
    {:else if children}
      {@render children()}
    {/if}
  </CollapsiblePrimitive.Content>
</CollapsiblePrimitive.Root>
