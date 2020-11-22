<script>
import vCalendar from 'v-calendar/lib/components/calendar.umd';
import PopoverRow from 'v-calendar/lib/components/popover-row.umd'

export default {
  name: "Calendar",
  components: {
    PopoverRow,
    vCalendar
  },
  props: {
    visibility: { type: String, default: 'hover' },
    hideIndicators: Boolean,
    step: { type: Number, default: 4 },
  },
  data() {
    const todos = [
      {
        description: 'Take Noah to basketball practice.',
        isComplete: false,
        dates: { weekdays: 6 },
        color: 'blue',
      },
    ];
    return {
      incId: todos.length,
      todos,
      today: null,
    };
  },
  mounted() {

  },
  computed: {
    attributes() {
      return [
        // Attributes for todos
        ...this.todos.map(todo => ({
          dates: todo.dates,
          dot: {
            color: todo.color,
            class: todo.isComplete ? 'opacity-75' : '',
          },
          popover: {
            label: todo.description,
            visibility: this.visibility,
            hideIndicator: this.hideIndicators,
          },
          customData: todo,
        })),
      ];
    },
  },
  methods: {

  }
};
</script>

<template>
  <div class="example">
    <v-calendar :attributes="attributes">
      <template #day-popover="{ day, format, masks, dayTitle, attributes }">
        <div>
          <span v-if="step === 1">Using my own content now</span>
          <div
            class="text-xs text-gray-300 font-semibold text-center"
            v-else-if="step === 2"
          >
            {{ format(day.date, `${masks.dayPopover}`) }}
          </div>
          <div
            class="text-xs text-gray-300 font-semibold text-center"
            v-else-if="step >= 3"
          >
            {{ dayTitle }}
          </div>
          <ul v-if="step === 3">
            <li
              v-for="{ key, customData } in attributes"
              :key="key"
              class="text-gray-100"
            >
              {{ customData.description }}
            </li>
          </ul>

          <div v-if="step === 4" class="popover-row-container">
            <PopoverRow
                v-for="(attr) in attributes"
                :key="attr.key"
                :attribute="attr"
                >
                {{ attr.customData.description }}
            </PopoverRow>
          </div>

        </div>
      </template>
    </v-calendar>
  </div>
</template>

<style lang="scss" scoped>
@import '../assets/styles/global';


</style>