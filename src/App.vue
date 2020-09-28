<template>
  <div id="app">
    <ul>
      <li v-for="d in data" :key="d">
        {{ d }}
      </li>
    </ul>
    <button @click="getTableKeys"> Get Keys </button>
    <button @click="download_csv(0)"> Students Csv</button>
    <button @click="download_csv(1)"> Tutorials Csv</button>


  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      data: {},
      tableKeys:[]
    }
  },
  created(){
    axios.get('src/data.json')
        .then((response) => { this.data= response.data; })
        .catch((error) => {
            console.log(error);
        });
  },
  methods: {
      getTableKeys(){
        Object.keys(this.data).forEach((key) => {
          this.tableKeys.push(key);
          console.log(key);  
        });
      },
      download_csv(index) {
        const data =this.data[this.tableKeys[index]];
        var csv='';
        //Add Key with ',' in csv 
        Object.keys(data[0]).forEach((key) => {
          csv += key+',';
          console.log(key);  
         
        });
        csv = csv.substring(0, csv.length - 1); 
        csv += '\n';
         
        //Add Value with ',' in csv 
        data.forEach(function(d) {
          var row =[];
          Object.keys(d).forEach((key) => {
            row.push(d[key]);
            });
            csv += row.join(',');
            csv += "\n";
        });
         
        //Create and Download CSV file
        var element = document.createElement('a');
        element.href = 'data:text/csv;charset=utf-8,' + encodeURI(csv);
        element.target = '_blank';
        element.download = this.tableKeys[index];
        element.click();
      }
  }
};
</script>