# REST-ASP-NET

Hey guys!
  Mais uma vez estou aqui para ajudar mais alguns companheiros criar uma aplicação utilizando web-Socket RealTime de forma assíncrona, só que dessa vez vamos fazer me Asp.net Core 2.1. Como sempre falo um bom dev não tem uma linguagem especifica, ele praticamente se adapta a qualquer que seja.
Como havia prometido a muito tempo de postar sempre conteúdos atualizados, infelizmente nem só de codigo vive um Homem ashahs,
let'go? 

- Quais a necessidade de usar isso na minha vida?
      - vamos lá, digamos que você tenha duas aplicação servida em um mesmo servidor e precise que a cada atualização de uma a outra tome alguma decisão, ou seja dispare um email, alerte ao administrador do sistema determinadas transações de contas bancarias, ou simplesmente alerte em grupo de usuario que algo foi alterado.
      
      Sobre minha necessidade de usar em diversas formas, tive que implementar um simples agenda multi-usuario 
      em tempo real e a cada agendamento os clientes conectados precisavam atualizar sua agenda para não dar conflito nos horários. 
      Parece uma aplicação simples né ?
      
  - Mas Julio, por que não fez fazendo uma request a cada 1 segundo ??? muita gambiarra isso ai!
      * Bom meu caro, gostaria de deixar bem claro, que a parte mais importante de uma aplicação é justamente isso, posso não ter muitas experiências sobre o assunto. Mas já teve pessoas de empresas de Rastreamento de veiculos me perguntando se era possivel migrar tudo para web-socket porque não estava aguentando 15 mil requisições por segundo. Acredite isso pode fazer sua aplicação parar.
  
  Seguindo...
    
    Vamós fazer uma simples API que será responsavel por conectar todos as aplicações de uma vez, podendo separar por Grupos de usuarios, mandar informações para todos conectados e muitos mais, lembre-se sua criatividade vai dar um jeito de usar essa belezura.
    
   A Principal biblioteca usada para isso funfar será ASP.NET SignalR
   
  1- Crie um novo projeto em no seu visual studio.
    -> aplicativo WEB ASP.NET Core -> API 
        E só aguardar montar a aplicação.
   
  2-  Adicione a biblioteca ao seu projeto.
      * Ferramentas -> Gerenciador de pacotes NuGet
      Pesquise por SignalR, será o primeiro, selecione o projeto e clique em install.
      
  3- No projeto raiz, adicione uma nova classe chamada SocketHub que será responsavel por fazer o mapeamento e router das conexões.
      Ficará assim :
      
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Threading.Tasks;

      namespace servidor_socket
      {
          public class SocketHub
          {
          }
      }

      Agora adiocione :Hub a sua classe SocketHub herdando todas as classes Microsoft.AspNetCore.SignalR.Hub, há não esqueça de add as         importações, ficando assim.

      using Microsoft.AspNetCore.SignalR;
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Threading.Tasks;

      namespace servidor_socket
      {
          public class SocketHub:Hub
          {
          }
      }
      
 4 - Dentro dessa classe, você ira criar uma classe chamada Send, que será chamada pela aplicação client-side e passado uma nova mensagem por parâmentro.
 
 Por exemplo:
 
          namespace servidor_socket
          {
              public class SocketHub:Hub
              {
              
               // Aqui dentro ficará as funções 
                public async Task Send(string message)
                {
                  return Clients.All.SendAsync("Send", message);
                }   
              }
          }
      

5 - vamos adicionar umas das partes principais para o socket funcionar.
    - Vá na classe chamada Startup.cs e adicione os serviço do SignlR e AddCors de para aceitar os requests CROSS Domain, não vou falar sobre isso agora mas aconselho bastante saber sobre isso!
    
    
     public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
             services.AddSignalR();
             services.AddCors();
        }
    
    
    Agora adicione na classe dentro Configure o caminho na qual o socket irá reponder 
    *OBS: É aqui o pulo do gato ashahas
    
          // Definindo rodas para o socket responder
            app.UseSignalR(routes =>
            {
                routes.MapHub<SocketHub>("/chat"); //  o '/chat' será a o caminho chamado pelo app.
            });
            app.UseCors(options => options.AllowAnyHeader().AllowAnyMethod().AllowAnyOrigin());
    
6- Agora vamos criar o Controller chamado Chat que irá ser responsavél para receber as requisições.
  - Clique em Controllers->adiconar -> Controlador -> Controlador API - Vazio.
     Coloque o nome de ChatController de acordo que você escolheu em routes.MapHub<SocketHub>("/chat"); 
     A nova classe ficará assim:
  
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore.Http;
      using Microsoft.AspNetCore.Mvc;

      namespace servidor_socket.Controllers
      {
          [Route("api/[controller]")]
          [ApiController]
          public class ChatController : ControllerBase
          {
          }
      }
      
 Agora Precisamos fazer uma injeção de dependência nessa classe ChatController da classe SocketHub.
  Ficará assim:
  OBS: adicione as importaçoes 
  
  using Microsoft.AspNetCore.SignalR;
  
    namespace servidor_socket.Controllers
    {
        [Route("api/[controller]")]
        [ApiController]
        public class ChatController : ControllerBase
        {
            private IHubContext<SocketHub> _messageHubContext;
            public ChatController(IHubContext<SocketHub> messageHubContext)
            {
                _messageHubContext = messageHubContext;
            }
        }
    }
  
  
 Agora vamos fazer a aplicaçao front-end em angular 6
 
Vá em Sollu
 
 
  
  
  
  
     
