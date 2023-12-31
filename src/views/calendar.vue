
<template>
  <insert @data-saved="getInfo" />
                  <table id="datatable-responsive" class="table table-bordered table-striped dt-responsive nowrap" cellspacing="0" width="80%" style="margin: 0 auto;">
                    <thead>
                      <tr>
                        <th>asd</th>
                        <th>CATEGORasdY</th>
                        <th>ACTIOasdN</th>
                        <th>ACTIOaasdsdN</th>
                        <th>ACTIOasdN</th>
                        <th>ACTIOasdN</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="info in info">
                        <td>{{ info.id}}</td>
                        <td>{{ info.image}}</td>
                        <td>{{ info.prod_name}}</td>
                        <td>{{ info.unit_price}}</td>
                        <td>{{ info.size_id}}</td>
                        <td>{{ info.quantity}}</td>
                        <td>
                          <button @click="editRecord" class="btn btn-success btn-sm edit"> EDIT</button>
                        </td>
                      </tr>
                    </tbody>
                  </table>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      info: [],
 
    }
  },
  created() {
    this.getInfo();
  },
  methods: {



    async getInfo() {
      try {
        const response = await axios.get('getgeData');
        this.info = response.data;
      } catch (error) {
        console.error(error);
      }
    },
  
  }
};
</script>