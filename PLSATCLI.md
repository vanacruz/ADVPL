#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±± ºPrograma  PLSATCLI  ºAutor   Vanessa Cruz         º Data    10/06/19  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Update                                                          º±±
±±º      Objetivo: O atendimento irá ulizar esta rotina, atualizando      º±±  
±±º                o campo : atualizado='N' que ira disparar um job       º±±
±±º                que solicita os dados do cliente.                      º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
#include "protheus.ch" 

User Function PLSATCLI()  //Funcao de usuário maximo 8 caracteres
Local cQuery     := ""
Local cUnimed    :=""
Local cCartao    := ""  
Local cTipo      := ""

Private cPerg   := "PLSATCLI" 

CriaSX1()

if !Pergunte(cPerg,.T.,"Disparando atualização de cliente(s) de outra Operadora ")
       return()
endif    
          
if (mv_par03==1) //Então a atualização é somente para este cliente
 if Empty(mv_par01)// Se o Codigo da Unimed do cliente estiver em branco
	   Alert("Para atualizar o cliente, preencha todos os campos.")
       Return 
 endif
 if Empty(mv_par02) //Se a matricula do cliente estiver em branco
   Alert("Para atualizar o cliente, preencha todos os campos.")
   Return
 endif    
Else //**************Atulizar Todos os  cliente da Operadora ******************** 
    if Empty(mv_par01)// Se o Codigo da Unimed do cliente estiver em branco
	  Alert("Para atualizar a Unimed deve ser preenchido somente o campo : Unimed do cliente.")
      Return
     endif
     if !Empty(mv_par02)// se a matricula do cliente estiver preenchida.
	  Alert("Para atualizar a Unimed deve ser preenchido somente o campo : Unimed do cliente.")
      Return
     endif  
endif     

cUnimed  := AllTrim(mv_par01)
cCartao  := AllTrim(mv_par02)
cTipo    :=cValtochar(mv_par03)
                                  
 If  mv_par03 == 1  //**************Selecionando o cliente ********************

  cQuery := "select UNIMED,MATANT,ATUALIZADO "
  cQuery += "from TB_AUTINTERCAMBIO "  
  cQuery += "where UNIMED ='"+cUnimed+"' "
  cQuery += "and MATANT ='"+cCartao+"' "  

 Else   //**************Selecionando a Op. ********************
    
  cQuery := "select UNIMED,MATANT,ATUALIZADO "
  cQuery += "from TB_AUTINTERCAMBIO "  
  cQuery += "where UNIMED ='"+cUnimed+"' "     


Endif  //Fechando o if do tipo =1   
                             
Plsquery(cQuery,"TMPTB_") // Rebece o resultado da query acima          

If  TMPTB_->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar os dados informados. " ) 
    TMPTB_->( DbCloseArea() ) //Fecha tabela TMPTB_ 
    Return 
EndIf 

TMPTB_->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

//cMensagem := "Este programa atualiza as informações do cliente de acordo com a Unimed dele."  + chr(13)+chr(10)   
//MSGINFO(cMensagem)

FWMsgRun( , {||AJUSCLI(cUnimed,cCartao,cTipo)} , 'Processando' , 'Aguarde...')  //função para o update 
	  
	  
cMensagem := "Processo no Protheus finalizado, aguardar retorno da Unimed." + chr(13)+chr(10) 

MSGINFO(cMensagem)
                   	 
/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor   Vanessa Cruz          º  Data   10/06/19          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Update                     º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/	 

Static Function AJUSCLI(cUnimed,cCartao,cTipo) //Função Static  será vista somente aqui.

Local Update := ""  

If  cTipo == '1' //**************Atulizar somente o cliente em questão ********************
	
	Begin Transaction
	
	Update := "update TB_AUTINTERCAMBIO  "
	Update += "set  ATUALIZADO='N' "
	Update += "where UNIMED ='"+cUnimed+"' "
	Update += "and MATANT='"+cCartao+"' "
	
	TCSQLEXEC(Update)
	
	End Transaction
Else
	If cTipo == '2'//**************Atulizar Todos os  cliente da Operadora ********************
		
		Begin Transaction
		
		Update := "update  TB_AUTINTERCAMBIO  "
		Update += "set ATUALIZADO='N'"
		Update += "where UNIMED ='"+cUnimed+"' "
		
		TCSQLEXEC(Update)  
		
	End Transaction

 Endif  //Fechando o if do tipo = 2
Endif  //Fechando o if do tipo =1          

Return //fechando função de Update da guia.

 /*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    07/11/18       º±±
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

PutSx1(cPerg,"01",OemToAnsi("Unimed do cliente: "),"","", "mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Matricula na Unimed do cliente ") ,"","", "mv_ch2","C",17,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Deseja Corrigir:")  ,"","","mv_ch3","N",01,0,0,"C","","","","","mv_par03","Somente este","","","","Toda Unimed","","", "","","","","","","","","",{},{},{})

return 