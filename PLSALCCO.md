#include "protheus.ch" 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±± ºPrograma  PLSALCCO  ºAutor   Vanessa Cruz         º Data    26/10/17  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Update do campo ba1_codcco               º±±
±±º      pois quando o cliente bloqueado não é permitido no          º±±
±±       fonte padrão realizar sua alteração                              º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User Function PLSALCCO()  //Funcao de usuário maximo 8 caracteres
Local cQuery    := ""
Local cCartao   := ""  
Local cCco      := "" 
Local cNvcco    := ""
Local cNome     := ""
Local cdtblo    := ""

Private cPerg   := "PLSALCCO" 

CriaSX1()
if !Pergunte(cPerg,.T.,"Alterando Numero do CCo de um cliente bloqueado ")
       return()
endif    
      
if Empty(mv_par01) //Se o código do cartão estiver vazio 
	Alert("Informe o cartao do cliente corretamente.")
    Return
endif


cCartao   := AllTrim(mv_par01) 
cNvcco    := mv_par02 

//Verificando  os campos digitados pelo usuário: cartaõ cliente + CCO 

cQuery := "select * from siga.ba1010 "
cQuery += "where ba1010.d_e_l_e_t_=' ' and ba1_codemp <>'0001' and ba1_filial =' ' and ba1_datblo<>' ' "
cQuery += "and ba1_codint||ba1_codemp||ba1_matric||ba1_tipreg||ba1_digito = '"+cCartao+"' "
Plsquery(cQuery,"TMPBA1") // Rebece o resultado da query acima 

If  TMPBA1->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Verificar  o cartao: " +cCartao +  " Dado nao encontrado ou cliente não esta bloqueado" ) 
    TMPBA1->( DbCloseArea() ) //Fecha tabela TMPBA1 que contem o select 
    Return 
EndIf  
       
cNome  := TMPBA1->ba1_nomusr
cdtblo := Dtoc(TMPBA1->ba1_datblo)
cCco   := TMPBA1->ba1_codcco

TMPBA1->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| PLSALCCO(cCartao,cNvcco)} , 'Processando' , 'Aguarde. Atualizando o CCO deste cliente...')  //chamando a função que realmente vai fazer o update  

//Alert("concluido")

cMensagem := "Cliente: " + cCartao + chr(13)+chr(10)
cMensagem += "Nome: " +  cNome + chr(13)+chr(10)
cMensagem += "Data Bloqueio do cliente: " +  cdtblo + chr(13)+chr(10)
cMensagem += "Cco anterior : " + cCco + chr(13)+chr(10)
cMensagem += "Novo CCo: " + cNvcco + chr(13)+chr(10)
MSGINFO(cMensagem)

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSALCCO  ºAutor   Vanessa Cruz          º  Data   24/10/17  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Update    º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function PLSALCCO(cCartao,cNvcco) //Função Static  será vista somente aqui.
Local Update := ""    
procregua(000) 

 Begin Transaction  
 	
     //update -Tabela ba1010
     
    Update := "update ba1010 "
    Update += "set ba1_codcco = '"+cNvcco+"' "
    Update += "where ba1010.d_e_l_e_t_=' ' and ba1_codemp <>'0001' and ba1_filial =' ' and ba1_datblo<>' ' "
    Update += "and ba1_codint||ba1_codemp||ba1_matric||ba1_tipreg||ba1_digito = '"+cCartao+"' "
    TCSQLEXEC(Update) 
 End Transaction 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    26/10/17       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.        Criando os parametros necessários para a rotina.          º±±
±±º             Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 


Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  
              
    //(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Cartao cliente  ?")                ,"","", "mv_ch1","C",17,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
aHelpPor := {"Digite corretamente o Número do CCo;",;
             "Digite espaço em Branco para apagar; ",;
             "o número de CCo"}
PutSx1(cPerg,"02",OemToAnsi("Novo Numero de CCO   ?")           ,"","", "mv_ch2","C",12,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",aHelpPor,{},{})

return 