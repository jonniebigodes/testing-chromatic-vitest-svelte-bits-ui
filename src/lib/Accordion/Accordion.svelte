<script lang="ts">
  import { untrack } from 'svelte';
  import { Accordion as AccordionPrimitive } from 'bits-ui';
  import './Accordion.css';
  import type { AccordionProps } from './Accordion.types';

  let { inverted = false, items }: AccordionProps = $props();

  let openItems = $state<string[]>(
    untrack(() => (items.length > 0 ? ['item-0'] : [])),
  );
</script>

<AccordionPrimitive.Root
  type="multiple"
  value={openItems}
  onValueChange={(next) => (openItems = next)}
  class={['accordion', inverted ? 'accordion--inverted' : ''].filter(Boolean).join(' ')}
>
  {#each items as item, index (index)}
    {@const itemValue = `item-${index}`}
  <AccordionPrimitive.Item value={itemValue}>
    <AccordionPrimitive.Header>
      <AccordionPrimitive.Trigger>
        <span>{item.title}</span>
        <span class="accordion__chevron">▼</span>
      </AccordionPrimitive.Trigger>
    </AccordionPrimitive.Header>
    <AccordionPrimitive.Content role="region" forceMount={false}>
      {item.content}
    </AccordionPrimitive.Content>
  </AccordionPrimitive.Item>
  {/each}
</AccordionPrimitive.Root>
