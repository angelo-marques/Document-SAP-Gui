# Document-SAP-Gui
Fonte SAP GUI Scripting API: <br/> https://help.sap.com/docs/sap_gui_for_windows/b47d018c3b9b45e897faf66a6c0885a8/babdf65f4d0a4bd8b40f5ff132cb12fa.html?locale=en-US&q=PressToolbarContextButton <br/>
Fonte da Microsoft SAP Gui: <br/> https://learn.microsoft.com/pt-pt/power-automate/guidance/rpa-sap-playbook/reviewing-generated-code

## Compartilhando Experiencias do SAP GUI Scripting API usando C#.

## Resumo:
Tive uma participação em um projeto criando robôs que usam o SAP para extrair dados e/ou manipular o mesmo, de forma obter dados dele.<br/>
Nesse caso tive que enfrentar uma falta de documentação e alguns pontos complicados nessa jornada. <br/>
Apesar de uma grande documentação em outras linguagens, alguns pontos ficaram complicados para serem entendidos em C#. <br/>
Acredito que não deve ser o problema, mas quero deixar documentado para aqueles que tiverem algo semelhante no futuro. <br/>

Para os exemplos estou usando a: Alefair.SAP.API <br/>
<p>https://www.nuget.org/packages/Alefair.SAP.API</p>
<p>https://nuget.info/packages/Alefair.SAP.API/1.0.7</p>

------------------------
## Transformação de códigos
No sentido do código feito na sua origem, ou fazer um processo de forçar o padrão para aceitar o modelo já usado em VB por exemplo: <br/>
<pre>(GuiTextField)SAPActive.SapSession.ActiveWindow.FindByName("RSYST-MANDT", "GuiTextField");</pre>

 (GuiTextField) <br/> dependa da dll, mas ele é o mais próximo da linguagem em Java e VB, logo a tipagem é a forma de obter o mesmo padrão de código podendo ser usado.

--------------------------
## Formato do C#:
No formato de importação ele fica usando os dados encapsulados no formato original e sendo transcritos para o modelo mais simples de usar. <br/>
Ou então vão para 'GuiSession.Focus()' ou seja em um modelo simples, como vemos no exemplo:
<pre>
GuiSession sapSession = new();
sapSession.Focus("wnd[0]/scriptsap");
</pre>
 
Como ele se compara ao SetFocus mas é também mais amplo porque se reuni nele alguns pontos como: <br/>
GuiCTextField | GuiTextField | GuiLabel | GuiShell <br/>
Os guais passam por um selector : SelectorType que tráz o que é desejado.<br/>
Ou seja ele pode ser usado para qualquer um desses elementos do SAP.<br/>
Por exemplo: 
<pre>
SapSession.Focus("wnd[0]/usr/cnt/shellcont/shell", TableSelectMethod.SelectRows, "0");
</pre>

Que geralmente é usado para selecionar uma Row (coluna do dentro de um Shell do SAP) e nesse caso com C# precisa dessa função para chegar nele.

---------------

## Mesclagem:
Sendo bom pontuar que podemos usar mesclagem dentro do C# como: <br/>
Se for manipular um o shell que geralmente é usado para gerar modelos de dados em excel usando (GuiShell). <br/>

<pre>
var shellToolbarContextButton = (GuiShell)GuiSession.ActiveWindow.FindById("wnd[0]/usr/shellcont/shell");
</pre>   
 
Vai ter retornar o manipulador do componente, nesse caso você passar por inserir ele dentro dizendo que é o Grid, nessa caso o as, e um Alias que faz ele entender o que ele é.

<pre>
var btnToolbarContextButton = shellToolbarContextButton as GuiGridView;
</pre>
 
Nesse caso vai conseguir acessar o menu e pressionar o botão que deseja como:
 
<pre>
btnToolbarContextButton?.PressToolbarContextButton("DISPLAY");
btnToolbarContextButton?.SelectContextMenuItem("DOWNLOAD");
</pre>
 
 Base de referencia direto do SAP Document: <br/> https://help.sap.com/docs/sap_gui_for_windows/b47d018c3b9b45e897faf66a6c0885a8/4af24c3281fb4d6a809e53238562d3b2.html?locale=en-US&q=PressToolbarContextButton

<pre>
sapSession.Click("scriptsap");
</pre>

(Se compara ao Text, sendo que ele usa o FindById para encontrar o que precisa dentro do tipos de textos que podem ser usados.)

<pre>
sapSession.GetText("scriptsap");
</pre>

O Session GetText contem dentro dele os: <br/>
GuiBarChart | GuiBox | GuiButton | GuiCalendar | GuiChart | GuiCheckBox | GuiComboBox | GuiCTextField | GuiCustomControl | GuiLabel | GuiOkCodeField | GuiPasswordField | GuiRadioButton | GuiTab | GuiTextedit | GuiTextField | GuiUserArea | GuiShell <br/>
(se refere ao text, e serve para inserir texto nos campos diversos do sap.) <br/>

<pre>
sapSession.SetTex("scriptsap");
</pre>

GuiBarChart | GuiBox | GuiButton | GuiCalendar | GuiChart | GuiCheckBox | GuiComboBox | GuiCTextField | GuiCustomControl | GuiLabel | GuiOkCodeField | GuiPasswordField | GuiRadioButton | GuiTab | GuiTextedit | GuiTextField | GuiUserArea | GuiShell 

------------------
## Formato comando do teclado no SAP Gui
Formato para enviar qualquer comando para o SAP Gui.

<pre>
sapSession.SendCommand(“/oCTRL+N”);
</pre>

----------------------------
## Formato para pegar titulo no SAP Gui
(Usado para pegar o titulo da janela, ajuda no processo de validar tela e pegar mensagens que precisam ser tradas.)

<pre>
sapSession.GetTitle("titulo");
</pre>

-------------------------
## Modelo SAP Gui para gerenciamento de session
Um ponto interessante poder usar as sessions do SAP Gui para manipula-la, assim como usar o command para manipular certos processos dentro do SAP Gui:

<pre>
string secaoAtivaNova; //armazena a seção para ser comparada.
string secaoAtiva;// usado para armazenar a sessions atual ou ativa.
GuiConnection connection = new();
GuiSession SapSession = new();
SAPFEWSELib gui = new(path: local onde o sap está instalado, tempo para processar a aplicação, false);
if (gui.GetSessions().Count > 0)
{
    var sessions = gui.GetSessions();
    //Na secssion zero, podemos colocar a secssion para armazenar a primeira e depois adicionar novas a partir dela.
    //primeiro verificar se existe uma seção;
    if (sessions.Where(x => x.Key == _secaoAtiva).ToList().Count == 0)//Verifica se tem sessions ativa dentro da seção, e se não tiver entra, caso contrário pega o que está na sessions.
    {
        secaoAtivaNova = sessions.Select(x => x.Key).ToList()[0]; //Se usar a zero ela mantera a sua base para as outras.
        SapSession = (GuiSession)sessions[$"{secaoAtivaNova}"];
        connection = gui.GetConnection(0);
        SapSession.SendCommand("/oCTRL+N"); //Se já tiver, usa o camanda para abrir uma nova seção.
        sessions = gui.GetSessions(); //usado para pegar a lista de seções.
        secaoAtiva = sessions.Select(x => x.Key).ToList()[sessions.Count - 1]; //para conseguir pegar a próxima sessions. 
        SapSession = (GuiSession)sessions[$"{secaoAtiva}"]; //pega a session ativa.
        connection = gui.GetConnection(sessions.Count - 1); //Usada para pegar a sessions secundaria, após pegar a primeira e abrir uma nova.
        _secaoAtiva = secaoAtiva.Clone().ToString(); //Nesse caso, temos um clone para manter os dados aqui, e usar na próxima vez que passar nessa parte.
     }
     else
     {
        return _secaoAtiva; //Se já tiver mais de uma ele teria que ter o sessions ativa. 
     }
     if (gui.GetSessions().Count > 1 && SapSession.GetTitle().Contains("System Messages")) //analisando se existe mais de uma sessions e pegando a  mensagem do sistema para ver aviso.
     {
        SapSession.SendCommand("/nF12"); //Comando F12 pode ser usado para fechar tela.
     }
 }
 if (gui.GetSessions().Count == 0) //Analisa se não existe nenhuma session e se não tiver entra e abre uma. 
 {
     sessions = gui.GetSessions(); //Pega as sessions para começar a gerar uma.
     secaoAtivaNova = sessions.Select(x => x.Key).ToList()[0]; //Pega a chave baseado na primeira estancia na lista. 
     SapSession = (GuiSession)sessions[$"{secaoAtivaNova}"]; //Pega a session zero pelo nome da 
     if (SapSession.Children.Count > 1 && !SapSession.GetTitle().Contains("System Messages")) //Verifica se tem multipla session 
     {
        SapSession.Focus("wnd[1]/usr/radMULTI_LOGON_OPT2"); //seleciona o focus na opção do meio.
        SapSession.Click("wnd[1]/usr/radMULTI_LOGON_OPT2"); //clica no focus para apertar o botão.
        SapSession.Click("wnd[1]/tbar[0]/btn[0]"); //clica no botão.
     }
     _secaoAtiva = secaoAtiva.Clone().ToString();
     if (SapSession.GetTitle().Contains("System Messages"))
     {
        SapSession.SendCommand("/nSHIFT+F3");
     }
 }
</pre>

