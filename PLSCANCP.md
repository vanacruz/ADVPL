#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSCANCP  ºAutor   Vanessa Cruz         º Data    01/10/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Permitir que o colaborador consiga cancelar vários     º±±
±±º               itens de prorrogação em uma unica ação.                 º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

User Function PLSCANCP()  //Funcao de usuário maximo 8 caracteres    

Local cImpresso := ""  
Private nOpca	:= 1
Private aCores 	:= {}  

Private aCpos1    	:= {{"TRB_OK"	,"C",02,0 },;
						{"NUMIMP"	,"C",TamSx3("BD7_NUMIMP")[1],0},; //Retorna o Tamanho/Decimal/Tipo de um Campo Informado
						{"GUIA"  	,"C",18,0 },;
						{"TIPO"     ,"C",10,0 },;
						{"SEQUEN"	,"C",03,0 },;
						{"CODPAD"	,"C",02,0 },;
						{"CODPRO"	,"C",TamSx3("BD7_CODPRO")[1],0 },;
						{"DESPRO"	,"C",TamSx3("BD6_DESPRO")[1],0 },;
						{"QTDPRO"	,"N",08,2 },; 
						{"DATPRO"	,"D",08,0 },; 
						{"HORPRO"	,"C",05,0 },;
						{"NRTROL"	,"C",10,0 },;
						{"CANC"     ,"C",03,0 },; 
						{"CHAVE"	,"N",17,0 }}
						
//Estes vao aparecer na tela para o usuario :
						
Private aCampos1 	:= {{"TRB_OK"	,," "},;
						{"NUMIMP",	,"Impresso","@!"},;
						{"GUIA",	,"Autorizacao","@!"},;
						{"TIPO",    ,"Tipo","@!"},;
						{"SEQUEN",	,"Seq.","@!"},;
						{"CODPAD",	,"Tabela","@!"},;
						{"CODPRO",	,"Procedimento","@!"},;
						{"DESPRO",	,"Desc.Proc.","@!"},;
						{"QTDPRO",	,"Qtd.Proc.","@E 999,999.99"},;
					    {"DATPRO",	,"Dt. Proced.","99/99/99"},;
					    {"HORPRO",	,"Hora","@!"},;
						{"NRTROL",	,"Transacao","@!"},;
						{"CANC",    ,"Cancelado","@!"}} 
						
Private lValida := .T. 
Private cPerg   := "PLSCANCP" 

CriaSX1()     
            


if !Pergunte(cPerg,.T.,"Cancelando prorrogação SEM comunicação on line.",.T.)
       return()
endif
    
    cMensagem := "Aqui serão canceladas somente as prorrogações que NÃO tem comunicacao On-line "  + chr(13)+chr(10)
    cMensagem += " e que ainda NÃO foram canceladas." + chr(13)+chr(10)
    MSGINFO(cMensagem)  


​	
if Empty(mv_par01) //Se o impresso estiver vazio 
​	Alert("Informe o impresso corretamente.")
​    Return
endif

cImpresso:=AllTrim(mv_par01)

FWMsgRun( , {||Validar(cImpresso)} , 'Processando' , 'Aguarde. Validando impresso...')  //chamando a função que realmente vai fazer o update  
                         
cMensagem := "Processo finalizado..."  + chr(13)+chr(10)
MSGINFO(cMensagem) 

Return

Static Function fUpdate()       

Local lInverte  := .F. 
Local cUpdate := " "  
Local cData  := DtoC(date())+' '+Time()

nCont 	:= 0

cArqTrb1 := CriaTrab(aCpos1,.T.)
Processa({|| dbUseArea( .T.,, cArqTrb1, "TRB", if(.F. .OR. .F., !.F., NIL), .F. )},"Aguarde...","Criando tabela temporaria...")

Private cMarca := GetMark()

dbSelectArea("TMPBQV")
dbGoTop()
While !TMPBQV->(EOF())
	
	RecLock("TRB",.T.)
	TRB->TRB_OK 	:= " "//cMarca		
	TRB->NUMIMP 	:= TMPBQV->NUMIMP  
	TRB->GUIA    	:= TMPBQV->GUIA   
	TRB->TIPO 	    := TMPBQV->TIPO 
	TRB->SEQUEN 	:= TMPBQV->SEQUEN
	TRB->CODPAD 	:= TMPBQV->CODPAD
	TRB->CODPRO 	:= TMPBQV->CODPRO
	TRB->DESPRO 	:= TMPBQV->DESPRO
	TRB->QTDPRO 	:= TMPBQV->QTDPRO
	TRB->DATPRO 	:= Stod(TMPBQV->DATPRO)
	TRB->HORPRO 	:= TMPBQV->HORPRO
	TRB->NRTROL 	:= TMPBQV->NRTROL  
	TRB->CANC 	    := TMPBQV->CANC    
	TRB->CHAVE  	:= TMPBQV->CHAVE
	
	MsUnlock()
	
	TMPBQV->(dbSkip())
	IncProc("Carregando tabela temporaria... " )
	nCont++
Enddo
											        
TMPBQV->(dbCloseArea())

dbSelectArea("TRB")//Define a área de trabalho especificada como ativa.
dbGoTop()//Posiciona a tabela corrente no primeiro registro lógico.


DEFINE MSDIALOG oDlg TITLE "Cancelar prorrogações que não tem  comunicação on-line" From 00,00 TO 360,900 PIXEL OF GetWndDefault() Style DS_MODALFRAME	
oDlg:lEscClose     := .F. //Nao permite sair ao se pressionar a tecla ESC.

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Monta CheckBox. Marcar desmarcar todos...                                 
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

lCheck := .F.
oCheck := IW_CheckBox(015,001,"Marca/Desmarca Todos","lCheck")
oCheck:bChange := {|| MsAguarde( {|| PLSCANCP() } ) }

oMark := MsSelect():New("TRB","TRB_OK","",aCampos1,@lInverte,@cMarca,{25, 05, 150,450},,,,,aCores)																					   				

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Cria tela para selecao dos arquivos...			                          
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ
ACTIVATE MSDIALOG oDlg CENTERED ON INIT EnchoiceBar(oDlg,{|| nOpca := 1, oDlg:End()},{|| nOpca := 0, ODlg:End()}) 
If nOpca == 1	
	                               
	lAtual_	:= .F.	
	nCont	:= 0     
	cNumimp	:= ""
	cUpdate := ""
	
	TRB->(dbGoTop())	//Posiciona a tabela corrente no primeiro registro lógico.		
	
	While !TRB->(Eof())
		If !Empty(TRB->TRB_OK)
			nCont++	     
	      	ProcessMessage()
		   lAtual_	:= .T.  
		   cNumimp	:= alltrim(TRB->NUMIMP)
				
	   //Cancelar itens da prorrogação
		
	   Begin Transaction
	     
	      cUpdate := " update bqv010 set bqv_cancel='1', " 
		  cUpdate += " bqv_obser1='CANCELADO PELA ROTINA PLSCANCP por:  "+cUserName+" Dt: "+cData+"' "  
	      cUpdate += " where " + RetSqlName('BQV') +".d_e_l_e_t_=' ' "
	      cUpdate += " and bqv_filial=' '   and bqv_nrtrol=' ' and   "
		  cUpdate += " r_e_c_n_o_ ="+cValToChar(TRB->CHAVE) "
		
	     TcSqlExec(cUpdate)
		 
		 End Transaction 
	
	    Endif		
		TRB->(dbSkip())
	Enddo	
 Endif
TRB->(dbCloseArea()) 
        
FErase(cArqTrb1 + GetDbExtension())  // Deletando o arquivo
FErase(cArqTrb1 + OrdBagExt())       // Deletando índice			

Return //Fecha a rotina não faça mais nada 	

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Função de  Validação dos Dados                                           
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function Validar(cImpresso) //Função Static  será vista somente aqui.
Local cQuery     := ""
Local cQuery1    := ""
Local cQuery2    := ""

// validação  1 : Verificando se guia esta no local 0003

cQuery1 :="select bd7_codldp,bd7_codpeg,bd7_numero "
cQuery1 +="from " +  RetSqlName("BD7") + " " 
cQuery1 +="where  " + RetSqlName("BD7") +".d_e_l_e_t_=' ' "
cQuery1 +="and bd7_filial=' ' and bd7_numimp = '"+cImpresso+"' and bd7_codldp in ('0002','0003') "
cQuery1 +="group by bd7_codldp,bd7_codpeg,bd7_numero "

Plsquery(cQuery1,"TMPBD7") // Rebece o resultado da query acima 

If  !TMPBD7->(Eof()) //Verificando se tabela diferente de vazia 
    Alert("Verificar impresso: " +cImpresso+ " Importado no  local 0003 não pode ser alterado.") 
	TMPBD7->(DbCloseArea() ) //Fecha tabela TMPBD7
    Return(.F.) //Fecha a rotina não faça mais nada 
Endif	

TMPBD7->(DbCloseArea() ) //Fecha tabela TMPBD7 

// validação 2 : Verificando se existe exame terceirizado vinculado a guia, que não esta cancelado

cQuery2 := "select bea_guipri, bea_numimp  "
cQuery2 += "from " +  RetSqlName("BEA") + " "
cQuery2 += "where " + RetSqlName("BEA") +".d_e_l_e_t_=' ' " 
cQuery2 += "and bea_filial=' ' "
cQuery2 += "and bea_cancel<>'1' "
cQuery2 += "and bea_guipri = '"+cImpresso+"' " 

Plsquery(cQuery2,"TMPBEA") // Rebece o resultado da query acima 

If  !TMPBEA->(Eof()) //Verificando se a tabela esta vazia - pois se esta vazia não tenho dados
     Alert("Verificar impresso: " +cImpresso+ " Existe(m) guias terceirizadas vinculadas a este impresso,Verifique!!!") 
	 TMPBEA->(DbCloseArea() ) //Fecha tabela TMPBEA
	 Return(.F.) //Fecha a rotina não faça mais nada 
Endif          

TMPBEA->(DbCloseArea() ) //Fecha tabela TMPBEA    

//Validação 3 - Existe prorrogação neste impresso? Ela não esta cancelada ?

cQuery := " select bea_numimp as NUMIMP, "
cQuery += " bqv_anoint||bqv_mesint||bqv_numint as GUIA, "
cQuery += " decode (bea_tipgui,'01','Consulta','02','SP/SADT','03','INTERNACAO') as TIPO, "  
cQuery += " bqv_sequen as SEQUEN, "
cQuery += " bqv_codpad as CODPAD, "
cQuery += " bqv_codpro as CODPRO, "
cQuery += " trim(bqv_despro) as DESPRO, "
cQuery += " bqv_qtdpro as QTDPRO, "
cQuery += " substr(bqv_datpro,7,2)||'/' ||substr(bqv_datpro,5,2)||'/'||substr(bqv_datpro,1,4) as DATPRO, "
cQuery += " bqv_horpro as HORPRO, "
cQuery += " bqv_nrtrol as NRTROL, "   
cQuery += " decode(bqv_cancel,'0','Nao','1','Sim') as CANC, "
cQuery += " bqv010.R_E_C_N_O_ AS CHAVE "
cQuery += " from " + RetSqlName("BEA") + " , " + RetSqlName("BQV") + " "
cQuery += " where " + RetSqlName("BEA") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BQV") + ".d_e_l_e_t_=' ' "
cQuery += " and bea_filial=' ' "     
cQuery += " and bqv_filial=' ' " 
cQuery += " and bqv_cancel='0' "
cQuery += " and bqv_nrtrol=' ' "  
cQuery += " and ((bea_anoint = bqv_anoint and  bea_mesint = bqv_mesint  and  bea_numint = bqv_numint) or "
cQuery += " (bea_anoaut= bqv_anoint  and  bea_mesaut = bqv_mesint  and  bea_numaut = bqv_numint)) and "
cQuery += " bqv_nrtrol=' '  and  bea_numimp='"+cImpresso+"' "
cQuery += " order by  5 "

Plsquery(cQuery,"TMPBQV") // Recebe o resultado da query acima 


If  TMPBQV->(Eof()) //Verificando se a tabela BQV vazia, não tem prorrogação 
    Alert("Verificar impresso: " +cImpresso+ " não consta prorrogação(ões) neste impresso, ou já esta(ão) cancelada(s).") 
    TMPBQV->( DbCloseArea() ) //Fecha tabela TMPBQV 
    Return(.F.)//Fecha a rotina não faça mais nada  
EndIf 

fUpdate() 

Return() //fechando função  de validação

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Faz o CheckBox -  Marcar  e desmarcar todos,funcionar                    
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function PLSCANCP()

MsProcTXT(If(lCheck,"Marcando","Desmarcando")+" ")
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

Return()

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    01/10/19       º±±
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
PutSx1(cPerg,"01",OemToAnsi("Numero Impresso ?"),"","","mv_ch1","C",12,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
return 