<script>
    import Icon from "./icon.svelte";
    import TodoItem from "./todo_item.svelte";
    import { onMount } from "svelte";

    let todos = [];
    let last_id = 0;

    const create_todo = async () => {
        let todo = {
            id: ++last_id,
            task: '',
            done: false,
            priority: 3
        };
        console.log("CREATE", todo);
        localStorage.setItem(`todo${todo.id}`, JSON.stringify(todo));
        todos = [...todos, todo]
    }

    const change_todo_item = async (e) => {
        switch(e.detail.type) {
            case 'update' :
                update_item(e.detail.id);
                break;
            case 'delete' :
                delete_item(e.detail.id);
                break;
        }
    }

    const update_item = (id) => {
        console.log("UPDATE:", id);

        const todo = todos.filter(t => t.id == id)[0];
        localStorage.setItem(`todo${id}`, JSON.stringify(todo));
    }

    const delete_item = (id) => {
        console.log("DELETE:", id);
        todos = todos.filter(t => t.id != id);

        localStorage.removeItem(`todo${id}`);
    }

    onMount(async () => {
        for(let i = 0; i < localStorage.length; i++){
            const key = localStorage.key(i);
            const keyn = +key.substring(4);
            if(keyn >= last_id)
                last_id = keyn;
            const todo = JSON.parsel(localStorage.getItem(key));
            if(todo != null)
                todos.push(todo);
        }
        todos = [...todos];
    });

</script>

<h1>Todos</h1>
<div class="todo-list">
    <div class="header"><Icon name="tag"/></div>
    <div class="header"><Icon name="task_alt"/></div>
    <div class="header"><Icon name="list"/></div>
    <div class="header"><Icon name="schedule"/></div>
    <div class="header header-last"><Icon name="add_box" handler={create_todo}/></div>

    {#each todos as todo}
        <TodoItem todo={todo} on:change={change_todo_item}/>
    {/each}

</div>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Kanit:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');

    .app-title {
        font-family: 'Kanit', serif;
        margin-top: 8px;
        font-size: 68px;
        opacity: 0.3;
    }
    .todo-list {
        display: grid;
        grid-template-columns: 1fr 1fr 4fr 2fr 1fr;
        border: 0px solid blue;
        width: 95%;
        margin: auto;
    }

    .header {
        border-bottom: 1px solid #E7ECEE;
        border-right: 1px solid #E7ECEE;
        text-align: center;
        padding-bottom: 20px;
    }

    .header-last {
        border-right: none;
    }

</style>