#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSLIBES  ºAutor  ³Vanessa Cruz       º Data ³   02/12/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.³ Update                                                          º±±
±±º    ³ Objetivo: Permitir colocar  item(s) da Autorização (be2010) na   º±±
±±º                        liberação especial,somente guias on-line(PEP)         º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

User Function PLSLIBES()  //Funcao de usuário maximo 8 caracteres    

Local cImpresso := ""  
Private nOpca	:= 1
Private aCores 	:= {}  

Private aCpos1    	:= {{"TRB_OK"	,"C",02,0 },;
						{"NUMIMP"	,"C",12,0 },; //Retorna o Tamanho/Decimal/Tipo de um Campo Informado
						{"AUT"      ,"C",03,0 },;
						{"AUDITO"   ,"C",03,0 },; 
						{"SEQUEN"	,"C",03,0 },;
						{"CODPAD"	,"C",02,0 },;
						{"CODPRO"	,"C",08,0 },;
						{"DESPRO"	,"C",40,0 },;
						{"QTDPRO"	,"N",08,2 },; 
						{"LIBESP"	,"C",03,0 },; 
                        {"DATPRO"	,"D",08,0 },;
						{"CHAVE"	,"N",17,0 }}
						
//Estes vao aparecer na tela para o usuario :
						
Private aCampos1 	:= {{"TRB_OK"	,," "},;
						{"NUMIMP",	,"Impresso","@!"},;
						{"AUT",	    ,"Autorizado","@!"},;
						{"AUDITO",  ,"Auditoria","@!"},;
						{"SEQUEN",	,"Seq.","@!"},;
						{"CODPAD",	,"Tabela","@!"},;
						{"CODPRO",	,"Procedimento","@!"},;
						{"DESPRO",	,"Desc.Proc.","@!"},;
						{"QTDPRO",	,"Qtd.","@E 999,999.99"},;
						{"LIBESP",  ,"Lib.Especial","@!"},;
					    {"DATPRO",	,"Dt.Proced.","99/99/99"}}
	
						
Private lValida := .T. 
Private cPerg   := "PLSLIBES" 

CriaSX1()     
            
if !Pergunte(cPerg,.T.,"LANÇANDO ITEM(S) NEGADO(S) PARA LIBERAÇÃO ESPECIAL.",.T.)
       return()
endif
	
if Empty(mv_par01) //Se o impresso estiver vazio 
	Alert("Informe o impresso corretamente.")
   Return(.F.)//Fecha a rotina não faça mais nada
endif   

cImpresso:=AllTrim(mv_par01) 

    cMensagem := "Aqui você estará  lançando itens para liberação especial! "  + chr(13)+chr(10)
    cMensagem += "somente de guias que passaram na ferramenta PEP e o item dever estar Não Autorizado." + chr(13)+chr(10)
    MSGINFO(cMensagem)  

FWMsgRun( , {||Validar(cImpresso)} , 'Processando' , 'Aguarde. Validando impresso...')  //chamando a função que realmente vai fazer o update  
                         

Return

Static Function fUpdate()       

Local lInverte  := .F. 
Local cUpdate := " "  

nCont 	:= 0

cArqTrb1 := CriaTrab(aCpos1,.T.)
Processa({|| dbUseArea( .T.,, cArqTrb1, "TRB", if(.F. .OR. .F., !.F., NIL), .F. )},"Aguarde...","Criando tabela temporaria...")

Private cMarca := GetMark()

dbSelectArea("TMPBE2")
dbGoTop()
While !TMPBE2->(EOF())
	
	RecLock("TRB",.T.)
	TRB->TRB_OK 	:= " "//cMarca		
	TRB->NUMIMP 	:= TMPBE2->NUMIMP  
	TRB->AUT     	:= TMPBE2->AUT   
	TRB->AUDITO     := TMPBE2->AUDITO
	TRB->SEQUEN 	:= TMPBE2->SEQUEN
	TRB->CODPAD 	:= TMPBE2->CODPAD
	TRB->CODPRO 	:= TMPBE2->CODPRO
	TRB->DESPRO 	:= TMPBE2->DESPRO
	TRB->QTDPRO 	:= TMPBE2->QTDPRO
	TRB->LIBESP 	:= TMPBE2->LIBESP
	TRB->DATPRO 	:= Stod(TMPBE2->DATPRO)
	TRB->CHAVE  	:= TMPBE2->CHAVE
	
	MsUnlock()
	
	TMPBE2->(dbSkip())
	IncProc("Carregando tabela temporaria... " )
	nCont++
Enddo
											        
TMPBE2->(dbCloseArea())

dbSelectArea("TRB")//Define a área de trabalho especificada como ativa.
dbGoTop()//Posiciona a tabela corrente no primeiro registro lógico.

DEFINE MSDIALOG oDlg TITLE "lançando item(s) negado(s) para liberaçao especial " From 00,00 TO 360,900 PIXEL OF GetWndDefault() Style DS_MODALFRAME	
oDlg:lEscClose     := .F. //Nao permite sair ao se pressionar a tecla ESC.

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Monta CheckBox. Marcar desmarcar todos...                                ³
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ
lCheck := .F.
oCheck := IW_CheckBox(015,001,"Marca/Desmarca Todos","lCheck")
oCheck:bChange := {|| MsAguarde( {|| PLSLIBES() } ) }

oMark := MsSelect():New("TRB","TRB_OK","",aCampos1,@lInverte,@cMarca,{25, 05, 150,450},,,,,aCores)																					   				

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Cria tela para selecao dos arquivos...			                         ³
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


​	
	TRB->(dbGoTop())	//Posiciona a tabela corrente no primeiro registro lógico.		
	
	While !TRB->(Eof())
		If !Empty(TRB->TRB_OK)
			nCont++	     
	      	ProcessMessage()
		   lAtual_	:= .T.  
		   cNumimp	:= alltrim(TRB->NUMIMP)
		   ncodpro  := alltrim(TRB->CODPRO)
				
	   //Alterando item para cair em liberação especial; 
		
	   Begin Transaction
	     
	      cUpdate := " update be2010 set be2_libesp='1' " 		  
	      cUpdate += " where " + RetSqlName('BE2') +".d_e_l_e_t_=' ' "
	      cUpdate += " and be2_filial =' ' "
		  cUpdate += " and  r_e_c_n_o_ ="+cValToChar(TRB->CHAVE) "
		
	     TcSqlExec(cUpdate)
		 
		 End Transaction 
	
	    Endif	
	    Hist(cNumimp,ncodpro) //Função para criar histório da guia.
		
		TRB->(dbSkip())
	Enddo	
 Endif
TRB->(dbCloseArea()) 
        
FErase(cArqTrb1 + GetDbExtension())  // Deletando o arquivo
FErase(cArqTrb1 + OrdBagExt())       // Deletando índice			
                                                          

cMensagem := "Processo finalizado."  + chr(13)+chr(10)
MSGINFO(cMensagem) 


Return //Fecha a rotina não faça mais nada 	

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Função que inseri historio na guia                                       ³
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ


Static Function Hist(cNumimp,ncodpro) //Função Static  será vista somente aqui Inserindo historio da guia.
Local cSql     := ""

cSql   := "SELECT nvl(MAX(ZZY_CODIGO),0) as CODIGO FROM ZZY010 "

PlsQuery(cSql, "TBZZY")

dbselectarea("TBZZY")
                                     
cCodigo := strzero(val(TBZZY->CODIGO)+1,10)

TBZZY->(dbclosearea())     

ZZY->(RECLOCK('ZZY',.T.)) // Aqui é feita a inserção 
         
ZZY->ZZY_GUIA   :=cNumimp
ZZY->ZZY_STATUS := '61'  
ZZY->ZZY_CODIGO := cCodigo
ZZY->ZZY_VIGINI := DATE() 
ZZY->ZZY_HORINI := TIME()  
ZZY->ZZY_OPERAI := SUBS(UPPER(CUSUARIO),7,15)
ZZY->ZZY_VIGFIM := DATE() 
ZZY->ZZY_HORFIM := TIME()  	
ZZY->ZZY_OPERAA := SUBS(UPPER(CUSUARIO),7,15)  	
ZZY->ZZY_OBS01  := 'Item: '+ncodpro+' Rotina - PLSLIBES '
ZZY->ZZY_CODOPE := RetCodUsr() 

ZZY->(MSUNLOCK())

return

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Função de  Validação dos Dados                                          ³
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function Validar(cImpresso) //Função Static  será vista somente aqui.
Local cQuery     := ""

 // Validando  o procedimento que irá para liberação especial 
                              
//be2_status =>1=Autorizada;0=Nao Autorizada                                                                                                                                                                                              

cQuery := " select bea_numimp as NUMIMP, "
cQuery += " decode(be2_status,'0','Nao','1','Sim') as AUT, "
cQuery += " decode(be2_audito,'0','Nao','1','Sim') as AUDITO, "
cQuery += " be2_sequen as SEQUEN, "
cQuery += " be2_codpad as CODPAD, "
cQuery += " be2_codpro as CODPRO, "
cQuery += " trim(be2_despro) as DESPRO, "
cQuery += " be2_qtdpro as QTDPRO, "
cQuery += " decode(be2_libesp,'0','Nao','1','Sim') as LIBESP, " 
cQuery += " be2_datpro as DATPRO, "
//cQuery += " substr(be2_datpro,7,2)||'/' ||substr(be2_datpro,5,2)||'/'||substr(be2_datpro,1,4) as DATPRO, "
cQuery += " be2010.R_E_C_N_O_ AS CHAVE "
cQuery += " from " + RetSqlName("BEA") + " , " + RetSqlName("BE2") + " "
cQuery += " where " + RetSqlName("BEA") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BE2") + ".d_e_l_e_t_=' ' "
cQuery += " and bea_filial=' ' "     
cQuery += " and be2_filial=' ' " 
cQuery += " and bea_anoaut = be2_anoaut  "
cQuery += " and bea_mesaut = be2_mesaut  "
cQuery += " and bea_numaut = be2_numaut  "
cQuery += " and bea_cancel ='0'  "
cQuery += " and bea_tipgui<>'03' " // Não é internação 
cQuery += " and bea_codemp<>'0001' " //Só cliente Unimed Uberaba
cQuery += " and bea_desope='online' "// Somente guias PEP
cQuery += " and be2_audito='0' " //Não esta auditoria
cQuery += " and be2_libesp<>'1' " // Não esta em liberação especial( contempla be2_libesp='0' e be2_libesp=vazio)
cQuery += " and bea_numimp='"+cImpresso+"' "
cQuery += " order by 3  " //Ordenando por be2_sequen 

Plsquery(cQuery,"TMPBE2") // Recebe o resultado da query acima 


If  TMPBE2->(Eof()) //Verificando se a tabela BE2 vazia, não tem prorrogação 
    Alert("Verificar impresso: " +cImpresso+ " ou o item não esta negado,ou já esta em auditoria,ou já esta em liberação especial.") 
    TMPBE2->( DbCloseArea() ) //Fecha tabela TMPBE2 
    Return(.F.)//Fecha a rotina não faça mais nada  
EndIf 

fUpdate() 

Return() //fechando função  de validação

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Faz o CheckBox -  Marcar  e desmarcar todos,funcionar                   ³
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function PLSLIBES()

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
±±ºPrograma  ³CriaSX1   ºAutor  Vanessa          º Data ³  01/10/19       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±

±±ºDesc.      Criando os parametros necessários para a rotina.          º±±
±±º           Serão informados pelo usuário:                            º±±
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