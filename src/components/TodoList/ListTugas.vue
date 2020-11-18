<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>

        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>

                <v-spacer></v-spacer>

                <v-select
                    v-model="sortTodo"
                    :items="['Penting', 'Tidak penting']"
                    label="Urutkan"
                    dense
                    outlined
                    hide-details
                    single-line
                    class="pr-5"
                >
                </v-select>

                <v-btn color="success" dark @click="dialog = true">
                    tambah
                </v-btn>
            </v-card-title>



            <v-data-table 
                :headers="headers" 
                :items="todos" 
                :search="search" 
                :expanded.sync="expanded" 
                item-key="task" 
                show-expand 
                class="elevation-1" >
                <template v-slot:[`item.priority`]="{ item }">
                        <v-chip
                           v-if="item.priority == 'Biasa'"
                           class="ma-2"
                           color="blue"
                           label
                           outlined
                        >
                        {{ item.priority }}
                        </v-chip>

                        <v-chip
                           v-if="item.priority == 'Tidak penting'"
                           class="ma-2"
                           color="green"
                           label
                           outlined
                           
                        >
                        {{ item.priority }}
                        </v-chip>

                        <v-chip
                           v-if="item.priority == 'Penting'"
                           class="ma-2"
                           color="red"
                           label
                           outlined
                           
                        >
                        {{ item.priority }}
                        </v-chip>

                </template>

                

                <template v-slot:[`item.actions`]="{ item }">
                    
                    <v-btn small text class="mr-2" @click="editItem(item)">
                        <v-icon color="blue">{{ icons.mdiPencil }}</v-icon>
                    </v-btn>

                    <v-btn small text @click="notifdeleteItem(item)">
                        <v-icon  color="red">{{ icons.mdiDelete }}</v-icon>
                    </v-btn>

                    <input type="checkbox" v-model="item.del" @change="checked(item)">
                </template>
                <template v-slot:expanded-item="{ headers, item }">
                <td :colspan="headers.length" class="text-sm-left pa-md-4 mx-lg-auto">
                    <h3>Note : </h3>
                    <p>{{ item.note }}</p>
                </td>
                </template>
            </v-data-table>
        </v-card>

        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline" 
                        v-if="adding == true" >
                           Form Todo - Add
                    </span>
                    <span class="headline" 
                        v-else>
                           Form Todo - Edit
                    </span>
                </v-card-title>

                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                            autofocus
                        ></v-text-field>

                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>

                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>

                    <v-btn v-if="adding == true" 
                        color="blue darken-1" 
                        text 
                        @click="save">
                        Save
                    </v-btn>

                    <v-btn v-else 
                        color="blue darken-1" 
                        text 
                        @click="edit(formTodo)">
                        Save
                    </v-btn>

                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Yakin ingin menghapus?</span>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                        <v-btn color="green darken-1" text @click="canceldeleteItem">
                            Tidak
                        </v-btn>

                        <v-btn color="red darken-1" text @click="deleteItem(item)">
                            Ya
                        </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-card v-if="itemChecked.length">
        <v-card-title>Delete Multiple</v-card-title>
        <v-card-text>
            <ul v-for="(todo,index) in itemChecked" :key="index">
                <li class="text-sm-left">
                    {{todo.task}}
                </li>
            </ul>
        </v-card-text>
        <v-card-actions>
            <v-btn @click="delAll" color="red" class="white--text">
            Delete All
            </v-btn>
        </v-card-actions>
    </v-card>

    </v-main>
</template>
<script>
import {
        mdiAccount,
        mdiPencil,
        mdiShareVariant,
        mdiDelete,
    } from '@mdi/js'
export default {
    name: "List",

    data() {
        return {
            icons: {
                    mdiAccount,
                    mdiPencil,
                    mdiShareVariant,
                    mdiDelete,
                },
            search: null,
            adding: true,
            edititem: null,
            dialog: false,
            dialogDelete: false,
            chip1: true,
            expanded: [],
            itemChecked:[],
            headers: [
            {
                text: "Task",
                align: "start",
                sortable: true,
                value: "task",
            },

            { text: "Priority", value: "priority" },
            { text: "Actions", value: "actions" },

            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "huffttt",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak penting",
                    note: "bersama tman2",
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                },
            ],

            formTodo: {
                task: null,
                priority: null,
                note: null,
            },
        };
    },

    methods: {
        save() {
            this.todos.push(this.formTodo);
            this.cancel();
        },

        cancel() {
            this.resetForm();
            this.dialog = false;
            this.edititem = null;
            this.adding = true;
        },

        notifdeleteItem(item) {
            this.dialogDelete = true;
            this.item = item;
        },

        deleteItem(item) {
            this.todos.splice(this.todos.indexOf(item), 1);
            this.dialogDelete = false;
        },

        canceldeleteItem() {
            this.dialogDelete = false;
        },

        editItem(item) {
            this.adding = false;
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            };
            this.dialog = true;
            this.edititem = item;
        },

        edit(formTodo) {
            this.edititem.task = formTodo.task;
            this.edititem.priority = formTodo.priority;
            this.edititem.note = formTodo.note;
            this.cancel();
        },

        checked(item){
            if(item.del){
                this.itemChecked.push(item);
            }else{
                let position = this.itemChecked.findIndex(el=>el.task === item.task);
                this.itemChecked.splice(position,1);
            }
        },
        delAll(){
            this.todos = this.todos.filter(el=>!this.itemChecked.includes(el));
            this.itemChecked = [];
        },
        
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
    },
};
</script>