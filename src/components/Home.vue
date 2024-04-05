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

          <v-btn class="text-white font-weight-bold text-capitalize" color="var(--vt-c-primary)" height="50">
            Add record
          </v-btn>

        </div>
      </template>

      <v-data-table-server v-model:items-per-page="itemsPerPage" :headers="headers" :items="serverItems"
        :items-length="totalItems" :loading="loading" :search="search" item-value="name" @update:options="loadItems">



      </v-data-table-server>

    </v-card>

  </body>
</template>

<script setup>
  //
</script>
