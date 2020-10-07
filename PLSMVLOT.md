#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSMVLOT  ºAutor  ³Vanessa Cruz        º Data ³  06/03/20    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.³ Update em parametro - Permitir que o colaborador consiga alterar o parametro º±±
±±ºMV_PLSMLOT para 1, sem intervenção da T.I  º±±          

 º±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
User Function PLSMVLOT()  //Funcao de usuário maximo 8 caracteres 

 Local aSay     := {}
 Local aButton  := {}  
 Local lOk      := .F.     
 Local cTitulo:= 'ALTERANDO PARÂMETRO MV_PLSMLOT PARA 1'    
 Local cDesc1 := 'Esta rotina tem como função alterar o conteúdo do parâmetro MV_PLSMLOT.'  
 Local cDesc2 := 'Conteúdos do parâmatro MV_PLSMLOT=1(sim) e MV_PLSMLOT=0 (Não).'     

 // Mensagens de Tela Inicial
aAdd( aSay, cDesc1 )
aAdd( aSay, cDesc2 )   

// Botoes Tela Inicial
aAdd(  aButton, {  1, .T., { || lOk := .T., FechaBatch() } } )
aAdd(  aButton, {  2, .T., { || lOk := .F., FechaBatch() } } )

FormBatch(  cTitulo,  aSay,  aButton,, 300, 550 ) 

If lOk   // Se clicou no botão ok ou seja for .T.                   
     Alterar()   
else  
    Msginfo("Ação Cancelada.") 
    return(.F.)
endif   

  Msginfo("Parâmetro alterado.")  

Static Function Alterar() //Função Static  será vista somente aqui.

DBSELECTAREA('SX6')
SX6->(dbSetOrder(1))
if SX6->(DbSeek(xFilial("SX6") + 'MV_PLSMLOT',.F.))
	SX6->(RECLOCK('SX6',.F.)) //Editando o parametro 
		SX6->X6_CONTEUD := '1'
	SX6->(MSUNLOCK())
endif	


Return()  