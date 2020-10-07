#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : ALTVRTPF  ºAutor  Vanessa        º Data   02/01/2020     º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. :Realizar ajustes de valores de cobrança(VLRTPF) local 0001  º±±
±±º           para refaturamento de guias.                                º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function ALTVRTPF()  //Funcao de usuário maximo 8 caracteres

Local cQuery     := ""
Local cLocal     := ""                                      
Local cPeg       := ""
Local cNpeg      := ""
Local cnImp      := "" 
Local cCodigo    := ""
Local cSeq       := ""
Local cAno       := ""
Local cMes       := ""
Local nVcob      := ""


Private cPerg:= "ALTVRTPF" 

CriaSX1()  
              


if !Pergunte(cPerg,.T.,"Altera Cobrança Intercâmbio VLTPF Refaturamento",.T.)
       return()
endif  


if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03).And. Empty(mv_par04).And.Empty(mv_par05).And.Empty(mv_par06).And.Empty(mv_par07).And.Empty(mv_par08).And.Empty(mv_par09) //Se ambos os campos estiverm vazios
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
     
if Empty(mv_par09) // Se o Tipo estiver em branco
	Alert("Informar o novo valor da Cobrança.")
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
nVcob   := VAL(strtran(mv_par09,",",".")) // Novo valor informado para cobrança //Convertendo virgula em ponto



cQuery:= " select bd7_tipgui,bd7_codldp,bd7_codpeg,bd7_numero,bd7_numimp,bd7_sequen, "
cQuery +=" bd7_codpro,bd7_vlrtpf,bd7_anopag,bd7_mespag "
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
cQuery +=" and bd7_codemp = '0001' "
cQuery +=" and bd7_codldp = '0007' " 
cQuery +=" and bd7_codpad in ('16','01') "  

Plsquery(cQuery,"TMPBD7") // Rebece o resultado da query acima  bd7010 

If  TMPBD7->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar o impresso: " +cnImp + " O item é um Mat/Med/Taxa?,O cliente é Intercâmbio,O local é 0007 ? " ) 
    TMPBD7->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select 
    Return 
EndIf  

TMPBD7->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {||ALTVRTPF(cLocal,cPeg,cNpeg,cnImp,cCodigo,cSeq,cAno,cMes, nVcob)} , 'Processando' , 'Aguarde. Atualizando item da guia...')  //chamando a função que realmente vai fazer o update 


cMensagem := "Atualização completa! " + chr(13)+chr(10) 
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
±±ºPrograma :ALTVRTPF  ºAutor  Vanessa Cruz          º Data   02/01/20  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Update                                                     º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

//Função para ajustar bd7010 e bd6010.

Static Function ALTVRTPF(cLocal,cPeg,cNpeg,cnImp,cCodigo,cSeq,cAno,cMes,nVcob) //Função Static  será vista somente aqui.

Local Update := ""  

   Begin Transaction  
 	
     //update - Tabela bd7010
     
    Update := " update " + RetSqlName('BD7') + " " 
    Update += " set  bd7_vlrtpf="+cValToChar(nVcob)+" "  
    Update += " where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '  "
    Update += " and bd7_codldp='"+cLocal+"' "
    Update += " and bd7_codpeg='"+cPeg+"' "
    Update += " and bd7_numero='"+cNpeg+"' "
    Update += " and bd7_numimp='"+cnImp+"' " 
    Update += " and bd7_codpro='"+cCodigo+"' "
    Update += " and bd7_sequen='"+cSeq+"' "
    Update += " and bd7_anopag='"+cAno+"' " 
    Update += " and bd7_mespag='"+cMes+"' "
    Update += " and bd7_codemp='0001' "
    Update += " and bd7_codldp='0007' " 
    Update += " and bd7_codpad in ('16','01') "  //Acrescentado o bd7_codpad = 01 dia 06/05/2019 
    TCSQLEXEC(Update) 

   

 End Transaction 

  Begin Transaction  
 	
     //update -Tabela bd6010
     
    Update := " update " + RetSqlName('BD6') + " "
    Update += " set bd6_vlrtpf = "+cValToChar(nVcob)+" "  
    Update += " where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' '  "  
    Update += " and bd6_codldp='"+cLocal+"' "
    Update += " and bd6_codpeg='"+cPeg+"' "
    Update += " and bd6_numero ='"+cNpeg+"' "
    Update += " and bd6_numimp='"+cnImp+"' " 
    Update += " and bd6_codpro='"+cCodigo+"' " 
    Update += " and bd6_sequen='"+cSeq+"' "
    Update += " and bd6_anopag='"+cAno+"' "
    Update += " and bd6_mespag='"+cMes+"' "
    Update += " and bd6_codemp= '0001' "
    Update += " and bd6_codldp= '0007' " 
    Update += " and bd6_codpad in ('16','01') " //Acrescentado o bd6_codpad = 01 dia 06/05/2019 
    TCSQLEXEC(Update) 

 End Transaction 

FWMsgRun( , {||SOMAGUI(cLocal,cPeg,cNpeg,cnImp,cAno,cMes)} , 'Processando' , 'Aguarde. Atualizando item da guia...')

Return //fechando função de Update da guia.

//Funcão para somar o cabeçalho da guia e atualiza-lo :

Static Function SOMAGUI (cLocal,cPeg,cNpeg,cnImp,cAno,cMes) //Função Static  será vista somente aqui.

Local Update := ""  
Local cQuery2 := ""

 //somando bd7010 para corrigir  o cabecalho da guia 

 cQuery2:=" select sum(bd7_vlrtpf) cob, "
 cQuery2+=" bd7_numimp impresso , "
 cQuery2+=" bd7_tipgui tipoguia, "
 cQuery2+=" bd7_codldp cloc, "
 cQuery2+=" bd7_codpeg peg, " 
 cQuery2+=" bd7_numero numero, " 
 cQuery2+=" bd7_anopag ano, "
 cQuery2+=" bd7_mespag mes "
 cQuery2+=" from " + RetSqlName('BD7') + " " 
 cQuery2+=" where "+ RetSqlName('BD7') +".d_e_l_e_t_=' ' "
 cQuery2+=" and bd7_filial = ' ' "
 cQuery2+=" and bd7_codldp = '"+cLocal+"' "
 cQuery2+=" and bd7_codpeg = '"+cPeg+"' "
 cQuery2+=" and bd7_numimp = '"+AllTrim(cnImp)+"' " 
 cQuery2+=" and bd7_numero = '"+cNpeg+"' "
 cQuery2+=" and bd7_anopag = '"+cAno+"' "
 cQuery2+=" and bd7_mespag = '"+cMes+"' "
 cQuery2+=" and bd7_codemp = '0001' "
 cQuery2+=" and bd7_codldp = '0007' " 
 cQuery2+=" group by bd7_numimp,bd7_tipgui,bd7_codldp,bd7_codpeg,bd7_numero,bd7_anopag,bd7_mespag "

Plsquery(cQuery2,"TMPSOM") // Rebece o resultado da query com a somatoria da tabela bd7010   

 If  TMPSOM->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Falha ao somar a guia.") 
    TMPSOM->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select     
    
    Return 
EndIf  
                                                                             
If TMPSOM->tipoguia='03'

 Begin Transaction  
       Update := " update "  + RetSqlName('BE4') + " "
	   Update += " set be4_vlrtpf="+cValToChar(TMPSOM->cob)+" "
	   Update += " where " +RetSqlName('BE4') +".d_e_l_e_t_ =' ' and be4_filial=' ' "
	   Update += " and be4_numimp ='"+AllTrim(TMPSOM->impresso)+"' "
	   Update += " and be4_codldp ='"+TMPSOM->cloc+"' "
	   Update += " and be4_codpeg ='"+TMPSOM->peg+"' "
	   Update += " and be4_numero ='"+TMPSOM->numero+"' "
	   Update += " and be4_anopag ='"+TMPSOM->ano+"' "
	   Update += " and be4_mespag ='"+TMPSOM->mes+"' "
    TCSQLEXEC(Update) 
 End Transaction   
 else
   Begin Transaction  
       Update := " update "  + RetSqlName('BD5') + " "
	   Update += " set bd5_vlrtpf= "+cValToChar(TMPSOM->cob)+" "
	   Update += " where "+RetSqlName('BD5') +".d_e_l_e_t_ =' ' and bd5_filial=' ' "
	   Update += " and bd5_numimp ='"+AllTrim(TMPSOM->impresso)+"' "
	   Update += " and bd5_codldp ='"+TMPSOM->cloc+"' "
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
±±ºPrograma  CriaSX1   ºAutor  Vanessa          º Data   02/01/20       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     Criando os parametros necessários para a rotina.          º±±
±±º               Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Local digitacao "),"","", "mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Numero Completo Peg ") ,"","", "mv_ch2","C",08,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Numero da guia na Peg "),"","", "mv_ch3","C",08,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"04",OemToAnsi("Numero impresso "),"","", "mv_ch4","C",25,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Procedimento ") ,"","", "mv_ch5","C",08,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"06",OemToAnsi("sequencia do item ") ,"","", "mv_ch6","C",03,0,0,"G","","","","","mv_par06","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"07",OemToAnsi("Ano Pagamento ") ,"","", "mv_ch7","C",04,0,0,"G","","","","","mv_par07","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"08",OemToAnsi("Mês Pagamento ") ,"","", "mv_ch8","C",02,0,0,"G","","","","","mv_par08","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"09",OemToAnsi("Novo Valor Cobrança "),"","", "mv_ch9","C",08,0,0,"G","","","","","mv_par09","","","","","","","","","","","","","","","","",{},{},{})
return