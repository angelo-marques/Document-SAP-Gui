# Document-SAP-Gui
Fonte SAP GUI Scripting API: https://help.sap.com/docs/sap_gui_for_windows/b47d018c3b9b45e897faf66a6c0885a8/babdf65f4d0a4bd8b40f5ff132cb12fa.html?locale=en-US&q=PressToolbarContextButton
Fonte da Microsoft SAP Gui: https://learn.microsoft.com/pt-pt/power-automate/guidance/rpa-sap-playbook/reviewing-generated-code
# Compartilhando Experiencias do SAP GUI Scripting API usando C#.
# Resumo:
* Tive uma participação em um projeto criando robôs que usam o SAP para extrair dados e/ou manipular o mesmo, de forma obter dados dele.
  Nesse caso tive que enfrentar uma falta de documentação e alguns pontos complicados nessa jornada. Apesar de uma grande documentação em outras linguagens, alguns pontos ficaram complicados para serem entendidos em C#. 
  Acredito que não deve ser o problema da maioria, mas quero deixar documentado para aqueles que tiverem o mesmo problema.

# Transformação de códigos
No sentido do código feito na sua origem, ou fazer um processo de forçar o padrão para aceitar o modelo já usado em VB por exemplo:

# Formatos VB e Java:
 (GuiTextField)SAPActive.SapSession.ActiveWindow.FindByName("RSYST-MANDT", "GuiTextField");
 (GuiTextField) = dependa da dll, mas ele é o mais próximo da linguagem em Java e VB, logo a tipagem é a forma de obter o mesmo padrão de código podendo ser usado.

# Formato do C#:
No formato de importação ele fica usando os dados encapsulados no formato original e sendo transcritos para o modelo mais simples de usar.  
O  então vão para GuiSession.Focus() ou seja em um modelo simples, como vemos no exemplo:
'
  GuiSession sapSession = new();
  sapSession.Focus("wnd[0]/scriptsap");
'
Como ele se compara ao SetFocus mas é também mais amplo porque se reuni nele alguns pontos como:
GuiCTextField | GuiTextField | GuiLabel | GuiShell
Os guais passam por um selector : SelectorType que tráz o que é desejado.
Ou seja ele pode ser usado para qualquer um desses elementos do SAP.
Por exemplo: SapSession.Focus("wnd[0]/usr/cnt/shellcont/shell", TableSelectMethod.SelectRows, "0");
Que geralmente é usado para selecionar uma Row (coluna do dentro de um Shell do SAP) e nesse caso com C# precisa dessa função para chegar nele.

---------------

# Mesclagem:
  Mas é bom pontuar que também podemos usar mesclagem dentro do c# como:
   Se for manipular um o shell que geralmente é usado para gerar modelos de dados em excel usando (GuiShell).
   var shellToolbarContextButton = (GuiShell)GuiSession.ActiveWindow.FindById("wnd[0]/usr/cntlNFE_CONTAINER/shellcont/shell");
 
 Vai ter retornar o manipulador do componente, nesse caso você passar por inserir ele dentro dizendo que é o Grid, nessa caso o as, e um Alias que faz ele entender o que ele é.
 var btnToolbarContextButton = shellToolbarContextButton as GuiGridView;

 Nesse caso vai conseguir acessar o menu e pressionar o botão que deseja como:
 btnToolbarContextButton?.PressToolbarContextButton("DISPLAY");
 btnToolbarContextButton?.SelectContextMenuItem("DOWNLOAD");
 
 Base de referencia direto do SAP Document: https://help.sap.com/docs/sap_gui_for_windows/b47d018c3b9b45e897faf66a6c0885a8/4af24c3281fb4d6a809e53238562d3b2.html?locale=en-US&q=PressToolbarContextButton

-------------
sapSession.Click("scriptsap");
(Se compara ao Text, sendo que ele usa o FindById para encontrar o que precisa dentro do tipos de textos que podem ser usados.)
sapSession.GetText("scriptsap");
O Session GetText contem dentro dele os:
GuiBarChart | GuiBox | GuiButton | GuiCalendar | GuiChart | GuiCheckBox | GuiComboBox | GuiCTextField | GuiCustomControl | GuiLabel | GuiOkCodeField | GuiPasswordField | GuiRadioButton | GuiTab | GuiTextedit | GuiTextField | GuiUserArea | GuiShell

----------------------
(se refere ao text, e serve para inserir texto nos campos diversos do sap.)
sapSessrion.SetTex("scriptsap");
GuiBarChart | GuiBox | GuiButton | GuiCalendar | GuiChart | GuiCheckBox | GuiComboBox | GuiCTextField | GuiCustomControl | GuiLabel | GuiOkCodeField | GuiPasswordField | GuiRadioButton | GuiTab | GuiTextedit | GuiTextField | GuiUserArea | GuiShell 

 
sapSession.SendCommand(“/oCTRL+N”)


