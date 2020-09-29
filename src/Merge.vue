<template>
  <div id="app">
    <button @click="mergeTable()"> Merge String Csv </button>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      data: {},
      tableKeys:[],
      stringTable:[]
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
        });
      },
      create_csv(data) {
        var csv='';
        //Add Key with ',' in csv 
        Object.keys(data[0]).forEach((key) => {
          csv += key+','; 
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
        return csv;
      },
      download_csv(csv){
        //Create and Download CSV file
        var element = document.createElement('a');
        element.href = 'data:text/csv;charset=utf-8,' + encodeURI(csv);
        element.target = '_blank';  
        element.download = "String";
        element.click();
      },
     getStudents(){
       // get student name, surname, number
        var ds = this.data[this.tableKeys[0]];
        ds.forEach( value => {
            this.stringTable.push({
            "Name Surname": value.firstName + ' ' +value.lastName,
            "Number": value.number
          });
        });
      },
      mergeTable(){
        this.getTableKeys();
        this.getStudents();
        var t=this.create_csv(this.stringTable); // create csv string for student information
        var grade =this.data[this.tableKeys[2]]; // get grades object
        var x=1,count=0;
        var csv=t.split('\n')[0];
        grade.forEach( value => {
          if(count===0) //fill keys in csv for grades and total
          {
           Array(10).fill().map((_, i) => csv=csv+','+i);
           csv+=','+'Total';
           csv+='\n';
           count++;
          }
          if(value.scores.tutorialId == 1) // fill value in csv
          {
              csv=csv+this.getScore(this.stringTable,value,t.split('\n')[x]);
          }
          x+=1;
        });
        this.download_csv(csv);
      },

      getScore(table,value,t) 
      {
        // get all scores and total 
        var k= value.userId-1;
        var i=0;
        var total=0;
        var row =[];
        Object.keys(value.scores.pages).forEach((key) => {
          row.push(value.scores.pages[key]);
          total=total+value.scores.pages[key];
          i++;
          });
          for(var j=i; j<10; j++){
            table[k][j]=0;
            row.push(0);
          } 
          row.push(total);
          t = t+','+row.join(',');
          t += '\n';
          return t;  
      }

  }
};
</script>