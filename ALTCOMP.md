#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma:  ALTCOMP  ºAutor  : Vanessa         º Data : 29/11/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. : Alterar a competencia de uma peg inteira                    º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function ALTCOMP()  //Funcao de usuário maximo 8 caracteres

Local cQuery     :=""
Local cprestador :=""
Local cLocal     :=""                                      
Local cPeg       :=""
Local cAnopag    :=""
Local cMespag    :=""
Local cNvAnop    :=""
Local cNMesp     :=""


Private cPerg:= "ALTCOMP" 

CriaSX1()  
              


if !Pergunte(cPerg,.T.,"Alterando competencia de uma peg inteira.",.T.)
       return()
endif  


if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03) .And. Empty(mv_par04) .And. Empty(mv_par05).And. Empty(mv_par06) .And. Empty(mv_par07) //Se ambos os campos estiverm vazios
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif   

if Empty(mv_par01) //Se o numero do prestador estiver vazio 
	Alert("Informe o codigo do prestador.")
    Return
endif

if Empty(mv_par02) // Se o local de digitação estiver vazio  
	Alert("Informe o local de digitação.")
 Return
endif

if Empty(mv_par03) // Se o numero da Peg estiver vazio 
	Alert("Informe o numero completo da PEG.")
 Return
endif

if Empty(mv_par04) // Se o  Ano Atual da Peg estiver vazio 
	Alert("Informe o Ano Atual da Peg.")
 Return
endif

if Empty(mv_par05) // Se o  Mês Atual da Peg estiver vazio 
	Alert("Informe o Mês Atual da Peg.")
 Return
endif

if Empty(mv_par06) // Se o novo Ano da Peg estiver vazio 
	Alert("Informe o novo ano da Peg.")
 Return
endif

if Empty(mv_par07) // Se o novo Mes da Peg estiver vazio  
	Alert("Informe o novo Mês da Peg.")
 Return
endif

cprestador:=AllTrim(mv_par01) 
cLocal := AllTrim (mv_par02)
cPeg  :=  AllTrim(mv_par03)
cAnopag := Alltrim(mv_par04)
cMespag := Alltrim(mv_par05)
cNvAnop := Alltrim(mv_par06)
cNMesp  := Alltrim(mv_par07)
           

//Encontrando Peg para alterar competencia:

cQuery :=" select bci_tipgui,bci_codldp,bci_codpeg,bci_ano,bci_mes ,bci_codrda "
cQuery +=" from " +  RetSqlName('BCI') + " " 
cQuery +=" where " + RetSqlName('BCI') +".d_e_l_e_t_=' ' "
cQuery +=" and bci_filial = ' ' "
cQuery +=" and bci_codrda=  '"+cprestador+"' "
cQuery +=" and bci_codldp=  '"+cLocal+"' "
cQuery +=" and bci_codpeg = '"+cPeg+"' "
cQuery +=" and bci_ano = '"+cAnopag+"' "
cQuery +=" and bci_mes = '"+cMespag+"' "

Plsquery(cQuery,"TMPBCOI") // Rebece o resultado da query acima  bci010


If  TMPBCOI->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar  a peg: " +cPeg + " Esta Peg esta correta ? " ) 
    TMPBCOI->( DbCloseArea() ) //Fecha tabela TMPBCOI que contem o select 
    Return 
EndIf  

TMPBCOI->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| ALTCOMP(cprestador,cLocal,cPeg,cAnopag,cMespag,cNvAnop,cNMesp)} , 'Processando' , 'Aguarde. Atualizando Peg...')//chamando a função que realmente vai fazer o update 

//Alert("concluido")

cMensagem := "Atualização completa! " + chr(13)+chr(10) 
MSGINFO(cMensagem)  

cMensagem := "Local trabalhado: " + cLocal+chr(13)+chr(10) 
cMensagem += "Peg trabalhada: " + cPeg+chr(13)+chr(10) 
cMensagem += "Competencia anterior: " +cAnopag+'/'+cMespag+chr(13)+chr(10)  
cMensagem += "Competencia Nova: " +cNvAnop+'/'+cNMesp+chr(13)+chr(10) 
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
±±ºPrograma  GLOSA7LW  ºAutor  : Vanessa Cruz          º Data   12/09/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.:Update da tabela bd7010                  º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function ALTCOMP(cprestador,cLocal,cPeg,cAnopag,cMespag,cNvAnop,cNMesp) //Função Static  será vista somente aqui.

Local Update := ""  
Local cQuery2 := ""


   Begin Transaction  
 	
     //update -Tabela bd7010
     
    Update := "update " + RetSqlName('BD7') + " " 
    Update += "set  bd7_anopag='"+cNvAnop+"', "
    Update += "bd7_mespag='"+cNMesp+"' "
    Update += "where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' ' and   "
    Update += "bd7_codldp='"+cLocal+"' "
    Update += "and bd7_codpeg='"+cPeg+"' "
    Update += "and bd7_anopag='"+cAnopag+"' " 
    Update += "and bd7_mespag='"+cMespag+"' "
    TCSQLEXEC(Update) 

   

 End Transaction 

  Begin Transaction  
 	
     //update -Tabela bd6010
     
    Update := "update " + RetSqlName('BD6') + " "
    Update += "set bd6_anopag= '"+cNvAnop+"', "
    Update += "bd6_mespag='"+cNMesp+"' "
    Update += "where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' ' and "  
    Update += "bd6_codldp='"+cLocal+"' "
    Update += "and bd6_codpeg='"+cPeg+"' "
    Update += "and bd6_anopag='"+cAnopag+"'"
    Update += "and bd6_mespag='"+cMespag+"' "
    TCSQLEXEC(Update) 

 End Transaction 


cQuery2:=" select bci_tipgui tipoguia ,bci_codldp loc ,bci_codpeg peg,bci_ano ano ,bci_mes mes,bci_codrda as rda  "
cQuery2 +=" from " +  RetSqlName('BCI') + " " 
cQuery2 +=" where " + RetSqlName('BCI') +".d_e_l_e_t_=' ' "
cQuery2 +=" and bci_filial = ' ' "
cQuery2 +=" and bci_codrda=  '"+cprestador+"' "
cQuery2 +=" and bci_codldp=  '"+cLocal+"' "
cQuery2 +=" and bci_codpeg = '"+cPeg+"' "
cQuery2 +=" and bci_ano = '"+cAnopag+"' "
cQuery2 +=" and bci_mes = '"+cMespag+"' "

Plsquery(cQuery2,"TMPBCO") // Rebece o resultado da query acima totais da Bd7010  


 If  TMPBCO->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Falha ao localizar a peg.") 
    TMPBCO->( DbCloseArea() ) //Fecha tabela TMPBCO que contem o select     
    
    Return 
EndIf  
  //Ajustando o cabecalho das guias                           

 If TMPBCO->tipoguia='03'

 Begin Transaction  
       Update := " update "  + RetSqlName('BE4') + " "
	   Update += " set be4_anopag='"+cNvAnop+"', "
	   Update += " be4_mespag='"+cNMesp+"' "
	   Update += " where " +RetSqlName('BE4') +".d_e_l_e_t_ =' ' and be4_filial=' '  "
	   Update += " and be4_codldp ='"+TMPBCO->loc+"' "
	   Update += " and be4_codpeg ='"+TMPBCO->peg+"' "
	   Update += " and be4_anopag ='"+TMPBCO->ano+"' "
	   Update += " and be4_mespag ='"+TMPBCO->mes+"' "
    TCSQLEXEC(Update) 
 End Transaction   
 else
   Begin Transaction  
       Update := " update "  + RetSqlName('BD5') + " "
	   Update += " set bd5_anopag='"+cNvAnop+"', "
	   Update += " bd5_mespag='"+cNMesp+"' "
	   Update += " where "+RetSqlName('BD5') +".d_e_l_e_t_ =' ' and bd5_filial=' ' "
	   Update += " and bd5_codldp ='"+TMPBCO->loc+"' "
	   Update += " and bd5_codpeg ='"+TMPBCO->peg+"' "
	   Update += " and bd5_anopag ='"+TMPBCO->ano+"' "
	   Update += " and bd5_mespag ='"+TMPBCO->mes+"' "
    TCSQLEXEC(Update) 
   End Transaction  

 EndIf 

  Begin Transaction  

  //update - Tabela bci010

    Update := "update " + RetSqlName('BCI') + " "
    Update += "set bci_ano='"+cNvAnop+"', "
    Update += "bci_mes='"+cNMesp+"' "
    Update += "where " + RetSqlName('BCI') +".d_e_l_e_t_=' ' and bci_filial = ' ' and "  
    Update += "bci_codrda='"+TMPBCO->rda+"' "
    Update += "and bci_codldp='"+TMPBCO->loc+"' "
    Update += "and bci_codpeg='"+TMPBCO->peg+"' "
    Update += "and bci_ano='"+TMPBCO->ano+"' "
    Update += "and bci_mes='"+TMPBCO->mes+"' "

   TCSQLEXEC(Update) 

  End Transaction  


TMPBCO->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 


Return//fechando função do update

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³CriaSX1   ºAutor  Vanessa          º Data ³  17/09/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Criando os parametros necessários para a rotina.          º±±
±±º          Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Prestador "),"","", "mv_ch1","C",06,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Local digitacao "),"","", "mv_ch2","C",04,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"03",OemToAnsi("Numero Completo Peg ") ,"","", "mv_ch3","C",08,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"04",OemToAnsi("Ano Atual ") ,"","", "mv_ch4","C",04,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Mês Atual ") ,"","", "mv_ch5","C",02,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"06",OemToAnsi("Novo Ano ") ,"","", "mv_ch6","C",04,0,0,"G","","","","","mv_par06","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"07",OemToAnsi("Novo Mes") ,"","", "mv_ch7","C",02,0,0,"G","","","","","mv_par07","","","","","","","","","","","","","","","","",{},{},{})
return