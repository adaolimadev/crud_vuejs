<template>
        <Message :msg="msg" v-show="msg" />
        <div id="burger-table">
            <div>
                <div id="burger-table-heading">
                    <div class="order-id">#:</div>
                    <div>Cliente: </div>
                    <div>Pão: </div>
                    <div>Carne: </div>
                    <div>Opcionais: </div>
                    <div>Ações: </div>
                </div>
            </div>
            <div id="burger-table-rows">
                <div class="burger-table-row" v-for="b in burgers" :key="b.id" >
                    <div class="order-number">{{ b.id }}</div>
                    <div>{{ b.nome }}</div>
                    <div>{{ b.pao }}</div>
                    <div>{{ b.carne }}</div>
                    <div>
                        <ul>
                            <li v-for="(opc, index) in b.opcionais" :key="index"> {{ opc}}</li>
                           
                        </ul>
                    </div>
                    <div>
                        <select name="status" class="status" @change="updatedBurger($event, b.id)">
                            <option v-for="s in status" :key="s.id" :value="s.tipo"  :selected="b.status == s.tipo">{{ s.tipo }}</option>
                        </select>
                        <button class="delete-btn" @click="deleteBurger(b.id)">Cancelar</button>
                    </div>
                </div>
            </div>
        </div>
</template>

<script>
import Message from "./Message.vue";
export default {
    name:"Dashboard",
    components: {
        Message
    },
    data(){
        return{
            burgers: null,
            burger_id: null,
            status: [],
            msg: ""
        }
    },
    methods:{
        async getPedidos(){
            const req = await fetch("http://localhost:3000/burgers");
            const data = await req.json();
            this.burgers = data;
        
            //resgatar o status
            this.getStatus();
        },
        async getStatus(){
            const req = await fetch("http://localhost:3000/status");
            const data = await req.json();
            this.status = data;
        },
        async deleteBurger(id){
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {method: "DELETE"});

            const res = await req.json();

            this.msg= `Pedido removido com sucesso.`;

            setTimeout(()=> this.msg="", 5000);

            this.getPedidos();
        },
        async updatedBurger(event, id){
            const option = event.target.value;

            const dataJson = JSON.stringify({status: option});

            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: "PATCH", 
                headers: {"Content-Type" : "application/json"} ,
                body: dataJson
            });

            const res = await req.json();

            this.msg= `Status do pedido N° ${res.id} alterado com sucesso.`;

            setTimeout(()=> this.msg="", 5000);

            console.log(res);
        }
    },

    mounted(){
       
        this.getPedidos();
    }
}
</script>

<style scoped>
    #burger-table{
        max-width: 1200px;
        margin: 0 auto;
    }

    #burger-table-heading,
    #burger-table-row,
    .burger-table-row{
        display: flex;
        flex-wrap: wrap;
    }

    #burger-table-heading{
        font-weight: bold;
        padding: 12px;
        border-bottom: 3px solid #333;
    }

    #burger-table-heading div,
    .burger-table-row div{
        width: 19%;
    }

    .burger-table-row{
        width: 100%;
        padding: 12px;
        border-bottom: 1px solid #ccc;
    }

    #burger-table-heading .order-id,
    .burger-table-row .order-number{
        width: 5%;
    }

    select{
        padding: 10px 6px;
        margin-right: 12px;
        font-size: 14px;          
    }

    .delete-btn{
        background-color: #222;
        color: #fcba03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 14px;
        margin: 0 auto;
        cursor: pointer;
        transition: 0.5s;
        border-radius: 5px;

    }

    .delete-btn:hover{
        background-color: #fcba03;
        color: #222;
    }

</style>