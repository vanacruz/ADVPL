#include "protheus.ch" 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSALSIB  ºAutor  Vanessa Cruz         º Data   24/10/17   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Premitir atualizaçao do campo ba1_YSTSIB               º±±
±±º          pois quando o cliente esta bloqueado não é permitido no          º±±
±±           fonte padrão realizar sua alteração                              º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User Function PLSALSIB()  //Funcao de usuário maximo 8 caracteres
Local cQuery    := ""
Local cCartao   := ""  
Local cCco      := "" 
Local cStatus   := ""
Local cNome     := ""
Local cdtblo    := ""

Private cPerg   := "PLSALSIB" 

CriaSX1()
if !Pergunte(cPerg,.T.,"Alterando Status do Sib de um  cliente bloqueado ")
       return()
endif    

if Empty(mv_par01) .And. Empty(mv_par02) //Se ambos os campos estiverm vazios
    Alert("Informe o cartão do cliente  e o novo status do sib na ANS.")
    Return
endif 
      
if Empty(mv_par01) //Se o código do cartão estiver vazio 
	Alert("Informe o cartao do cliente corretamente.")
    Return
endif
if Empty(mv_par02) // Se o novo Status do Sib estiver vazio 
	Alert("Informe o novo Status do Sib na ANS para este cliente.")
    Return
endif


cCartao   := AllTrim(mv_par01) 
cStatus   := AllTrim(mv_par02) 

//Verificando  os campos digitados pelo usuário: cartaõ cliente + CCO 

cQuery := "select * from ba1010 "
cQuery += "where ba1010.d_e_l_e_t_=' ' and ba1_codemp <>'0001' and ba1_filial =' ' and ba1_datblo<>' ' "
cQuery += "and ba1_codint||ba1_codemp||ba1_matric||ba1_tipreg||ba1_digito = '"+cCartao+"' "

Plsquery(cQuery,"TMPBA1") // Rebece o resultado da query acima 


If  TMPBA1->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Verificar  o cartao: " +cCartao + " Dado nao encontrado ou cliente não esta bloqueado" ) 
    TMPBA1->( DbCloseArea() ) //Fecha tabela TMPBA1 que contem o select 
    Return 
EndIf  
       
cNome  := TMPBA1->ba1_nomusr
cdtblo := Dtoc(TMPBA1->ba1_datblo)  
cCco   := TMPBA1->ba1_codcco

TMPBA1->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| PLSALSIB(cCartao,cStatus)} , 'Processando' , 'Aguarde. Atualizando o Status do Sib deste cliente...')  //chamando a função que realmente vai fazer o update  



//Alert("concluido")

cMensagem := "Cliente: " + cCartao + chr(13)+chr(10)
cMensagem += "Nome: " +  cNome + chr(13)+chr(10)
cMensagem += "Data Bloqueio do cliente: " +  cdtblo + chr(13)+chr(10)
cMensagem += "Novo Status do Sib: " + cStatus + chr(13)+chr(10)
MSGINFO(cMensagem)

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSALSIB  ºAutor   Vanessa Cruz          º ³Data   24/10/17  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Update                º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function PLSALSIB(cCartao,cStatus) //Função Static  será vista somente aqui.
Local Update := ""    
procregua(000) 

 Begin Transaction  
 	
     //update -Tabela ba1010
     
    Update := "update ba1010 "
    Update += "set ba1_ystsib = '"+cStatus+"' "
    Update += "where ba1010.d_e_l_e_t_=' ' and ba1_codemp <>'0001' and ba1_filial =' ' and ba1_datblo<>' ' "
    Update += "and ba1_codint||ba1_codemp||ba1_matric||ba1_tipreg||ba1_digito = '"+cCartao+"' "
    TCSQLEXEC(Update) 
 End Transaction 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data   24/10/17       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Criando os parametros necessários para a rotina.          º±±
±±º            Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 


Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa,               cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Cartao cliente  ?")          ,"","", "mv_ch1","C",17,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})

aHelpPor := {"  0=N ENV;1=ATV;2=EXC;3=ENV INC;4=ENV ALT;",;
                "5=ENV EXC;6=ENV REA;7=CRI INC;8=CRI ALT; ",;
                "9=CRI EXC;A (maiúsculo)= CRI REA; ",;
                "Y (maiúsculo) = Ctr Ati "}    


Aadd(aHelpPor)
PutSx1(cPerg,"02",OemToAnsi("Novo Status Sib na ANS  ?")  ,"","", "mv_ch2","C",01,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",aHelpPor,{},{})
    

return 