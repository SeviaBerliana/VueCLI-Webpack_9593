<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List </h3>
        
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
                <v-btn color="success" dark @click="dialog = true">Tambah</v-btn>
            </v-card-title>

            <v-data-table 
                :headers="headers" 
                :items="todos" 
                :search="search"
                :expanded.sync="expanded"
                item-key="note"
                show-expand>
                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-chip v-if="item.priority=='Penting'" color="red" label outlined>{{item.priority}}</v-chip>
                        <v-chip v-else-if="item.priority=='Biasa'" color="blue" label outlined>{{item.priority}}</v-chip>
                        <v-chip v-else-if="item.priority=='Tidak penting'" color="green" label outlined>{{item.priority}}</v-chip>
                    </td> 
                </template>
                <template v-slot:[`item.actions`]="{ item }" >
                    <v-btn small class="mr-2" @click="editItem(item)">
                        edit
                    </v-btn>
                    <v-btn small @click="deleteItem(item)">
                        delete
                    </v-btn>
                </template>
                <template v-slot:[`expanded-item`]="{ item }">
                    <h5><strong>Note:</strong></h5><br>
                    <td :colspan="headers.length">
                        {{ item.note }}
                    </td>
                </template>
            </v-data-table>
        </v-card>

        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline" v-if="adding==true">ADD Form Todo </span>
                    <span class="headline" v-else>EDIT Form Todo </span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>

                        <v-select
                            v-model=" formTodo.priority"
                            :items="['Penting',  'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>

                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required>
                        </v-textarea>
                    </v-container>
                </v-card-text>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn v-if="adding==true" color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                    <v-btn v-else color="blue darken-1" text @click="edit(formTodo)">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" persistent max-width="500px">
            <v-card>
                <v-card-title class="headline">Yakin ingin menghapus?</v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="green darken-1" text @click="cancel">Tidak</v-btn>
                    <v-btn color="red darken-1" text @click="deleteItemConfirm">Ya</v-btn>
                    <v-spacer></v-spacer>
                </v-card-actions>
            </v-card>
        </v-dialog>

    </v-main>
</template>

<script>
    export default {
        name: "List",
        data(){
            return{
                adding: true,
                edititem: null,
                search: null,
                dialog: false,
                dialogDelete: false,
                headers: [
                    {
                        text: "Task",
                        align: "start",
                        sortable: true,
                        value: "task",
                    },
                    { 
                        text: "Priority",
                        value: "priority" 
                    },
                    { 
                        text: "Actions", 
                        value: "actions"
                    },
                ],
                todos:[
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
                formTodo :{
                    task : null ,
                    priority : null ,
                    note : null ,
                },
            };
        },

        methods:{
            save(){
                this.todos.push (this.formTodo);
                this.resetForm();
                this.dialog=false;
            },
            cancel(){
                this.resetForm();
                this.dialog=false;
                this.adding=true;
                this.edititem=null;
                this.dialogDelete=false;
            },
            resetForm(){
                this.formTodo={
                    task : null ,
                    priority : null ,
                    note : null ,
                };
            },
            editItem(item) {
                this.adding=false;
                this.formTodo = {
                    task: item.task,
                    priority: item.priority,
                    note: item.note,
                };
                this.dialog = true;
                this.edititem = item;
            },
            edit(formTodo){
                this.edititem.task = formTodo.task;
                this.edititem.priority = formTodo.priority;
                this.edititem.note = formTodo.note;
                this.cancel();
            },
            deleteItem (item) {
                this.editedItem = item;
                this.dialogDelete = true;
            },

            deleteItemConfirm () {
                this.todos.splice(this.todos.indexOf(this.editedItem),1);
                this.dialogDelete = false;
            },
        },
    };
</script>