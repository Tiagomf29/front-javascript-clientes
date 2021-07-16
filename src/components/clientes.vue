<template>
    <div>
        <input type="text" size="50"/><br/><br />
        <button>Cadastrar</button><br /><br />
        <table>

        </table>
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
            let botao = document.querySelector("button");
            botao.addEventListener('click', e =>{
                e.pageX;
                let texto = document.querySelector("input");
                if (texto.value == '') {
                    alert("Informe um nome de cliente!");
                    texto.focus();
                }else{
                  this.listar();
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
               
        }
        
    },
}
</script>

<style >
  input:hover{
      background-color: aqua;
  } 
  input{
      font-size: 30px;
      width: 400px;
  } 
  button{
      font-size: 30px;
  }
  td{
      font-size: 20px;
      color: blue;  
      border-bottom: 1px solid;    
  }

</style>