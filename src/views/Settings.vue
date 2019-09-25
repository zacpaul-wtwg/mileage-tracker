<template>
  <v-container class="about">
    <v-card>
      <v-card-title>Initial Settings</v-card-title>
      <v-card-text>
        <v-text-field v-model="startingMileage" label="Starting Mileage"/>
        <v-date-picker v-model="startingDate" type="date" />
        <v-text-field v-model="leaseTerm" label="Lease Term" :suffix="termText"/>
        <v-text-field v-model="milesPer" :label="`Miles Per ${termText}`" />
        <v-switch v-model="isPerMonth" :label="termText" />
      </v-card-text>
      <v-card-actions>
        <v-btn @click="save">Save</v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>
<script>
import { LOCAL_STORAGE_KEY } from '../constants';

export default {
  data() {
    return {
      startingMileage: 0,
      startingDate: new Date().toISOString().substr(0, 10),
      leaseTermYears: 3,
      milesPerYear: 12000,
      isPerMonth: true,
    };
  },
  computed: {
    termText() {
      return this.isPerMonth ? 'Month' : 'Year';
    },
    monthMultiplier() {
      return this.isPerMonth ? 12 : 1;
    },
    milesPer: {
      get() {
        return this.milesPerYear / this.monthMultiplier;
      },
      set(value) {
        this.milesPerYear = value * this.monthMultiplier;
      },
    },
    leaseTerm: {
      get() {
        return this.leaseTermYears * this.monthMultiplier;
      },
      set(value) {
        this.leaseTermYears = value / this.monthMultiplier;
      },
    },
  },
  methods: {
    save() {
      localStorage.setItem(LOCAL_STORAGE_KEY, JSON.stringify({
        startingMileage: this.startingMileage,
        startingDate: this.startingDate,
        leaseTermYears: this.leaseTermYears,
        milesPerYear: this.milesPerYear,
      }));
      this.$router.push('/');
    },
  },
  created() {
    const maybeData = localStorage.getItem(LOCAL_STORAGE_KEY);
    if (maybeData == null) {
      console.log('returned?');
      return;
    }
    const { startingMileage, startingDate, leaseTermYears, milesPerYear } = JSON.parse(maybeData);
    
    this.startingMileage = startingMileage;
    this.startingDate = startingDate;
    this.leaseTermYears = leaseTermYears;
    this.milesPerYear = milesPerYear;
  },

};

</script>
