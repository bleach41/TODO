<script setup>
    import { reactive } from 'vue'
    import InputNew from './InputNew.vue';
    import { watch } from 'vue';

    const localStorageKey = 'boardsData';
    const storedData = localStorage.getItem(localStorageKey);

    let boards
    if (storedData) {
        boards = reactive(JSON.parse(storedData));
    } else {
        // ... tableros predeterminados ...
        boards = reactive([
            {
                id: crypto.randomUUID(),
                name: 'tablero 1',
                items: [
                    {
                        id: crypto.randomUUID(),
                        title: "feature de archivos"
                    },
                    {
                        id: crypto.randomUUID(),
                        title: "resolve bug"
                    },

                ]
            },
            {
                id: crypto.randomUUID(),
                name: 'tablero 2',
                items: [
                    {
                        id: crypto.randomUUID(),
                        title: "mandar reporte"
                    },
                    {
                        id: crypto.randomUUID(),
                        title: "code review"
                    },

                ]
            }
        ])

    }


    watch(boards, (newBoards) => {
        localStorage.setItem(localStorageKey, JSON.stringify(newBoards));
    })

    function handleNewItem(text, board) {
        board.items.push({
            id: crypto.randomUUID(),
            title: text.value
        })

    }

    function handleNewBoard() {
        const name = prompt('Name of the board')
        if (!!name) {
            boards.push({
                id: crypto.randomUUID(),
                name: name,
                items: []
            })

        }

    }

    function starDrag(evt, board, item) {
        evt.dataTransfer.setData('text/plain', JSON.stringify({ boardId: board.id, itemId: item.id }))

    }

    function onDrop(evt, dest) {
        const { boardId, itemId } = JSON.parse(evt.dataTransfer.getData('text/plain'))
        const originBoard = boards.find((item) => item.id === boardId)
        const originItem = originBoard.items.find((item) => item.id === itemId)
        dest.items.push({ ...originItem })
        originBoard.items = originBoard.items.filter((item) => item !== originItem)
        console.log(boardId, itemId)
        console.log(originBoard.name, originItem.title)


    }


</script>

<template>
    <nav>
        <ul>
            <li>
                <a href="#" @click="handleNewBoard">Create board</a>
            </li>
        </ul>
    </nav>

    <div class="boards-container">
        <div class="boards">
            <div class="board" @drop="onDrop($event, board)" @dragover.prevent @dragenter.prevent v-for="board in boards"
                :key="board.id">
                <div>{{ board.name }}</div>
                <InputNew @on-new-item="(text) => handleNewItem(text, board)" />
                <div class="items">
                    <div class="item" draggable="true" @dragstart="starDrag($event, board, item)"
                        v-for="item in board.items" :key="item.id">{{
                            item.title }}</div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    nav {

        display: flex;

        background-color: #303030;
    }

    nav ul {

        list-style: none;
    }

    nav ul li a {

        color: azure;
    }

    nav ul li a:hover {
        transform: scale(1.2);
    }

    .boards {
        display: flex;
        gap: 10px;

    }

    .board {
        padding: 10px;
        color: black;
        background-color: #6f6b6b;
    }

    .items {
        display: flex;
        flex-direction: column;
        gap: 5px;
    }

    .item {
        background-color: white;
        padding: 10px;
        box-sizing: border-box;
    }
</style>
