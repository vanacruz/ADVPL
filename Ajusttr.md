#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : AJUSTTR  ºAutor  : Vanessa        º Data:  12/11/18        º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. : Realizar ajustes de  Percentual de valor apresentado em taxas              º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function AJUSTTR()  //Funcao de usuário maximo 8 caracteres

Local cQRY       := ""
Local cQuery     := ""   
Local cLocal     := ""                                      
Local cPeg       := ""
Local cNpeg      := ""
Local cnImp      := "" 
Local cCodigo    := ""
Local cSeq       := ""
Local cAno       := ""
Local cMes       := ""
Local nPerc      := 0 // Podera receber os seguintes valores  50% , 70% ou 80% 
Local nPHMR      := 0
Local nPCOR      := 0
Private nTotpag  := 0
Private nTotcob  := 0

Private cPerg:= "AJUSTT" 

CriaSX1()  
              
if !Pergunte(cPerg,.T.,"Ajustando percentual",.T.)
       return()
endif  
      

if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03); 
       .And. Empty(mv_par04) .And. Empty(mv_par05) .And. Empty(mv_par06);
       .And. Empty(mv_par07) .And. Empty(mv_par08) .And. Empty(mv_par09) .And. Empty(mv_par10) .And. Empty(mv_par11) 
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif   


if Empty(mv_par01) // Se o local de digitação estiver vazio  
	Alert("Informe o local de digitação.")
 Return
endif

if Empty(mv_par02) // Se o numero da Peg estiver vazio 
	Alert("Informe o numero completo da PEG.")
 Return
endif

if Empty(mv_par03) // Se o numero da guia dentro da peg
	Alert("Informe o numero da guia completo  dentro da peg .")
    Return
endif  

if Empty(mv_par04) // Se o numero impresso estiver vazio 
	Alert("Informe o numero completo  impresso da guia.")
 Return
endif

if Empty(mv_par05) // Se o código do procedimento estiver vazio 
	Alert("Informe o procedimento.")
 Return
endif

if Empty(mv_par06) // Se a sequencia do procedimento estiver vazio 
	Alert("Informe a sequencia do procedimento .")
 Return
endif

if Empty(mv_par07) // Se o ano da competência estiver vazio 
	Alert("Informe o ano de competência .")
 Return
endif

if Empty(mv_par08) // Se o mês da competência estiver vazio
	Alert("Informe o mês da competência.")
    Return
endif 
     
 if Empty(mv_par09) // Se percentual estiver vazio 
	Alert("Informe o percentual a ser aplicado.")
    Return
endif 

 if ((mv_par09 != '50') .And. (mv_par09 != '70')  .And. (mv_par09 != '80'))  //mv_par( caracter)comparando com caracter por causa das aspas 
	Alert("Os percentuais(%) previstos sao: 50 ou 70 ou 80")
    Return
endif  


 if Empty(mv_par10) // Se o valor para pagamento da Unidade HMR estiver vazio
	Alert("Por favor informe o valor de pagamento da Unidade HMR .")
    Return
endif  

 if Empty(mv_par11) // Se o valor para pagamento da Unidade COR estiver vazio
	Alert("Por favor informe o valor de pagamento da Unidade COR .")
    Return
endif     	

cLocal  := AllTrim(mv_par01)
cPeg    := AllTrim(mv_par02)
cNpeg   := AllTrim(mv_par03)  
cnImp   := AllTrim(mv_par04) 
cCodigo := AllTrim(mv_par05)
cSeq    := AllTrim(mv_par06)
cAno    := Alltrim(mv_par07)
cMes    := Alltrim(mv_par08)
nPerc   := Alltrim(mv_par09) 
nPHMR   := VAL(strtran(mv_par10,",",".")) //valor informado para pagamento da Unidade de Medica HM. //Convertendo virgula em ponto
nPCOR   := VAL(strtran(mv_par11,",",".")) //valor informado para pagamento  da Unidade de Medica COR //Convertendo virgula em ponto
                                                        
 //VERIFICANDO  COMPETENCIA  

cQRY := "SELECT BA0_YCPTCM FROM BA0010 "
cQRY += "WHERE D_E_L_E_T_ = ' ' " 
cQRY += "AND BA0_CODINT = '021' "
cQRY += "AND BA0_CODIDE = '0'"  

Plsquery(cQRY,"TMP_BA0") 

If (AllTrim(mv_par07)+AllTrim(mv_par08)) <> AllTrim(TMP_BA0->BA0_YCPTCM) 
	Alert("Ano e Mes de pagamento informados é diferente  que competencia atual " + Chr(13) + Chr(10) +"do Contas Médicas.") 
	TMP_BA0->(dbCloseArea())
	Return
EndIf
TMP_BA0->(dbCloseArea())

 

cQuery:= " select bd7_codldp,bd7_codpeg,bd7_numero,bd7_numimp,bd7_sequen, "
cQuery +=" bd7_codpro,bd7_vlrtpf,bd7_vlrglo,bd7_vlrpag,bd7_anopag,bd7_mespag "
cQuery +=" from " +  RetSqlName('BD7') + " " 
cQuery +=" where " + RetSqlName('BD7') + ".d_e_l_e_t_=' ' "
cQuery +=" and bd7_filial = ' ' "
cQuery +=" and bd7_codldp=  '"+cLocal+"' "
cQuery +=" and bd7_codpeg = '"+cPeg+"' "
cQuery +=" and bd7_numero = '"+cNpeg+"' "
cQuery +=" and bd7_numimp = '"+cnImp+"' "
cQuery +=" and bd7_codpro=  '"+cCodigo+"' "
cQuery +=" and bd7_sequen = '"+cSeq+"' "
cQuery +=" and bd7_anopag = '"+cAno+"' "
cQuery +=" and bd7_mespag = '"+cMes+"' " 
cQuery +=" and bd7_codpad = '14'"
cQuery +=" and substr(bd7_codpro,1,2) ='41'" 
cQuery +=" and bd7_situac='1'"  
cQuery +=" and bd7_fase<>'4'"



Plsquery(cQuery,"TMPBD7") // Rebece o resultado da query acima  bd7010 

If  TMPBD7->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar o impresso: " +cnImp + " Esta correto? Esta ativa e diferente de faturada? " ) 
    TMPBD7->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select 
    Return 
EndIf  

TMPBD7->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 
                                                                          
																		  																 
FWMsgRun( , {||AJUSTTR(cLocal,cPeg,cNpeg,cnImp,cCodigo,cSeq,cAno,cMes,nPerc,nPHMR,nPCOR)} , 'Processando' , 'Aguarde. Atualizando percentual do item...')  //chamando a função que realmente vai fazer o update 
FWMsgRun( , {||SOMAGUI(cLocal,cPeg,cNpeg,cnImp,cAno,cMes)} , 'Processando' , 'Aguarde. Atualizando cabeçalho da guia...')

//Alert("Concluido")

cMensagem := "Atualização completa! " + chr(13)+chr(10) 

MSGINFO(cMensagem)

cMensagem := "Resumo: " + chr(13)+chr(10) 
cMensagem += "Guia: " + cnImp  + chr(13)+chr(10)
cMensagem += "Peg: " + cPeg  + chr(13)+chr(10)   
cMensagem += "Numero: " + cNpeg  + chr(13)+chr(10)  
cMensagem += "Ano: " + cAno  + chr(13)+chr(10) 
cMensagem += "Mes: " + cMes  + chr(13)+chr(10) 
cMensagem += "Codigo: " + cCodigo  + chr(13)+chr(10) 
cMensagem += "Sequen: " + cSeq  + chr(13)+chr(10) 
cMensagem += "Total a ser pago: " +cValToChar(nTotpag) + chr(13)+chr(10)
cMensagem += "Total a ser cobrado: " +cValToChar(nTotcob) + chr(13)+chr(10)


MSGINFO(cMensagem)   

//Tentando limpar a tela quando abrir novamente a rotina   

dbSelectArea("SX1")
dbSetOrder(1)
If dbSeek(Padr(cPerg, 10) + "01")
	RecLock("SX1", .F.)
	SX1->X1_CNT01 := " "
	SX1->(MsUnLock())
Endif
                   
Return     

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  AJUSTTR  ºAutor : Vanessa Cruz          º Data   17/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. : Update                      º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

//Função para ajustar bd7010 e bd6010 tomografia e ressonância  de acordo com a necessidade.

Static Function AJUSTTR(cLocal,cPeg,cNpeg,cnImp,cCodigo,cSeq,cAno,cMes,nPerc,nPHMR,nPCOR) //Função Static  será vista somente aqui.

Local Update := " "  

  //update - Tabela bd7010 Unidade de Medida HMR  

   Begin Transaction   
	 
    Update := " update " + RetSqlName('BD7') + " " 
    Update += " set  bd7_perac="+cValToChar(nPerc)+", " 	
    Update += " bd7_vlrpag="+cValToChar(nPHMR)+", " 
    Update += " bd7_vlrtpf="+cValToChar(nPHMR)+" " 	
    Update += " where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '  "
    Update += " and bd7_codldp='"+cLocal+"' "
    Update += " and bd7_codpeg='"+cPeg+"' "
    Update += " and bd7_numero='"+cNpeg+"' "
    Update += " and bd7_numimp='"+cnImp+"' " 
    Update += " and bd7_codpro='"+cCodigo+"' "
    Update += " and bd7_sequen='"+cSeq+"' "
    Update += " and bd7_anopag='"+cAno+"' " 
    Update += " and bd7_mespag='"+cMes+"' "
    Update += " and bd7_codpad = '14'"
    Update += " and substr(bd7_codpro,1,2) ='41'"
    Update += " and bd7_codunm ='HMR'"
    
    TCSQLEXEC(Update) 
    
    End Transaction 

   //update - Tabela bd7010 Unidade de Medida COR 	

  Begin Transaction  
 	 	 
    Update := " update " + RetSqlName('BD7') + " " 
    Update += " set  bd7_perac="+cValToChar(nPerc)+", " 	
    Update += " bd7_vlrpag="+cValToChar(nPCOR)+", " 
    Update += " bd7_vlrtpf="+cValToChar(nPCOR)+" " 		
    Update += " where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '  "
    Update += " and bd7_codldp='"+cLocal+"' "
    Update += " and bd7_codpeg='"+cPeg+"' "
    Update += " and bd7_numero='"+cNpeg+"' "
    Update += " and bd7_numimp='"+cnImp+"' " 
    Update += " and bd7_codpro='"+cCodigo+"' "
    Update += " and bd7_sequen='"+cSeq+"' "
    Update += " and bd7_anopag='"+cAno+"' " 
    Update += " and bd7_mespag='"+cMes+"' "
    Update += " and bd7_codpad = '14'"
    Update += " and substr(bd7_codpro,1,2) ='41'"
    Update += " and bd7_codunm ='COR'"
    
    TCSQLEXEC(Update) 
  End Transaction 

  //update - Tabela bd6010 

  Begin Transaction  

 	Update := " update " + RetSqlName('BD6') + " " 
	Update += " set bd6_vlrpag=(select sum(bd7_vlrpag) from " +  RetSqlName('BD7') + " "  
    Update += " where " + RetSqlName('BD7') + ".d_e_l_e_t_=' '  and bd7_filial = ' '  "
    Update += " and bd7_codldp = bd6_codldp "
	Update += " and bd7_codpeg = bd6_codpeg "
	Update += " and bd7_numero = bd6_numero "
    Update += " and bd7_sequen = bd6_sequen "
	Update += "	and bd7_numimp = bd6_numimp "
	Update += "	and bd7_anopag = bd6_anopag "
    Update += " and bd7_mespag = bd6_mespag),"
	Update += " bd6_vlrtpf=(select sum(bd7_vlrtpf) from " +  RetSqlName('BD7') + " "  	
    Update += " where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd6_filial = ' '  "
	Update += " and bd7_codldp = bd6_codldp "
	Update += " and bd7_codpeg = bd6_codpeg "
	Update += " and bd7_numero = bd6_numero "
    Update += " and bd7_sequen = bd6_sequen "
	Update += "	and bd7_numimp = bd6_numimp "
	Update += "	and bd7_anopag = bd6_anopag "
    Update += " and bd7_mespag = bd6_mespag )"
    Update += " where  " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' '  "
    Update += " and bd6_codldp='"+cLocal+"' "
    Update += " and bd6_codpeg='"+cPeg+"' "
	Update += " and bd6_numero='"+cNpeg+"' "
    Update += " and bd6_numimp='"+cnImp+"' " 
    Update += " and bd6_codpro='"+cCodigo+"' "
    Update += " and bd6_sequen='"+cSeq+"' "
	Update += " and bd6_anopag='"+cAno+"' " 
    Update += " and bd6_mespag='"+cMes+"' "
    Update += " and bd6_codpad = '14'"
    Update += " and substr(bd6_codpro,1,2) ='41'"
     
    TCSQLEXEC(Update)

 End Transaction 


Return //fechando função de Update da guia.

//Funcão para somar o cabeçalho da guia e atualiza-lo :


Static Function SOMAGUI (cLocal,cPeg,cNpeg,cnImp,cAno,cMes) //Função Static  será vista somente aqui.

Local Update := ""  
Local cQuery2 := ""

 //somando bd7010 para corrigir  o cabecalho da guia 

 cQuery2:=" select sum(bd7_vlrpag) pag, "
 cQuery2+=" sum(bd7_vlrglo) glosa , "
 cQuery2+=" sum(bd7_vlrtpf) cobra , "
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
 cQuery2+=" and bd7_numero = '"+cNpeg+"' "
 cQuery2+=" and bd7_numimp = '"+AllTrim(cnImp)+"' "
 cQuery2+=" and bd7_anopag = '"+cAno+"' "
 cQuery2+=" and bd7_mespag = '"+cMes+"' "
 cQuery2+=" group by bd7_numimp,bd7_tipgui,bd7_codldp,bd7_codpeg,bd7_numero,bd7_anopag,bd7_mespag "


Plsquery(cQuery2,"TMPSOM") // Rebece o resultado da query com a somatoria da tabela bd7010  



 If  TMPSOM->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Falha ao somar a guia.") 
    TMPSOM->( DbCloseArea() ) //Fecha tabela TMPBD7T que contem o select       
    Return 
EndIf  

nTotpag := TMPSOM->pag
nTotcob := TMPSOM->cobra
                                                                             
If TMPSOM->tipoguia='03'

 Begin Transaction  
       Update := " update "  + RetSqlName('BE4') + " "
	   Update += " set be4_vlrpag="+cValToChar(TMPSOM->pag)+", "
	   Update += " be4_vlrglo="+cValToChar(TMPSOM->glosa)+", "
	   Update += " be4_vlrtpf="+cValToChar(TMPSOM->cobra)+" "
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
	   Update += " bd5_vlrglo="+cValToChar(TMPSOM->glosa)+", "
	   Update += " bd5_vlrtpf="+cValToChar(TMPSOM->cobra)+" "
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

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : CriaSX1   ºAutor  Vanessa          º Data:  07/11/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. : Criando os parametros necessários para a rotina.          º±±
±±º           Serão informados pelo usuário:                            º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso       ³ AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Local digitacao "),        "","",  "mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Numero Completo Peg "),    "","",  "mv_ch2","C",08,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Numero da guia na Peg "),  "","",  "mv_ch3","C",08,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"04",OemToAnsi("Numero impresso "),        "","",  "mv_ch4","C",12,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Procedimento "),           "","",  "mv_ch5","C",08,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"06",OemToAnsi("sequencia do item "),      "","",  "mv_ch6","C",03,0,0,"G","","","","","mv_par06","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"07",OemToAnsi("Ano Pagamento ") ,         "","",  "mv_ch7","C",04,0,0,"G","","","","","mv_par07","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"08",OemToAnsi("Mês Pagamento ") ,         "","",  "mv_ch8","C",02,0,0,"G","","","","","mv_par08","","","","","","","","","","","","","","","","",{},{},{})    
PutSx1(cPerg,"09",OemToAnsi("Novo Percentual") ,        "","",  "mv_ch9","C",03,0,0,"G","","","","","mv_par09","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"10",OemToAnsi("Novo Valor Pagamento HMR"),"","", "mv_ch10","C",08,0,0,"G","","","","","mv_par10","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"11",OemToAnsi("Novo Valor Pagamento COR"),"","", "mv_ch11","C",08,0,0,"G","","","","","mv_par11","","","","","","","","","","","","","","","","",{},{},{})
return 