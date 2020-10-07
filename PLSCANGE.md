#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSCANGE  ºAutor  Vanessa Cruz       º Data    09/12/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. Permitir o cancelamento do item 99999994 nas tabelas   º±±
±±º                be2010,bej010,bqv010,bd6010,bd7010                     º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

User Function PLSCANGE()  //Funcao de usuário maximo 8 caracteres    

Local cImpresso := ""  
Private nOpca	:= 1
Private aCores 	:= {}  

Private aCpos1    	:= {{"TRB_OK"	,"C",02,0 },;
						{"NUMIMP"	,"C",12,0 },; 
						{"AUT"      ,"C",03,0 },;
						{"AUDITO"   ,"C",03,0 },; 
						{"SEQUEN"	,"C",03,0 },;
						{"SEQPTU"	,"C",03,0 },;
						{"CODPAD"	,"C",02,0 },;
						{"CODPRO"	,"C",08,0 },;
						{"DESPRO"	,"C",40,0 },;
						{"QTDPRO"	,"N",08,2 },; 
                        {"DATPRO"	,"D",08,0 },;
						{"TABELA"   ,"C",06,0 },;
						{"CHAVE"	,"N",17,0 }}
						
//Estes vao aparecer na tela para o usuario :
						
Private aCampos1 	:= {{"TRB_OK",   ," "},;
						{"NUMIMP",	,"Impresso","@!"},;
						{"AUT",	    ,"Autorizado","@!"},;
						{"AUDITO",  ,"Auditoria","@!"},;
						{"SEQUEN",	,"Seq.","@!"},;
						{"SEQPTU",  ,"Seq.Ptu","@!"},;
						{"CODPAD",	,"Tabela","@!"},;
						{"CODPRO",	,"Procedimento","@!"},;
						{"DESPRO",	,"Desc.Proc.","@!"},;
						{"QTDPRO",	,"Qtd.","@E 999,999.99"},;
					    {"DATPRO",	,"Dt.Proced.","99/99/99"}}
	
						
Private lValida := .T. 
Private cPerg   := "PLSCANGE" 

CriaSX1()     
            
if !Pergunte(cPerg,.T.,"CANCELANDO OPME GENERICO DA AUTORIZAÇÃO .",.T.)
       return()
endif
	
if Empty(mv_par01) //Se o impresso estiver vazio 
	Alert("Informe o impresso corretamente.")
   Return(.F.)//Fecha a rotina não faça mais nada
endif   

cImpresso:=AllTrim(mv_par01) 

FWMsgRun( , {||Validar(cImpresso)} , 'Processando' , 'Aguarde. Validando impresso...') 
                         
Return

Static Function fUpdate()       

Local lInverte  := .F. 
Local cUpdate := " "  

nCont 	:= 0

cArqTrb1 := CriaTrab(aCpos1,.T.)
Processa({|| dbUseArea( .T.,, cArqTrb1, "TRB", if(.F. .OR. .F., !.F., NIL), .F. )},"Aguarde...","Criando tabela temporaria...")

Private cMarca := GetMark()

dbSelectArea("TMPITEM")
dbGoTop()
While !TMPITEM->(EOF())
	
	RecLock("TRB",.T.)
	TRB->TRB_OK 	:= " "//cMarca		
	TRB->NUMIMP 	:= TMPITEM->NUMIMP  
	TRB->AUT     	:= TMPITEM->AUT   
	TRB->AUDITO     := TMPITEM->AUDITO
	TRB->SEQUEN 	:= TMPITEM->SEQUEN
	TRB->SEQPTU     := TMPITEM->SEQPTU
	TRB->CODPAD 	:= TMPITEM->CODPAD
	TRB->CODPRO 	:= TMPITEM->CODPRO
	TRB->DESPRO 	:= TMPITEM->DESPRO
	TRB->QTDPRO 	:= TMPITEM->QTDPRO
	TRB->DATPRO 	:= Stod(TMPITEM->DATPRO)
	TRB->CHAVE  	:= TMPITEM->CHAVE
	TRB->TABELA     := TMPITEM->TABELA
	
	MsUnlock()
	
	TMPITEM->(dbSkip())
	IncProc("Carregando tabela temporaria... " )
	nCont++
Enddo


TMPITEM->(dbCloseArea())

dbSelectArea("TRB")//Define a área de trabalho especificada como ativa.
dbGoTop()//Posiciona a tabela corrente no primeiro registro lógico.

DEFINE MSDIALOG oDlg TITLE "CANCELANDO OPME GENERICO DA AUTORIZAÇÃO " From 00,00 TO 360,900 PIXEL OF GetWndDefault() Style DS_MODALFRAME	
oDlg:lEscClose     := .F. //Nao permite sair ao se pressionar a tecla ESC.

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Monta CheckBox. Marcar desmarcar todos...                                
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ
lCheck := .F.
oCheck := IW_CheckBox(015,001,"Marca/Desmarca Todos","lCheck")
oCheck:bChange := {|| MsAguarde( {|| PLSCANGE() } ) }

oMark := MsSelect():New("TRB","TRB_OK","",aCampos1,@lInverte,@cMarca,{25, 05, 150,450},,,,,aCores)																					   				

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Cria tela para selecao dos arquivos...			                         
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ
ACTIVATE MSDIALOG oDlg CENTERED ON INIT EnchoiceBar(oDlg,{|| nOpca := 1, oDlg:End()},{|| nOpca := 0, ODlg:End()}) 

If nOpca <> 1 
 cMensagem := "Nenhum item foi marcado."+ chr(13)+chr(10)
 MSGINFO(cMensagem)
 TRB->(dbCloseArea()) 
 return(.T.)	
Endif

If Empty(TRB->TRB_OK)   
 cMensagem := "Nenhum item foi marcado,ação Cancelada."+ chr(13)+chr(10)
 MSGINFO(cMensagem)
 TRB->(dbCloseArea()) 
 return(.T.)	
Endif

If nOpca == 1	
	                               
	lAtual_	:= .F.	
	nCont	:= 0     
	cNumimp	:= ""
	cUpdate := ""
	ncodpro := ""
	ctabela := ""
	
	TRB->(dbGoTop())	//Posiciona a tabela corrente no primeiro registro lógico.		
	
	While !TRB->(Eof())
		If !Empty(TRB->TRB_OK)
			nCont++	     
	      	ProcessMessage()
		   lAtual_	:= .T.  
		   cNumimp	:= alltrim(TRB->NUMIMP)
		   ncodpro  := alltrim(TRB->CODPRO)
		   ctabela  := alltrim(TRB->TABELA)
		   csequen  := alltrim(TRB->SEQUEN)
		   
	         if (ctabela=='TABBE2')
	     
	            Begin Transaction
	     
	              cUpdate := " update be2010 set be2_filial='OV',be2_audito='0',be2_status='0',d_e_l_e_t_='*' " 		  
	              cUpdate += " where " + RetSqlName('BE2') +".d_e_l_e_t_=' ' "
	              cUpdate += " and be2_filial =' ' and be2_codpro='99999994' " 
		          cUpdate += " and  r_e_c_n_o_ ="+cValToChar(TRB->CHAVE)+" "
		
	            TcSqlExec(cUpdate)
		 
		        End Transaction 
	            Hist(cNumimp,ncodpro,ctabela,csequen) //Função para criar histório da guia.
	         Endif
	           if (ctabela=='TABBQV')
			     Begin Transaction
				  cUpdate := " update bqv010 set bqv_filial='OV',bqv_audito='0',bqv_status='0',d_e_l_e_t_='*' " 		  
	              cUpdate += " where " + RetSqlName('BQV') +".d_e_l_e_t_=' ' "
	              cUpdate += " and bqv_filial =' ' and bqv_codpro='99999994' " 
		          cUpdate += " and  r_e_c_n_o_ = " +cValToChar(TRB->CHAVE)+" "  
	             TcSqlExec(cUpdate)
				 
		         End Transaction 
				 
	             Hist(cNumimp,ncodpro,ctabela,csequen) //Função para criar histório da guia.
			  Endif
			      if (ctabela=='TABBEJ')
			      
			        cQuery := " select  be2010.R_E_C_N_O_ AS CHAVE " 
					cQuery += " from " + RetSqlName("BE2") + " , " + RetSqlName("BEJ") + " "
					cQuery += " where " + RetSqlName("BE2") + ".d_e_l_e_t_=' ' "
					cQuery += " and " + RetSqlName("BEJ") + ".d_e_l_e_t_=' ' "
					cQuery += " and be2_filial=' ' "     
					cQuery += " and bej_filial=' ' " 
					cQuery += " and be2_anoaut = bej_anoint  "
					cQuery += " and be2_mesaut = bej_mesint  "
					cQuery += " and be2_numaut = bej_numint  "
					cQuery += " and be2_sequen = bej_sequen  "
					cQuery += " and be2_codpro ='99999994' " // Procedimento 
					cQuery += " and bej010.r_e_c_n_o_ ="+cValToChar(TRB->CHAVE) "
			         
			         Plsquery(cQuery,"TMPBE2") // Recebe o resultado da query acima 
			      
			        Begin Transaction
				      cUpdate := " update be2010 set be2_filial='OV',be2_audito='0',be2_status='0',d_e_l_e_t_='*' " 		  
	                  cUpdate += " where " + RetSqlName('BE2') +".d_e_l_e_t_=' ' "
	                  cUpdate += " and be2_filial =' ' and be2_codpro='99999994' " 
		              cUpdate += " and  r_e_c_n_o_ ="+cValToChar(TMPBE2->CHAVE)+" "
		              TcSqlExec(cUpdate)
		            End Transaction 
				  
			        Begin Transaction
					
				      cUpdate := " update bej010 set bej_filial='OV',bej_audito='0',bej_status='0',d_e_l_e_t_='*' " 		  
	                  cUpdate += " where " + RetSqlName('BEJ') +".d_e_l_e_t_=' ' "
	                  cUpdate += " and bej_filial =' ' and bej_codpro='99999994' " 
		              cUpdate += " and r_e_c_n_o_ ="+cValToChar(TRB->CHAVE)+" "
	                TcSqlExec(cUpdate)
					
		            End Transaction 
					
	                Hist(cNumimp,ncodpro,ctabela,csequen) //Função para criar histório da guia.
				Endif	
			  //Ajustando  BD6010 ( Contas Médicas)  
	            Begin Transaction
				
				      cUpdate := " update bd6010 set bd6_filial='OV',bd6_status='0',d_e_l_e_t_='*' " 		  
	                  cUpdate += " where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' "
	                  cUpdate += " and bd6_filial =' ' and bd6_codpro='99999994' " 
		              cUpdate += " and bd6_numimp= '"+cNumimp+"' "
	                  cUpdate += " and bd6_sequen= '"+csequen+"' "
	                  TcSqlExec(cUpdate)
					  
		        End Transaction 
	          //Ajustando  BD7010 ( Contas Médicas)             
	            Begin Transaction
				      cUpdate := " update bd7010 set bd7_filial='OV',d_e_l_e_t_='*' " 		  
	                  cUpdate += " where " + RetSqlName('BD7') +".d_e_l_e_t_=' ' "
	                  cUpdate += " and bd7_filial =' ' and bd7_codpro='99999994' " 
		              cUpdate += " and bd7_numimp= '"+cNumimp+"' "
	                  cUpdate += " and bd7_sequen= '"+csequen+"' "
	                  TcSqlExec(cUpdate)
		        End Transaction 
		 Endif         
		TRB->(dbSkip())
	Enddo	
 Endif
TRB->(dbCloseArea()) 
        
FErase(cArqTrb1 + GetDbExtension())  // Deletando o arquivo
FErase(cArqTrb1 + OrdBagExt())       // Deletando índice 

cMensagem := "Processo finalizado."  + chr(13)+chr(10)
MSGINFO(cMensagem)			
                                                         
Return() //Fecha a rotina 

Static Function Contas()  


return()


//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Função que inseri historio na guia                                       
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ


Static Function Hist(cNumimp,ncodpro,ctabela,csequen) //Função Static  será vista somente aqui Inserindo historio da guia.
Local cSql     := ""

cSql   := "SELECT nvl(MAX(ZZY_CODIGO),0) as CODIGO FROM ZZY010 "

PlsQuery(cSql, "TBZZY")

dbselectarea("TBZZY")
                                     
cCodigo := strzero(val(TBZZY->CODIGO)+1,10)

TBZZY->(dbclosearea())     

ZZY->(RECLOCK('ZZY',.T.)) // Aqui é feita a inserção 
         
ZZY->ZZY_GUIA   :=cNumimp
ZZY->ZZY_STATUS := '62'  
ZZY->ZZY_CODIGO := cCodigo
ZZY->ZZY_VIGINI := DATE() 
ZZY->ZZY_HORINI := TIME()  
ZZY->ZZY_OPERAI := SUBS(UPPER(CUSUARIO),7,15)
ZZY->ZZY_VIGFIM := DATE() 
ZZY->ZZY_HORFIM := TIME()  	
ZZY->ZZY_OPERAA := SUBS(UPPER(CUSUARIO),7,15)  	
ZZY->ZZY_OBS01  := 'Procedimento: '+ncodpro+' Tabela: '+ctabela+ ' Rotina: PLSCANGE'
ZZY->ZZY_CODOPE := RetCodUsr() 

ZZY->(MSUNLOCK())

return

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Função de  Validação dos Dados                                          
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function Validar(cImpresso) //Função Static  será vista somente aqui.
Local cQuery     := ""

//Procurando o procedimento 99999994 na autorização(bea010xbe2010/bea010_bqv010/bea010_bej010)
                              
//be2_status =>1=Autorizada;0=Nao Autorizada                                                                                                                                                                                              

cQuery := " select bea_numimp as NUMIMP,'TABBE2'as TABELA ,"
cQuery += " decode(be2_status,'0','Nao','1','Sim') as AUT, "
cQuery += " decode(be2_audito,'0','Nao','1','Sim') as AUDITO, "
cQuery += " be2_sequen as SEQUEN, "
cQuery += " be2_seqptu as SEQPTU, "
cQuery += " be2_codpad as CODPAD, "
cQuery += " be2_codpro as CODPRO, "
cQuery += " trim(be2_despro) as DESPRO, "
cQuery += " be2_qtdpro as QTDPRO, "
cQuery += " be2_datpro as DATPRO, "
cQuery += " be2010.R_E_C_N_O_ AS CHAVE "
cQuery += " from " + RetSqlName("BEA") + " , " + RetSqlName("BE2") + " "
cQuery += " where " + RetSqlName("BEA") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BE2") + ".d_e_l_e_t_=' ' "
cQuery += " and bea_filial=' ' "     
cQuery += " and be2_filial=' ' " 
cQuery += " and bea_anoaut = be2_anoaut  "
cQuery += " and bea_mesaut = be2_mesaut  "
cQuery += " and bea_numaut = be2_numaut  "
cQuery += " and bea_cancel <>'1'  "
cQuery += " and be2_codpro ='99999994' " // Procedimento 
cQuery += " and bea_tipgui<>'03' " //Para evitar duplicidade na tela entre Be2010 e bej010 quando for internacao
cQuery += " and bea_numimp='"+cImpresso+"' "
cQuery += " union all "                   // Unindo os selects  (BEA010 X BQV010) 
cQuery += " select bea_numimp as NUMIMP,'TABBQV'as TABELA , "
cQuery += " decode(bqv_status,'0','Nao','1','Sim') as AUT, "
cQuery += " decode(bqv_audito,'0','Nao','1','Sim') as AUDITO, "
cQuery += " bqv_sequen as SEQUEN, "
cQuery += " bqv_seqptu as SEQPTU, "
cQuery += " bqv_codpad as CODPAD, "
cQuery += " bqv_codpro as CODPRO, "
cQuery += " trim(bqv_despro) as DESPRO, "
cQuery += " bqv_qtdpro as QTDPRO, " 
cQuery += " bqv_datpro as DATPRO, "
cQuery += " bqv010.R_E_C_N_O_ AS CHAVE "
cQuery += " from " + RetSqlName("BEA") + " , " + RetSqlName("BQV") + " "
cQuery += " where " + RetSqlName("BEA") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BQV") + ".d_e_l_e_t_=' ' "
cQuery += " and bea_filial=' ' "     
cQuery += " and bqv_filial=' ' " 
cQuery += " and bea_anoaut = bqv_anoint  "
cQuery += " and bea_mesaut = bqv_mesint  "
cQuery += " and bea_numaut = bqv_numint  "
cQuery += " and bea_cancel <>'1'  "
cQuery += " and bqv_codpro ='99999994' " // Procedimento
cQuery += " and bea_numimp='"+cImpresso+"' "
cQuery += " union all "         // Unindo os selects  (BEA010 X BEJ010) 
cQuery += " select bea_numimp as NUMIMP,'TABBEJ'as TABELA , "
cQuery += " decode(bej_status,'0','Nao','1','Sim') as AUT, "
cQuery += " decode(bej_audito,'0','Nao','1','Sim') as AUDITO, "
cQuery += " bej_sequen as SEQUEN, "
cQuery += " bej_seqptu as SEQPTU, "
cQuery += " bej_codpad as CODPAD, "
cQuery += " bej_codpro as CODPRO, "
cQuery += " trim(bej_despro) as DESPRO, "
cQuery += " bej_qtdpro as QTDPRO, " 
cQuery += " bej_datpro as DATPRO, "
cQuery += " bej010.R_E_C_N_O_ AS CHAVE "
cQuery += " from " + RetSqlName("BEA") + " , " + RetSqlName("BEJ") + " "
cQuery += " where " + RetSqlName("BEA") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BEJ") + ".d_e_l_e_t_=' ' "
cQuery += " and bea_filial=' ' "     
cQuery += " and bej_filial=' ' " 
cQuery += " and bea_anoaut = bej_anoint  "
cQuery += " and bea_mesaut = bej_mesint  "
cQuery += " and bea_numaut = bej_numint  "
cQuery += " and bea_cancel <>'1'  "
cQuery += " and bej_codpro ='99999994' " // Procedimento
cQuery += " and bea_numimp='"+cImpresso+"' "
cQuery += " order by 4  " //Ordenando por be2_sequen 

Plsquery(cQuery,"TMPITEM") // Recebe o resultado da query acima 


If  TMPITEM->(Eof()) //Verificando se a tabela  vazia.
    Alert("Verificar impresso: " +cImpresso+ " não consta o código 99999994.") 
    TMPITEM->( DbCloseArea() ) //Fecha tabela TMPITEM 
    Return(.F.)//Fecha a rotina não faça mais nada  
EndIf 

fUpdate() 

Return() //fechando função  de validação

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Faz o CheckBox -  Marcar  e desmarcar todos,funcionar                   
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function PLSCANGE()

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
±±ºPrograma  CriaSX1   ºAutor  Vanessa          º Data   01/10/19       º±±
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


//(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Numero Impresso "),"","","mv_ch1","C",12,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
return 