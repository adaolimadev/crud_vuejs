<template>
    <div>
        <div>
            <Message :msg="msg" v-show="msg"/>
            <form id="burger-form" @submit.prevent="createBurger()">
                <div class="input-container">
                    <label for="nome">Nome do cliente</label>
                    <input type="text" name="nome" id="nome" v-model="nome" placeholder="Digite seu nome">
                </div>
                <div class="input-container">
                    <label for="pao">Escolha o pão</label>
                    <select name="pao" id="pao" v-model="pao" >
                        <option v-for="pao in jpaes" :key="pao.id" :value="pao.tipo">
                        {{ pao.tipo }}
                        </option>
                    </select>
                </div>
                <div class="input-container">
                    <label for="carne">Escolha a carne do seu Burger:</label>
                    <select name="carne" id="carne" v-model="carne" >
                        
                        <option v-for="carne in jcarnes" :key="carne.id" :value="carne.tipo">
                        {{ carne.tipo }}
                        </option>
                    </select>
                </div>
                <div class="input-container" id="opcionais-container">
                    <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
                   <div class="checkbox-container"  v-for="op in jopcionais" :key="op.id">
                    <input type="checkbox" name="opcionais"  v-model="opcionais" :value="op.tipo" >
                    <span>{{ op.tipo }}</span>
                   </div>
                </div>
                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Criar Burger!">
                </div>

            </form>
            
        </div>
    </div>
</template>

<script>
import Message from "./Message.vue";
export default {
    name: "BurgerForm",
    data(){
        return {
            jpaes:null,
            jcarnes:null,
            jopcionais: null,
            nome: null,
            pao:null,
            carne: null,
            opcionais:[],
            msg:null
        }
    },
    computed:{
        nome_pao(){
            return `${this.nome} ${this.pao}`
        }
    },
    methods:{
        async getIngredientes(){
            
            console.log("Abriu metodo.")
            //
            const req = await fetch("http://localhost:3000/ingredientes");
            const data = await req.json();

            //
            this.jpaes = data.paes;
            this .jcarnes = data.carnes;
            this.jopcionais = data.opcionais;
            console.log("Carregou ingredientes.")
     
        },
        async createBurger(){
           
            const dataBurger = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opcionais: Array.from(this.opcionais),
                status: "Solicitado"
            }
            console.log("criou hamburger:");
            console.log(dataBurger);
           
            const burgerJson = JSON.stringify(dataBurger);

            const req = await fetch("http://localhost:3000/burgers", {
                method: "POST",
                headers:{"Content-Type": "application/json"},
                body: burgerJson
            });

            const res = await req.json();
            console.log("Mandou para o server:");
            console.log(res);

            
            this.limparCampos();

            this.msg= `Pedido N° ${res.id} realizado com sucesso`;

            setTimeout(()=> this.msg="", 5000);

        },
         async limparCampos(){
            this.nome = "";
            this.carne = "";
            this.pao = "";
            this.opcionais=[];

         }
    },
    mounted(){
        this.getIngredientes()
    },
    components:{
        Message
    }
}
</script>

<style scoped>
    #burger-form{
        max-width: 300px;
        margin: 0 auto;
    }

    .input-container{
        display: flex;
        flex-direction: column;
        margin-bottom: 20px;
    }

    label{
        font-weight: bold;
        margin-bottom: 20px;
        color: #222;
        padding: 5px 10px;
        border-left: 4px solid #fcba03;
    }

    input, select{
        padding: 5px 10px;
        width: 300px;
    }

    #opcionais-container{
        flex-direction: row;
        flex-wrap: wrap;
    }

    #opcionais-title{
        width: 100%;
    }

    .checkbox-container{
        display: flex;
        align-items: flex-start;
        width: 50%;
        margin-bottom: 20px;
    }

    .checkbox-container span,
    .checkbox-container input{
        width: auto;
    }

    .checkbox-container span{
        margin-left: 6px;
        font-weight: bold;
    }

    .submit-btn{
        background-color: #222;
        color: #fcba03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: 0.5s;
    }

    .submit-btn:hover{
        background-color: #fcba03;
        color: #222;
    }



</style>