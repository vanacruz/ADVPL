#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSADEL  ºAutor   Vanessa Cruz         º Data    29/07/19   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.   Permitir que o colaborador consiga deletar as bd7      º±±
±±º                de uma guia muito extensa.                             º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User Function PLSADEL()  //Funcao de usuário maximo 8 caracteres
Local cQuery    := ""
Local cImpresso := ""  
Local cLocal:= ""  
Local cPeg:= ""   
Local cNumero:=""
Local cAno:= ""  
Local cMes:= ""  

Local lInverte 		:= .F.
Private nOpca		:= 1
Private aCores 		:= {}



Private aCpos1    	:= {{"TRB_OK"	,"C",02,0 },;
						{"NUMIMP"	,"C",TamSx3("BD7_NUMIMP")[1],0},; //Retorna o Tamanho/Decimal/Tipo de um Campo Informado
						{"CODRDA"	,"C",6,0 },;
						{"NOMRDA"	,"C",20,0 },;
						{"SEQUEN"	,"C",03,0 },;
						{"CODUNM"	,"C",03,0 },;
						{"CODPAD"	,"C",02,0 },;
						{"CODPRO"	,"C",TamSx3("BD7_CODPRO")[1],0 },;
						{"DESPRO"	,"C",TamSx3("BD6_DESPRO")[1],0 },;
						{"QTDPRO"	,"N",08,2 },;
						{"VLRPAG"	,"N",08,2 },;
						{"DATPRO"	,"D",08,0 },;
                        {"ANOPAG"	,"C",04,0 },;
						{"MESPAG"	,"C",02,0 } }

Private aCampos1 	:= {{"TRB_OK"	,," "},;
						{"NUMIMP",	,"Impresso","@!"},;
						{"CODRDA",	,"Prestador","@!"},;
						{"NOMRDA",	,"Nome","@!"},;
						{"SEQUEN",	,"Seq.","@!"},;
						{"CODUNM",	,"Unidade","@!"},;
						{"CODPAD",	,"Tabela","@!"},;
						{"CODPRO",	,"Procedimento","@!"},;
						{"DESPRO",	,"Desc.Proc.","@!"},;
						{"QTDPRO",	,"Qtd.Proc.","@E 999,999.99"},;
						{"VLRPAG",	,"VL.Pago.","@E 999,999.99"},;
					    {"DATPRO",	,"Dt. Proced.","99/99/99"},;
						{"ANOPAG",	,"Ano","@!"},;
						{"MESPAG",	,"Mes","@!"}}
						
Private lValida := .T. 
Private cPerg   := "PLSADEL" 

CriaSX1()
if !Pergunte(cPerg,.T.,"Deletando itens de uma guia.",.T.)
       return()
endif    

if Empty(mv_par01) //Se o impresso estiver vazio 
	Alert("Informe o impresso corretamente.")
    Return
endif

if Empty(mv_par02) //Se o Local estiver vazio 
	Alert("Informe o Local completo  corretamente.")
    Return
endif

if Empty(mv_par03) //Se o numero da Peg estiver vazio 
	Alert("Informe o número da Peg completo.")
    Return
endif                         

if Empty(mv_par04) //Se o numero da peg estiver vazio 
	Alert("Informe o Numero da guia dentro da peg.")
    Return
endif

if Empty(mv_par05) //Se o Ano estiver vazio 
	Alert("Informe o Ano da competência.")
    Return
endif

if Empty(mv_par06) //Se o Mês estiver vazio 
	Alert("Informe o Mês da competência.")
    Return
endif    

cImpresso:=AllTrim(mv_par01) 
cLocal:=AllTrim(mv_par02)
cPeg:=AllTrim(mv_par03)   
cNumero:=AllTrim(mv_par04) 
cAno:=AllTrim(mv_par05)
cMes:=AllTrim(mv_par06)

nCont 	:= 0

//Buscando dados da guia bd7010 e bd6010:

cQuery := "SELECT  bd7_numimp as NUMIMP, "
cQuery += "bd7_codrda as CODRDA, "
cQuery += "bd7_nomrda as NOMRDA, "
cQuery += "bd7_sequen as SEQUEN, "
cQuery += "bd7_codunm as CODUNM, "
cQuery += "bd7_codpad as CODPAD, "
cQuery += "bd7_codpro as CODPRO, "
cQuery += "bd6_despro as DESPRO, "
cQuery += "bd6_qtdpro as QTDPRO, "
cQuery += "bd7_vlrpag as VLRPAG, "
cQuery += "bd7_datpro as DATPRO, "
cQuery += "bd7_anopag as ANOPAG, "
cQuery += "bd7_mespag as MESPAG  "
cQuery += "from " + RetSqlName('BD7')+ ",  "+Retsqlname('BD6')+ " where  " 
cQuery += RetSqlName('BD7') +".d_e_l_e_t_=' ' and "
cQuery += RetSqlName('BD6') +".d_e_l_e_t_=' '  "
cQuery += "and bd7_filial =' ' "
cQuery += "and bd6_filial=' ' "
cQuery += "and bd7_codldp = bd6_codldp "
cQuery += "and bd7_codpeg = bd6_codpeg "
cQuery += "and bd7_numero = bd6_numero "
cQuery += "and bd7_sequen = bd6_sequen "
cQuery += "and bd7_anopag = bd6_anopag "
cQuery += "and bd7_mespag = bd6_mespag "
cQuery += "and bd7_numimp = '"+cImpresso+"' "
cQuery += "and bd7_codldp = '"+cLocal+"' "
cQuery += "and bd7_codpeg = '"+cPeg+"' " 
cQuery += "and bd7_numero = '"+cNumero+"' "
cQuery += "and bd7_anopag = '"+cAno+"' "
cQuery += "and bd7_mespag = '"+cMes+"' "
cQuery += "order by bd7_sequen "


Plsquery(cQuery,"TMPBD7") // Rebece o resultado da query acima 

If  TMPBD7->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Verificar impresso: " +cImpresso+ "Impresso nao encontrado!") 
    TMPBD7->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o selct 
    Return 
EndIf  

cArqTrb1 := CriaTrab(aCpos1,.T.)
Processa({|| dbUseArea( .T.,, cArqTrb1, "TRB", if(.F. .OR. .F., !.F., NIL), .F. )},"Aguarde...","Criando tabela temporaria...")

Private cMarca := GetMark()

dbSelectArea("TMPBD7")//Define a área de trabalho especificada como ativa.
dbGoTop()//Posiciona a tabela corrente no primeiro registro lógico.
While !TMPBD7->(EOF())
	
	RecLock("TRB",.T.)  //Trava o registro 
	TRB->TRB_OK 	:= " "//cMarca		
	TRB->NUMIMP 	:= TMPBD7->NUMIMP
	TRB->CODRDA 	:= TMPBD7->CODRDA
	TRB->NOMRDA 	:= TMPBD7->NOMRDA
	TRB->SEQUEN 	:= TMPBD7->SEQUEN
	TRB->CODUNM 	:= TMPBD7->CODUNM
	TRB->CODPAD 	:= TMPBD7->CODPAD
	TRB->CODPRO 	:= TMPBD7->CODPRO
	TRB->DESPRO 	:= TMPBD7->DESPRO
	TRB->QTDPRO 	:= TMPBD7->QTDPRO
	TRB->VLRPAG 	:= TMPBD7->VLRPAG
	TRB->DATPRO 	:= Stod(TMPBD7->DATPRO)
	TRB->ANOPAG 	:= TMPBD7->ANOPAG
	TRB->MESPAG 	:= TMPBD7->MESPAG
	
	MsUnlock()//Distravando registro 
	
	TMPBD7->(dbSkip()) //Posicionando no primeiro registro 
	IncProc("Carregando tabela temporaria... " )
Enddo
											        
TMPBD7->(dbCloseArea())
dbSelectArea("TRB")//Define a área de trabalho especificada como ativa.
dbGoTop()//Posiciona a tabela corrente no primeiro registro lógico.

DEFINE MSDIALOG oDlg TITLE "Deletando itens de uma guia" From 00,00 TO 360,900 PIXEL OF GetWndDefault() Style DS_MODALFRAME	
oDlg:lEscClose     := .F. //Nao permite sair ao se pressionar a tecla ESC.


//  Monta CheckBox. Marcar desmarcar todos...                                
lCheck := .F.
oCheck := IW_CheckBox(015,001,"Marca/Desmarca Todos","lCheck")
oCheck:bChange := {|| MsAguarde( {|| PLSADEL() } ) }

oMark := MsSelect():New("TRB","TRB_OK","",aCampos1,@lInverte,@cMarca,{25, 05, 150,450},,,,,aCores)																					   				

//  Cria tela para selecao dos arquivos...			   


ACTIVATE MSDIALOG oDlg CENTERED ON INIT EnchoiceBar(oDlg,{|| nOpca := 1, oDlg:End()},{|| nOpca := 0, ODlg:End()}) 	

If nOpca == 1	
	                               
	lAtual_	:= .F.	
	nCont	:= 0     
	cNumimp	:= ""  
	cSequent:= ""
	cUpdate := "" 


​	
	TRB->(dbGoTop())	//Posiciona a tabela corrente no primeiro registro lógico.		
	
	While !TRB->(Eof())
		If !Empty(TRB->TRB_OK)
			nCont++	
		   lAtual_	:= .T.  
		   cNumimp	:= alltrim(TRB->NUMIMP)
		   cSequen  := alltrim(TRB->SEQUEN)
				
	    //Deletar itens da bd7010:
		      
	      cUpdate := "update bd7010 set bd7_filial='DI', D_E_L_E_T_='*' 
	      cUpdate += "where " + RetSqlName('BD7') +".d_e_l_e_t_=' ' "
	      cUpdate += "and bd7_numimp = '"+cNumimp+ "' "
	      cUpdate += "and bd7_codldp = '"+cLocal+ "' "		
	      cUpdate += "and bd7_codpeg = '"+cPeg+ "' " 
	      cUpdate += "and bd7_numero = '"+cNumero+"' "  
	      cUpdate += "and bd7_sequen = '"+cSequen+"' "
	      cUpdate += "and bd7_anopag = '"+cAno+"' "
	      cUpdate += "and bd7_mespag = '"+cMes+"' " 
	              			
	      TcSqlExec(cUpdate)
		 
		if TcSqlEXEC(cUpdate) < 0
	       
	      MsgAlert("Nao foi possível deletar este item!!!!!  "+TcSqlError())
	      Return   
	    endif
		
	     //Deletando itens da Bd6010:
	   	     
	   cUpdate := "update bd6010 set bd6_filial='DI', D_E_L_E_T_='*' 
	   cUpdate += "where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' "
	   cUpdate += "and bd6_numimp = '"+cNumimp+"' "
	   cUpdate += "and bd6_codldp = '"+cLocal+"' "		
	   cUpdate += "and bd6_codpeg = '"+cPeg+"' " 
	   cUpdate += "and bd6_numero = '"+cNumero+"' "
	   cUpdate += "and bd6_sequen = '"+cSequen+"' "
	   cUpdate += "and bd6_anopag = '"+cAno+"' "
	   cUpdate += "and bd6_mespag = '"+cMes+"' "
	   		
	   TcSqlExec(cUpdate)
		 
	    if TcSqlEXEC(cUpdate) < 0
	      
	       MsgAlert("Nao foi possível deletar este item!!!!!  "+TcSqlError()) 
	       Return
	    endif
	
	    Endif		
		TRB->(dbSkip())  //Desloca para outro registro na tabela corrente.
	
	End	

Endif        


TRB->(dbCloseArea())
FErase(cArqTrb1 + GetDbExtension())  // Deletando o arquivo
FErase(cArqTrb1 + OrdBagExt())       // Deletando índice 

if nCont > 0
	MsgInfo("Fim de processo de deleção!!!")	   
	MsgInfo("Recalculo do cabeçalho da guia,concluído!!!")	
else
    MsgInfo("Não foi marcado nenhum item !!!")    
endif

FWMsgRun( , {||SOMAGUI(cImpresso,cLocal,cPeg,cNumero,cAno,cMes)} , 'Processando' , 'Aguarde.Recalculando cabeçalho da guia...')      



/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÚÄÄÄÄÄÄÄÄÄÄÂÄÄÄÄÄÄÄÄÄÂÄÄÄÄÄÄÄÂÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÂÄÄÄÄÄÄÂÄÄÄÄÄÄÄÄÄÄÄÄ¿±±
±±  Funcao     | Autor  Vanessa             Data   31/07/2019               ±±
±±ÃÄÄÄÄÄÄÄÄÄÄÅÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄ´±±
±± Descri‡„o   Recalculando o cabeçalho da guia                             ±±
±±ÀÄÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/  
Static Function SOMAGUI (cImpresso,cLocal,cPeg,cNumero,cAno,cMes) //Função Static  será vista somente aqui.

Local Update := ""  
Local cQuery2 := ""

 //somando bd7010 para corrigir  o cabecalho da guia 

 cQuery2:=" select sum(bd7_vlrpag) pag, "
 cQuery2+=" sum(bd7_vlrglo) glosa , "
 cQuery2+=" bd7_numimp impresso , "
 cQuery2+=" bd7_tipgui tipoguia, "
 cQuery2+=" bd7_codldp local, "
 cQuery2+=" bd7_codpeg peg, " 
 cQuery2+=" bd7_numero numero, " 
 cQuery2+=" bd7_anopag ano, "
 cQuery2+=" bd7_mespag mes "
 cQuery2+=" from " +  RetSqlName('BD7') + " " "
 cQuery2+=" where "+ RetSqlName('BD7') +".d_e_l_e_t_=' ' "
 cQuery2+=" and bd7_filial = ' ' "
 cQuery2+=" and bd7_codldp = '"+cLocal+"' "
 cQuery2+=" and bd7_codpeg = '"+cPeg+"' "
 cQuery2+=" and bd7_numimp = '"+AllTrim(cImpresso)+"' " 
 cQuery2+=" and bd7_numero = '"+cNumero+"' "
 cQuery2+=" and bd7_anopag = '"+cAno+"' "
 cQuery2+=" and bd7_mespag = '"+cMes+"' "
 cQuery2+=" group by bd7_numimp,bd7_tipgui,bd7_codldp,bd7_codpeg,bd7_numero,bd7_anopag,bd7_mespag "

Plsquery(cQuery2,"TMPSOM") // Rebece o resultado da query com a somatoria da tabela bd7010   

 If  TMPSOM->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Falha ao somar a guia,será que foram deletados todos os itens da guia?") 
    TMPSOM->( DbCloseArea() ) //Fecha tabela TMPBD7T que contem o select        
    Return 
EndIf  
                                                                             
If TMPSOM->tipoguia='03'

 Begin Transaction  
       Update := " update "  + RetSqlName('BE4') + " "
	   Update += " set be4_vlrpag="+cValToChar(TMPSOM->pag)+", "
	   Update += " be4_vlrglo="+cValToChar(TMPSOM->glosa)+" "
	   Update += " where " +RetSqlName('BE4') +".d_e_l_e_t_ =' ' and be4_filial=' ' "
	   Update += " and be4_numimp ='"+AllTrim(TMPSOM->impresso)+"' "
	   Update += " and be4_codldp ='"+TMPSOM->local+"' "
	   Update += " and be4_codpeg ='"+TMPSOM->peg+"' "
	   Update += " and be4_numero ='"+TMPSOM->numero+"' "
	   Update += " and be4_anopag ='"+TMPSOM->ano+"' "
	   Update += " and be4_mespag ='"+TMPSOM->mes+"' "
    TCSQLEXEC(Update) 
 End Transaction   
 else
   Begin Transaction  
       Update := " update "  + RetSqlName('BD5') + " "
	   Update += " set bd5_vlrpag= "+cValToChar(TMPSOM->pag)+", "
	   Update += " bd5_vlrglo="+cValToChar(TMPSOM->glosa)+" "
	   Update += " where "+RetSqlName('BD5') +".d_e_l_e_t_ =' ' and bd5_filial=' ' "
	   Update += " and bd5_numimp ='"+AllTrim(TMPSOM->impresso)+"' "
	   Update += " and bd5_codldp ='"+TMPSOM->local+"' "
	   Update += " and bd5_codpeg ='"+TMPSOM->peg+"' "
	   Update += " and bd5_numero ='"+TMPSOM->numero+"' "
	   Update += " and bd5_anopag ='"+TMPSOM->ano+"' "
	   Update += " and bd5_mespag ='"+TMPSOM->mes+"' "
    TCSQLEXEC(Update) 
   End Transaction  

 EndIf 

TMPSOM->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

Return //fechando função de soma da guia 
                           
/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÚÄÄÄÄÄÄÄÄÄÄÂÄÄÄÄÄÄÄÄÄÂÄÄÄÄÄÄÄÂÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÂÄÄÄÄÄÄÂÄÄÄÄÄÄÄÄÄÄÄÄ¿±±
±±  Funcao     | Autor  Vanessa             Data   29/07/2019               ±±
±±ÃÄÄÄÄÄÄÄÄÄÄÅÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄ´±±
±± Descri‡„o   Marca/Desmarca todos os itens do Browse...                   ±±
±±ÀÄÄÄÄÄÄÄÄÄÄÁÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/  

Static Function PLSADEL()

MsProcTXT(If(lCheck,"Marcando","Desmarcando")+" Todos os itens ...")
ProcessMessage()

TRB->(dbGoTop())
While !TRB->(Eof())
	If lCheck
		RecLock("TRB", .F.)
		TRB->TRB_OK := cMarca
		TRB->(MsUnLock())	
	Else
		RecLock("TRB", .F.)
		TRB->TRB_OK := " "
		TRB->(MsUnLock())
	Endif
	TRB->(dbSkip())
End

TRB->(DbGoTop())

oMark:oBrowse:Refresh()

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    29/07/19       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.    Criando os parametros necessários para a rotina.          º±±
±±º             Serão informados pelo usuário:                            º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso         AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 


Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


//(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Numero Impresso?"),"","","mv_ch1","C",12,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Local completo ?"),"","","mv_ch2","C",04,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Peg completa   ?"),"","","mv_ch3","C",08,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"04",OemToAnsi("Numero da guia na Peg ?"),"","","mv_ch4","C",08,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Ano Competencia?"),"","","mv_ch5","C",04,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"06",OemToAnsi("Mes Competencia?"),"","","mv_ch6","C",02,0,0,"G","","","","","mv_par06","","","","","","","","","","","","","","","","",{},{},{})

return 