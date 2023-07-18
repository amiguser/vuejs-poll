<template>
    <div >
        <template v-if="field.type == 'text'">
            <v-text-field 
                :label=field.label
                v-model=field.value
                :rules = field.rules
                :required = field.required
                
            ></v-text-field>
        </template>
        <template v-if="field.type == 'checkbox'">
            <div>
                {{field.label}}
            </div> 
            <v-checkbox
            v-for="option in field.options"
                v-model=field.value
                :required = field.required
                :multiple="field.multiple"
                :rules = field.rules
                :key="option.key"
                :label="option.label"
                :value="option.label"
            ></v-checkbox>
            
        </template>        
        <template v-if="field.type == 'radio'">
            <v-radio-group 
                :label = field.label
                :required = field.required
                :rules = field.rules
                :row = "field.row"
                :multiple="field.multiple"
                v-model="field.value"
            >
            <v-radio 
                v-for="option in field.options"
                :key="option.key"
                :label="option.label"
                :value="option.label"
            ></v-radio>
            
            </v-radio-group>
        </template>
        <template v-if="field.type == 'textarea'">
            <v-textarea
                :rules = field.rules
                :label=field.label
                v-model=field.value
            ></v-textarea>
        </template>
        <template v-if="field.type == 'integer'">
            <v-text-field 
                :label=field.label
                v-model=field.value
            ></v-text-field>
        </template>

        <template v-if="field.type == 'file'">
            <v-card
                max-width="600"
                v-if="field.value && field.value != 0"
            >
                <v-list
                >
                <v-list-item
                    v-for="(file, index) in field.value"
                    :key="file.index"
                >
                    <v-list-item-avatar>
                        <v-icon>
                            mdi-file
                        </v-icon>
                    </v-list-item-avatar>

                    <v-list-item-content>
                        <v-list-item-title v-if="file.hash" v-text="file.name +'.'+ file.type"></v-list-item-title>
                        <v-list-item-title v-if="!file.hash" v-text="file.name"></v-list-item-title>
                    </v-list-item-content>

                    <v-list-item-action>
                        <v-btn 
                            @click="removeFile(index)"
                            icon
                        >
                            <v-icon color="#ef5350">mdi-delete</v-icon>
                        </v-btn>
                    </v-list-item-action>
                </v-list-item>
                </v-list>
            </v-card>
            
        </template>

        <v-combobox 
            v-if="field.type=='select'"
            :label=field.label
            v-model=field.value
            :items=field.dataset 
            :multiple = field.multiple
            :rules = field.rules
            
        >
        </v-combobox>

        <template 
            v-if="field.type=='datetime' "
        >
            <v-menu
                v-model="fromDateMenu"
                :close-on-content-click="false"
                :nudge-right="40"
                
                transition="scale-transition"
                offset-y
                full-width
                max-width="290px"
                min-width="290px"
            >
                <template v-slot:activator="{ on }">
                    <v-text-field
                        :label=field.label
                        v-on="on"
                        readonly
                        prepend-icon="mdi-calendar"
                        :value=fromDateDisp
                    ></v-text-field>
                </template>
                <v-date-picker
                    :label=field.label
                    locale="ru-ru"
                    v-model="field.value"
                    no-title
                    @input="fromDateMenu = false"
                ></v-date-picker>
            </v-menu>
        </template>
        <hr>
    </div>
</template>

<script >
export default ({
    props: [
        'field',
    ],
    data() {
        //submit: ''
        return {
            fromDateMenu: false,
            fvalue:'',
            DatePickerMenu: false,
            isSelecting: false,

            
        }
    },
    mounted() {
        //console.log("field:" + this.field.multiple);
    },
    watch: {
        field() {
            this.fvalue = this.field.value
        }
    },
    computed: {
        //fromDateDisp() {return this.field.value;}
    },
    methods: {
        
            
        testprobe(){
            alert(this.type);
        },
    },
})
</script>
<style>
    .fattach {
        margin-top: 10px;
    }
</style>