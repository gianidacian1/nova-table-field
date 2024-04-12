<template>
  <div class="flex items-center key-value-item">
    <div class="flex flex-grow border-b border-50 key-value-fields" :class="{
       'row-error' : tableErrors[index],
    }">
      <div
        :key="`column-${index}`"
        @click="handleColumnFieldFocus(index)"
        class="flex-grow border-l border-50"
        :class="{'index-column': index == 0}"
        :readonly="index == 0"
        v-for="(cell, index) in row.cells"
      >
        <textarea
          :class="{
            'bg-white': !isEditable,
            'hover:bg-20 focus:bg-white': isEditable,
          }"
          :disabled="!isEditable"
          :dusk="`key-value-value-${index}`"
          :key="cell.id"
          @focus="handleColumnFieldFocus(index)"
          class="font-mono text-sm block min-h-input w-full form-control form-input form-input-row py-4 text-90 min-h-full"
          ref="columnFields"
          v-model="row.cells[index]"
        />
      </div>
    </div>

    <div class="flex justify-center h-11 absolute " :class="tableErrors[index] ? 'error-message' : ''" v-if="tableErrors[index]">
        <span class="flex">
            {{ tableErrors[index] }}
        </span>
    </div>

    <div class="flex justify-center h-11 w-11 absolute" style="right: -40px" v-if="isEditable && canDelete">
      <button
        @click="$emit('remove-row', row.id)"
        class="flex appearance-none cursor-pointer text-70 hover:text-danger active:outline-none active:shadow-outline focus:outline-none focus:shadow-outline"
        style="align-items: center"
        tabindex="-1"
        title="Delete"
        type="button"
      >
        <icon type="trash" />
      </button>
    </div>
  </div>
</template>

<script>
import autosize from 'autosize';

export default {
  props: {
    index: Number,
    row: Object,
    disabled: {
      type: Boolean,
      default: false,
    },
    readOnly: {
      type: Boolean,
      default: false,
    },
    canDelete: {
      type: Boolean,
      default: true,
    },
    errors:{
      type: Array,
      default:[]
    }
  },

  mounted() {
    autosize(this.$refs.columnFields);
  },

  data: () => ({ tableErrors:[] }),

  methods: {
    handleColumnFieldFocus(index) {
      this.$refs.columnFields[index].select();
    },
  },

  computed: {
    isEditable() {
      return !this.readOnly && !this.disabled;
    },
      tableErrors() {
        let data = this.errors.errors || []
        let tableErrorFormated = []

        if (data.extracted_tables && data.extracted_tables.length > 0) {
          const formatErrors = data.extracted_tables[0].split(' ~~~~ ');
          const regexpSize = /Rij\s(\d+)/;

          formatErrors.forEach(error => {
            const match = error.match(regexpSize);
            if (match && match[1]) {
              const currentIndex = parseInt(match[1]) - 1;
              tableErrorFormated[currentIndex] = error;
            }
          });
        }

        return tableErrorFormated
      }
  },
};
</script>

<style scoped>
  .row-error {
    border:1px solid #EF4444;
    border-radius:2px;
  }
  .error-message {
    font-size: 0.8rem;
    color: white;
    left: -35%;
  }
  .error-message > span {
    padding: 5px;
    background: #EF4444;
    align-self: center;
  }
  .index-column{
    width: 12% !important;
  }
</style>
