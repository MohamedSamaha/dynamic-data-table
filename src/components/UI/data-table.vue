<script>
import {computed, onMounted, ref} from "vue";
import axios from "axios";

export default {
  props: {
    tableHeader: Array,
    tableBody: Array,
  },
  setup(props){
    const searchInput = ref('');
    const info = ref(null)

    // Computed property to filter the table data
    const filteredData = computed(() => {
      if (!searchInput.value) return props.tableBody; // Return full data if input is empty
      return props.tableBody.filter(row =>
          row.some(cell => typeof cell === 'string' && cell.toLowerCase().includes(searchInput.value.toLowerCase()))
      );
    });
    onMounted(() => {
      axios.get('https://api.coindesk.com/v1/bpi/currentprice.json')
          .then(response => {
            info.value = response.data // Use .value to update ref VERY VERY IMPORTANT
          })
          .catch(error => {
            console.error(error)
          })
    })

    return { info, searchInput , filteredData};
  }

}
</script>

<template>
  <div>
    <p>{{ info }}</p>
  </div>

  <div class="input-group">
    <input type="text" placeholder="Search ..." class="form-control"  v-model="searchInput">
  </div>
 <div class="table-responsive">
    <table id="add-row" class="display table table-striped table-hover" >
      <thead >
      <tr>
        <th v-for="header in tableHeader" :key="header.id">{{ header }}</th>
      </tr>
      </thead>
      <tfoot>
      <tr>
        <th v-for="footer in tableHeader" :key="footer.id" >{{ footer }}</th>

      </tr>
      </tfoot>
      <tbody>
      <tr v-for="(row, rowIndex) in filteredData" :key="rowIndex">
        <td v-for="(cell, cellIndex) in row" :key="cellIndex">{{ cell }}</td>
      </tr>

      </tbody>
    </table>
  </div>
</template>

<style scoped>

</style>