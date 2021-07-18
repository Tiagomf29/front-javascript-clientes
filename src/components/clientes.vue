<template>
    <div id="principal">
        <div id="form">
            <label>Nome do cliente</label><br />
            <input type="text" size="50"/><br/><br />
            <button id="cd">Cadastrar</button>
        </div> 
        <hr>   
        <div id="pesquisa">         
            <legend>Pesquisar por nome</legend>
            <input id="edtPesq" type="text" size="50" />
            <br /><br />
            <button id="pq">Pesquisar</button>         
        </div>
        <hr>  
        <div id="divTab">
            <strong></strong>
            <table>         
            </table>
        </div>             
    </div>
</template>

<script>
import $ from "jquery";
import swal from 'sweetalert2';
export default {
    
    mounted() {
        this.acaoBotao();
        this.botaoPesquisa();
    },
    methods: {
        acaoBotao:function () {            
            let botao = document.getElementById("cd");
            botao.addEventListener('click', e =>{               
                e.pageX;
                let texto = document.querySelector("input");
                if (texto.value == '') {
                    swal.fire("Informe um nome de cliente!");
                    texto.focus();
                }else{
                  this.inserirCliente();                                     
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
        listarPorNome:function(){
            $("table").empty();            
            let texto =  document.querySelector("strong");                
            texto.style.color= "black";
            let pesquisaNome = document.getElementById("edtPesq").value;
            pesquisaNome ="https://aapi-cadastro-cliente.herokuapp.com/api/listarPorNome/"+pesquisaNome;
            $.ajax({

                url: pesquisaNome,
                type: "GET",
                
                beforeSend : function(){
                    document.querySelector("strong").innerHTML = "Buscando dados no servidor de banco de dados AWS. Aguarde!" 
                },
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

            }).done(function(msg){
                console.log(msg)
                document.querySelector("strong").innerHTML ="";

            }).fail(function(jqXHR, textStatus, msg){
                console.log(msg);
                let texto =  document.querySelector("strong");
                texto.innerHTML = "Problema na tentativa de recuperação de dados no serviço da AWS. Serviço pode estar parado!";
                texto.style.color= "red";
            });
               
        },
        inserirCliente:function(){               
            $("table").empty();            
            let texto =  document.querySelector("strong");                
            texto.style.color= "black";                      
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
                     document.querySelector("strong").innerHTML = "Realizando comunicação com o servidor de banco de dados AWS. Aguarde!" 
                },
                success:function(response){
                    console.log(response);
                    let edtNomeCliente = document.querySelector("input");
                    edtNomeCliente.innerHTML = "";
                    if (response != undefined){
                        swal.fire('Registrado', 'Cadastro realizado com sucesso!','success'); 
                    }else{
                       swal.fire({
                                    icon: 'error',
                                    title: 'Não Registrado!',
                                    text: 'Nome do cliente já foi cadastrado. Verifique!'
                                })
                    }
                }

            })
            .done(function(msg){
                console.log(msg)
                document.querySelector("strong").innerHTML ="";                                
            })
            .fail(function(jqXHR, textStatus, msg){
                console.log(msg);
                let texto =  document.querySelector("strong");
                texto.innerHTML = "Problema na comunicação com o banco de dados da AWS. Serviço pode estar parado!";
                texto.style.color= "red";                
            });
        },
        botaoPesquisa:function(){
          let botao = document.getElementById("pq");
          botao.addEventListener('click', e=>{
                console.log(e); 
            $("table").empty();
              let texto = document.getElementById("edtPesq");
                if (texto.value == '') {                   
                    swal.fire("Informe um valor para a pesquisa!");
                    texto.focus();
                }else{
                   this.listarPorNome();
                }
          });           
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
      width: 240px;
  } 
  button{
      font-size: 15px;
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
      background-color: #d1e0eb;      
  }
  #form, #pesquisa{
      margin: 15px 15px 15px 15px ;
      text-align: center;
  }

  #pq{
      position: relative;
      
  }

  fieldset{
     width: 200px; 
  }

  table{
    border: 1px ridge; 
    text-align: center;       
  }
  #divTab{
    margin: auto auto auto auto; 
    width: 200px;
  }

  hr{
    width: 200px;
    border: 2px dotted;
  }
</style>