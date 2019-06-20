<template>
    <div class="list__element" id="el-drag">
        <div id="elementId" style="display: none">{{ id }}</div>
        <header>
            <span v-if="!isEdit">{{ localName }}</span>
            <input type="text"  v-on:keyup.enter="textareaFocus" placeholder = "Название карточки"  v-model="localName" v-if="isEdit">
            <a href="" v-if="!isEdit">
                <object type="image/svg+xml" :data="arrow">
                </object>
            </a>
        </header>
        <div class="border">
            <div class="description">
                <p v-if="!isEdit">{{ localDescription }}</p>
                <label for="textarea"></label>
                <textarea  id="textarea" v-if="isEdit" v-model="localDescription" wrap="hard" cols="40"></textarea>
            </div>
            <div class="date-start" v-if="startHide()" >
                <span class="text">Дата начала: </span>
                <span class="time" id="date-start">{{ localStartDate }}</span>
            </div>
            <div class="date-end" v-if="finishHide()" >
                <span class="text">Дата конца: </span>
                <span class="time" id="date-finish">{{ localFinishDate  }}</span>
            </div>
            <div class="icons">
                <a @click.prevent="editMode">
                    <object class="edit" type="image/svg+xml" :data="edit">
                    </object>

                </a>
                <a @click.prevent="del">
                    <object class="del" type="image/svg+xml" :data="bin">
                    </object>

                </a>
            </div>

        </div>
    </div>
</template>

<script>
    import axios from "axios"
    import store from "./Store"
    import moment from 'moment'
    export default {
        name: "TodoElement",
        data() {
            return {
                arrow: require("../assets/arrow.svg"),
                bin: require("../assets/bin.svg"),
                edit: require("../assets/edit.svg"),
                del: this.delete,
                isEdit: false,
                localDescription: this.description,
                localName: this.name,

            }
        },
        props: {
            id: Number,
            name: String,
            description: {
                type: String,
                default: "Пустое описание"
            },
            startDate: {
              type: String,
                default: "",
            },
            finishDate: {
                type: String,
                default: "",
            }


        },
        filters: {
            time(date) {
                return moment(date).format('DD.MM.YYYY, HH:mm');
            },
        },
        computed: {
            backlog() {
                return store.state.backlog;
            },
            todo() {
                return store.state.todo;
            },
            done() {
                return store.state.done;
            },
            localStartDate() {
                return this.startDate;
            } ,
            localFinishDate() {
              return this.finishDate;
            },
        },
        methods: {
            textareaFocus(ev) {
                ev.path[2].querySelector("#textarea").focus();
            },
            startHide() {
                var t = this.$options.parent.$el.id;
                return t !== "backlog";
            },
            finishHide() {
                var t = this.$options.parent.$el.id;
                return t !== "backlog" && t !== "todo";
            },
            delete(ev) {
                let send = {
                    id: this.id
                };

                let par = ev.path[4].id;
                if (par === "backlog") {
                    store.commit("setBacklog", this.backlog.filter(td => td.id !== this.id));
                } else if (par === "todo") {
                    store.commit("setTodo", this.todo.filter(td => td.id !== this.id));
                } else if (par === "done") {
                    store.commit("setDone", this.done.filter(td => td.id !== this.id));
                }


                axios
                    .post("https://localhost:5001/api/values/delete", send)

            },
            getSendObject() {
                return {
                    id: this.id,
                    name: this.localName,
                    description: this.localDescription,
                    startDate: this.startDate,
                    finishDate: this.finishDate
                }
            },
            editMode() {
                if (!this.isEdit) {
                    this.isEdit = true;
                } else {
                    this.isEdit = false;
                    var send = this.getSendObject();
                    axios
                        .post("https://localhost:5001/api/values/edit", send)
                }


            },

        },
    }
</script>
