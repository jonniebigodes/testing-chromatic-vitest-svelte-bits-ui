<script lang="ts">
  import { DatePicker as DatePickerPrimitive, Calendar as CalendarPrimitive } from 'bits-ui';
  import type { DateValue } from '@internationalized/date';
  import './DatePicker.css';
  import type { DatePickerProps } from './DatePicker.types';

  let {
    type = 'single',
    value,
    onValueChange,
    open,
    onOpenChange,
    placeholder,
    isDateUnavailable,
    isDateDisabled,
    required = false,
    errorMessageId,
    disableDaysOutsideMonth = false,
    closeOnDateSelect = false,
    weekStartsOn = 0,
    weekdayFormat = 'short',
    fixedWeeks = false,
    minValue,
    maxValue,
    locale = 'en-US',
    disabled = false,
    readOnly = false,
    monthFormat = 'long',
    children,
    name,
  }: DatePickerProps = $props();

  let labelId = `datepicker-label-${Math.random().toString(36).slice(2, 9)}`;

  const weekStart = $derived(weekStartsOn as 0 | 1 | 2 | 3 | 4 | 5 | 6);

  const unavailableMatcher = $derived(
    isDateUnavailable ? (date: DateValue) => isDateUnavailable(date, locale) : undefined,
  );

  function emit(next: DateValue[]) {
    onValueChange?.({
      value: next,
      valueAsString: next.map((v) => v.toString()),
    });
  }

  function handleOpenChange(next: boolean) {
    onOpenChange?.({ open: next });
  }

  function clearValue() {
    if (!disabled) emit([]);
  }

  const displayValue = $derived(
    value?.length
      ? value
          .map((d) =>
            new Intl.DateTimeFormat(locale).format(
              new Date(d.year, d.month - 1, d.day),
            ),
          )
          .join(', ')
      : '',
  );

  const dayClass = (
    selected: boolean,
    isDayDisabled: boolean,
    outsideMonth: boolean,
  ) => {
    const classes = ['date-picker__day'];
    if (selected) classes.push('date-picker__day--selected');
    if (isDayDisabled) classes.push('date-picker__day--disabled');
    if (outsideMonth) classes.push('date-picker__day--outside');
    return classes.join(' ');
  };
</script>

{#if type === 'multiple'}
  <div class="date-picker">
    {#if children}
      <span id={labelId} class="date-picker__label">{children}</span>
    {/if}
    <div class="date-picker__controls">
      <input
        type="text"
        readonly
        value={displayValue}
        aria-describedby={errorMessageId}
        aria-labelledby={children ? labelId : undefined}
        {required}
        {disabled}
        class="date-picker__input"
      />
      <button
        type="button"
        aria-label="📅"
        aria-haspopup="dialog"
        aria-expanded={open ?? false}
        {disabled}
        class="date-picker__trigger"
        onclick={() => handleOpenChange(!(open ?? false))}
      >
        📅
      </button>
      <button type="button" {disabled} class="date-picker__clear" onclick={clearValue}>
        Clear
      </button>
    </div>
    {#if open}
      <div role="dialog" class="date-picker__dialog">
        <CalendarPrimitive.Root
          type="multiple"
          value={value ?? []}
          onValueChange={(next) => {
            emit(next);
            if (closeOnDateSelect) handleOpenChange(false);
          }}
          {placeholder}
          weekStartsOn={weekStart}
          {weekdayFormat}
          {fixedWeeks}
          {isDateDisabled}
          isDateUnavailable={unavailableMatcher}
          {minValue}
          {maxValue}
          {locale}
          {disabled}
          readonly={readOnly}
          {disableDaysOutsideMonth}
          monthFormat={monthFormat}
        >
          {#snippet children({ months, weekdays })}
            <div class="date-picker__view-control">
              <CalendarPrimitive.PrevButton aria-label="Previous month" class="date-picker__nav">
                ←
              </CalendarPrimitive.PrevButton>
              <CalendarPrimitive.Heading class="date-picker__view-trigger" />
              <CalendarPrimitive.NextButton aria-label="Next month" class="date-picker__nav">
                →
              </CalendarPrimitive.NextButton>
            </div>
            <CalendarPrimitive.Grid class="date-picker__table">
              <CalendarPrimitive.GridHead>
                <CalendarPrimitive.GridRow>
                  {#each weekdays as weekDay, id (id)}
                    <CalendarPrimitive.HeadCell class="date-picker__weekday">{weekDay}</CalendarPrimitive.HeadCell>
                  {/each}
                </CalendarPrimitive.GridRow>
              </CalendarPrimitive.GridHead>
              <CalendarPrimitive.GridBody>
                {#each months as month (month.value.toString())}
                  {#each month.weeks as weekDates (weekDates[0]?.toString())}
                    <CalendarPrimitive.GridRow>
                      {#each weekDates as date (date.toString())}
                        {@const outsideMonth = date.month !== month.value.month}
                        <CalendarPrimitive.Cell {date} month={month.value} class="date-picker__cell">
                          <CalendarPrimitive.Day>
                            {#snippet children({ day, disabled: dayDisabled, selected, unavailable })}
                              <button
                                type="button"
                                aria-disabled={dayDisabled || unavailable || undefined}
                                disabled={disabled || readOnly || dayDisabled}
                                class={dayClass(selected, dayDisabled || unavailable, outsideMonth)}
                              >
                                {day}
                              </button>
                            {/snippet}
                          </CalendarPrimitive.Day>
                        </CalendarPrimitive.Cell>
                      {/each}
                    </CalendarPrimitive.GridRow>
                  {/each}
                {/each}
              </CalendarPrimitive.GridBody>
            </CalendarPrimitive.Grid>
          {/snippet}
        </CalendarPrimitive.Root>
      </div>
    {/if}
    {#if name}
      <input type="hidden" {name} value={displayValue} />
    {/if}
  </div>
{:else}
  <div class="date-picker">
  <DatePickerPrimitive.Root
    value={value?.[0]}
    onValueChange={(next) => emit(next ? [next] : [])}
    open={open}
    onOpenChange={handleOpenChange}
    {placeholder}
    isDateUnavailable={unavailableMatcher}
    {isDateDisabled}
    {required}
    {errorMessageId}
    {disableDaysOutsideMonth}
    closeOnDateSelect={closeOnDateSelect}
    weekStartsOn={weekStart}
    {weekdayFormat}
    {fixedWeeks}
    {minValue}
    {maxValue}
    {locale}
    {disabled}
    readonly={readOnly}
    monthFormat={monthFormat}
  >
    {#if children}
      <span id={labelId} class="date-picker__label">{children}</span>
    {/if}
    <div class="date-picker__controls">
      <input
        type="text"
        readonly
        value={displayValue}
        class="date-picker__input"
        aria-describedby={errorMessageId}
        aria-labelledby={children ? labelId : undefined}
      />
      <DatePickerPrimitive.Trigger class="date-picker__trigger" aria-label="📅" {disabled}>
        📅
      </DatePickerPrimitive.Trigger>
      <button type="button" {disabled} class="date-picker__clear" onclick={clearValue}>
        Clear
      </button>
    </div>
    <DatePickerPrimitive.Portal>
      <DatePickerPrimitive.Content class="date-picker__dialog" role="dialog">
        <DatePickerPrimitive.Calendar>
          {#snippet children({ months, weekdays })}
            <div class="date-picker__view-control">
              <DatePickerPrimitive.PrevButton aria-label="Previous month" class="date-picker__nav">
                ←
              </DatePickerPrimitive.PrevButton>
              <DatePickerPrimitive.Heading class="date-picker__view-trigger">
                {#snippet children({ headingValue })}
                  {headingValue}
                {/snippet}
              </DatePickerPrimitive.Heading>
              <DatePickerPrimitive.NextButton aria-label="Next month" class="date-picker__nav">
                →
              </DatePickerPrimitive.NextButton>
            </div>
            <DatePickerPrimitive.Grid class="date-picker__table">
              <DatePickerPrimitive.GridHead>
                <DatePickerPrimitive.GridRow>
                  {#each weekdays as weekDay, id (id)}
                    <DatePickerPrimitive.HeadCell class="date-picker__weekday">
                      {weekDay}
                    </DatePickerPrimitive.HeadCell>
                  {/each}
                </DatePickerPrimitive.GridRow>
              </DatePickerPrimitive.GridHead>
              <DatePickerPrimitive.GridBody>
                {#each months as month (month.value.toString())}
                  {#each month.weeks as weekDates (weekDates[0]?.toString())}
                    <DatePickerPrimitive.GridRow>
                      {#each weekDates as date (date.toString())}
                        {@const outsideMonth = date.month !== month.value.month}
                        <DatePickerPrimitive.Cell {date} month={month.value} class="date-picker__cell">
                          <DatePickerPrimitive.Day>
                            {#snippet children({ day, disabled: dayDisabled, selected, unavailable })}
                              <button
                                type="button"
                                aria-disabled={dayDisabled || unavailable || undefined}
                                disabled={disabled || readOnly || dayDisabled}
                                class={dayClass(selected, dayDisabled || unavailable, outsideMonth)}
                              >
                                {day}
                              </button>
                            {/snippet}
                          </DatePickerPrimitive.Day>
                        </DatePickerPrimitive.Cell>
                      {/each}
                    </DatePickerPrimitive.GridRow>
                  {/each}
                {/each}
              </DatePickerPrimitive.GridBody>
            </DatePickerPrimitive.Grid>
          {/snippet}
        </DatePickerPrimitive.Calendar>
      </DatePickerPrimitive.Content>
    </DatePickerPrimitive.Portal>
    {#if name}
      <input type="hidden" {name} value={displayValue} />
    {/if}
  </DatePickerPrimitive.Root>
  </div>
{/if}
