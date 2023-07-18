<template>
    <div 
    >
        <h4>Отчет по опросу</h4>
             <p class="report-header">{{ poll.name }} c {{poll.start}} по {{poll.end}}</p>
        <v-card>

            <v-simple-table>
                <thead>
                <tr>
                    <th></th>
                    <th>
                        Вариант ответа
                    </th>
                    <th>
                        Итого
                    </th>
                    <th>
                        Итого %
                    </th>
                </tr>
            </thead>
            <tbody>
                <template v-for="group in groups" >
                    <tr :key="group.key">
                        <td colspan="4" style="background-color: antiquewhite;">
                            <strong>{{ group.label }}</strong>
                        </td>
                    </tr>
                    <tr v-for="ans in group.answers " :key="ans">
                        <td style="width: 15%"></td>
                        <td>{{ ans.var }}</td>
                        <td>{{ ans.total }}</td>
                        <td>{{ ans.percent }} %</td>
                    </tr>
                    
                </template>
            </tbody>
        </v-simple-table>
        
        
    </v-card>
        
  </div>
  </template>
  <style scoped>
  .report-header{
    font-size: 1.2em;
    color:black;
  }
  </style>


  <script>
  import { dataService } from '@/modules/ajax.service';
  
  export default {
    name: 'PollReport',
    props: [
        
    ],
    
    components: {
      //ContractView,
      //ContractList,
    },
    data: () => ({
      repdata:[],
      poll:{},
      poll_id:1,
      groups:[],
    }),
    computed: {
    },
    mounted() {
        this.$store.commit("changeapp", "Опрос - итоги");
        this.getReport(this.poll_id);
        
    },  
    methods: {
        async getReport(poll_id){
            const res = await dataService.getData('/poll/report/'+poll_id);
            const data = res.data;
            this.poll = res.poll;
            const labels = res.labels;
            const total = res.total;
            //console.log(data);
            var repdata = [
                {
                    question: 'age',
                    answer: '25-30',
                    total: 2,
                },
                {
                    question: 'age',
                    answer: '25',
                    total: 5,
                },
                {
                    question: 'age',
                    answer: '35',
                    total: 1,
                },
                {
                    question: 'sex',
                    answer: 'M',
                    total: 2,
                },
                {
                    question: 'sex',
                    answer: 'F',
                    total: 5,
                },
            ];

            repdata = [];
            let d = {}
            for(let key in data) {
                d = {};
                d.label = labels[key];
                d.key = key;
                d.answers = [];
                
                for(let i in data[key]) {
                    let ans = {};
                    ans.total = data[key][i];
                    ans.var = i;
                    ans.percent = (data[key][i]/total*100).toFixed(1);

                    d.answers.push(ans)
                }
                repdata.push(d)
                //console.log(d);
            }
            this.groups=repdata;

        }
    }
  };
  </script>