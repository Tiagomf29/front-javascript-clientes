<template>
    <div id="principal">        
        <div id="form"> 
            <h3>Aplicativo de cadastro de nomes de clientes</h3>      
            <input type="text" maxlength="40" size="40" placeholder="Informe o nome do cliente"/><br/>
            <button id="cd" class="ccd">Cadastrar</button>
        </div> 
        <hr> 
        <div id="pesquisa">         
            <input id="edtPesq" type="text" size="50" placeholder="Pesquise por nome do cliente"/>
            <br />
            <button id="pq" class="cpq">Pesquisar</button>         
        </div>
        <hr>  
        <div id="divTab">
            <strong></strong>
            <table>         
            </table>
        <button id="excluir">Excluir</button>    
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
        this.botaoExluir();
    },
    methods: {
        acaoBotao:function () {            
            let botao = document.getElementById("cd");
            botao.addEventListener('click', e =>{               
                e.pageX;
                let texto = document.querySelector("input");
                if (texto.value == '') {
                    swal.fire("Informe um nome de cliente!");
                                                         
                }else{
                  this.inserirCliente();                                     
                }
            })
        },
        listar:function(){
            $("table").empty();
            $.ajax({

                url: "https://ccliente-api.herokuapp.com/api/listar",
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
            if(document.getElementById("edtPesq").value !=""){
                pesquisaNome ="https://ccliente-api.herokuapp.com/api/listarPorNome/"+pesquisaNome;
            }else{
                pesquisaNome ="https://ccliente-api.herokuapp.com/api/listar";
            }
            $.ajax({

                url: pesquisaNome,
                type: "GET",
                
                beforeSend : function(){
                    document.querySelector("strong").innerHTML = "Buscando dados no servidor de banco de dados AWS. Aguarde!" 
                },
                success:function(response){
                   if (response.length == 0){
                      swal.fire({
                                    icon: 'info',
                                    title: 'Não localizado!',
                                    text: 'Não foi localizado nome de clientes conforme a pesquisa realizada. Verifique!'
                                }) 
                       $("#excluir").hide();
                       $("table").hide();                         
                  }else{
                  response.forEach((element,vlr) =>{
                    let linha = document.createElement("tr");
                      console.log(element);
                      linha.innerHTML = `
                           
                        <td><input class="marca" type="checkbox" /></td><td id="tdIdCliente">${response[vlr].id}</td><td>${response[vlr].nome}</td>
                           
                      `
                      document.querySelector("table").appendChild(linha);
                  });
                    $("#excluir").show(); 
                    $("table").show(); 
                  }
                 
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
                url: "https://ccliente-api.herokuapp.com/api/inserir",
                type: "POST",
                data:JSON.stringify({
                    "nome": nomeCliente
                }),
                beforeSend : function(){
                     document.querySelector("strong").innerHTML = "Realizando comunicação com o servidor de banco de dados AWS. Aguarde!" 
                },
                success:function(response){
                    console.log(response);                    
                    if (response != undefined){
                        let edtNomeCliente = document.querySelector("input");
                        edtNomeCliente.value = "";
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
            this.listarPorNome();             
          });           
        },
        exluirNomes:function(){

                let linha = document.querySelectorAll("tr");
                let contador = 0;
                linha.forEach(e=>{
                    console.log(e);
                                                 
                    if(linha[contador].children[0].children[0].checked == true){

                        console.log("Excluindo Cliente de código"+linha[contador].children[1].innerHTML)

                        $("table").empty();            
                        let texto =  document.querySelector("strong");                
                        texto.style.color= "black";                      
                        let idCliente = +linha[contador].children[1].innerHTML;            
                        $.ajax({
                            headers: { 
                                'Accept': 'application/json',
                                'Content-Type': 'application/json' 
                            },                
                            url: "https://ccliente-api.herokuapp.com/api/excluir",
                            type: "DELETE",
                            data:JSON.stringify({
                                "id": idCliente
                            }),
                            beforeSend : function(){
                                document.querySelector("strong").innerHTML = "Realizando comunicação com o servidor de banco de dados AWS. Aguarde!" 
                            },
                            success:function(response){
                                console.log(response);                    
                               
                                let edtNomeCliente = document.querySelector("input");
                                edtNomeCliente.value = "";                                                                     
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


                        }

                    contador = contador+1;  

                })              

        },
        botaoExluir:function(){

            let excluir = document.getElementById("excluir");
            excluir.addEventListener('click', e=>{
                console.log(e);
                swal.fire({
                                title: 'Exclusão de registros!',
                                text: "Tem certeza que deseja realizar a exclusão do(s) registro(s)?",
                                icon: 'warning',
                                showCancelButton: true,
                                confirmButtonColor: '#3085d6',
                                cancelButtonColor: '#d33',
                                confirmButtonText: 'Sim'
                                }).then((result) => {
                                if (result.isConfirmed) {                                    
                                    this.exluirNomes(); 
                                    $("table").empty();                                   
                                    swal.fire(
                                    'Deletado!',
                                    'Registro(s) Deletado(s) com sucesso!.',
                                    'Sucesso'
                                    )
                                }
                            })                               
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
      font-size: 15px;
      width: 240px;
  } 
  button{
      font-size: 15px;
  }
  td{
      font-size: 15px;
      color: black;  
      border-bottom: 1px solid; 
      padding: 10px;
      border: 1px solid;   
  }

tr{
   font-size: 15px;
   color: blue;  
   border: 1px solid;    
  
}

  #principal{
      border: 3px solid;
      border-radius: 15px;
      margin-left: auto;
      margin-right: auto;
      width: 340px;
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
    border: 2px solid; 
    text-align: center; 
    margin-left: auto;
    margin-right: auto;  
  }
  #divTab{
    margin: auto auto auto auto; 
    width: 330px;
    text-align: center;    
  }

  hr{
    width: 200px;
    border: 2px dotted;
  }

.cpq {
  display: inline-block;
  padding: 15px 25px;
  font-size: 24px;
  cursor: pointer;
  text-align: center;
  text-decoration: none;
  outline: none;
  color: black;
  background-color: #d1e0eb;
  border: none;
  border-radius: 15px;
  box-shadow: 0 9px #999;
}

.cpq:hover {background-color: honeydew}

.cpq:active {
  background-color: #3e8e41;
  box-shadow: 0 5px #666;
  transform: translateY(4px);
}


.ccd {
  position: relative;
  background-color:#d1e0eb;
  border: 1px solid;
  border-radius: 8px;
  font-size: 22px;
  color: black;
  padding: 20px;
  width: 150px;
  text-align: center;
  transition-duration: 0.4s;
  text-decoration: none;
  overflow: hidden;
  cursor: pointer;
}

.ccd:after {
  content: "";
  background: #f1f1f1;
  display: block;
  position: absolute;
  padding-top: 300%;
  padding-left: 350%;
  margin-left: -20px !important;
  margin-top: -120%;
  opacity: 0;
  transition: all 0.8s
}

.ccd:active:after {
  padding: 0;
  margin: 0;
  opacity: 1;
  transition: 0s
}

input[type=text], select {
  width: 100%;
  padding: 12px 20px;
  margin: 8px 0;
  display: inline-block;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-sizing: border-box;
}

h3{
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    font-size: 25px;
}

.marca{
    width: 20px;
    display: block;
    position: relative;
    padding-left: 35px;
    margin-bottom: 12px;
    cursor: pointer;
    font-size: 22px;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;    
}

#excluir{
  background-color: #f4511e;
  border: none;
  border-radius: 10px;
  color: white;
  padding: 16px 32px;
  text-align: center;
  font-size: 16px;
  margin: 4px 2px;
  opacity: 0.6;
  transition: 0.3s;
  display: inline-block;
  text-decoration: none;
  cursor: pointer;
}

#excluir:hover {
    opacity: 1
}

#excluir{
    display: none;
}

#tdIdCliente{
    visibility: visible;
}

</style>