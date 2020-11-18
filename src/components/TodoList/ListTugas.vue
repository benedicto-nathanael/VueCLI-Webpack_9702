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

            <v-data-table 
                :headers="headers" 
                :items="priorityFilter" 
                :search="search"
                :expanded.sync="expanded"
                :single-select="singleSelect"
                item-key="note"
                show-expand>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon small color="red" class="mr-2" @click="editItem(item)">
                        mdi-pencil
                    </v-icon>
                    <v-icon small color="blue" @click="deleteItem(item)">
                        mdi-delete
                    </v-icon>
                </template>
                <template v-slot:[`item.checkBox`]="{ item }">
                    <input 
                        small
                        type="checkbox" 
                        id="checkBox" 
                        v-model="checked"
                        @click="delList(item)">
                </template>
                <template v-slot:expanded-item="{ headers, item }">
                    <td align="start" :colspan="headers.length">
                        <v-title>
                            <h4>Note: </h4>
                        </v-title>
                        {{ item.note }}
                    </td>
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

        <v-dialog >
            <v-card >
                <v-card-title>
                    <h2>Delete List</h2>
                </v-card-title>
                <v-container>
                    <!-- <ul>
                        <li v-for="select in selected" :key="select">
                            {{ select.task }}
                        </li>
                    </ul> -->
                </v-container>
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
            selected: [
                {
                    task: null,
                    priority: null,
                    note: null,
                }
            ],
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Actions", value: "actions" },
                { text: "", value: "checkBox"},
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
            this.formTodo.task = item.task;
            this.formTodo.priority = item.priority;
            this.formTodo.note = item.note;
            this.dialog = true;
        },
        deleteItem(item) {
            let confm = window.confirm("Yakin ingin menghapus ? ");
            if(confm === true) {
                let index = this.findIndx(item)
                this.todos.splice(index, 1);
            }
        },
        delList(item) {
            let index = this.findIndx(item)
            this.todos.push(this.selected[index]);
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