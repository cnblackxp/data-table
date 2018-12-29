<template>
  <div class="mt-5">
    <form class="form" @submit.prevent="addNewTableElement">
      <div class="row">
        <template v-for="column in tableSchema">
          <div :class="columnWidthClass" v-if="column.Field !== 'id'" :key="column.Field">
            <div class="form-group">
              <label for>{{column.Field}}</label>
              <input type="text" :placeholder="column.Field" class="form-control" v-model="newRowData[column.Field]">
            </div>
          </div>
        </template>
        <div :class="columnWidthClass">
          <div class="form-group">
            <label for=""></label>
            <input type="submit" class="btn btn-block btn-success">
          </div>
        </div>
      </div>
    </form>
    <table class="hazem-table table table-hover table-striped">
      <thead>
        <tr>
          <th v-for="column in tableSchema" :key="column.Field">
            <div class="form-group">
              <label for="">{{column.Field}}</label>
              <input type="text" class="form-control" :placeholder="`filter (${column.Field})`" v-model="filterRowData[column.Field]" @keyup="rerender">
            </div>
          </th>
          <th>ACTIONS</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="(row, index) in tableData" >
          <tr v-if="canShowRow(row)" :key="row.id">
            <td v-for="column in tableSchema" :key="column.Field">{{row[column.Field]}}</td>
            <td>
              <b-button-group size="sm">
                <b-btn variant="primary" v-b-modal.updateTableRowModal @click="currentRowIndex = index">
                  <i class="fa fa-pencil"></i>
                </b-btn>
                <b-btn variant="danger" v-b-modal.deleteTableRowModal @click="currentRowIndex = index">
                  <i class="fa fa-trash"></i>
                </b-btn>
              </b-button-group>
            </td>
          </tr>
        </template>
        
      </tbody>
    </table>

    <b-modal id="deleteTableRowModal" title="Delete Row" 
      :header-bg-variant="deleteRowModalVariant"
      :header-text-variant="deleteRowModalTextVariant"
      :body-bg-variant="deleteRowModalVariant"
      :body-text-variant="deleteRowModalTextVariant"
      :footer-bg-variant="deleteRowModalVariant"
      :footer-text-variant="deleteRowModalTextVariant"
      @ok="deleteRow"
    >
      <p>Are you sure ?</p>
    </b-modal>
    <b-modal id="updateTableRowModal" ref="updateRowModal" title="Update Row" @ok="updateTableRow" @cancel="retriveTableData">
      <template v-if="currentRowIndex !== -1">
        <form class="form" @submit.prevent="updateTableRow">
          <div class="row">
            <template v-for="column in tableSchema">
              <div class="col-md-12" v-if="column.Field !== 'id'" :key="column.Field">
                <div class="form-group">
                  <label for>{{column.Field}}</label>
                  <input type="text" :placeholder="column.Field" class="form-control" v-model="tableData[currentRowIndex][column.Field]">
                </div>
              </div>
            </template>
            <div class="col-md-12">
              <div class="form-group">
                <label for=""></label>
                <input type="submit" class="btn btn-block btn-success">
              </div>
            </div>
          </div>
        </form>
      </template>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: {
    tableName: String,
    columnWidth: {
      type: Number,
      default: 3,
      validator(value) {
        return (value <= 12 || value >= 1);
      }
    }
  },
  async created() {
    await this.retriveTableData();
  },
  data() {
    return {
      tableSchema: [],
      tableData: [],
      newRowData: {},
      filterRowData: {},
      deleteRowModalVariant: 'danger',
      deleteRowModalTextVariant: 'light',
      currentRowIndex: -1
    };
  },
  computed: {
    columnWidthClass() {
      return `col-md-${this.columnWidth}`;
    }
  },
  methods: {
    async addNewTableElement() {
      await axios.post(
        `http://localhost:3000/${this.tableName}/insert`, 
        this.newRowData
      );
      await this.retriveTableData();
    },
    async updateTableRow() {
      this.$refs.updateRowModal.hide();
      await axios.patch(
        `http://localhost:3000/${this.tableName}/update`, 
        this.tableData[this.currentRowIndex]
      )
      await this.retriveTableData();
    },
    populateNewRowDataObjectFromSchema() {
      for (let i = 0; i< this.tableSchema.length; i ++) {
        this.filterRowData[this.tableSchema[i].Field] = '';
        if (this.tableSchema[i].Field !== 'id')
        this.newRowData[this.tableSchema[i].Field] = '';
      }
    },
    async retriveTableData() {
      const tableSchema = await axios.get(
        `http://localhost:3000/${this.tableName}/schema`
      );
      const tableData = await axios.get(
        `http://localhost:3000/${this.tableName}`
      );
      this.tableSchema = tableSchema.data;
      this.tableData = tableData.data;
      this.populateNewRowDataObjectFromSchema();
    },
    async deleteRow() {
      await axios.delete(
        `http://localhost:3000/${this.tableName}/delete`,
        {data: {id: this.tableData[this.currentRowIndex].id}}
      );
      this.retriveTableData();
    },
    canShowRow(row) {
      const filteredColumns = [];
      for (let column in this.filterRowData) {
        if (this.filterRowData[column].trim() !== '')
          filteredColumns.push(column)
      }
      for (let i = 0; i < filteredColumns.length; i ++) {
        if (row[filteredColumns[i]].toString().indexOf(this.filterRowData[filteredColumns[i]]) === -1) {
          return false;
        }
      }
      return true;
    },
    rerender() {
      this.$forceUpdate();
    }
  }
};
</script>

<style>
</style>
