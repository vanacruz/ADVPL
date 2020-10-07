#include "protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10) 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSDELDC  ºAutor   Vanessa Cruz        º Data    11/11/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±Desc.  Permitir que o colaborador possa excluir uma faixa    de  Débitos e Créditos.         º±±                                                                                                                                        ±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

User Function PLSDELDC()  //Funcao de usuário maximo 8 caracteres    

Local cSeqI := ""  
Local cSeqF := ""  
Local cAno  := ""  
Local cMes  := ""  
Local cLanc := ""
Private nOpca	:= 1
Private aCores 	:= {}  

Private aCpos1    	:= {{"TRB_OK"	    ,"C",02,0 },;
						{"Sequen"	,"C",TamSx3("BSQ_CODSEQ")[1],0},; //Retorna o Tamanho/Decimal/Tipo de um Campo Informado
						{"Usuario"  	,"C",17,0 },;
						{"Contrato"     ,"C",12,0 },;
						{"Subcon"	    ,"C",09,0 },;
						{"Matric"	    ,"C",06,0 },;
						{"Ano"	        ,"C",04,0 },;
						{"Mes"	        ,"C",02,0 },;
						{"Lanca"	    ,"C",03,0 },; 
						{"Valor"	    ,"N",08,2 },; 
						{"Tipo"         ,"C",07,0 },;
						{"Obs"	        ,"C",45,0 }}
					
						
//Estes vao aparecer na tela para o usuario :
						
Private aCampos1 	:= {{"TRB_OK"	,," "},;
						{"SEQUEN",      ,"Sequencia","@!"},;
						{"USUARIO",	    ,"Usuario","@!"},;
						{"CONTRATO",    ,"Contrato","@!"},;
						{"SUBCON",	    ,"Subcontrato","@!"},;
						{"MATRIC",	    ,"Matricula","@!"},;
						{"ANO",	        ,"Ano","@!"},;
						{"MES",	        ,"Mes","@!"},;
						{"LANCA",	    ,"Lancamento","@!"},;
					    {"VALOR",	    ,"Valor","@E 999,999.99"},;
					    {"TIPO",	    ,"Tipo","@!"},;
						{"OBS",	        ,"Observacao","@!"}}
						
						
Private lValida := .T. 
Private cPerg   := "PLSDELDC" 

CriaSX1()     
            


if !Pergunte(cPerg,.T.,"Deletando faixa de Debitos/Creditos.",.T.)
       return()
endif
    

if Empty(mv_par01) //Se a sequencia inicial 
	Alert("Informe a Sequência Inicial,corretamente.")
    Return
endif

if Empty(mv_par02) //Se a sequencia Final 
	Alert("Informe a Sequência Final,corretamente.")
    Return
endif

if Empty(mv_par03) //Se a sequencia Final 
	Alert("Informe o Ano,corretamente.")
    Return
endif

if Empty(mv_par04) //Se a sequencia Final 
	Alert("Informe o Mês, corretamente.")
    Return
endif

if Empty(mv_par05) //Se a sequencia Final 
	Alert("Informe o Lançamento, corretamente.")
    Return
endif


cSeqI:=AllTrim(mv_par01)
cSeqF:=AllTrim(mv_par02)
cAno :=AllTrim(mv_par03)
cMes :=AllTrim(mv_par04)
cLanc:=AllTrim(mv_par05)

FWMsgRun( , {||Validar(cSeqI,cSeqF,cAno,cMes,cLanc)} , 'Processando' , 'Aguarde. Validando informações...')  //chamando a função que realmente vai fazer o update  
                         
Return

Static Function fUpdate()       

Local lInverte  := .F. 
Local cUpdate := " "   
Local cData  := DtoC(date())+' '+Time()

nCont 	:= 0

cArqTrb1 := CriaTrab(aCpos1,.T.)
Processa({|| dbUseArea( .T.,, cArqTrb1, "TRB", if(.F. .OR. .F., !.F., NIL), .F. )},"Aguarde...","Criando tabela temporaria...")

Private cMarca := GetMark()

dbSelectArea("TMPBSQ")
dbGoTop()
While !TMPBSQ->(EOF())
	
	RecLock("TRB",.T.)
	TRB->TRB_OK 	 := " "//cMarca		
	TRB->SEQUEN 	 := TMPBSQ->SEQUEN 
	TRB->USUARIO     := TMPBSQ->USUARIO   
	TRB->CONTRATO 	 := TMPBSQ->CONTRATO 
	TRB->SUBCON      := TMPBSQ->SUBCON
	TRB->MATRIC      := TMPBSQ->MATRIC
	TRB->ANO 	     := TMPBSQ->ANO
	TRB->MES 	     := TMPBSQ->MES
	TRB->LANCA       := TMPBSQ->LANCA
	TRB->VALOR 	     := TMPBSQ->VALOR
	TRB->TIPO 	     := TMPBSQ->TIPO
	TRB->OBS         := TMPBSQ->OBS  


​	
	MsUnlock()    
	
	TMPBSQ->(dbSkip())
	IncProc("Carregando tabela temporaria... " )
	nCont++
Enddo
											        
TMPBSQ->(dbCloseArea())

dbSelectArea("TRB")//Define a área de trabalho especificada como ativa.
dbGoTop()//Posiciona a tabela corrente no primeiro registro lógico.


DEFINE MSDIALOG oDlg TITLE "Deletando faixa de Debitos/Creditos" From 00,00 TO 360,900 PIXEL OF GetWndDefault() Style DS_MODALFRAME	
oDlg:lEscClose     := .F. //Nao permite sair ao se pressionar a tecla ESC.

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Monta CheckBox. Marcar desmarcar todos...                                 
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

lCheck := .F.
oCheck := IW_CheckBox(015,001,"Marca/Desmarca Todos","lCheck")
oCheck:bChange := {|| MsAguarde( {|| PLSDELDC() } ) }

oMark := MsSelect():New("TRB","TRB_OK","",aCampos1,@lInverte,@cMarca,{25, 05, 150,450},,,,,aCores)																					   				

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Cria tela para selecao dos arquivos...			                          
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
	cSeq := "" 
	cUpdate := ""
	
	TRB->(dbGoTop())	//Posiciona a tabela corrente no primeiro registro lógico.		
	
	While !TRB->(Eof())
		If !Empty(TRB->TRB_OK)
			nCont++	     
	      //	ProcessMessage()
		   lAtual_	:= .T.  
		   
	    //Cancelando Debitos/Creditos
		
	   Begin Transaction
	     
	      cUpdate := " update bsq010 set d_e_l_e_t_='*', bsq_filial='DE' , " 
		  cUpdate += " bsq_guia='PLSDELDC por:  "+cUserName+" Dt: "+cData+"' "  
	      cUpdate += " where " + RetSqlName('BSQ') +".d_e_l_e_t_=' ' "
	      cUpdate += " and bsq_filial=' ' and bsq_ano='"+(TRB->ANO)+"'  and   "
		  cUpdate += " bsq_mes='"+(TRB->MES)+"' and bsq_codlan='"+(TRB->LANCA)+"' and  "
		  cUpdate += " bsq_codseq='"+(TRB->SEQUEN)+"' "
	      TcSqlExec(cUpdate)
		 
		 End Transaction 
	
	    Endif		
		TRB->(dbSkip())
	Enddo	
Endif
TRB->(dbCloseArea()) 
        
FErase(cArqTrb1 + GetDbExtension())  // Deletando o arquivo
FErase(cArqTrb1 + OrdBagExt())       // Deletando índice			
             
cMensagem := "Processo finalizado,Favor verificar.."  + chr(13)+chr(10)
MSGINFO(cMensagem) 


Return //Fecha a rotina não faça mais nada 	

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Função de  Validação dos Dados                                           
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function Validar(cSeqI,cSeqF,cAno,cMes,cLanc) //Função Static  será vista somente aqui.
Local cQuery     := " "

// validação : Existem dados para Deletar ?

cQuery :="select bsq_codseq as SEQUEN, "
cQuery +="bsq_usuari as USUARIO, "
cQuery +="bsq_conemp as CONTRATO, "
cQuery +="bsq_subcon as SUBCON, "
cQuery +="bsq_matric as MATRIC, "
cQuery +="bsq_ano as ANO, "
cQuery +="bsq_mes as MES, "
cQuery +="bsq_codlan as LANCA, "
cQuery +="bsq_valor as VALOR, "
cQuery +="case when bsq_tipo='1' then 'Débito' when bsq_tipo='2' then 'Crédito' end as TIPO,bsq_obs as OBS "
cQuery +="from " +  RetSqlName("BSQ") + " " 
cQuery +="where  " + RetSqlName("BSQ") +".d_e_l_e_t_=' ' "
cQuery +="and bsq_filial=' ' and bsq_codseq >= '"+cSeqI+"' and bsq_codseq<='"+cSeqF+"' and "
cQuery +="bsq_ano='"+cAno+"' and bsq_mes = '"+cMes+"' and bsq_codlan='"+cLanc+"' "
cQuery +="order by bsq_codseq "

Plsquery(cQuery,"TMPBSQ") // Rebece o resultado da query acima 

If  TMPBSQ->(Eof()) //Verificando se tabela diferente de vazia 
    Alert("Por favor verificar esta sequencia não foi encontrada!") 
	TMPBSQ->(DbCloseArea() ) //Fecha tabela TMPBSQ
    Return(.F.) //Fecha a rotina não faça mais nada 
Endif	

fUpdate() // Update 
	
Return() //fechando função  de validação

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//  Faz o CheckBox -  Marcar  e desmarcar todos,funcionar                    
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

Static Function PLSDELDC()

MsProcTXT(If(lCheck,"Marcando","Desmarcando")+" ")
//ProcessMessage()

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
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    11/11/19       º±±
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
PutSx1(cPerg,"01",OemToAnsi("Seq.Inicial "),"","","mv_ch1","C",8,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Seq.Final "),"","","mv_ch2","C",8,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Ano "),"","","mv_ch3","C",4,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"04",OemToAnsi("Mês "),"","","mv_ch4","C",2,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Lançamento "),"","","mv_ch5","C",03,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
return 