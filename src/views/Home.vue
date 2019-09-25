<template>
 <v-container class="about">
    <v-card>
      <v-card-text>Today's Mileage Limit</v-card-text>
      <v-card-title>{{estimatedMileage}} Miles</v-card-title>
      <v-card-text>

            <span v-for="item in dataList" :key="item.title">{{item.title}}: {{item.value}}<br></span>


      </v-card-text>
      <v-card-actions>
        <v-btn fab dark small color="primary" @click="goToSettings">
          <v-icon>mdi-settings</v-icon>
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
import { LOCAL_STORAGE_KEY } from '../constants';
import differenceInCalendarDays from 'date-fns/differenceInCalendarDays';

export default {
  data() {
    return {
      startingMileage: null,
      startingDate: null,
      leaseTermYears: null,
      milesPerYear: null,
    };
  },
  computed: {
    dataList() {
      return [
        {
          title: 'Starting Miles',
          value: this.startingMileage,
        },
        {
          title: 'Starting Date',
          value: this.startingDate,
        }
      ];
    },
    estimatedMileage(){

      const today = new Date();

      const date = new Date(today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate());

      const startingDateMilli = new Date(this.startingDate);
      
      const daysSinceStart = date.getTime() / 86400000 - startingDateMilli/86400000;

      const daysInLease = this.leaseTermYears * 365;

      const totalMiles = (parseFloat(this.leaseTermYears) * parseFloat(this.milesPerYear));

      const milesPerDay = parseFloat(totalMiles) / parseFloat(daysInLease);

      return Math.floor(parseFloat(milesPerDay) * parseFloat(daysSinceStart) + parseFloat(this.startingMileage));

    },
  },
  methods: {
    goToSettings() {
      this.$router.push('/settings');    
    },
  },
  created() {
    const maybeData = localStorage.getItem(LOCAL_STORAGE_KEY);
    if (maybeData == null) {
      this.router.push('/settings');
    }
    const { startingMileage, startingDate, leaseTermYears, milesPerYear } = JSON.parse(maybeData);
    
    this.startingMileage = startingMileage;
    this.startingDate = startingDate;
    this.leaseTermYears = leaseTermYears;
    this.milesPerYear = milesPerYear;
  },
};
</script>
