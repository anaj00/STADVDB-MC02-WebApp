<script setup>
import { ref } from 'vue';

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
  console.log(formData.value);
};

</script>

<template>

  <body>
    <v-card class="w-75 h-75 pa-4">

      <template v-slot:text>
        <div class="d-flex justify-space-between align-center">

          <div class="w-100 mr-6">
            <v-text-field v-model="name" label="Search" prepend-inner-icon="mdi-magnify" variant="outlined" hide-details
              single-line>
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
                  <v-text-field label="pxid" required></v-text-field>

                  <!-- apptid -->
                  <v-text-field label="apptid" required></v-text-field>

                  <!-- status -->
                  <v-text-field label="status" required></v-text-field>

                  <!-- TimeQueued -->
                  <v-date-picker label="TimeQueued" required></v-date-picker>

                  <!-- QueueDate -->
                  <v-date-picker label="QueueDate" required></v-date-picker>

                  <!-- StartTime -->
                  <v-date-picker label="StartTime" required></v-date-picker>

                  <!-- EndTime -->
                  <v-date-picker label="EndTime"></v-date-picker>

                  <!-- type -->
                  <v-text-field label="type" required></v-text-field>

                  <!-- isVirtual -->
                  <v-checkbox label="isVirtual"></v-checkbox>

                  <!-- hospitalname -->
                  <v-text-field label="hospitalname" required></v-text-field>

                  <!-- IsHospital -->
                  <v-checkbox label="IsHospital"></v-checkbox>

                  <!-- City -->
                  <v-text-field label="City" required></v-text-field>

                  <!-- Province -->
                  <v-text-field label="Province" required></v-text-field>

                  <!-- RegionName -->
                  <v-text-field label="RegionName" required></v-text-field>

                  <!-- mainspecialty -->
                  <v-text-field label="mainspecialty" required></v-text-field>

                  <!-- age_x -->
                  <v-text-field label="age_x" type="number" required></v-text-field>

                  <!-- age_y -->
                  <v-text-field label="age_y" type="number" required></v-text-field>

                  <!-- gender -->
                  <v-text-field label="gender" required></v-text-field>

                  <!-- island -->
                  <v-select label="island" :items="['Luzon', 'Visayas', 'Mindanao']" required></v-select>

                  <!-- Submit button -->
                  <v-btn type="submit" color="primary">Submit</v-btn>

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

      <v-data-table-server v-model:items-per-page="itemsPerPage" :headers="headers" :items="serverItems"
        :items-length="totalItems" :loading="loading" :search="search" item-value="name" @update:options="loadItems">



      </v-data-table-server>

    </v-card>

  </body>
</template>
