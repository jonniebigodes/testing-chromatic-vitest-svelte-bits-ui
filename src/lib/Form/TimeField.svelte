<script lang="ts">
  import { Time, parseTime } from '@internationalized/date';
  import { TimeField as TimeFieldPrimitive } from 'bits-ui';
  import './TimeField.css';
  import type { TimeFieldProps, TimeValue } from './TimeField.types';

  let {
    value = '',
    onValueChange,
    placeholder,
    required = false,
    oninvalid,
    'aria-describedby': ariaDescribedby,
    min,
    max,
    disabled = false,
    readonly = false,
    children,
    name,
    allowSeconds = false,
    hourCycle,
  }: TimeFieldProps = $props();

  function parseTimeString(timeString: string): TimeValue {
    const parts = timeString.split(':');
    return {
      hour: parseInt(parts[0] || '0', 10),
      minute: parseInt(parts[1] || '0', 10),
      second: parts[2] ? parseInt(parts[2], 10) : undefined,
    };
  }

  function formatTimeValue(time: Time | undefined): string {
    if (!time) return '';
    const pad = (n: number) => String(n).padStart(2, '0');
    if (allowSeconds) {
      return `${pad(time.hour)}:${pad(time.minute)}:${pad(time.second ?? 0)}`;
    }
    return `${pad(time.hour)}:${pad(time.minute)}`;
  }

  const bitsValue = $derived(value ? parseTime(value) : undefined);

  const minValue = $derived(min ? parseTime(min) : undefined);
  const maxValue = $derived(max ? parseTime(max) : undefined);

  function handleValueChange(next: Time | undefined) {
    const formatted = formatTimeValue(next);
    onValueChange?.({
      value: formatted,
      valueAsTime: next
        ? {
            hour: next.hour,
            minute: next.minute,
            second: allowSeconds ? next.second : undefined,
          }
        : parseTimeString(''),
    });
  }

  function handleClear() {
    onValueChange?.({
      value: '',
      valueAsTime: { hour: 0, minute: 0 },
    });
  }
</script>

<div class="time-field">
  <TimeFieldPrimitive.Root
    value={bitsValue}
    onValueChange={handleValueChange}
    {required}
    {disabled}
    readonly={readonly}
    minValue={minValue}
    maxValue={maxValue}
    {hourCycle}
    granularity={allowSeconds ? 'second' : 'minute'}
    onInvalid={oninvalid}
  >
    {#if children}
      <label class="time-field__label">
        {@render children()}
        {#if required}
          <span class="time-field__required">*</span>
        {/if}
      </label>
    {/if}

    <div
      class={[
        'time-field__wrapper',
        disabled && 'time-field__wrapper--disabled',
      ]
        .filter(Boolean)
        .join(' ')}
    >
      <TimeFieldPrimitive.Input
        class={['time-field__input', disabled && 'time-field__input--disabled']
          .filter(Boolean)
          .join(' ')}
        {placeholder}
        {name}
        aria-describedby={ariaDescribedby}
      />

      {#if value && !disabled && !readonly}
        <button
          type="button"
          class="time-field__clear"
          onclick={handleClear}
          aria-label="Clear time"
        >
          ×
        </button>
      {/if}
    </div>
  </TimeFieldPrimitive.Root>
</div>
