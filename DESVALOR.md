#Include "Protheus.ch"
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   DESVALOR  ºAutor   Vanessaº              Data:07/03/2019    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Desmarcar valorização de pagamento e cobrança de guias      º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/


User Function DESVALOR()

Local _cPerg:= 'DESVALOR'
Local _lExec:= .T.
Local lRetp	:= .T.
Local lRetc	:= .T.
Local cQRY :=' '
Local cQuery :=' '
Local cOpcao:= ""
Local cAno :=""
Local cMes :=""
Local cRD1 :=""
Local cRD2 :=""
Local cloc1:=""
Local cloc2:=""  
Local cDataDe:=""
Local cDataAte:=""
Private AGuiasP:={} //declaramos como vazio porque não sabemos o tamanho do arry (Pagamento).
Private AGuiasC:={} //declaramos como vazio porque não sabemos o tamanho do arry.(cobrança). 
Private AGuiasPC:={} //declaramos como vazio porque não sabemos o tamanho do arry(ambos). 
AjustaSX1( _cPerg )

//Pergunte( _cPerg , .T. ) 
If !Pergunte(_cPerg, .T.)  // Se clicar no fechar ou cancelar 
	Msginfo("Cancelado pelo usuário.")
	Return()
EndIf

//VERIFICANDO  COMPETENCIA

cQRY := "SELECT BA0_YCPTCM FROM BA0010 "
cQRY += "WHERE D_E_L_E_T_ = ' ' "
cQRY += "AND BA0_CODINT = '021' "
cQRY += "AND BA0_CODIDE = '0'"

Plsquery(cQRY,"TMP_BA0")

If (AllTrim(mv_par01)+AllTrim(mv_par02)) <> AllTrim(TMP_BA0->BA0_YCPTCM)
	Alert("Ano e Mes de pagamento informados são diferentes  da competencia atual " + Chr(13) + Chr(10) +"do Contas Médicas.")
	TMP_BA0->(dbCloseArea())
	Return
EndIf
TMP_BA0->(dbCloseArea())

//Validação de parametros não podem ser vazios.

if Empty(mv_par01) // Se o Ano da competencia estiver vazio
	Alert("Favor informar ano da competência a ser trabalhada.")
	Return
endif

if Empty(mv_par02) // Se o mês da competencia  estiver vazio
	Alert("Favor informe o mês da competência a ser trabalhada.")
	Return
endif

if Empty(mv_par03) // Se a RDA Inicial  estiver vazia
	Alert("Favor informe a RDA Inicial que devera ser trabalhada.")
	Return
endif

if Empty(mv_par04) // Se a RDA Final estiver vazia
	Alert("Favor informe a RDA Final que devera ser trabalhada.")
	Return
endif

if Empty(mv_par05) // Se Local de digitação Inicial estiver vazio
	Alert("Favor informe o local de digitação Inicial a ser trabalhado.")
	Return
endif

if Empty(mv_par06) // Se Local de digitação Final estiver vazio
	Alert("Favor informe o local de digitação Final a ser trabalhado.")
	Return
endif

//------------------------Fim das validações de variáveis/ Inicio de Atribuições------------------


cAno := ALLTRIM(MV_PAR01)
cMes := ALLTRIM(MV_PAR02)
cRD1 := ALLTRIM(MV_PAR03)
cRD2 := ALLTRIM(MV_PAR04)
cloc1:= ALLTRIM(MV_PAR05)
cloc2:= ALLTRIM(MV_PAR06)
cOpcao:= MV_PAR07 //Atribuição da opção escolhida pelo usuário para saber qual update realizar.
cDataDe:=DtoS(MV_PAR08)
cDataAte:=DtoS(MV_PAR09)

FWMsgRun( , {||GUIAS (cOpcao,cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte)} , 'Processando' , 'Aguarde. Selecionando guias...')

if (cOpcao==1) // Se a opção escolhida pelo usuário  for desmarcar valorização pagamento
	FWMsgRun( , {|| lRetp := LIMPVPG(cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte) } ,, 'Desmarcando Valorização Pagamento SADT/Internacao...' )
	
else
	if (cOpcao==2) // Se a opção escolhida pelo usuário for  desmarcar valorização  cobrança
		
		FWMsgRun( , {|| lRetc := LIMPVCB(cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte) } ,, 'Desmarcando Valorização Cobrança  SADT/Internacao...' )
	else
		if (cOpcao==3) // Se a opção escolhida pelo usuário for ambas :
			
			FWMsgRun( ,{|| lRetp := LIMPVPG(cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte) } ,, 'Desmarcando Valorização Pagamento SADT/Internacao...' )
			FWMsgRun( ,{|| lRetc := LIMPVCB(cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte) } ,, 'Desmarcando Valorização Cobrança  SADT/Internacao...' )
		endif
	endif
endif

If (lRetp ==.T.) .And. (cOpcao==1) // se não houve erro no update da funcao LIMPVPG e a opção foi desmarcar só pagamento
	GERLOG(lRetp,lRetc,cOpcao)
	MsgAlert("Fim do processo! Desmarcado valorização de pagamento!"+Chr(13)+Chr(10)+ ;
	         "ATENÇÃO! Por favor revalorizar o pagamento."+Chr(13)+Chr(10)+ ;
	         "Log gerado!")
else //  Houve erro no update da funcao LIMPVPG e a opção foi desmarcar só pagamento
	If (lRetp ==.F.) .And. (cOpcao==1)
		GERLOG(lRetp,lRetc,cOpcao)
		MsgAlert("Processo de desmarcar Valorização Pagamento com problemas.")
	Endif
Endif
If (lRetc ==.T.) .And. (cOpcao==2) // se não houve erro no update da funcao LIMPVCB e a opção foi desmarcar só Cobrança
	GERLOG(lRetp,lRetc,cOpcao)
	MsgAlert("Fim do processo! Desmarcado valorização de Cobrança!"+Chr(13)+Chr(10)+ ;
             "ATENÇÃO! Por favor revalorizar a Cobrança."+Chr(13)+Chr(10)+ ;
	         "Log gerado!")
else//  Houve erro no update da funcao LIMPVCB e a opção foi desmarcar só cobrança
	If (lRetc ==.F.) .And. (cOpcao==2)
		GERLOG(lRetp,lRetc,cOpcao)
		MsgAlert("Processo de desmarcar Valorização Cobrança com problemas.")
	Endif
Endif
If (lRetp ==.T.) .And.(lRetc ==.T.) .And. (cOpcao==3) //se não houve erro nos updates das funcões( LIMPVPG e LIMPVCB) a opção foi Ambas
	GERLOG(lRetp,lRetc,cOpcao)
	MsgAlert("Fim do processo! Desmarcado valorização de Pagamento e cobrança!"+Chr(13)+Chr(10)+ ;
	         "ATENÇÃO! Por favor revalorizar o pagamento e a cobrança."+Chr(13)+Chr(10)+ ;
	         "Log gerado!")
else
	If (lRetp ==.F.) .And.(lRetc ==.F.) .And. (cOpcao==3)
		GERLOG(lRetp,lRetc,cOpcao)
		MsgAlert("Processo de desmarcar Pagamento e Cobrança com problemas.")
	Endif
EndIf


Return()

/*

Programa--------:
Autor-----------: Vanessa

Data da Criacao-: 22/03/2018

Descrição-------: Rotina para identificar as guias a serem trabalhadas

Parametros------: cOpção : Opção escolhido pelo usuário

Retorno---------: cQuery : Retornar a query com as guias selecionadas.

*/
Static Function GUIAS (cOpcao,cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte)

Local cQuery :=''

if (cOpcao==1)  //Opção só Pagamento selecionando guias
	
	cQuery := " SELECT  BD5_NUMIMP as impresso "
	cQuery += " FROM " +  RetSqlName('BD5') + " BD5 "
	cQuery += " WHERE  BD5.BD5_FILIAL = '  '  AND BD5.D_E_L_E_T_ = ' ' "
	cQuery += " AND BD5.BD5_SITUAC = '1' "
	cQuery += " AND BD5.BD5_FASE = '3' "
	cQuery += " AND BD5.BD5_ANOPAG = '"+cAno+"' "
	cQuery += " AND BD5.BD5_MESPAG = '"+cMes+"' "
	cQuery += " AND BD5.BD5_CODLDP>= '"+cloc1+"' AND BD5.BD5_CODLDP<='"+cloc2+"' "
	cQuery += " AND EXISTS( SELECT * "
	cQuery += " FROM " + RetSqlName('BD7') + " BD7 "
	cQuery += " WHERE  BD7.BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
	cQuery += " AND BD7.BD7_CODLDP = BD5.BD5_CODLDP "
	cQuery += " AND BD7.BD7_CODPEG = BD5.BD5_CODPEG "
	cQuery += " AND BD7.BD7_NUMERO = BD5.BD5_NUMERO "
	cQuery += " AND BD7.BD7_SITUAC = BD5.BD5_SITUAC "
	cQuery += " AND BD7.BD7_ANOPAG = BD5.BD5_ANOPAG "
	cQuery += " AND BD7.BD7_MESPAG = BD5.BD5_MESPAG " 
	cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+ "' AND BD7.BD7_DATPRO<='"+cDataAte +"' " 
	cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "' AND BD7.BD7_CODRDA<= '"+cRD2+"' )"
	cQuery += " UNION ALL "
	cQuery += " SELECT BE4_NUMIMP as impresso"
	cQuery += " FROM " +  RetSqlName('BE4') + " BE4 "
	cQuery += " WHERE  BE4.BE4_FILIAL = '  '  AND BE4.D_E_L_E_T_ = ' ' "
	cQuery += " AND BE4.BE4_SITUAC = '1' "
	cQuery += " AND BE4.BE4_FASE = '3' "
	cQuery += " AND BE4.BE4_ANOPAG = '"+cAno+"' "
	cQuery += " AND BE4.BE4_MESPAG = '"+cMes+"' "
	cQuery += " AND BE4.BE4_CODLDP >= '" +cloc1+"' AND BE4.BE4_CODLDP<='"+cloc2+"' "
	cQuery += " AND EXISTS(SELECT * "
	cQuery += " FROM "+ RetSqlName('BD7') + " BD7 "
	cQuery += " WHERE  BD7.BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
	cQuery += " AND BD7.BD7_CODLDP = BE4.BE4_CODLDP "
	cQuery += " AND BD7.BD7_CODPEG = BE4.BE4_CODPEG "
	cQuery += " AND BD7.BD7_NUMERO = BE4.BE4_NUMERO "
	cQuery += " AND BD7.BD7_SITUAC = BE4.BE4_SITUAC "
	cQuery += " AND BD7.BD7_ANOPAG = BE4.BE4_ANOPAG "
	cQuery += " AND BD7.BD7_MESPAG = BE4.BE4_MESPAG "
	cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
	cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "' AND BD7.BD7_CODRDA<= '"+cRD2+"' )"
	Plsquery(cQuery,"TMPGUIP") // Rebece o resultado da query acima
	
	If TMPGUIP->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados
		Alert("Dados não encontrados" )
		TMPGUIP->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select
		Return
	Else
	    While TMPGUIP->(!Eof())         
	    	//aadd (AGuias,Alltrim{TMPGUI->IMPRESSO}) //Usado somente de o arry tiver mais de uma informação
	    	aadd (AGuiasP,{Alltrim(TMPGUIP->IMPRESSO)})
	    	TMPGUIP->(dbSkip( )) // Posicione no proximo 
	    Enddo
	Endif
	TMPGUIP->( DbCloseArea() )  

else
	If(cOpcao==2) // Só cobrança selectionado  guias 
	
		cQuery := " SELECT  BD5_NUMIMP as impresso"
		cQuery += " FROM " +  RetSqlName('BD5') + " BD5 "
		cQuery += " WHERE  BD5.BD5_FILIAL = '  '  AND BD5.D_E_L_E_T_ = ' ' "
		cQuery += " AND BD5.BD5_SITUAC = '1' "   //fixo
		cQuery += " AND BD5.BD5_FASE > '2' "    //fixo
		cQuery += " AND BD5.BD5_STAFAT<> '0' "  //fixo
		cQuery += " AND BD5.BD5_ANOPAG = '" +cAno+"' "  //informando ano
		cQuery += " AND BD5.BD5_MESPAG = '" +cMes+"' "  //informando mes
		cQuery += " AND BD5.BD5_CODLDP>= '" +cloc1+"' AND BD5.BD5_CODLDP<= '"+cloc2+"' " //informa local de digitacao
		cQuery += " AND EXISTS( SELECT * "
		cQuery += " FROM "+ RetSqlName('BD7') +" BD7 " // ESCREVE BD7010 E ALIAS
		cQuery += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
		cQuery += " AND BD7.BD7_CODLDP = BD5.BD5_CODLDP "
		cQuery += " AND BD7.BD7_CODPEG = BD5.BD5_CODPEG "
		cQuery += " AND BD7.BD7_NUMERO = BD5.BD5_NUMERO "
		cQuery += " AND BD7.BD7_SITUAC = BD5.BD5_SITUAC "
		cQuery += " AND BD7.BD7_ANOPAG = BD5.BD5_ANOPAG "
		cQuery += " AND BD7.BD7_MESPAG = BD5.BD5_MESPAG "
		cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
		cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
		cQuery += " UNION ALL "
		cQuery += " SELECT BE4_NUMIMP as impresso"
		cQuery += " FROM " +  RetSqlName('BE4') + " BE4 "
		cQuery += " WHERE  BE4.BE4_FILIAL = '  '  AND BE4.D_E_L_E_T_ = ' ' "
		cQuery += " AND BE4.BE4_SITUAC = '1' " //fixo
		cQuery += " AND BE4.BE4_FASE = '3' " //fixo
		cQuery += " AND BE4.BE4_STAFAT<> '0' "  //fixo
		cQuery += " AND BE4.BE4_ANOPAG = '"+cAno+"' "  //informando ano
		cQuery += " AND BE4.BE4_MESPAG = '"+cMes+"' "   //informando mes
		cQuery += " AND BE4.BE4_CODLDP>='"+cloc1+"' AND BE4.BE4_CODLDP<='"+cloc2+"' " //informa local digitacao
		cQuery += " AND EXISTS(SELECT * "
		cQuery += " FROM "+ RetSqlName('BD7') +" BD7 " //  ESCREVE BD5010 E ALIAS
		cQuery += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
		cQuery += " AND BD7.BD7_CODLDP = BE4.BE4_CODLDP "
		cQuery += " AND BD7.BD7_CODPEG = BE4.BE4_CODPEG "
		cQuery += " AND BD7.BD7_NUMERO = BE4.BE4_NUMERO "
		cQuery += " AND BD7.BD7_SITUAC = BE4.BE4_SITUAC "
		cQuery += " AND BD7.BD7_ANOPAG = BE4.BE4_ANOPAG "
		cQuery += " AND BD7.BD7_MESPAG = BE4.BE4_MESPAG " 
	    cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
		cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
		Plsquery(cQuery,"TMPGUIC") // Rebece o resultado da query acima
		
		If  TMPGUIC->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados
			Alert("Dados não encontrados " )
			TMPGUIC->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select
			Return
		Else
	        While TMPGUIC->(!Eof())         
	        	aadd (AGuiasC,{Alltrim(TMPGUIC->IMPRESSO)})
	        	TMPGUIC->(dbSkip( )) //POSICINA, VAI PARA PROXIMO  
	        Enddo
	    Endif
		TMPGUIC->( DbCloseArea() )  		
	Else
		if (cOpcao==3) //Ambas ( Pagamento e cobrança )
			
			cQuery := " SELECT  BD5_NUMIMP as impresso"
			cQuery += " FROM " +  RetSqlName('BD5') + " BD5 "
			cQuery += " WHERE  BD5.BD5_FILIAL = '  '  AND BD5.D_E_L_E_T_ = ' ' "
			cQuery += " AND BD5.BD5_SITUAC = '1' "
			cQuery += " AND BD5.BD5_FASE = '3' "
			cQuery += " AND BD5.BD5_ANOPAG = '"+cAno+"' "
			cQuery += " AND BD5.BD5_MESPAG = '"+cMes+"' "
			cQuery += " AND BD5.BD5_CODLDP>= '"+cloc1+"' AND BD5.BD5_CODLDP<='"+cloc2+"' "
			cQuery += " AND EXISTS( SELECT * "
			cQuery += " FROM " + RetSqlName('BD7') + " BD7 "
			cQuery += " WHERE  BD7.BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
			cQuery += " AND BD7.BD7_CODLDP = BD5.BD5_CODLDP "
			cQuery += " AND BD7.BD7_CODPEG = BD5.BD5_CODPEG "
			cQuery += " AND BD7.BD7_NUMERO = BD5.BD5_NUMERO "
			cQuery += " AND BD7.BD7_SITUAC = BD5.BD5_SITUAC "
			cQuery += " AND BD7.BD7_ANOPAG = BD5.BD5_ANOPAG "
			cQuery += " AND BD7.BD7_MESPAG = BD5.BD5_MESPAG " 
			cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
			cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "' AND BD7.BD7_CODRDA<= '"+cRD2+"' )"
			cQuery += " UNION ALL "
			cQuery += " SELECT BE4_NUMIMP as impresso"
			cQuery += " FROM " +  RetSqlName('BE4') + " BE4 "
			cQuery += " WHERE  BE4.BE4_FILIAL = '  '  AND BE4.D_E_L_E_T_ = ' ' "
			cQuery += " AND BE4.BE4_SITUAC = '1' "
			cQuery += " AND BE4.BE4_FASE = '3' "
			cQuery += " AND BE4.BE4_ANOPAG = '"+cAno+"' "
			cQuery += " AND BE4.BE4_MESPAG = '"+cMes+"' "
			cQuery += " AND BE4.BE4_CODLDP >= '" +cloc1+"' AND BE4.BE4_CODLDP<='"+cloc2+"' "
			cQuery += " AND EXISTS(SELECT * "
			cQuery += " FROM "+ RetSqlName('BD7') + " BD7 "
			cQuery += " WHERE  BD7.BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
			cQuery += " AND BD7.BD7_CODLDP = BE4.BE4_CODLDP "
			cQuery += " AND BD7.BD7_CODPEG = BE4.BE4_CODPEG "
			cQuery += " AND BD7.BD7_NUMERO = BE4.BE4_NUMERO "
			cQuery += " AND BD7.BD7_SITUAC = BE4.BE4_SITUAC "
			cQuery += " AND BD7.BD7_ANOPAG = BE4.BE4_ANOPAG "
			cQuery += " AND BD7.BD7_MESPAG = BE4.BE4_MESPAG "   
			cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
			cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "' AND BD7.BD7_CODRDA<= '"+cRD2+"' )"
			cQuery += " UNION ALL "
			cQuery += " SELECT  BD5_NUMIMP "
			cQuery += " FROM " +  RetSqlName('BD5') + " BD5 "
			cQuery += " WHERE  BD5.BD5_FILIAL = '  '  AND BD5.D_E_L_E_T_ = ' ' "
			cQuery += " AND BD5.BD5_SITUAC = '1' "   //fixo
			cQuery += " AND BD5.BD5_FASE > '2' "    //fixo
			cQuery += " AND BD5.BD5_STAFAT<> '0' "  //fixo
			cQuery += " AND BD5.BD5_ANOPAG = '" +cAno+"' "  //informando ano
			cQuery += " AND BD5.BD5_MESPAG = '" +cMes+"' "  //informando mes
			cQuery += " AND BD5.BD5_CODLDP>= '" +cloc1+"' AND BD5.BD5_CODLDP<= '"+cloc2+"' " //informa local de digitacao
			cQuery += " AND EXISTS( SELECT * "
			cQuery += " FROM "+ RetSqlName('BD7') +" BD7 " // ESCREVE BD7010 E ALIAS
			cQuery += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
			cQuery += " AND BD7.BD7_CODLDP = BD5.BD5_CODLDP "
			cQuery += " AND BD7.BD7_CODPEG = BD5.BD5_CODPEG "
			cQuery += " AND BD7.BD7_NUMERO = BD5.BD5_NUMERO "
			cQuery += " AND BD7.BD7_SITUAC = BD5.BD5_SITUAC "
			cQuery += " AND BD7.BD7_ANOPAG = BD5.BD5_ANOPAG "
			cQuery += " AND BD7.BD7_MESPAG = BD5.BD5_MESPAG "
			cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
			cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
			cQuery += " UNION ALL "
			cQuery += " SELECT BE4_NUMIMP "
			cQuery += " FROM " +  RetSqlName('BE4') + " BE4 "
			cQuery += " WHERE  BE4.BE4_FILIAL = '  '  AND BE4.D_E_L_E_T_ = ' ' "
			cQuery += " AND BE4.BE4_SITUAC = '1' " //fixo
			cQuery += " AND BE4.BE4_FASE = '3' " //fixo
			cQuery += " AND BE4.BE4_STAFAT<> '0' "  //fixo
			cQuery += " AND BE4.BE4_ANOPAG = '"+cAno+"' "  //informando ano
			cQuery += " AND BE4.BE4_MESPAG = '"+cMes+"' "   //informando mes
			cQuery += " AND BE4.BE4_CODLDP>='"+cloc1+"' AND BE4.BE4_CODLDP<='"+cloc2+"' " //informa local digitacao
			cQuery += " AND EXISTS(SELECT * "
			cQuery += " FROM "+ RetSqlName('BD7') +" BD7 " //  ESCREVE BD5010 E ALIAS
			cQuery += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
			cQuery += " AND BD7.BD7_CODLDP = BE4.BE4_CODLDP "
			cQuery += " AND BD7.BD7_CODPEG = BE4.BE4_CODPEG "
			cQuery += " AND BD7.BD7_NUMERO = BE4.BE4_NUMERO "
			cQuery += " AND BD7.BD7_SITUAC = BE4.BE4_SITUAC "
			cQuery += " AND BD7.BD7_ANOPAG = BE4.BE4_ANOPAG "
			cQuery += " AND BD7.BD7_MESPAG = BE4.BE4_MESPAG "
			cQuery += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
			cQuery += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
			
			Plsquery(cQuery,"TMPGUIPC") // Rebece o resultado da query acima
			
			If  TMPGUIPC->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados
				Alert("Dados não encontrados " )
				TMPGUIPC->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select
				Return
			else
	            While TMPGUIPC->(!Eof())         
	    	     aadd (AGuiasPC,{Alltrim(TMPGUIPC->IMPRESSO)})
	    	     TMPGUIPC->(dbSkip( )) //POSICINA, VAI PARA PROXIMO 
	            Enddo
	         Endif
	         TMPGUIPC->( DbCloseArea())
		Endif
	Endif
Endif
Return()    

/*

Programa--------: GERLOG
Autor-----------: Laura Peghini

Data da Criacao-: 08/03/2018

Descrição-------: Rotina de geração de log do processamento

Parametros------: Nenhum

Retorno---------: Nenhum

*/

Static Function GERLOG( lRetp,lRetc,cOpcao)

Local _cCaminho :=''
Local _cLin		:=''
Local cdescri   :=''   
Private cCpo	:= ""


Private nHdl

_cCaminho := Alltrim(cGetFile("*.*","Selecione o Diretorio de Destino",1,"j:\",.F.,GETF_LOCALHARD + GETF_NETWORKDRIVE + GETF_RETDIRECTORY))
_cCaminho += "log_desmarca_valorizacao_guias"+DTOS( Date() ) + StrTran( Time() , ':' , '' )+".txt"
nHdl := fCreate(_cCaminho)

If nHdl == -1
	MsgAlert("O arquivo de nome "+_cCaminho+" nao pode ser executado! Verifique os parametros.","Atencao!")
	Return
Endif

//Criando depara para monstar a opção que o cliente escolheu dentro do arquivo de log.

//Opçao escolhida pelo usuário:

If (cOpcao==1)
	cdescri :='Só Pagamento'
Else
	If (cOpcao==2)
		cdescri :='Só Cobrança'
	Else
		If (cOpcao==3)
			cdescri :='Ambos'
		Endif
	Endif
Endif

//Cabeçalho do Log:

cLin := "===================================================================="+cEOL
cLin += "Usuário logado: " +cUserName+cEOL+cEOL
cLin += "Data: "+SubStr(DtoC(Date()),1,2)+'/'+SubStr(DtoC(Date()),4,2)+'/'+SubStr(DtoC(Date()),7,4)+space(01)+Time()+cEOL+cEOL
cLin += "Ano Pagamento: "+MV_PAR01+cEOL+cEOL
cLin += "Mês Pagamento: "+MV_PAR02+cEOL+cEOL
cLin += "Cód.RDA.De: "+MV_PAR03+" até "+MV_PAR04+cEOL+cEOL
cLin += "Local Digitação: "+MV_PAR05+" até "+MV_PAR06+cEOL+cEOL 
cLin += "Data De: "+Dtoc(MV_PAR08)+" até "+Dtoc(MV_PAR09)+cEOL+cEOL //EXEMPLO: Dtoc=22/05/2019 JÁ DtoS=20190522
cLin += "Opção marcada: "+cdescri+cEOL+cEOL
cLin += cEOL+"==================================================================="+cEOL

//Mensagem de deverá aparecer no corpo do Log do arquivo :

If (lRetp ==.F.) .And. (cOpcao==1) // Se falso  e a oção for pagamento .(Se diferente de .T. que recebeu la no inicio do fonte.)
	cLin += "Processo de desmarcar valorização de pagamento com problemas."+cEOL+cEOL
Else // Senão
	if (lRetp ==.T.) .And. (cOpcao==1)
		cLin += "Processo de desmarcar valorização de pagamento realizado com sucesso."+cEOL+cEOL
	Else
		If (lRetc ==.F.) .And. (cOpcao==2)//Se falso e a opção for cobrança.
			cLin += "Processo de desmarcar valorização de cobrança com problemas."+cEOL+cEOL
		Else //Senão
			If (lRetc ==.T.) .And. (cOpcao==2)
				cLin += "Processo de demarcar valorização de cobrança  realizado com sucesso."+cEOL+cEOL
			Else
				If (lRetp ==.F.) .And.(lRetc ==.F.) .And. (cOpcao==3)
					cLin += "Processo de desmarcar valorização de pagamento com problemas." +cEOL+cEOL
					cLin += "Processo de desmarcar valorização de cobrança com problemas."+cEOL+cEOL
				Else
					cLin += "Processo de desmarcar valorização de pagamento realizado com sucesso." +cEOL+cEOL
					cLin += "Processo de desmarcar valorização de cobrança  realizado com sucesso."  +cEOL+cEOL
				Endif
			Endif
		Endif
	Endif
Endif

If fWrite(nHdl,cLin,Len(cLin)) != Len(cLin)
	If !MsgAlert("Ocorreu um erro na gravacao do arquivo. Continua?","Atencao!")
		return
	Endif
Endif

//	Escrever as  guias no log 

cLin := "==================================================================="+cEOL+cEOL 
cLin += "GUIAS: " + Chr(10)   

If fWrite(nHdl,cLin,Len(cLin)) != Len(cLin)
	If !MsgAlert("Ocorreu um erro na gravacao do arquivo. Continua?","Atencao!")
		return
	Endif
Endif    
               
x:=0 //contador para as colunas neste caso serão 10 colunas 
y:=0 //contador para a linha quando tiver menos de 10 colunas 
cLin:="" //começa limpo - primeiro espaçinho

if cOpcao==1 
	For n:=1 to Len(AGuiasP) // contandor da posição do arry ( guias)
		cLin += AGuiasP[n][1]+', '  //vai acumulando as guias		
		x++ 
		y:=n //controlar a linha quando tiver menos de 10 elementos    
		if x ==10 .And. x <= Len(AGuiasP)  
			cLin+=cEOL  //quebra de linha 
			fWrite(nHdl,cLin) //escrevendo 
			x:=0  //reiniciando o contador das colunas
			y+=10 //y=y+10
	   		cLin:="" //limpando linha 	
		Endif 
		if y == Len(AGuiasP)
			fWrite(nHdl,cLin)
			cLin:=""  //limpnado linha 
	    endif      
	Next  
Endif	
if cOpcao==2 
	For n:=1 to Len(AGuiasC) // contandor da posição do arry ( guias)
		cLin += AGuiasC[n][1]+', '  //vai acumulando as guias		
		x++ 
		y:=n //controlar a linha quando tiver menos de 10 elementos    
		if x ==10 .And. x <= Len(AGuiasC)  
			cLin+=cEOL  //quebra de linha 
			fWrite(nHdl,cLin) //escrevendo 
			x:=0 //reiniciando o contador das colunas
			y+=10 //y=y+10
	   		cLin:="" //limpando linha 	
		Endif 
		if y == Len(AGuiasC)
			fWrite(nHdl,cLin)
			cLin:=""  //limpnado linha 
	    endif      
	Next
Endif
if cOpcao==3 
	For n:=1 to Len(AGuiasPC) // contandor da posição do arry ( guias)
		cLin += AGuiasPC[n][1]+', '  //vai acumulando as guias		
		x++ 
		y:=n //controlar a linha quando tiver menos de 10 elementos    
		if x ==10 .And. x <= Len(AGuiasPC)  
			cLin+=cEOL  //quebra de linha 
			fWrite(nHdl,cLin) //escrevendo 
			x:=0//reiniciando o contador das colunas
			y+=10 //y=y+10
	   		cLin:="" //limpando linha 	
		Endif 
		if y == Len(AGuiasPC)
			fWrite(nHdl,cLin)
			cLin:=""  //limpnado linha 
	    endif      
	Next
Endif		
	
fClose(nHdl)


Return()

/*

Programa--------: LIMPVPG
Autor-----------: Vanessa Cruz

Data da Criacao-: 07/03/2019

Descrição-------: Rotina de update limpar Valorização de Pagamento bd5010 e be4010

Parametros------: Nenhum

Retorno---------: Nenhum

*/

//----------------------------Funcão para desmarcar Valorização de Pagamento  tabelas bd5010 e be4010-------------------------------

Static Function LIMPVPG(cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte)

Local cUpdpag	:= '' //Variavel para update de desmarcar pagamento
Local _cAlias	:= GetNextAlias()
Local lRetp		:= .T.  //Variavel para erro
Local nStatus	:= 0


//select guias

//Enquanto select guias  preencher arry

Begin Transaction   // Update tabela bd5010

cUpdpag := " UPDATE  "+RetSqlName('BD5')+" BD5 "  //  ESCREVE BD5010 E ALIAS
cUpdpag += " SET  "
cUpdpag += " BD5.BD5_SUBESP = ' '  " // LIMPANDO PAGAMENTO
cUpdpag += " WHERE "+ RetSqlCond('BD5')
cUpdpag += " AND BD5.BD5_SITUAC = '1' "   //fixo
cUpdpag += " AND BD5.BD5_FASE = '3' "    //fixo
cUpdpag += " AND BD5.BD5_ANOPAG = '" +cAno+"' "  //informou ano
cUpdpag += " AND BD5.BD5_MESPAG = '" +cMes+"' "  //informou mes
cUpdpag += " AND BD5.BD5_CODLDP>= '"+cloc1+"' AND BD5.BD5_CODLDP<='"+cloc2+"' " //informou local digitacao
cUpdpag += " AND EXISTS( SELECT * "
cUpdpag += " FROM "+ RetSqlName('BD7') +" BD7 " // ESCREVE BD7010 E ALIAS
cUpdpag += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
cUpdpag += " AND BD7.BD7_CODLDP = BD5.BD5_CODLDP "
cUpdpag += " AND BD7.BD7_CODPEG = BD5.BD5_CODPEG "
cUpdpag += " AND BD7.BD7_NUMERO = BD5.BD5_NUMERO "
cUpdpag += " AND BD7.BD7_SITUAC = BD5.BD5_SITUAC "
cUpdpag += " AND BD7.BD7_ANOPAG = BD5.BD5_ANOPAG "
cUpdpag += " AND BD7.BD7_MESPAG = BD5.BD5_MESPAG "
cUpdpag += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
cUpdpag += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
TCSQLEXEC(cUpdpag)
End Transaction

nStatus := TCSqlExec( cUpdpag )

If nStatus < 0 // Se ocorreu erro no update da bd5010
	ConOut( "TCSQLError() " + TCSQLError() )
	lRetp := .F.
EndIf

Begin Transaction // Update tabela be4010

cUpdpag := " UPDATE "+ RetSqlName('BE4') +" BE4 " //  ESCREVE BE4010 E ALIAS
cUpdpag += " SET  "
cUpdpag += " BE4.BE4_SUBESP = ' '  " //LIMPANDO PAGAMENTO
cUpdpag += " WHERE "+ RetSqlCond('BE4')
cUpdpag += " AND BE4.BE4_SITUAC = '1' " //fixo
cUpdpag += " AND BE4.BE4_FASE = '3' " //fixo
cUpdpag += " AND BE4.BE4_ANOPAG = '"+cAno+"' " //informou ano
cUpdpag += " AND BE4.BE4_MESPAG = '"+cMes+"' " //informou mes
cUpdpag += " AND BE4.BE4_CODLDP >= '"+cloc1+"' AND BE4.BE4_CODLDP<='"+cloc2+"' " //informou local de digitacao
cUpdpag += " AND EXISTS(SELECT * "
cUpdpag += " FROM "+ RetSqlName('BD7') +" BD7 " //  ESCREVE BD5010 E ALIAS
cUpdpag += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
cUpdpag += " AND BD7.BD7_CODLDP = BE4.BE4_CODLDP "
cUpdpag += " AND BD7.BD7_CODPEG = BE4.BE4_CODPEG "
cUpdpag += " AND BD7.BD7_NUMERO = BE4.BE4_NUMERO "
cUpdpag += " AND BD7.BD7_SITUAC = BE4.BE4_SITUAC "
cUpdpag += " AND BD7.BD7_ANOPAG = BE4.BE4_ANOPAG "
cUpdpag += " AND BD7.BD7_MESPAG = BE4.BE4_MESPAG "
cUpdpag += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
cUpdpag += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
TCSQLEXEC(cUpdpag)

End Transaction

nStatus := TCSqlExec( cUpdpag )

If nStatus < 0 // Se ocorreu erro no update da be4010
	ConOut( "TCSQLError() " + TCSQLError() )
	lRetp := .F.
EndIf


Return(lRetp)

/*

Programa--------: LIMPVCB
Autor-----------: Vanessa Cruz

Data da Criacao-: 07/03/2019

Descrição-------: Rotina de update limpar Valorização de Cobrança bd5010 e be4010

Parametros------: Nenhum

Retorno---------: Nenhum

*/
//----------------------------Função para desmarcar Valorização de Cobrança  tabelas bd5010 e be4010
Static Function LIMPVCB(cAno,cMes,cloc1,cloc2,cRD1,cRD2,cDataDe,cDataAte)

Local cUpdcob	:= ''
Local lRetc		:= .T.
Local nStatus	:= 0

Begin Transaction // Update tabela bd5010(Limpando Cobrança)

cUpdcob := " UPDATE  "+RetSqlName('BD5') + " BD5 " //  ESCREVE BD5010 E ALIAS
cUpdcob += " SET  "
cUpdcob += " BD5.BD5_CC = ' '  " //LIMPANDO COBRANÇA
cUpdcob += " WHERE "+ RetSqlCond('BD5')
cUpdcob += " AND BD5.BD5_SITUAC = '1' "   //fixo
cUpdcob += " AND BD5.BD5_FASE > '2' "    //fixo
cUpdcob += " AND BD5.BD5_STAFAT<> '0' "  //fixo
cUpdcob += " AND BD5.BD5_ANOPAG = '" +cAno+"' "  //informando ano
cUpdcob += " AND BD5.BD5_MESPAG = '" +cMes+"' "  //informando mes
cUpdcob += " AND BD5.BD5_CODLDP>= '" +cloc1+"' AND BD5.BD5_CODLDP<= '"+cloc2+"' " //informa local de digitacao
cUpdcob += " AND EXISTS( SELECT * "
cUpdcob += " FROM "+ RetSqlName('BD7') +" BD7 " // ESCREVE BD7010 E ALIAS
cUpdcob += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
cUpdcob += " AND BD7.BD7_CODLDP = BD5.BD5_CODLDP "
cUpdcob += " AND BD7.BD7_CODPEG = BD5.BD5_CODPEG "
cUpdcob += " AND BD7.BD7_NUMERO = BD5.BD5_NUMERO "
cUpdcob += " AND BD7.BD7_SITUAC = BD5.BD5_SITUAC "
cUpdcob += " AND BD7.BD7_ANOPAG = BD5.BD5_ANOPAG "
cUpdcob += " AND BD7.BD7_MESPAG = BD5.BD5_MESPAG "   
cUpdcob += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
cUpdcob += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
TCSQLEXEC(cUpdcob)
End Transaction

nStatus := TCSqlExec( cUpdcob )

If nStatus < 0
	ConOut( "TCSQLError() " + TCSQLError() )
	lRetc := .F.
EndIf

Begin Transaction // Update tabela be4010(Limpando Cobrança)

cUpdcob := " UPDATE "+ RetSqlName('BE4') +" BE4 " //  ESCREVE BE4010 E ALIAS
cUpdcob += " SET  "
cUpdcob += " BE4.BE4_CC = ' ' "// LIMPANDO COBRANÇA
cUpdcob += " WHERE "+ RetSqlCond('BE4')
cUpdcob += " AND BE4.BE4_SITUAC = '1' " //fixo
cUpdcob += " AND BE4.BE4_FASE = '3' " //fixo
cUpdcob += " AND BE4.BE4_STAFAT<> '0' "  //fixo
cUpdcob += " AND BE4.BE4_ANOPAG = '"+cAno+"' "  //informando ano
cUpdcob += " AND BE4.BE4_MESPAG = '"+cMes+"' "   //informando mes
cUpdcob += " AND BE4.BE4_CODLDP>='"+cloc1+"' AND BE4.BE4_CODLDP<='"+cloc2+"' " //informa local digitacao
cUpdcob += " AND EXISTS(SELECT * "
cUpdcob += " FROM "+ RetSqlName('BD7') +" BD7 " //  ESCREVE BD5010 E ALIAS
cUpdcob += " WHERE  BD7_FILIAL = '  '  AND BD7.D_E_L_E_T_ = ' ' "
cUpdcob += " AND BD7.BD7_CODLDP = BE4.BE4_CODLDP "
cUpdcob += " AND BD7.BD7_CODPEG = BE4.BE4_CODPEG "
cUpdcob += " AND BD7.BD7_NUMERO = BE4.BE4_NUMERO "
cUpdcob += " AND BD7.BD7_SITUAC = BE4.BE4_SITUAC "
cUpdcob += " AND BD7.BD7_ANOPAG = BE4.BE4_ANOPAG "
cUpdcob += " AND BD7.BD7_MESPAG = BE4.BE4_MESPAG " 
cUpdcob += " AND BD7.BD7_DATPRO >='"+cDataDe+"' AND BD7.BD7_DATPRO <='"+cDataAte+"' " 
cUpdcob += " AND BD7.BD7_CODRDA >='"+cRD1+ "'AND BD7.BD7_CODRDA<= '"+cRD2+"' )" //informa RDA
TCSQLEXEC(cUpdcob)
End Transaction
nStatus := TCSqlExec( cUpdcob )

If nStatus < 0
	ConOut( "TCSQLError() " + TCSQLError() )
	lRetc := .F.
EndIf

Return(lRetc)



/*

Programa--------: AjustaSX1
Autor-----------: Laura Peghini

Data da Criacao-: 08/03/2018

Descrição-------: Rotina de manutenção do grupo de perguntas (SX1)

Parametros------: _cPerg - Código de identificação do grupo de perguntas

Retorno---------: Nenhum

*/
Static Function AjustaSX1( _cPerg )

Local _aHelpAux := {}

_aHelpAux := { 'Informe o ano de pagamento de referência'		, ' para filtrar o relatório.' }
U_NTPutSX1( _cPerg , "01" , "Ano Pag."		,"","","","C",04,0,0,"G","","","","","MV_PAR01","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe o mês de pagamento de referência'			, ' para filtrar o relatório.' }
U_NTPutSX1( _cPerg , "02" , "Mês Pag."		,"","","","C",02,0,0,"G","","","","","MV_PAR02","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe o código RDA Inicial para filtrar'		, ' o relatório.' }
U_NTPutSX1( _cPerg , "03" , "Cód. RDA de"	,"","","","C",06,0,0,"G","","BAUPLS","","","MV_PAR03","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe o código RDA final para'		, ' filtrar o relatório.' }
U_NTPutSX1( _cPerg , "04" , "Cód. RDA até"		,"","","","C",06,0,0,"G","","BAUPLS","","","MV_PAR04","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe o local de digitação inicial para'		, ' filtrar o relatório.' }
U_NTPutSX1( _cPerg , "05" , "Local Dig. de"	,"","","","C",04,0,0,"G","","","","","MV_PAR05","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe o local de digitação final para'		, ' filtrar o relatório.' }
U_NTPutSX1( _cPerg , "06" , "Local Dig. até" ,"","","","C",04,0,0,"G","","","","","MV_PAR06","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe qual valorizacao será desmarcada'		, ' filtrar o relatório.' }
U_NTPutSX1( _cPerg , "07" , "Desmarcar qual valorizacao" ,"","","","C",01,0,0,"C","","","","","MV_PAR07","So Pagamento","","","","So Cobrança","","","Ambos","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )

_aHelpAux := { 'Informe Data de Atendimento De'		 , ' filtrar o relatório.' }
U_NTPutSX1( _cPerg , "08" , "Dt. Maior/Igual que" ,"","","","D",08,0,0,"G","","","","","MV_PAR08","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )    

_aHelpAux := { 'Informe Data de Atendimento Ate'		 , ' filtrar o relatório.' }
U_NTPutSX1( _cPerg , "09" , "Dt. Menor/Igual que" ,"","","","D",08,0,0,"G","","","","","MV_PAR09","","","","","","","","","","","","","","","","",_aHelpAux,_aHelpAux,_aHelpAux )
Return()