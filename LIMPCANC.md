#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   Cancelando Guias Eletronicas Autor   Vanessa º Data   18/03/2019º ±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ±±
±±ºDesc.      Atendendo uma necessidade da equipe de atendimento,foi criado    º±±
±±º            este recurso, que limpa a comunicação de uma guia eletronica e   º±±
±±º           realiza o cancelamento.                                          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function LIMPCANC(cImp,cTrol)  //Funcao de usuário maximo 8 caracteres

Local cQuery    :=""
Local cImpr     :="" 
Local cNtrol    :=""
Local cAno      :=""
Local cMes      :=""
Local cNum      :=""
Local cTipgui   :=""
Local cQuery    :=""

Private cPerg:= "LIMPCANC" 

if valtype(cImp) <> 'U' .AND. valtype(cTrol) <> 'U'
	cImpr := alltrim(cImp)
	cNtrol:= alltrim(cTrol)
	if !MsgNoYes ("Você está prestes a cancelar toda essa transação. Deseja continuar?","Atenção")
		Return
	endif
else
	CriaSX1()  

	if !Pergunte(cPerg,.T.,"Retira comunicação On-line de Guia eletronica e cancela no Protheus",.T.)
		Return
	endif  
	
	if Empty(mv_par01) .And. Empty(mv_par02) //Se ambos os campos estiverm vazios
	    Alert("É obrigatório informar todos os campos.")
	    Return
	endif   
	
	if Empty(mv_par01) //Se o numero da guia estiver vazio 
		Alert("É obrigatório informar o numero impresso da guia.")
	    Return
	endif
	
	if Empty(mv_par02) // Se o numero da Transação estiver vazio 
		Alert("É obrigatório informar o numero da transação da guia. ")
	 	Return
	endif
	cImpr := ALLTRIM(mv_par01)
	cNtrol:= ALLTRIM(mv_par02)
endif

cQuery := "select bea_tipgui,bea_numimp,bea_cancel,bea_audito,bea_status,bea_comuni,"
cQuery += "bea_anoaut,bea_mesaut,bea_numaut,bea_nrtrol,bea_desope,bea_codemp,bea_msg05" 
cQuery += " from " +  RetSqlName('BEA')
cQuery += " where " + RetSqlName('BEA') + ".d_e_l_e_t_=' '"
cQuery += " and bea_filial = ' '"
cQuery += " and bea_numimp=  '"+cImpr+"'"
cQuery += " and bea_nrtrol = '"+cNtrol+"'"
cQuery += " and bea_cancel<>'1'"
cQuery += " and bea_codemp<>'0001'"
cQuery += " and bea_desope='ELETRONICA'"
cQuery += " and bea_codrda like '500%'"
plsquery(cQuery,'TMPBEA')

If  TMPBEA->(Eof()) 
    Alert("Verificar se esta guia já não esta cancelada,se é cliente Unimed Uberaba" + Chr(13) + Chr(10)+"atendido em outra Unimed, pois nao foi encontrada guia nestas condições." )
    TMPBEA->( DbCloseArea() ) 
    Return() 
EndIf  
         
cTipgui := TMPBEA->bea_tipgui
cAno := TMPBEA->bea_anoaut
cMes := TMPBEA->bea_mesaut
cNum := TMPBEA->bea_numaut

TMPBEA->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

cMensagem := "Este programa retira a comunicação on-line desta guia e a cancela no Prohteus"  + chr(13)+chr(10)   
MSGINFO(cMensagem)

FWMsgRun( , {||LIMPCANC(cImpr,cNtrol,cTipgui,cAno,cMes,cNum)} , 'Processando' , 'Aguarde. Atualizando guia...')  //chamando a função que realmente vai fazer o update 

cMensagem := "Comunicação retirada e guia cancelada!" + chr(13)+chr(10)
MSGINFO(cMensagem)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor   Vanessa Cruz          º  Data   18/03/19          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Update propriamente dito                                  º±±
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso         AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function LIMPCANC(cImpr,cNtrol,cTipgui,cAno,cMes,cNum) //Função Static  será vista somente aqui.

Local Update := " "  
Local cData  := DtoC(date())+' '+Time()

	Update := " update " + RetSqlName('BEA')
	Update += " set  bea_comuni='0',bea_audito='0',bea_nrtrol=' ',bea_cancel='1',bea_status='3', "
	Update += " bea_msg05 = 'Trans: "+cNtrol+" cancelada,Rotina LIMPCANC por:  "+cUserName+" Dt: "+cData+"' " 
	Update += " where  " + RetSqlName('BEA') + ".d_e_l_e_t_=' ' and bea_filial = ' '  "
	Update += " and bea_numimp='"+cImpr+"' "
	Update += " and bea_nrtrol = '"+cNtrol+"' "
	Update += " and bea_cancel<>'1' "
	Update += " and bea_codemp<>'0001' "
	Update += " and bea_desope ='ELETRONICA' "
	Update += " and bea_codrda like '500%'"
	TCSQLEXEC(Update)
	
	Update := " update " + RetSqlName('BE2')
	Update += " set be2_audito='0',be2_status ='0' "
	Update += " where  " + RetSqlName('BE2') + ".d_e_l_e_t_=' ' and be2_filial = ' '  "
	Update += " and be2_anoaut='"+cAno+"' "
	Update += " and be2_mesaut='"+cMes+"' "
	Update += " and be2_numaut='"+cNum+"' "
	Update += " and be2_codemp<>'0001' "
	Update += " and be2_codrda like '500%'"	
	TCSQLEXEC(Update)
	
	Update := " update " + RetSqlName('BD6')
	Update += " set bd6_situac='2', bd6_blopag='1', bd6_status='0' where " 
	Update += RetSqlName('BD6') + ".d_e_l_e_t_=' ' "
	Update += " and bd6_numimp='"+cImpr+"' "
	Update += " and bd6_status='1' "
	Update += " and bd6_codemp<>'0001'"
	TCSQLEXEC(Update)
	
	Update := " update " + RetSqlName('BD7')
	Update += " set bd7_situac='3', bd7_blopag='1' where " 
	Update += RetSqlName('BD7') + ".d_e_l_e_t_=' ' "
	Update += " and bd7_numimp='"+cImpr+"' "
	Update += " and bd7_codemp<>'0001'"
	TCSQLEXEC(Update)
	
	If cTipgui <> '03'
	    Update := " update " + RetSqlName('BD5')
	    Update += " set bd5_situac='2', bd5_blopag='1' "
	    Update += " where  " + RetSqlName('BD5') + ".d_e_l_e_t_=' ' and bd5_filial = ' '  "
	    Update += " and bd5_numimp='"+cImpr+"' "
	    Update += " and bd5_codemp<>'0001' "
	    TCSQLEXEC(Update) 
	Else
	    Update := " update " + RetSqlName('BE4')
	    Update += " set be4_situac='2',be4_comuni='0',be4_audito='0',be4_nrtrol=' ',be4_cancel='1',be4_status='3', "
		Update += " be4_msg05 = 'Trans: "+cNtrol+" cancelada,Rotina LIMPCANC por:  "+cUserName+" Dt: "+cData+"' " 
	    Update += " where  " + RetSqlName('BE4') + ".d_e_l_e_t_=' ' and be4_filial = ' '  "
	    Update += " and be4_nrtrol = '"+cNtrol+"' "
	    Update += " and be4_anoint='"+cAno+"' "
	    Update += " and be4_mesint='"+cMes+"' "
	    Update += " and be4_numint='"+cNum+"' "
	    Update += " and be4_codemp<>'0001' "
	    Update += " and be4_desope ='ELETRONICA' "
	    Update += " and be4_codrda like '500%'"
	    TCSQLEXEC(Update) 
		 
	    Update := " update " + RetSqlName('BEJ')
	    Update += " set  bej_audito='0',bej_status ='0' "
	    Update += " where  " + RetSqlName('BEJ') + ".d_e_l_e_t_=' ' and bej_filial = ' '  "
	    Update += " and bej_anoint='"+cAno+"' "
	    Update += " and bej_mesint='"+cMes+"' "
	    Update += " and bej_numint='"+cNum+"' "
	    TCSQLEXEC(Update) 
	    
	Endif
	
	Update += "UPDATE b0t010 SET B0T_CONTEU='1' where b0t_numseq='"+alltrim(cNtrol)+"' AND b0t_tiptra='00600' and b0t_variav in ('ID_AUTORIZ')"
	TCSQLEXEC(Update) 
	
	Update += "UPDATE b0t010 SET B0T_CONTEU='2041' where b0t_numseq='"+alltrim(cNtrol)+"' AND b0t_tiptra='00600' and b0t_variav in ('CD_MENS_E1')"
	TCSQLEXEC(Update) 

Return
/*	
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    18/03/19       º±±
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

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Numero Impresso: ") ,"","", "mv_ch1","C",20,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Numero Transacao: ") ,"","", "mv_ch2","C",15,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})

return 	
	