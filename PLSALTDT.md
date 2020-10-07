#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSALTDT  ºAutor   Vanessa Cruz         º Data    24/07/19   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Update :  Alterando data de previsao de internação e data        º±±
±±º                       data do procedimento : campos : BEA_DATPRO,                      º±±
±±                         BE4_DATPRO,	BE4_PRVINT        º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User Function PLSALTDT()  //Funcao de usuário maximo 8 caracteres
Local cQuery    := ""
Local cImpresso := ""  
Local cData     := ""    

Private lValida := .T. 
Private cPerg   := "PLSALDT" 

CriaSX1()
if !Pergunte(cPerg,.T.,"Alterando Previsão de internação e data informada na prorrogação",.T.)
       return()
endif    

if Empty(mv_par01) //Se o impresso estiver vazio 
	Alert("Informe o impresso corretamente.")
    Return
endif

cImpresso :=AllTrim(mv_par01) 
cData     :=Dtos(mv_par02)  



cQuery := "select *  "
cQuery += "from " +  RetSqlName('BEA') + " " 
cQuery +=" where " + RetSqlName('BEA') +".d_e_l_e_t_=' ' "
cQuery += "and bea_numimp  = '"+cImpresso+"' "

Plsquery(cQuery,"TMPBEA") // Rebece o resultado da query acima 


If  TMPBEA->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Verificar impresso: " +cImpresso+ "Impresso nao encontrado ") 
    TMPBEA->( DbCloseArea() ) //Fecha tabela TMPBEA que contem o select 
    Return 
EndIf  

TMPBEA->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| PLSALTDT(cImpresso,cData)} , 'Processando' , 'Aguardo ajustando data ...')  //chamando a função que realmente vai fazer o update  

//Alert("concluido")
MSGINFO("Alteração Finalizada...", "Verifique a guia!")
Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSALTDT  ºAutor   Vanessa Cruz         º  Data   15/05/17  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Update                             º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function PLSALTDT(cImpresso,cData) //Função Status estar será vista somente aqui.
Local Update := ""    
procregua(000) 

 Begin Transaction  
 	
     //update 01 -Tabela bea010
     
    Update := "update bea010 "
    Update += "set bea_datpro = '" +cData+ "' "
    Update += " where " + RetSqlName('BEA') +".d_e_l_e_t_=' ' "
    Update += "and bea_numimp = '" +cImpresso+ "' "
    
    TCSQLEXEC(Update) 

 End Transaction 

 Begin Transaction     
	//update 02 - Tabela be4010
	
    Update := "update be4010 "
    Update += "set be4_datpro= '" +cData+ "',be4_prvint ='" +cData+ "' "
    Update += "where " + RetSqlName('BE4') +".d_e_l_e_t_=' ' "
    Update += "and be4_numimp = '" +cImpresso+ "' "

   TCSQLEXEC(Update) 
 End Transaction   
 Return 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    15/05/17       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.        Criando os parametros necessários para a rotina.          º±±
±±º                 Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 


Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


//(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Numero Impresso ?")        ,"","", "mv_ch1","C",12,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Nova data       ?")        ,"","", "mv_ch2","D",08,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})

return 