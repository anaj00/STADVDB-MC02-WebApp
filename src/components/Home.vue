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
  const url = "http://localhost:3001/records";
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
// [END] For adding a record


// For editing a record
// For editing a record
const editdialog = ref(false);

// Store the ID of the edited record
let editedRecordId = null;

const editItem = async (recordId) => {
  try {
    const url = `http://localhost:3001/records/${recordId}`;
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error('Failed to fetch record');
    }
    const record = await response.json();

    // Prefill formData with record data
    formData.value = { ...record };

    // Store the ID of the edited record
    editedRecordId = recordId;

    // Open the edit dialog
    editdialog.value = true;
  } catch (error) {
    console.error('Error fetching record:', error);
  }
};

// Save edited data
const saveEditedData = async () => {
  try {
    const url = `http://localhost:3001/records/${editedRecordId}`;
    const options = {
      method: 'PUT', 
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(formData.value)
    };

    const response = await fetch(url, options);
    if (!response.ok) {
      throw new Error('Failed to save edited data');
    }

    console.log('Record updated successfully');
    window.location.reload();

    // Close the edit dialog after saving
    editdialog.value = false;
  } catch (error) {
    console.error('Error saving edited data:', error);
  }
};

// [END] For editing a record


// For deleting a record
const deleteItem = (recordId) => {
  if (!recordId) {
    console.error('Invalid record ID');
    return;
  }

  const url = `http://localhost:3001/records/${recordId}`;
  const options = {
    method: 'DELETE',
  };

  fetch(url, options)
    .then(response => {
      if (!response.ok) {
        throw new Error('Failed to delete record');
      }
      console.log('Record deleted successfully');
      window.location.reload();
    })
    .catch(error => {
      console.error('Error deleting record:', error);
    });
};
// [END] For deleting a record


// For generate report
const generateReport = async () => {
  try {
    const response = await fetch('http://localhost:3001/generatetxt');
    if (!response.ok) {
      throw new Error('Failed to generate TXT');
    }
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    
    // Set href attribute of the anchor element
    const txtDownloadLink = document.createElement('a');
    txtDownloadLink.href = url;
    txtDownloadLink.download = 'yearly_analysis.txt';
    document.body.appendChild(txtDownloadLink);
    txtDownloadLink.click();
    document.body.removeChild(txtDownloadLink);
  } catch (error) {
    console.error('Error generating report:', error);
  }
};
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

          <v-btn class="text-white font-weight-bold text-capitalize mr-3" color="var(--vt-c-primary)" height="50" @click="generateReport">
            Generate Report
          </v-btn>

          <v-dialog v-model="dialog" max-width="600">
            <template v-slot:activator="{ props: activatorProps }">
              <v-btn class="text-white font-weight-bold text-capitalize" color="var(--vt-c-primary)" height="50"
                v-bind="activatorProps">
                Add record
              </v-btn>
            </template>

            <v-card 
              title="Add record"
              >

              <div class="ma-4">
                <v-form>
                  <!-- pxid -->
                  <v-text-field v-model="formData.pxid" label="Patient ID" required></v-text-field>

                  <!-- apptid -->
                  <v-text-field v-model="formData.apptid" label="Appointment ID" required></v-text-field>

                  <!-- status -->
                  <v-select v-model="formData.status" label="Status" :items="['Completed', 'Queued', 'NoShow', 'Serving', 'Cancel', 'Skip', 'Admitted']" required></v-select>

                  <!-- TimeQueued -->
                  <v-text-field v-model="formData.TimeQueued" label="Time Queued" type='time' required></v-text-field>

                  <!-- QueueDate -->
                  <v-text-field v-model="formData.QueueDate" label="Queue Date" type='date' required></v-text-field>

                  <!-- StartTime -->
                  <v-text-field v-model="formData.StartTime" label="Start Time" type='time' required></v-text-field>

                  <!-- EndTime -->
                  <v-text-field v-model="formData.EndTime" type='time' label="EndTime"></v-text-field>

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
                  <v-select 
                    v-model="formData.RegionName" 
                    label="RegionName" 
                    :items="[ 'National Capital Region (NCR) - Metro Manila',
                              'Ilocos Region (Region I)',
                              'Cagayan Valley (Region II)',
                              'Central Luzon (Region III)',
                              'CALABARZON (Region IV-A)',
                              'MIMAROPA (Region IV-B)',
                              'Bicol Region (Region V)',
                              'Western Visayas (Region VI)',
                              'Central Visayas (Region VII)',
                              'Eastern Visayas (Region VIII)',
                              'Zamboanga Peninsula (Region IX)',
                              'Northern Mindanao (Region X)',
                              'Davao Region (Region XI)',
                              'SOCCSKSARGEN (Region XII)',
                              'Caraga (Region XIII)',
                              'Bangsamoro Autonomous Region in Muslim Mindanao (BARMM)']" 
                    required></v-select>

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
            size="small"
            @click="editItem(item.apptid)"
          >
            mdi-pencil
          </v-icon>
          
          <v-icon
            size="small"
            @click="deleteItem(item.apptid)"
          >
            mdi-delete
          </v-icon>

        </template>
      </v-data-table-server>
    </v-card>

  
   <!-- Edit Dialog --> 
   <v-dialog v-model="editdialog" max-width="600">
      <v-card title="Edit record">
      <div class="ma-4">
        <v-form>
          <!-- pxid -->
          <v-text-field v-model="formData.pxid" label="Patient ID" required></v-text-field>

          <!-- apptid -->
          <v-text-field v-model="formData.apptid" label="Appointment ID" required></v-text-field>

          <!-- status -->
          <v-select v-model="formData.status" label="Status" :items="['Completed', 'Queued', 'NoShow', 'Serving', 'Cancel', 'Skip', 'Admitted']" required></v-select>

          <!-- TimeQueued -->
          <v-text-field v-model="formData.TimeQueued" label="Time Queued" type='time' required></v-text-field>

          <!-- QueueDate -->
          <v-text-field v-model="formData.QueueDate" label="Queue Date" type='date' required></v-text-field>

          <!-- StartTime -->
          <v-text-field v-model="formData.StartTime" label="Start Time" type='time' required></v-text-field>

          <!-- EndTime -->
          <v-text-field v-model="formData.EndTime" type='time' label="EndTime"></v-text-field>

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
          <v-select 
            v-model="formData.RegionName" 
            label="RegionName" 
            :items="[ 'National Capital Region (NCR) - Metro Manila',
                      'Ilocos Region (Region I)',
                      'Cagayan Valley (Region II)',
                      'Central Luzon (Region III)',
                      'CALABARZON (Region IV-A)',
                      'MIMAROPA (Region IV-B)',
                      'Bicol Region (Region V)',
                      'Western Visayas (Region VI)',
                      'Central Visayas (Region VII)',
                      'Eastern Visayas (Region VIII)',
                      'Zamboanga Peninsula (Region IX)',
                      'Northern Mindanao (Region X)',
                      'Davao Region (Region XI)',
                      'SOCCSKSARGEN (Region XII)',
                      'Caraga (Region XIII)',
                      'Bangsamoro Autonomous Region in Muslim Mindanao (BARMM)']" 
            required></v-select>

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

        </v-form>
      </div>


      <v-card-actions>
        <v-spacer></v-spacer>

        <v-btn text="Close" variant="plain" @click="editdialog = false"></v-btn>

        <v-btn color="primary" text="Update" variant="tonal" @click="saveEditedData"></v-btn>
      </v-card-actions>
      </v-card>
    </v-dialog>
  </body>
</template>

<script>

// For getting all records
const getRecords = {
  async fetch({ page, itemsPerPage, search }) {
    const url = `http://localhost:3001/records?page=${page}&itemsPerPage=${itemsPerPage}&search=${search}`;

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
