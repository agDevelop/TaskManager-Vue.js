
<script>
    import Vue from 'vue'
    import Vuex from "vuex"

    Vue.use(Vuex);


    export default new Vuex.Store({
        state: {
            backlog: [],
            todo: [],
            done: [],
            num: 0,
        },
        actions: {

        },
        mutations: {
            addCard(state, card) {
                state.backlog.push(card);
            },
            setBacklog(state, arr) {
                state.backlog = arr;
            },
            setTodo(state, arr) {
                state.todo = arr;
            },
            setDone(state, arr) {
                state.done = arr;
            },
            numInc(state) {
                state.num++;
            },
            setNum(state, n) {
              state.num = n;
            },
            setStartDate(state, date){
              let temp =  state.todo.filter(td => td.id === date.id)[0];
              let index = state.todo.indexOf(temp);
              state.todo[index].startDate = date.date;
            },
            setFinishDate(state, date){
                let temp =  state.done.filter(td => td.id === date.id)[0];
                let index = state.done.indexOf(temp);
                state.done[index].finishDate = date.date;
            },
            setStartDateInDone(state, date){
                let temp =  state.done.filter(td => td.id === date.id)[0];
                let index = state.done.indexOf(temp);
                state.done[index].startDate = date.date;
            },

        },
        getters: {
            getBacklog: state => {
              return state.backlog;
            },
            getTodo: state => {
              return state.todo;
            },
            getStartDate: state => id => {
                let temp=  state.done.find(td => td.id === id);
                let index = state.done.indexOf(temp);
                return state.done[index].startDate;
            },
        },
        modules: {}
    });


</script>
