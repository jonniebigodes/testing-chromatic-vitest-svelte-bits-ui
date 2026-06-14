<script lang="ts">
  import { Calendar as CalendarPrimitive } from 'bits-ui';
  import type { DateValue } from '@internationalized/date';
  import './Calendar.css';
  import type { CalendarProps } from './Calendar.types';

  let {
    type = 'single',
    value,
    onValueChange,
    placeholder,
    weekStartsOn = 0,
    weekdayFormat = 'short',
    calendarLabel,
    fixedWeeks = false,
    isDateDisabled,
    isDateUnavailable,
    minValue,
    maxValue,
    locale = 'en-US',
    disabled = false,
    readOnly = false,
    disableDaysOutsideMonth = false,
    maxDays,
    monthFormat = 'long',
    children,
    name,
  }: CalendarProps = $props();

  const isMultiple = $derived(type === 'multiple');
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

  function handleSingleChange(next: DateValue | undefined) {
    emit(next ? [next] : []);
  }

  function handleMultipleChange(next: DateValue[]) {
    emit(next);
  }

  const dayClass = (
    selected: boolean,
    isDisabled: boolean,
    outsideMonth: boolean,
  ) => {
    if (selected) return 'calendar__day calendar__day--selected';
    if (isDisabled) return 'calendar__day calendar__day--disabled';
    if (outsideMonth) return 'calendar__day calendar__day--outside';
    return 'calendar__day calendar__day--default';
  };
</script>

<div class="calendar">
  {#if children}
    <div class="calendar__heading">{children}</div>
  {/if}

  {#if isMultiple}
    <CalendarPrimitive.Root
      type="multiple"
      value={value ?? []}
      onValueChange={handleMultipleChange}
      {placeholder}
      weekStartsOn={weekStart}
      {weekdayFormat}
      {calendarLabel}
      {fixedWeeks}
      {isDateDisabled}
      isDateUnavailable={unavailableMatcher}
      {minValue}
      {maxValue}
      {locale}
      {disabled}
      readonly={readOnly}
      {disableDaysOutsideMonth}
      {maxDays}
      monthFormat={monthFormat}
    >
      {#snippet children({ months, weekdays })}
        <div class="calendar__view-control">
          <CalendarPrimitive.PrevButton
            aria-label="Previous month"
            {disabled}
          >
            ←
          </CalendarPrimitive.PrevButton>
          <CalendarPrimitive.Heading>
            {#snippet children({ headingValue })}
              {headingValue}
            {/snippet}
          </CalendarPrimitive.Heading>
          <CalendarPrimitive.NextButton
            aria-label="Next month"
            {disabled}
          >
            →
          </CalendarPrimitive.NextButton>
        </div>
        <CalendarPrimitive.Grid>
          <CalendarPrimitive.GridHead>
            <CalendarPrimitive.GridRow>
              {#each weekdays as weekDay, id (id)}
                <CalendarPrimitive.HeadCell>{weekDay}</CalendarPrimitive.HeadCell>
              {/each}
            </CalendarPrimitive.GridRow>
          </CalendarPrimitive.GridHead>
          <CalendarPrimitive.GridBody>
            {#each months as month (month.value.toString())}
              {#each month.weeks as weekDates (weekDates[0]?.toString())}
                <CalendarPrimitive.GridRow>
                  {#each weekDates as date (date.toString())}
                    {@const outsideMonth = date.month !== month.value.month}
                    <CalendarPrimitive.Cell {date} month={month.value}>
                      <CalendarPrimitive.Day>
                        {#snippet child({ props })}
                        <button
                          {...props}
                          type="button"
                          class={dayClass(!!props.selected, !!(props.disabled || props.unavailable), outsideMonth)}
                          aria-label={outsideMonth ? `${date.day} outside` : String(date.day)}
                        >
                          {date.day}
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
  {:else}
    <CalendarPrimitive.Root
      type="single"
      value={value?.[0]}
      onValueChange={handleSingleChange}
      {placeholder}
      weekStartsOn={weekStart}
      {weekdayFormat}
      {calendarLabel}
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
        <div class="calendar__view-control">
          <CalendarPrimitive.PrevButton
            aria-label="Previous month"
            {disabled}
          >
            ←
          </CalendarPrimitive.PrevButton>
          <CalendarPrimitive.Heading>
            {#snippet children({ headingValue })}
              {headingValue}
            {/snippet}
          </CalendarPrimitive.Heading>
          <CalendarPrimitive.NextButton
            aria-label="Next month"
            {disabled}
          >
            →
          </CalendarPrimitive.NextButton>
        </div>
        <CalendarPrimitive.Grid>
          <CalendarPrimitive.GridHead>
            <CalendarPrimitive.GridRow>
              {#each weekdays as weekDay, id (id)}
                <CalendarPrimitive.HeadCell>{weekDay}</CalendarPrimitive.HeadCell>
              {/each}
            </CalendarPrimitive.GridRow>
          </CalendarPrimitive.GridHead>
          <CalendarPrimitive.GridBody>
            {#each months as month (month.value.toString())}
              {#each month.weeks as weekDates (weekDates[0]?.toString())}
                <CalendarPrimitive.GridRow>
                  {#each weekDates as date (date.toString())}
                    {@const outsideMonth = date.month !== month.value.month}
                    <CalendarPrimitive.Cell {date} month={month.value}>
                      <CalendarPrimitive.Day>
                        {#snippet child({ props })}
                        <button
                          {...props}
                          type="button"
                          class={dayClass(!!props.selected, !!(props.disabled || props.unavailable), outsideMonth)}
                          aria-label={outsideMonth ? `${date.day} outside` : String(date.day)}
                        >
                          {date.day}
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
  {/if}

  {#if name}
    <input
      type="hidden"
      {name}
      value={value?.map((v) => v.toString()).join(',') ?? ''}
    />
  {/if}
</div>
