#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    LIMPCAMP  ºAutor   Vanessa        º Data    05/07/19       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Limpar o conteúdo dos campos:                              º±±
±±º            ba3_tippag,ba3_bcocli,ba3_agecli,ba3_ctacli,ba3_portad,    º±±
±±º            ba3_agedep,ba3_ctacor de uma família desligada.            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function LIMPCAMP()  //Funcao de usuário maximo 8 caracteres

Local cQuery     := ""                                  
Local cOperadora := ""
Local cEmpresa   := ""
Local cMatric    := "" 

Private cPerg:= "LIMPCAMP" 

CriaSX1()  
             
if !Pergunte(cPerg,.T.,"Limpando Campos Familia Desligada",.T.)
       return()
endif  

if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03) //Se ambos os campos estiverm vazios
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif   

if Empty(mv_par01) // Se a operadora estiver vazia  
	Alert("Informe a Operadora")
 Return
endif

if Empty(mv_par02) // Se a empresa estiver vazia 
	Alert("Informe a empresa.")
 Return
endif

if Empty(mv_par03) // Se a matricula estiver vazia
	Alert("Informe a matricula.")
    Return
endif  

cOperadora:= AllTrim(mv_par01)
cEmpresa:= AllTrim(mv_par02)
cMatric := AllTrim(mv_par03)  

cQuery:= " select ba3_tippag,ba3_bcocli,ba3_agecli,ba3_ctacli,ba3_portad,ba3_agedep,ba3_ctacor,ba3_datblo "
cQuery +=" from " +  RetSqlName('BA3') + " " 
cQuery +=" where " + RetSqlName('BA3') + ".d_e_l_e_t_=' ' "
cQuery +=" and ba3_filial = ' ' "
cQuery +=" and ba3_codint= '"+cOperadora+"' "
cQuery +=" and ba3_codemp= '"+cEmpresa+"' "
cQuery +=" and ba3_matric= '"+cMatric+"' "
cQuery +=" and ba3_datblo<>' ' "


Plsquery(cQuery,"TMPBA3") // Rebece o resultado da query acima   

If  TMPBA3->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Por favor verifique os dados digitados pois não encontramos família com estes dados. Esta família esta desligada? " ) 
    TMPBA3->( DbCloseArea() ) //Fecha tabela TMPBA3 que contem o select 
    Return 
EndIf  

TMPBA3->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 


cMensagem := "Esta rotina irá apagar/limpar os campos,somente da família desligada:  " + chr(13)+chr(10) 
cMensagem += "Forma de Pagto(ba3_tippag)  " + chr(13)+chr(10) 
cMensagem += "Banco do cliente(ba3_bcocli) " + chr(13)+chr(10) 
cMensagem += "Agencia do clinte(ba3_agecli) " + chr(13)+chr(10) 
cMensagem += "Conta do cliente(ba3_ctacli) " + chr(13)+chr(10) 
cMensagem += "Banco da Operadora(ba3_portad) " + chr(13)+chr(10) 
cMensagem += "Agencia da Operadora(ba3_agedep) " + chr(13)+chr(10)
cMensagem += "Conta da Operadora(ba3_ctacor) " + chr(13)+chr(10)    

MSGINFO(cMensagem)

FWMsgRun( , {||LIMPCAMP(cOperadora,cEmpresa,cMatric)} , 'Processando' , 'Aguarde. Limpando dados...')  //chamando a função que realmente vai fazer o update 

//Alert("Concluido")

cMensagem := "Atualização completa! " + chr(13)+chr(10) 

MSGINFO(cMensagem)
                   
Return     

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  LIMPCAMP  ºAutor   Vanessa Cruz          º  Data   05/07/19  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Update     º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/


Static Function LIMPCAMP(cOperadora,cEmpresa,cMatric) //Função Static  será vista somente aqui.

Local Update := ""  


 Begin Transaction  
 	
     //update - Tabela ba3010 
     
    Update := " update " + RetSqlName('BA3') + " " 
    Update += " set ba3_tippag=' ' ,ba3_bcocli=' ' , ba3_agecli=' ' , ba3_ctacli=' ',ba3_portad=' ' ,ba3_agedep=' ',ba3_ctacor=' '  
    Update += " where  " + RetSqlName('BA3') +".d_e_l_e_t_=' ' and ba3_filial = ' '  "
    Update += " and ba3_codint= '"+cOperadora+"' "
    Update += " and ba3_codemp= '"+cEmpresa+"' "
    Update += " and ba3_matric= '"+cMatric+"' "
    Update += " and ba3_datblo<>' ' "
    TCSQLEXEC(Update) 

 End Transaction 


Return //fechando função 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    05/10/18       º±±
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

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Operadora "),"","","mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Empresa ")  ,"","","mv_ch2","C",04,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Matricula "),"","","mv_ch3","C",06,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{}) 

return 