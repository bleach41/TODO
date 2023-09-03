<script setup>
    import { reactive, ref } from 'vue'
    import InputNew from './InputNew.vue'
    import { watch } from 'vue'

    const localStorageItem = 'ItemData'

    const storedData = localStorage.getItem(localStorageItem)



    let boards
    if (storedData) {
        boards = reactive(JSON.parse(storedData));
    }
    else {
        // ... tableros predeterminados ...
        boards = reactive([
            {
                id: crypto.randomUUID(),
                name: 'Dia 1',
                items: [
                    {
                        id: crypto.randomUUID(),
                        title: "Sacar al perro"
                    },
                    {
                        id: crypto.randomUUID(),
                        title: "Ir al gym"
                    },

                ]
            },
            {
                id: crypto.randomUUID(),
                name: 'Dia 2',
                items: [
                    {
                        id: crypto.randomUUID(),
                        title: "Jugar dota",
                        checked: false
                    },
                    {
                        id: crypto.randomUUID(),
                        title: "Jugar wow",
                        checked: false
                    },

                ]
            }
        ])

    }


    watch(boards, (newBoards) => {
        localStorage.setItem(localStorageItem, JSON.stringify(newBoards));
    })

    function handleNewItem(text, board) {
        board.items.push({
            id: crypto.randomUUID(),
            title: text.value,
            checked: false
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
    const deleteItem = (id) => {
        boards.forEach((board) => {
            const index = board.items.findIndex(item => item.id === id)
            if (index !== -1) {
                board.items.splice(index, 1)
            }
        })
    }
    function deleteBoard(boardId) {
        const index = boards.findIndex(board => board.id === boardId)
        if (index !== -1) {
            boards.splice(index, 1)
        }
    }


</script>

<template>
    <nav>
        <ul>
            <li>
                <a href="#" @click="handleNewBoard">➕ Create board</a>
            </li>
        </ul>
    </nav>

    <div class="boards-container">
        <div class="boards">
            <div class="board" @drop="onDrop($event, board)" @dragover.prevent @dragenter.prevent v-for="board in boards"
                :key="board.id">
                <div>{{ board.name }} <button class="btn-board" @click="deleteBoard(board.id)">✖</button></div>
                <InputNew @on-new-item="(text) => handleNewItem(text, board)" />
                <div class="items">
                    <div class="item" draggable="true" @dragstart="starDrag($event, board, item)"
                        v-for="item in board.items" :key="item.id">
                        <input type="checkbox" :checked="item.checked" @change="item.checked = !item.checked">
                        {{ item.title }}
                        <button @click="deleteItem(item.id)">✖</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .item button {
        position: absolute;
        translate: 8em;

        background-color: #303030;
        padding: 0 5px;
    }

    .board .btn-board {
        position: absolute;
        translate: 1em;

        background-color: #303030;
        padding: 0 5px;
    }


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
        display: flex;
        align-content: space-around;
        background-color: white;
        padding: 10px;
        box-sizing: border-box;
    }
</style>
