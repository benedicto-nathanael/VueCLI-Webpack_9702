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
                    :items="['None','Penting', 'Biasa', 'Tidak Penting']"
                    v-model="filters"
                    @change="priorityFilter"
                    label="Priority"
                    outlined
                    hide-details
                    class="mr-4"
                    dense></v-select>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>

            <v-data-table :headers="headers" :items="priorityFilter" :search="search">
                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small class="mr-2" @click="editItem(item)">
                        edit
                    </v-btn>
                    <v-btn small @click="deleteItem(item)">
                        delete
                    </v-btn>
                </template>
            </v-data-table>
        </v-card>

        <v-dialog v-model="dialog" persistent max-width="600px" transition="dialog-transition">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak Penting']"
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
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>

<script>
export default {
    name: "List",
    data() {
        return {
            search: null,
            dialog: false,
            temp: null,
            filters: "None",
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
                    priority: "Tidak Penting",
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
        findIndx(item) {
            return this.todos.findIndex(obj => obj.task === item.task)
        },
        save() {
                let indx = this.findIndx(this.formTodo);
                if(indx < 0) {
                    this.todos.push(this.formTodo);
                }else {
                    this.todos[indx] = this.formTodo;
                }
                this.resetForm();
                this.dialog = false;
            },
        cancel() {
                console.log(this.tempTodo);
                this.resetForm();
                this.dialog = false;
            },
        resetForm() {
                this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        editItem(item) {
            this.formTodo = item;
            this.dialog = true;
        },
        deleteItem(item) {
            let indx = this.findIndx(this.formTodo);
            let confm = window.confirm("Yakin ingin menghapus ? ");
            if(confm === true) {
                this.todos.splice(this.item[indx], 1);
            }
        }
    },
    computed: {
        priorityFilter() {
            let filter = this.filters;
            if(filter == "None") {
                return this.todos;
            }else {
                var filtered = this.todos.filter(function(x){
                    return x.priority == filter;
                })
                return filtered;
            }
        }

    }
};
</script>