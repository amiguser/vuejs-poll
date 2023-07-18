<template>
    <b-container>
        <h1>{{ poll.name }}</h1>
        <h2 v-if="done">Вы уже участвовали в этом опросе. Спасибо!</h2>
        <v-card 
            v-if="!done"
            elevation="4"
        >
            
            <v-card-subtitle class="poll-description">{{ poll.description }}</v-card-subtitle>
            <v-card-text>
            <v-form  
                ref="form"
                @submit.prevent="submit"
            >
                <template  v-for="field in poll.questions">

                    <FieldInput  v-bind:key="field.name" :field="field" v-if="poll.questions[field.cond.index].value==field.cond.value" />

                </template>
                <v-btn
                  class="ma-2"
                  color="primary"
                  type="submit"
                >
                  Отправить
                </v-btn>
            </v-form>
        </v-card-text>
        <v-card-actions>
      <v-spacer></v-spacer>
        </v-card-actions>
        <v-snackbar :color="notificationColor" v-model="notification">
      {{ notificationText }}

      <template v-slot:action="{ attrs }">
        <v-btn color="blue" text v-bind="attrs" @click="notification = false">
          Закрыть
        </v-btn>
      </template>
    </v-snackbar>
        </v-card>
    </b-container>
</template>
<style >
    .poll-description {
        white-space: pre-wrap;
        word-break: keep-all;
        font-size: 1.3em;
    }
</style>
<script>
import FieldInput from "./components/FieldInput"
import { dataService } from '@/modules/ajax.service';

export default {
    name: 'Poll',
    data() { 
        return {
            reqRules: [v => !!v || 'Необходимо заполнить',],
            age:'',
            ssex:'',
            done: false,
            notification:false,
      notificationText:'',
      notificationColor:'',

            poll:{},
    }},
    components: {
        'FieldInput': FieldInput,
    },
    setup() {
        
    },
    watch: {
        notification( value ) {
            if ( value == false ) {
                if (this.notificationColor == "success") {
                    this.$router.push("/");
                }
            }
        }
    },
    mounted() {
        this.loadPoll(1);
        //console.log(JSON.stringify(this.poll));
        
        //console.log("guid="+this.depGuid);
    },
    computed: {
    },
    methods: {
        async loadPoll(poll_id){
            var poll = await dataService.getData('/poll/'+poll_id);
            
            if ( parseInt(poll.ip) > 0 ){
                this.done = true
                
            } else {
                this.poll = JSON.parse(poll.questions);
                this.poll.questions['last'] = {};
                this.poll.questions['last'].value = 0;
                for(let i in this.poll.questions) {
                    if ( this.poll.questions[i].required == true )
                    {
                        this.poll.questions[i].rules = this.reqRules;
                    } else {
                        this.poll.questions[i].rules = [];
                    }
                    if ( this.poll.questions[i].cond == undefined ) {
                        this.poll.questions[i].cond = {}
                        this.poll.questions[i].cond.index = 'last';
                        this.poll.questions[i].cond.value = 0;
                    }
                }
            }
            //console.log(this.poll);
            // this.poll.id = poll.id;
            // this.poll.name = poll.name;
            // this.poll.questions = JSON.parse(poll.questions);
        },
        async submit() {
            let f = this.$refs.form.validate();
            
            if ( f ) {
                var post ={};
                post.poll_id = this.poll.poll_id;
                post.answers = []
                for(let i in this.poll.questions){
                    var q = {};
                    q.key = this.poll.questions[i].name;
                    q.value = this.poll.questions[i].value;
                    post.answers.push(q);
                }
                //validated
                /*
                const requestOptions = {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(post)
                };
                */
               //console.log(post);
                //await fetch('http://127.0.0.1:8000/api/poll/store', requestOptions);
               const result =  await dataService.storeAnonData('/poll/store', post);
                //fetch("http://127.0.0.1:8000/api/poll/store", {title: "dsdsd", name:"jfjkdsfjkdjf"}).then()
                if (result.status < 300) {
          this.notification = true;
          this.notificationColor = "success";
          this.notificationText = "Спасибо! Ваше мнение учтено!";
          //this.$refs.form.reset();
          
          //this.$router.push("/");
          //this.getBtripData();
        } else {
          this.notification = true;
          this.notificationColor = "danger";
          this.notificationText =
            result.statusText + " (" + result.status + ") Ошибка сервиса. Попробуйте повторить позднее.";
        }               
            }
        },
    },
}
</script>
