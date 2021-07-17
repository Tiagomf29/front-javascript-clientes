<template>
    <div id="principal">
        <div id="form">
            <label>Nome do cliente</label><br />
            <input type="text" size="50"/><br/><br />
            <button id="cd">Cadastrar</button>
            <button id="pq">Pesquisar</button><br /><br />
            <table>         
            </table>
        </div>       
    </div>
</template>/

<script>
import $ from "jquery";
export default {
    
    mounted() {
        this.acaoBotao();
    },
    methods: {
        acaoBotao:function () {
            let botao = document.getElementById("cd");
            botao.addEventListener('click', e =>{
                e.pageX;
                let texto = document.querySelector("input");
                if (texto.value == '') {
                    alert("Informe um nome de cliente!");
                    texto.focus();
                }else{
                  this.inserirCliente(); 
                  this.listar();
                  alert("Registro inserido com sucesso!"); 
                }
            })
        },
        listar:function(){
            $("table").empty();
            $.ajax({

                url: "https://aapi-cadastro-cliente.herokuapp.com/api/listar",
                type: "GET",
                success:function(response){
                  
                  response.forEach((element,vlr) =>{
                    let linha = document.createElement("tr");
                      console.log(element);
                      linha.innerHTML = `
                           
                        <td>${response[vlr].nome}</td>
                           
                      `
                      document.querySelector("table").appendChild(linha);
                  });
                 
                }

            });
               
        },
        inserirCliente:function(){

            let nomeCliente = document.querySelector("input").value;            
            $.ajax({
                headers: { 
                    'Accept': 'application/json',
                    'Content-Type': 'application/json' 
                },                
                url: "https://aapi-cadastro-cliente.herokuapp.com/api/inserir",
                type: "POST",
                data:JSON.stringify({
                    "nome": nomeCliente
                }),
                beforeSend : function(){
                     
                }

            })
            .done(function(msg){
                console.log(msg)

            })
            .fail(function(jqXHR, textStatus, msg){
                 console.log(msg+''+textStatus);
            });

        },
        botaoPesquisa:function(){


          

           

        }
        
    },
}
</script>

<style >
  input:hover{
      background-color: aqua;
  } 
  input{
      font-size: 20px;
      width: 260px;
  } 
  button{
      font-size: 20px;
  }
  td{
      font-size: 20px;
      color: blue;  
      border-bottom: 1px solid;    
  }
  #principal{
      border: 3px solid;
      border-radius: 15px;
      margin-left: auto;
      margin-right: auto;
      width: 300px;      
  }
  #form{
      margin: 15px 15px 15px 15px ;
  }
  #pq{
      position: relative;
      left: 10px;
  }

</style>