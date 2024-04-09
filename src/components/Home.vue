<script setup>
import { ref } from 'vue';

// For adding a record
const dialog = ref(false);

const formData = ref({
  pxid: '',
  apptid: '',
  status: '',
  TimeQueued: '',
  QueueDate: '',
  StartTime: '',
  EndTime: '',
  type: '',
  isVirtual: '',
  hospitalname: '',
  IsHospital: '',
  City: '',
  Province: '',
  RegionName: '',
  mainspecialty: '',
  age_x: '',
  age_y: '',
});

const submitForm = () => {
  const url = "http://localhost:5000/records";
  const options = {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(formData.value)
  }

  fetch(url, options)
    .then(response => {
      if(!response.ok){
        throw new Error('Internal server error');
      }
      return(response.json());
    })
    .then(data =>{
      console.log('Form submitted successfully:', data);
      // reset form values
      formData.value = {
        pxid: '',
        apptid: '',
        status: '',
        TimeQueued: '',
        QueueDate: '',
        StartTime: '',
        EndTime: '',
        type: '',
        isVirtual: '',
        hospitalname: '',
        IsHospital: '',
        City: '',
        Province: '',
        RegionName: '',
        mainspecialty: '',
        age_x: '',
        age_y: ''
      };
    })
    .catch(error =>{
      console.log('Error submitting form:', error);
    });
};
// [END] For editing a record

</script>

<template>
  <body>
    <v-card class="main-div pa-4">
      <template v-slot:text>
        <div class="d-flex justify-space-between align-center">

          <div class="w-100 mr-6 ">
            <v-text-field v-model="search" label="Search" prepend-inner-icon="mdi-magnify" variant="outlined" hide-details single-line>
            </v-text-field>
          </div>

          <v-btn class="text-white font-weight-bold text-capitalize mr-3" color="var(--vt-c-primary)" height="50">
            Generate Report
          </v-btn>

          <v-dialog v-model="dialog" max-width="600">
            <template v-slot:activator="{ props: activatorProps }">
              <v-btn class="text-white font-weight-bold text-capitalize" color="var(--vt-c-primary)" height="50"
                v-bind="activatorProps">
                Add record
              </v-btn>
            </template>

            <v-card title="Add record">

              <div class="ma-4">
                <v-form>
                  <!-- pxid -->
                  <v-text-field v-model="formData.pxid" label="pxid" required></v-text-field>

                  <!-- apptid -->
                  <v-text-field v-model="formData.apptid" label="apptid" required></v-text-field>

                  <!-- status -->
                  <v-text-field v-model="formData.status" label="status" required></v-text-field>

                  <!-- TimeQueued -->
                  <v-text-field v-model="formData.TimeQueued" label="TimeQueued" required></v-text-field>

                  <!-- QueueDate -->
                  <v-text-field v-model="formData.QueueDate" label="QueueDate" required></v-text-field>

                  <!-- StartTime -->
                  <v-text-field v-model="formData.StartTime" label="StartTime" required></v-text-field>

                  <!-- EndTime -->
                  <v-text-field v-model="formData.EndTime" label="EndTime"></v-text-field>

                  <!-- type -->
                  <v-text-field v-model="formData.type" label="type" required></v-text-field>

                  <!-- isVirtual -->
                  <v-checkbox v-model="formData.isVirtual" label="isVirtual"></v-checkbox>

                  <!-- hospitalname -->
                  <v-text-field v-model="formData.hospitalname" label="hospitalname" required></v-text-field>

                  <!-- IsHospital -->
                  <v-checkbox v-model="formData.IsHospital" label="IsHospital"></v-checkbox>

                  <!-- City -->
                  <v-text-field v-model="formData.City" label="City" required></v-text-field>

                  <!-- Province -->
                  <v-text-field v-model="formData.Province" label="Province" required></v-text-field>

                  <!-- RegionName -->
                  <v-text-field v-model="formData.RegionName" label="RegionName" required></v-text-field>

                  <!-- mainspecialty -->
                  <v-text-field v-model="formData.mainspecialty" label="mainspecialty" required></v-text-field>

                  <!-- age_x -->
                  <v-text-field v-model="formData.age_x" label="age_x" type="number" required></v-text-field>

                  <!-- age_y -->
                  <v-text-field v-model="formData.age_y" label="age_y" type="number" required></v-text-field>

                  <!-- gender -->
                  <v-text-field v-model="formData.gender" label="gender" required></v-text-field>

                  <!-- island -->
                  <v-select v-model="formData.island" label="island" :items="['Luzon', 'Visayas', 'Mindanao']" required></v-select>

                  <!-- Submit button -->
                  <v-btn type="submit" color="primary" @click.prevent="submitForm">Submit</v-btn>

                </v-form>
              </div>


              <v-card-actions>
                <v-spacer></v-spacer>

                <v-btn text="Close" variant="plain" @click="dialog = false"></v-btn>

                <v-btn color="primary" text="Save" variant="tonal" @click="dialog = false"></v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>

        </div>
      </template>

      <v-data-table-server
        class="mt-4"
        fixed-header
        hover
        density="compact"
        :height="700"
        v-model:items-per-page="itemsPerPage"
        :headers="headers"
        :items="serverItems"
        :items-length="totalItems"
        :loading="loading"
        :search="search"
        item-value="name"
        @update:options="loadItems"
      >

        <template v-slot:item.actions="{ item }">
          <v-icon
            class="me-2"
            size="small"
            @click="editItem(item)"
          > 
            mdi-pencil
          </v-icon>
          <v-icon
            size="small"
            @click="deleteItem(item)"
          >
            mdi-delete
          </v-icon>
        </template>
      </v-data-table-server>

    </v-card>

  </body>
</template>

<script>

// For getting all records
const getRecords = {
  async fetch({ page, itemsPerPage, search }) {
    const url = `http://localhost:5000/records?page=${page}&itemsPerPage=${itemsPerPage}&search=${search}`;

    try {
      const response = await fetch(url);
      const data = await response.json();

      console.log('Fetched data:', data);
      return data;
    } catch (error) {
      console.error('Error fetching data:', error);
      throw new Error('Failed to fetch data');
    }
  },
};

export default {
  data: () => ({
    itemsPerPage: 7,
    headers: [
      { title: 'Actions', key: 'actions', sortable: false },
      { title: 'PXID', key: 'pxid' },
      { title: 'Appt ID', key: 'apptid' },
      { title: 'Status', key: 'status' },
      { title: 'Time Queued', key: 'TimeQueued' },
      { title: 'Queue Date', key: 'QueueDate' },
      { title: 'Start Time', key: 'StartTime' },
      { title: 'End Time', key: 'EndTime' },
      { title: 'Type', key: 'type' },
      { title: 'Virtual', key: 'isVirtual' },
      { title: 'Hospital Name', key: 'hospitalname' },
      { title: 'Is Hospital', key: 'IsHospital' },
      { title: 'City', key: 'City' },
      { title: 'Province', key: 'Province' },
      { title: 'Region Name', key: 'RegionName' },
      { title: 'Main Specialty', key: 'mainspecialty' },
      { title: 'Age X', key: 'age_x' },
      { title: 'Age Y', key: 'age_y' },
      { title: 'Gender', key: 'gender' },
      { title: 'Islands', key: 'islands' },
    ],
    search: '',
    serverItems: [],
    loading: true,
    totalItems: 0,
    search: '',
  }),
  
  methods: {
    loadItems({ page, itemsPerPage }) {
      this.loading = true;
      getRecords.fetch({ page, itemsPerPage, search: this.search }).then(({ items, total }) => {
        this.serverItems = items;
        this.totalItems = total;
        this.loading = false;
      });
    },
  },
}
// [END] For getting all records

</script>

<style scoped>
  .main-div {
    width: 90%; /* Custom width */
    height: 90vh; /* Custom height */
  }
</style>
