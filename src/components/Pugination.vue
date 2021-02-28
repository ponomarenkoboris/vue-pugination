<template>
    <div class="pug">
        <div class="conatiner">
            <h1>Todos</h1>
            <div class="columns">
                <div class="small__collumn__wrapepr" @click="sortList">
                    <h4 class="column__name">ID</h4>
                </div>
                <div class="big__collumn__wrapepr">
                    <h4 class="column__name">TITLE</h4>
                </div>
                <div class="small__collumn__wrapepr">
                    <h4 class="column__name">TEXT</h4>
                </div>
            </div>
            <hr style="margin: 0; border: .5px solid #000;">
            <div v-if="!renderTodo.length" class="message__wrapper">
                <h1>Loading...</h1>
            </div>
            <div v-else class="todo__wrapper">
                <div v-for="(todo, idx) in renderTodo" :key="idx" class="todo__item">
                    <div class="id__wrapper">
                        <p class="item__id">{{ todo.id }}</p>
                    </div>
                    <div class="title__wrapper">
                        <h3 class="item__title">{{ todo.title }}</h3>
                    </div>
                    <div class="text__wrapper">
                        <p class="item__text"><strong>{{ todo.userId + idx }} hi there </strong></p>
                    </div>
                </div>
                <div class="btns-prev-next">
                    <button class="prev" ref="prevRef" disabled @click="prevPage">Previous page</button>
                    <button class="next" ref="nextRef" @click="nextPage">Next page</button>
                </div>
            </div>
        </div>
        <div class="all-pages">
            <h1>All Pages List</h1>
            <div class="links__toPage__wrapper" v-if="allPages">
                <div class="item__toPage" v-for="(num, idx) in allPages" :key="idx" >
                    <button class="toPage__btn" @click="goToPage(num)">{{ num }}</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
    name: 'PaginationTest',
    setup() {
        let allPages = ref(null); // pages count
        let currentPage = 1; // current page (default === 1)
        const onOnePage = 5; // count of todos on page
        let todos = []; // todos array 
        const renderTodo = ref([]); // todos that will be render on page
        const nextRef = ref(null); // btn next DOM
        const prevRef = ref(null); // btn prev DOM

        async function getTodos() {
            try {
                const list = await fetch('https://jsonplaceholder.typicode.com/todos'); // get data
                const data = await list.json(); // parse to JS array of objects
                todos = data;
                allPages.value = Math.ceil(todos.length / onOnePage);
            } catch (error) {
                console.error("ERROR IN REQUEST FUNCTION: ", error.status);
                return null
            }
        }

        onMounted(async () => {
            // after component has been mounted
            await getTodos();
            for (let i = 0; i < onOnePage; i++) {
                renderTodo.value.push(todos[i]);
            }
        });

        // Manipulations with todos
        function nextPrevController() {
            if (currentPage === allPages.value + 1) {
                nextRef.value.disabled = true;
                return
            }
            if (currentPage === 1) {
                prevRef.value.disabled = true;
                return
            }
            nextRef.value.disabled = false;
            prevRef.value.disabled = false;
            const arrayRender = todos.slice((currentPage - 1) * onOnePage, currentPage * onOnePage);
            renderTodo.value = arrayRender;
        }

        // Custom sort function
        let sortSwitcher = false;
        function sortingById(id) {
            if (!sortSwitcher) {
                sortSwitcher = true
                return (a, b) => a[id] < b[id] ? 1 : -1;
            } else {
                sortSwitcher = false
                return (a, b) => a[id] > b[id] ? 1 : -1;
            }
        }

        // Event handlers
        function sortList() {
            renderTodo.value = '';
            todos.sort(sortingById("id"))
            renderTodo.value = todos.slice((currentPage - 1) * onOnePage, currentPage * onOnePage);
        }
        function nextPage() {
            prevRef.value.disabled = false;
            currentPage++;
            nextPrevController();
            if (currentPage + 1 === 41) {
                nextRef.value.disabled = true;
                return
            }
        }
        function prevPage() {
            currentPage--;
            nextPrevController();
            if (currentPage - 1 === 0) {
                prevRef.value.disabled = true;
                return
            }
        }
        function goToPage(pageNumber) {
            currentPage = pageNumber;
            if (currentPage === allPages.value) {
                nextRef.value.disabled = true;
                prevRef.value.disabled = false;
            } else if (currentPage === 1) {
                nextRef.value.disabled = false;
                prevRef.value.disabled = true;
            } else {
                prevRef.value.disabled = false;
                nextRef.value.disabled = false;
            }
            const arrayRender = todos.slice((pageNumber - 1) * onOnePage, pageNumber * onOnePage);
            renderTodo.value = arrayRender;
        }

        return {
            renderTodo,
            sortList,
            nextPage,
            prevPage,
            goToPage,
            allPages,
            nextRef,
            prevRef
        }
    }
}
</script>

<style scoped>
.pug {
    display: flex;
    justify-content: space-around;
    align-items: flex-start;
}
.all-pages {
    width: 600px;
    height: 300px;
}
.links__toPage__wrapper {
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start; 
}
.item__toPage {
    margin: 10px 5px;
    width: auto;
}
.toPage__btn {
    padding: 5px 10px;
    cursor: pointer;
    border-radius: 15px;
    border: none;
    background-color: #fff;
    outline: none;
    transition: background-color .22s linear;
}
.toPage__btn:hover {
    background-color: #46E0A6;
}
.toPage__btn:active {
    background-color: rgba(73, 235, 173, .01);
}
.conatiner {
    width: 600px;
    border: 1px solid #ccc;
    border-radius: 20px;
}
.message__wrapper {
    height: 650px;
    display: flex;
    justify-content: center;
    align-items: center;
}
.columns {
    display: flex;
    justify-content: space-around;
}
.todo__wrapper {
    width: 100%;
}
.todo__item {
    display: flex;
    justify-content: space-around;
    align-items: center;
    border-bottom: 1px solid #ccc;
    height: 120px;
}
.id__wrapper,
.text__wrapper,
.title__wrapper,
.small__collumn__wrapepr,
.big__collumn__wrapepr {
    height: 100%;
    width: 20%;
    display: flex;
    align-items: center;
    justify-content: center;
}
.small__collumn__wrapepr,
.big__collumn__wrapepr {
    border-top: 1px solid #000;
    border-left: 1px solid #000;
    border-right: 1px solid #000;
}
.title__wrapper,
.big__collumn__wrapepr {
    width: 60%;
    border-left: 1px solid #ccc;
    border-right: 1px solid #ccc;
}
.small__collumn__wrapepr {
    cursor: pointer;
}
.btns-prev-next {
    height: 50px;
    display: flex;
    justify-content: space-around;
    align-items: center;
}
.prev,
.next {
    cursor: pointer;
    background-color: #fff;
    padding: 7px 14px;
    border-radius: 10px;
    outline: none;
    border: none;
}
.prev:hover,
.next:hover {
    background-color: #46E0A6;
}
.prev:active,
.next:active {
    background-color: rgba(73, 235, 173, .01);
}
</style>
