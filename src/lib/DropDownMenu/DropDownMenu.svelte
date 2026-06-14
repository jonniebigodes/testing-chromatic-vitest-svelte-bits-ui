<script lang="ts">
  import { DropdownMenu as DropdownMenuPrimitive } from 'bits-ui';
  import './DropDownMenu.css';
  import type { DropDownMenuProps } from './DropDownMenu.types';

  let {
    color: customColor,
    label,
    children,
    inverted = false,
    onSelect,
  }: DropDownMenuProps = $props();

  const buttonColor = $derived(
    inverted ? 'var(--color-slate-800)' : (customColor ?? 'var(--color-blue-500)'),
  );
</script>

<div class="dropdown-menu">
<DropdownMenuPrimitive.Root>
  <DropdownMenuPrimitive.Trigger
    style={`background-color: ${buttonColor}`}
  >
    {label}
    <span class="dropdown-menu__chevron">▼</span>
  </DropdownMenuPrimitive.Trigger>
  <DropdownMenuPrimitive.Portal>
    <DropdownMenuPrimitive.Content>
      <div
        role="menu"
        class={[
          'dropdown-menu__menu',
          inverted ? 'dropdown-menu__menu--inverted' : 'dropdown-menu__menu--default',
        ].join(' ')}
      >
        {#each children as item, index (index)}
          <DropdownMenuPrimitive.Item
            onSelect={() => onSelect?.(item)}
          >
            {item}
          </DropdownMenuPrimitive.Item>
        {/each}
      </div>
    </DropdownMenuPrimitive.Content>
  </DropdownMenuPrimitive.Portal>
</DropdownMenuPrimitive.Root>
</div>
