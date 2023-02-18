<template>
  <div class="wrapper">
    <v-sheet class="mx-auto">
      <v-form @submit.prevent>
        <v-text-field
          class="inputV"
          v-model="firstUrl"
          label="Input URL"
        ></v-text-field>
        <v-text-field
          class="inputV"
          v-model="pageCount"
          label="How many pages?"
        ></v-text-field>
        <v-btn @click="getData" type="submit" block class="mt-2">Go Viktor!</v-btn>
      </v-form>
      <div class="out-block">{{ output }}</div>
      <v-progress-linear
        color="deep-purple-accent-4"
        indeterminate
        rounded
        height="6"
        :class="showLoader.join(' ')"
      ></v-progress-linear>
    </v-sheet>
  </div>
</template>

<script>
import axios from 'axios';
import xlsx from 'xlsx/dist/xlsx.full.min';

export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data() {
    return {
      firstUrl: null,
      pageCount: null,
      output: '',
      showLoader: ['hide'],
    };
  },

  methods: {
    async getData() {
      this.showLoader.push('show');
      this.output = '';
      if (!this.firstUrl) {
        // eslint-disable-next-line no-alert
        alert('Input url!');
        this.showLoader = ['hide'];
        return;
      }

      try {
        const response = await axios.post(
          'https://vikpar.herokuapp.com/api/vikpar',
          {
            url: this.firstUrl,
            pageCount: this.pageCount || 1,
          },
        );

        const { data } = response;

        // eslint-disable-next-line no-unused-expressions
        data
          ? (this.output = 'Successfully')
          : (this.output = 'Something wrong...');

        if (data) {
          const XLSX = xlsx;
          const workbook = XLSX.utils.book_new();
          const worksheet = XLSX.utils.json_to_sheet(data);
          XLSX.utils.book_append_sheet(workbook, worksheet, 'data');
          XLSX.writeFile(workbook, 'data.xlsx');
        }
        this.showLoader = ['hide'];
        return;
      } catch (error) {
        this.output = 'Something wrong...';
        this.showLoader = ['hide'];
      }
    },
  },
};
</script>

<style scoped>
.wrapper {
width: 100%;
}
.out-block {
  height: 40px;
  margin: 10px;
  text-align: center;
}

.hide {
 visibility: hidden;
}

.show {
  visibility: visible;
}
</style>
