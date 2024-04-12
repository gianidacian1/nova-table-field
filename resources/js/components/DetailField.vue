<template>
  <PanelItem :index="index" :field="field">
    <template #value>
      <Table :edit-mode="false" class="overflow-hidden" v-if="theData.length > 0">
        <div class="bg-white overflow-hidden">
          <TableRow :disabled="true" :key="index" :row="row" v-for="(row, index) in columnsData" />
          <TableRow :disabled="true" :key="index" :row="row" v-for="(row, index) in theData" />
        </div>
      </Table>
    </template>
  </PanelItem>
</template>

<script>
import TableRow from './FormFields/TableRow';
import Table from './FormFields/Table';

export default {
  props: ['resource', 'resourceName', 'resourceId', 'field'],

  components: { Table, TableRow },

  data: () => ({ columnsData:[], theData: [] }),

  created() {
    let columns = Array.isArray(this.field.value.columns) ? this.field.value.columns : JSON.parse(this.field.value.columns);
    let rows = Array.isArray(this.field.value.rows) ?this.field.value.rows : JSON.parse(this.field.value.rows);

    if (!Array.isArray(columns) || !columns.length) columns = [];
    if (!Array.isArray(rows) || !rows.length) rows = [];

    this.columnsData = [{cells:columns}];
    this.theData = _.map(rows, (cells) => ({ cells }));
  },
};
</script>
