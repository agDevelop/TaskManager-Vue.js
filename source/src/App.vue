<template>
    <div id="app">
        <header class="header">
            <div class="header__input">
                <input type="text" class="header__input__test" placeholder="Название карточки"
                       v-on:keydown.enter="addCard" v-model="cardName">
                <button class="header__input__button" @click="addCard">Добавить</button>
            </div>
        </header>
        <div class="list">
            <div class="wrap">
                <h2>Backlog ({{ backlog.length }}) </h2>
                <draggable :list="backlog" :options="{group:'people'}" class="zero" id="backlog" @add="onBacklog">
                    <to-do v-for="el in backlog" :key="el.id" :name="el.name" :description="el.description"
                           :id="el.id"></to-do>
                </draggable>
            </div>


            <div class="wrap">
                <h2>To do ({{ todo.length }})</h2>
                <draggable :list="todo" :options="{group:'people'}" class="zero" id="todo" @add="onTodo">
                    <to-do v-for="el in todo" :key="el.id" :name="el.name" :description="el.description"
                           :id="el.id" :start-date="el.startDate"></to-do>
                </draggable>
            </div>
            <div class="wrap">
                <h2>Done ({{ done.length }})</h2>
                <draggable :list="done" :options="{group:'people'}" class="zero" id="done" @add="onDone">
                    <to-do v-for="el in done" :key="el.id" :name="el.name" :description="el.description"
                           :id="el.id" :start-date="el.startDate" :finish-date="el.finishDate"></to-do>
                </draggable>
            </div>


        </div>

    </div>
</template>

<script>
    import TodoElement from "./components/TodoElement"
    import draggable from "vuedraggable"
    import axios from "axios"
    import store from "./components/Store"

    export default {
        name: 'app',
        data() {
            return {
                cardName: "",
            }
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
            num() {
                return store.state.num;
            },
        },
        components: {
            toDo: TodoElement,
            draggable,
        },
        methods: {
            addCard() {
                let cardData = {
                    id: this.num,
                    name: this.cardName,
                    state: 0,
                    description: "Пустое описание",
                    startDate: "",
                    finishDate: ""
                };
                store.commit("addCard", cardData);
                store.commit("numInc");
                this.cardName = "";
                this.addToBd(cardData);
            },
            addToBd(obj) {
                let send = {
                    newstate: 0,
                    obj: obj
                };
                axios
                    .post("https://localhost:5001/api/values/add", send)

            },
            getSendObject(obj) {

                return {
                    id: +obj.item.querySelector("#elementId").textContent,
                    name: obj.item.querySelector("span").textContent,
                    description: obj.item.querySelector(".description").textContent,
                    startDate: "",
                    finishDate: ""
                }
            },
            onBacklog: function (evt) {

                let obj = this.getSendObject(evt);
                let send = {
                    newstate: 0,
                    obj: obj
                };
                axios
                    .post("https://localhost:5001/api/values/add", send)
            },
            onTodo: function (evt) {


                let obj = this.getSendObject(evt);
                let k = +obj.id;
                let t = new Date().toLocaleString("ru").slice(0, -3);
                store.commit("setStartDate", {id: k, date: t});
                obj.startDate = t;
                let send = {
                    newstate: 1,
                    obj: obj
                };
                axios
                    .post("https://localhost:5001/api/values/add", send)
            },
            onDone: function (evt) {
                console.log(evt.from.id);
                let obj = this.getSendObject(evt);
                let k = +obj.id;
                let t = new Date().toLocaleString("ru").slice(0, -3);
                store.commit("setFinishDate", {id: k, date: t});
                if (evt.from.id === "backlog") {
                    store.commit("setStartDateInDone", {id: k, date: t});
                }
                obj.startDate = store.getters.getStartDate(k);
                obj.finishDate = t;
                let send = {
                    newstate: 2,
                    obj: obj
                };

                axios
                    .post("https://localhost:5001/api/values/add", send)
            },
        },
        mounted() {

            axios.get("https://localhost:5001/api/values/GetBacklog").then(response => (store.commit("setBacklog", response.data)));
            axios.get("https://localhost:5001/api/values/GetTodo").then(response => (store.commit("setTodo", response.data)));
            axios.get("https://localhost:5001/api/values/GetDone").then(response => (store.commit("setDone", response.data)));
            axios.get("https://localhost:5001/api/values/GetTaskCount").then(response => (store.commit("setNum", response.data["count"] + 1)));

        },
    }
</script>
