#INCLUDE "PROTHEUS.CH"
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : AJUSTAX  ºAutor : Vanessa        º Data :  07/11/18        º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. : Realizar ajustes de valor apresentado em taxas              º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function AJUSTAX()  //Funcao de usuário maximo 8 caracteres

Local cQuery     := ""
Local cLocal     := ""                                      
Local cPeg       := ""
Local cNpeg      := ""
Local cnImp      := "" 
Local cCodigo    := ""
Local cSeq       := ""
Local cAno       := ""
Local cMes       := ""
Local nVapr      := ""
Local cTipo      := ""
Local nZero      := ""    

//Variaveis para guardar os paramentros   

Local cLocA  :=" "
Local cPegA  :=" "
Local cNpegA :=" "
Local cnImpA :=" "
Local cCodA  :=" "
Local cSeqA  :=" "
Local cAnoA  :=" "
Local cMesA  :=" "
Local nVaprA :=" " 
Local cTipoA :=" "

Private lValida := .T. 
Private cPerg:= "AJUSTAX" 

CriaSX1(cPerg)   // Nesta forma de criar a pergunta ela guarda o ultimo resultado digitado no parametro
if !Pergunte(cPerg,.T.,"Ajusta Valor Apresentado em Taxa. NAO é valor de pagamento")
   	return()
endif               

/*CriaSX1()  // Nesta forma de criar a pergunta ela nao guarda o ultimo resultado digitado no parametro. 

if !Pergunte(cPerg,.T.,"Ajusta Valor Apresentado em Taxa. NAO é valor de pagamento",.T.)
       return()
endif  
*/

if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03).And. Empty(mv_par04).And.;
   Empty(mv_par05).And.Empty(mv_par06).And.Empty(mv_par07).And.Empty(mv_par08) //Se ambos os campos estiverm vazios
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

if Empty(mv_par03) // Se o numero da guia dentreo da peg
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
     
if (mv_par10== 1) //  Então o valor apresentado deverá ser zerado 
	 if !Empty(mv_par09) //valor   apresentado
	   Alert(" Se o valor apresentado deverá ser zero, não precisa preencher este campo.")
       Return 
    endif  
Else 
   if (mv_par10==2) //  Então o valor  apresentado deverá ser alteração e não é zero.
	   if Empty(mv_par09) // Valor apresentado 
	     Alert("Informe o novo valor apresentado para este item .")
         Return
      endif        
  endif
endif       


cLocal  := AllTrim(mv_par01)
cPeg    := AllTrim(mv_par02)
cNpeg   := AllTrim(mv_par03)  
cnImp   := AllTrim(mv_par04) 
cCodigo := AllTrim(mv_par05)
cSeq    := AllTrim(mv_par06)
cAno    := Alltrim(mv_par07)
cMes    := Alltrim(mv_par08)
nVapr   := VAL(strtran(mv_par09,",",".")) //valor informado para pagamento //Convertendo virgula em ponto
nZero   := 0
cTipo   := mv_par10

cQuery:= " select bd7_codldp,bd7_codpeg,bd7_numero,bd7_numimp,bd7_sequen, "
cQuery +=" bd7_codpro,bd7_vlrapr,bd7_vlrglo,bd7_vlrpag,bd7_anopag,bd7_mespag "
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
cQuery +=" and bd7_codpad = '01' "
cQuery +=" and bd7_codunm = 'REA' "

Plsquery(cQuery,"TMPBD7") // Rebece o resultado da query acima  bd7010 

If  TMPBD7->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar o impresso: " +cnImp + " Esta correto ? Este item é uma taxa ? " ) 
    TMPBD7->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select 
    Return 
EndIf 
//Guardando os parametros da guia anterior para verificar se não é a mesma guia:adicionado em 29/04/2019  chamado numero:46818

cLocA    := Alltrim(TMPBD7->bd7_codldp)
cPegA    := Alltrim(TMPBD7->bd7_codpeg)
cNpegA   := Alltrim(TMPBD7->bd7_numero) 
cnImpA   := Alltrim(TMPBD7->bd7_numimp)
cCodA    := Alltrim(TMPBD7->bd7_codpro)
cSeqA    := Alltrim(TMPBD7->bd7_sequen)
cAnoA    := Alltrim(TMPBD7->bd7_anopag)
cMesA    := Alltrim(TMPBD7->bd7_mespag)
nVaprA   := (TMPBD7->bd7_vlrapr) 
cTipoA   := cTipo

TMPBD7->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 
                                                                          
//Validando se a guia já não sofreu alteração , adicionado em 29/04/2019 chamado numero 46818

If (cLocal==cLocA) .And. (cPeg==cPegA ).And. (cNpeg==cNpegA) .And. (cnImp==cnImpA) .And. (cCodigo==cCodA) .And. ;
(cSeq==cSeqA) .And. (cAno==cAnoA) .And. (cMes==cMesA) .And. (nVapr==nVaprA) .And. (cTipoA==cTipo)

cMensagem := "Este item já possui os dados que você colocou nos parametros. " + chr(13)+chr(10) 
cMensagem += "Por favor confira os dados informados: " + chr(13)+chr(10)   
cMensagem += "Local: " + cLocA  + chr(13)+chr(10)
cMensagem += "Peg: " + cPegA  + chr(13)+chr(10)   
cMensagem += "Numero: " + cNpegA  + chr(13)+chr(10) 
cMensagem += "Impresso: " + cnImpA  + chr(13)+chr(10) 
cMensagem += "Codigo: " + cCodA  + chr(13)+chr(10) 
cMensagem += "Sequencia: " + cSeqA  + chr(13)+chr(10)
cMensagem += "Ano: " + cAnoA  + chr(13)+chr(10) 
cMensagem += "Mes: " + cMesA  + chr(13)+chr(10) 
cMensagem += "Valor Apresentado: " + CvalToChar(nVaprA) + chr(13)+chr(10)  

MSGINFO(cMensagem)
Return
Endif

cMensagem := "Este programa altera apenas o valor apresentado do item dentro da guia"  + chr(13)+chr(10)   
cMensagem += "Ele não revaloriza a guia e não irá alterar valor de pagamento e cobrança" + chr(13)+chr(10)
MSGINFO(cMensagem)


FWMsgRun( , {||AJUSTAX(cLocal,cPeg,cNpeg,cnImp,cCodigo,cSeq,cAno,cMes,nVapr,nZero,cTipo)} , 'Processando' , 'Aguarde. Atualizando item da guia...')  //chamando a função que realmente vai fazer o update 



//Alert("Concluido")

cMensagem := "Atualização completa! Apenas alterou-se o valor apresentado " + chr(13)+chr(10) 
cMensagem += "Necessário revalorizar a guia: Pagamento e Cobrança" + chr(13)+chr(10) 
cMensagem += "Guia: " + cnImp  + chr(13)+chr(10)
cMensagem += "Peg: " + cPeg  + chr(13)+chr(10)   
cMensagem += "Numero: " + cNpeg  + chr(13)+chr(10)  
cMensagem += "Ano: " + cAno  + chr(13)+chr(10) 
cMensagem += "Mes: " + cMes  + chr(13)+chr(10) 
MSGINFO(cMensagem)

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  AJUSTAX  Autor  : Vanessa Cruz           Data:   17/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Update propriamente dito.                                  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

//Função para ajustar bd7010 e bd6010 de acordo com a necessidade.

Static Function AJUSTAX(cLocal,cPeg,cNpeg,cnImp,cCodigo,cSeq,cAno,cMes,nVapr,nZero,cTipo) //Função Static  será vista somente aqui.

Local Update := ""  


If  cTipo == 1 //**************Atulizar o valor apresentado para zero ******************** 

   Begin Transaction  
 	
     //update - Tabela bd7010
     
    Update := " update " + RetSqlName('BD7') + " " 
    Update += " set  bd7_vlrapr="+cValToChar(nZero)+" "  
    Update += " where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '  "
    Update += " and bd7_codldp='"+cLocal+"' "
    Update += " and bd7_codpeg='"+cPeg+"' "
    Update += " and bd7_numero='"+cNpeg+"' "
    Update += " and bd7_numimp='"+cnImp+"' " 
    Update += " and bd7_codpro='"+cCodigo+"' "
    Update += " and bd7_sequen='"+cSeq+"' "
    Update += " and bd7_anopag='"+cAno+"' " 
    Update += " and bd7_mespag='"+cMes+"' "
    Update += " and bd7_codpad = '01' "
    
    TCSQLEXEC(Update) 

   

 End Transaction 

  Begin Transaction  
 	
     //update -Tabela bd6010
     
    Update := " update " + RetSqlName('BD6') + " "
    Update += " set  bd6_vlrapr="+cValToChar(nZero)+" "  
    Update += " where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' '  "  
    Update += " and bd6_codldp='"+cLocal+"' "
    Update += " and bd6_codpeg='"+cPeg+"' "
    Update += " and bd6_numero ='"+cNpeg+"' "
    Update += " and bd6_numimp='"+cnImp+"' " 
    Update += " and bd6_codpro='"+cCodigo+"' " 
    Update += " and bd6_sequen='"+cSeq+"' "
    Update += " and bd6_anopag='"+cAno+"' "
    Update += " and bd6_mespag='"+cMes+"' "
    Update += " and bd6_codpad = '01' "
    TCSQLEXEC(Update) 

 End Transaction 

Else 

   If cTipo == 2// **********************Atulizar alterar o valor apresentado para o que foi digitado************

    Begin Transaction  
     	
     //update - Tabela bd7010
     
    Update := " update " + RetSqlName('BD7') + " " 
    Update += " set  bd7_vlrapr= "+cValToChar(nVapr)+" "     
    Update += " where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '   "
    Update += " and bd7_codldp='"+cLocal+"' "
    Update += " and bd7_codpeg='"+cPeg+"' "
    Update += " and bd7_numero='"+cNpeg+"' "
    Update += " and bd7_numimp='"+cnImp+"' " 
    Update += " and bd7_codpro='"+cCodigo+"' "
    Update += " and bd7_sequen='"+cSeq+"' "
    Update += " and bd7_anopag='"+cAno+"' " 
    Update += " and bd7_mespag='"+cMes+"' "  
    Update += " and bd7_codpad = '01' "
    TCSQLEXEC(Update) 

   

    End Transaction 
     
    Begin Transaction  
     	
     //update -Tabela bd6010
     
      Update := " update " + RetSqlName('BD6') + " "
      Update += " set  bd6_vlrapr= "+cValToChar(nVapr)+" " 
      Update += " where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' '  "  
      Update += " and bd6_codldp='"+cLocal+"' "
      Update += " and bd6_codpeg='"+cPeg+"' "
      Update += " and bd6_numero ='"+cNpeg+"' "
      Update += " and bd6_numimp='"+cnImp+"' " 
      Update += " and bd6_codpro='"+cCodigo+"' " 
      Update += " and bd6_sequen='"+cSeq+"' "
      Update += " and bd6_anopag='"+cAno+"' "
      Update += " and bd6_mespag='"+cMes+"' "  
      Update += " and bd6_codpad = '01' "
    TCSQLEXEC(Update) 		
    End Transaction 


 Endif  //Fechando o if do tipo = 2
Endif  //Fechando o if do tipo =1  

Return //fechando função de Update da guia.

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : CriaSX1   ºAutor :Vanessa          º Data :  07/11/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. :Criando os parametros necessários para a rotina.          º±±
±±º           Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


//(cGrupo,cOrdem,cPergunt,                 cPerSpa, cPerEng,          cVar,   cTipo,nTamanho, nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,    cDef01,     cDefSpa1,cDefEng1,    cCnt01,    cDef02,     cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Local digitacao "),        "",   "", "mv_ch1",   "C",   04,       0,        0,     "G",  "",   "",   "",    "",  "mv_par01",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {}) 
PutSx1(cPerg,"02",OemToAnsi("Numero Completo Peg "),    "",   "", "mv_ch2",   "C",   08,       0,        0,     "G",  "",   "",   "",    "",  "mv_par02",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"03",OemToAnsi("Numero da guia na Peg "),  "",   "", "mv_ch3",   "C",   08,       0,        0,     "G",  "",   "",   "",    "",  "mv_par03",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {}) 
PutSx1(cPerg,"04",OemToAnsi("Numero impresso "),        "",   "", "mv_ch4",   "C",   25,       0,        0,     "G",  "",   "",   "",    "",  "mv_par04",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"05",OemToAnsi("Procedimento ") ,          "",   "", "mv_ch5",   "C",   08,       0,        0,     "G",  "",   "",   "",    "",  "mv_par05",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"06",OemToAnsi("sequencia do item ") ,     "",   "", "mv_ch6",   "C",   03,       0,        0,     "G",  "",   "",   "",    "",  "mv_par06",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"07",OemToAnsi("Ano Pagamento ") ,         "",   "", "mv_ch7",   "C",   04,       0,        0,     "G",  "",   "",   "",    "",  "mv_par07",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"08",OemToAnsi("Mês Pagamento ") ,         "",   "", "mv_ch8",   "C",   02,       0,        0,     "G",  "",   "",   "",    "",  "mv_par08",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"09",OemToAnsi("Novo Valor Apresentado "), "",   "", "mv_ch9",   "C",   08,       0,        0,     "G",  "",   "",   "",    "",  "mv_par09",   "",        "",          "",    "",        "",             "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
PutSx1(cPerg,"10",OemToAnsi("Ajustar Valor Apresentado "),"", "", "mv_ch10",  "N",   01,       0,        0,     "C",  "",   "",   "",    "",  "mv_par10","Zerar Valor","",          "",    "",    "Alterar Valor",    "",        "",    "",      "",      "",    "",      "",      "",    "",      "",      "",       {},     {},      {})
return 