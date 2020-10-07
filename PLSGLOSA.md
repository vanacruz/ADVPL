#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSGLOSA ºAutor  Vanessa        º Data   18/06/19         º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     Realizar glosas em guias no contas.                                             
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function PLSGLOSA()  //Funcao de usuário maximo 8 caracteres

Local cQuery    := ""
Local cprestador :=""
Local cLocal     :=""                                      
Local cPeg       :=""
Local nImpresso  :="" 
Local cCodigo    :=""
Local cSequen    :=""
Local cAnopag    :=""
Local cMespag    :="" 
Local cGlosa     :=""

Private lValida := .T. 
Private cPerg:= "PLSGLOSA" 

CriaSX1(cPerg)   // Nesta forma de criar a pergunta ela guarda o ultimo resultado digitado no parametro             

if !Pergunte(cPerg,.T.,"Glosando Pagamento e Cobrança...")
       return()
endif  


if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03).And. Empty(mv_par04).And.Empty(mv_par05).And.Empty(mv_par06).And.Empty(mv_par07).And.Empty(mv_par08)//Se ambos os campos estiverm vazios
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
	Alert("Informe o numero da PEG.")
 Return
endif

if Empty(mv_par04) // Se o numero impresso estiver vazio 
	Alert("Informe o numero impresso da guia.")
 Return
endif

if Empty(mv_par05) // Se o código do procedimento estiver vazio 
	Alert("Informe o código do procedimento.")
 Return
endif

if Empty(mv_par06) // Se a sequencia do procedimento estiver vazio 
	Alert("Informe a sequencia do item na guia.")
 Return
endif

if Empty(mv_par07) // Se o ano da competência estiver vazio 
	Alert("Informe o ano em que a guia esta.")
 Return
endif

if Empty(mv_par08) // Se o mês da competência estiver vazio
	Alert("Informe o mes em que a guia esta.")
    Return
endif 
    
cprestador := AllTrim(mv_par01) 
cLocal     := AllTrim(mv_par02)
cPeg       := AllTrim(mv_par03)
nImpresso  := AllTrim(mv_par04) 
cCodigo    := AllTrim(mv_par05)
cSequen    := AllTrim(mv_par06)
cAnopag    := Alltrim(mv_par07)
cMespag    := Alltrim(mv_par08)
cMotivo    := Alltrim(mv_par09) 
cGlosa     := cValtochar(mv_par10)
                         
//Encontrando item para atualizar:


cQuery:= " select bd7_tipgui,bd7_codldp,bd7_codpeg,bd7_numimp,bd7_sequen,bd7_codrda, "
cQuery +=" bd7_codpro,bd7_blopag,bd7_motblo,bd7_desblo,bd7_vlrglo,bd7_vlrpag,bd7_anopag,bd7_mespag "
cQuery +=" from " +  RetSqlName('BD7') + " " 
cQuery +=" where " + RetSqlName('BD7') +".d_e_l_e_t_=' ' "
cQuery +=" and bd7_filial = ' ' "
cQuery +=" and bd7_codrda=  '"+cprestador+"' "
cQuery +=" and bd7_codldp=  '"+cLocal+"' "
cQuery +=" and bd7_codpeg =  '"+cPeg+"' "
cQuery +=" and bd7_numimp = '"+nImpresso+"' "
cQuery +=" and bd7_codpro=  '"+cCodigo+"' "
cQuery +=" and bd7_sequen = '"+cSequen+"' "
cQuery +=" and bd7_anopag = '"+cAnopag+"' "
cQuery +=" and bd7_mespag = '"+cMespag+"' "

Plsquery(cQuery,"TMPBD7") // Recebe o resultado da query acima  bd7010 

If  TMPBD7->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar  o impresso: " +nImpresso + " Esta guia esta correta ? " ) 
    TMPBD7->( DbCloseArea() ) //Fecha tabela TMPBD7 que contem o select 
    Return 
EndIf  

TMPBD7->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| PLSGLOSA(cprestador,cLocal,cPeg,nImpresso,cCodigo,cSequen,cAnopag,cMespag,cMotivo,cGlosa)} , 'Processando' , 'Aguarde. Atualizando item da guia...')  //chamando a função que realmente vai fazer o update 

//Alert("concluido")

cMensagem := "Atualização completa! " + chr(13)+chr(10) 
cMensagem += "Guia: " + nImpresso  + chr(13)+chr(10) 
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
±±ºPrograma  GLOSA717  ºAutor  Vanessa Cruz          º Data   30/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Update propriamente dito da tabela bd7010                  º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function PLSGLOSA(cprestador,cLocal,cPeg,nImpresso,cCodigo,cSequen,cAnopag,cMespag,cMotivo,cGlosa) //Função Static  será vista somente aqui.
Local Update  := ""  
Local cQuery2 := ""
Local cQuery3 := ""   
Local cDescri := ""  

cQuery3:= " select bct_descri "
cQuery3 +=" from " +  RetSqlName('BCT') + " " 
cQuery3 +=" where " + RetSqlName('BCT') +".d_e_l_e_t_=' ' "
cQuery3 +=" and bct_filial= ' ' "
cQuery3 +=" and bct_codope= '0021'"
cQuery3 +=" and bct_propri||bct_codglo= '"+cMotivo+"' "

Plsquery(cQuery3,"TMPBCT") // Rebece o resultado da query acima    

cDescri:=Alltrim(TMPBCT->bct_descri) 


TMPBCT->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta    


If cGlosa == '1' // Glosar pagamento e Cobranca 

 Begin Transaction  
 	
     //update -Tabela bd7010
     
    Update := "update " + RetSqlName('BD7') + " " 
    Update += "set  bd7_vlrglo=bd7_vlrpag, "
    Update += "bd7_vlrpag=0, "
    Update += "bd7_motblo='"+cMotivo+"', "
    Update += "bd7_Desblo='"+substr(cDescri,1,120)+"' "
    Update += "where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '  and  "
    Update += "bd7_codrda ='"+cprestador+"' "
    Update += "and bd7_codldp='"+cLocal+"' "
    Update += "and bd7_codpeg='"+cPeg+"' "
    Update += "and bd7_numimp='"+nImpresso+"' " 
    Update += "and bd7_codpro='"+cCodigo+"' "
    Update += "and bd7_sequen='"+cSequen+"' "
    Update += "and bd7_anopag='"+cAnopag+"' " 
    Update += "and bd7_mespag='"+cMespag+"' "
    TCSQLEXEC(Update) 

   

 End Transaction 

  Begin Transaction  
 	
     //update -Tabela bd6010
     
    Update := "update " + RetSqlName('BD6') + " "
    Update += "set bd6_vlrglo=bd6_vlrpag, "
    Update += "bd6_vlrpag = 0, "
    Update += "bd6_blocpa ='1', "
    Update += "bd6_motbpf='"+cMotivo+"', "
    Update += "bd6_desbpf='"+substr(cDescri,1,30)+"' "
    Update += "where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' ' and "  
    Update += "bd6_codrda ='"+cprestador+"' " 
    Update += "and bd6_codldp='"+cLocal+"' "
    Update += "and bd6_codpeg='"+cPeg+"' "
    Update += "and bd6_numimp='"+nImpresso+"' " 
    Update += "and bd6_codpro='"+cCodigo+"' " 
    Update += "and bd6_sequen='"+cSequen+"' "
    Update += "and bd6_anopag='"+cAnopag+"' 
    Update += "and bd6_mespag='"+cMespag+"' "
    TCSQLEXEC(Update) 

 End Transaction 

endif

If cGlosa == '2' // Glosar somente pagamento

Begin Transaction  
 	
     //update -Tabela bd7010
     
    Update := "update " + RetSqlName('BD7') + " " 
    Update += "set  bd7_vlrglo=bd7_vlrpag, "
    Update += "bd7_vlrpag=0, "
    Update += "bd7_motblo='"+cMotivo+"', "
    Update += "bd7_Desblo='"+substr(cDescri,1,120)+"' "
    Update += "where  " + RetSqlName('BD7') +".d_e_l_e_t_=' ' and bd7_filial = ' '  and  "
    Update += "bd7_codrda ='"+cprestador+"' "
    Update += "and bd7_codldp='"+cLocal+"' "
    Update += "and bd7_codpeg='"+cPeg+"' "
    Update += "and bd7_numimp='"+nImpresso+"' " 
    Update += "and bd7_codpro='"+cCodigo+"' "
    Update += "and bd7_sequen='"+cSequen+"' "
    Update += "and bd7_anopag='"+cAnopag+"' " 
    Update += "and bd7_mespag='"+cMespag+"' "
    TCSQLEXEC(Update) 

 End Transaction
endif  

    If cGlosa == '3' //Glosar somente cobranca
     
     Begin Transaction  
     	
     //update -Tabela bd6010
     
    Update := "update " + RetSqlName('BD6') + " "
    Update += "set bd6_vlrglo=bd6_vlrpag, "
    Update += "bd6_vlrpag = 0, "
    Update += "bd6_blocpa ='1', "
    Update += "bd6_motbpf='"+cMotivo+"', "
    Update += "bd6_desbpf='"+substr(cDescri,1,30)+"' "
    Update += "where " + RetSqlName('BD6') +".d_e_l_e_t_=' ' and bd6_filial = ' ' and "  
    Update += "bd6_codrda ='"+cprestador+"' " 
    Update += "and bd6_codldp='"+cLocal+"' "
    Update += "and bd6_codpeg='"+cPeg+"' "
    Update += "and bd6_numimp='"+nImpresso+"' " 
    Update += "and bd6_codpro='"+cCodigo+"' " 
    Update += "and bd6_sequen='"+cSequen+"' "
    Update += "and bd6_anopag='"+cAnopag+"' 
    Update += "and bd6_mespag='"+cMespag+"' "
    TCSQLEXEC(Update) 

   End Transaction 
endif 


 //somnado bd7010 para corrigir  o cabecalho da guia 
 cQuery2:=" select sum(bd7_vlrpag) soma , "
 cQuery2+=" sum(bd7_vlrglo) glosa , "
 cQuery2+=" bd7_numimp impresso , "
 cQuery2+=" bd7_tipgui tipoguia, "
 cQuery2+=" bd7_codldp local, "
 cQuery2+=" bd7_codpeg peg, " 
 cQuery2+=" bd7_anopag ano, "
 cQuery2+=" bd7_mespag mes "
 cQuery2+=" from " +  RetSqlName('BD7') + " " "
 cQuery2+=" where "+ RetSqlName('BD7') +".d_e_l_e_t_=' ' "
 cQuery2+=" and bd7_filial = ' ' "
 cQuery2+=" and bd7_codldp= '"+cLocal+"' "
 cQuery2+=" and bd7_codpeg = '"+cPeg+"' "
 cQuery2+=" and bd7_numimp = '"+AllTrim(nImpresso)+"' "
 cQuery2+=" and bd7_anopag = '"+cAnopag+"' "
 cQuery2+=" and bd7_mespag = '"+cMespag+"' "
 cQuery2+=" group by bd7_numimp,bd7_tipgui,bd7_codldp,bd7_codpeg,bd7_anopag,bd7_mespag "

Plsquery(cQuery2,"TMPSOM") // Rebece o resultado da query acima totais da Bd7010  


 If  TMPSOM->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Falha ao somar a guia.") 
    TMPSOM->( DbCloseArea() ) //Fecha tabela TMPBD7T que contem o select     
    
    Return 
EndIf  
  //Ajustando o cabecalho das guias                                                                            

 If TMPSOM->tipoguia='03'

 Begin Transaction  
       Update := " update "  + RetSqlName('BE4') + " "
	   Update += " set be4_vlrpag="+cValToChar(TMPSOM->soma)+", "
	   Update += " be4_vlrglo="+cValToChar(TMPSOM->glosa)+" "
	   Update += " where " +RetSqlName('BE4') +".d_e_l_e_t_ =' ' and be4_filial=' ' "
	   Update += " and be4_numimp ='"+AllTrim(TMPSOM->impresso)+"' "
	   Update += " and be4_codldp ='"+TMPSOM->local+"' "
	   Update += " and be4_codpeg ='"+TMPSOM->peg+"' "
	   Update += " and be4_anopag ='"+TMPSOM->ano+"' "
	   Update += " and be4_mespag ='"+TMPSOM->mes+"' "
    TCSQLEXEC(Update) 
 End Transaction   
 else
   Begin Transaction  
       Update := " update "  + RetSqlName('BD5') + " "
	   Update += " set bd5_vlrpag= "+cValToChar(TMPSOM->soma)+", "
	   Update += " bd5_vlrglo="+cValToChar(TMPSOM->glosa)+" "
	   Update += " where "+RetSqlName('BD5') +".d_e_l_e_t_ =' ' and bd5_filial=' ' "
	   Update += " and bd5_numimp ='"+AllTrim(TMPSOM->impresso)+"' "
	   Update += " and bd5_codldp ='"+TMPSOM->local+"' "
	   Update += " and bd5_codpeg ='"+TMPSOM->peg+"' "
	   Update += " and bd5_anopag ='"+TMPSOM->ano+"' "
	   Update += " and bd5_mespag ='"+TMPSOM->mes+"' "
    TCSQLEXEC(Update) 
   End Transaction  

 EndIf 

TMPSOM->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

Return//fechando função do update

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  CriaSX1   ºAutor  Vanessa          º Data   17/09/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Criando os parametros necessários para a rotina.          º±±
±±º               Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,                       cPerSpa, cPerEng,     cVar, cTipo,nTamanho,nDecimal,nPresel,cGSC,cValid ,cF3, cGrpSxg,cPyme,    cVar01,   cDef01,      cDefSpa1,cDefEng1,   cCnt01,    cDef02,     cDefSpa2,cDefEng2,cDef03,   cDefSpa3,cDefEng3,cDef04, cDefSpa4,cDefEng4, cDef05, cDefSpa5,  cDefEng5,  aHelpPor, aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"02",OemToAnsi("Local digitacao ")    ,"",   "",      "mv_ch2",  "C",      04,      0,     0,    "G",   "",  "",      "",     "", "mv_par02",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {}) 
PutSx1(cPerg,"03",OemToAnsi("Numero Completo Peg "),"",   "",      "mv_ch3",  "C",      08,      0,     0,    "G",   "",  "",      "",     "", "mv_par03",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {}) 
PutSx1(cPerg,"04",OemToAnsi("Numero impresso ")    ,"",   "",      "mv_ch4",  "C",      25,      0,     0,    "G",   "",  "",      "",     "", "mv_par04",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {})
PutSx1(cPerg,"01",OemToAnsi("Prestador ")          ,"",   "",      "mv_ch1",  "C",      06,      0,     0,    "G",   "",  "",      "",     "", "mv_par01",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {}) 
PutSx1(cPerg,"05",OemToAnsi("Procedimento ")       ,"",   "",      "mv_ch5",  "C",      08,      0,     0,    "G",   "",  "",      "",     "", "mv_par05",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {})
PutSx1(cPerg,"06",OemToAnsi("Sequencia do item ")  ,"",   "",      "mv_ch6",  "C",      03,      0,     0,    "G",   "",  "",      "",     "", "mv_par06",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {})
PutSx1(cPerg,"07",OemToAnsi("Ano Pagamento ")      ,"",   "",      "mv_ch7",  "C",      04,      0,     0,    "G",   "",  "",      "",     "", "mv_par07",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {})
PutSx1(cPerg,"08",OemToAnsi("Mês Pagamento ")      ,"",   "",      "mv_ch8",  "C",      02,      0,     0,    "G",   "",  "",      "",     "", "mv_par08",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "",        {},       {},      {})
PutSx1(cPerg,"09",OemToAnsi("Motivo Bloqueio ")    ,"",   "",      "mv_ch9",  "C",      03,      0,     0,    "G",   "",  "BCTPLS","",     "", "mv_par09",   "",              "",      "",       "",    "",            "",      "",    "",            "",      "",    "",       "",      "",     "",      "",         "")
PutSx1(cPerg,"10",OemToAnsi("Tipo de Glosa ")      ,"",   "",      "mv_ch10", "N",      01,      0,     0,    "C",   "",  "",      "",     "", "mv_par10",   "Ambos",         "",      "",       "",    "Só Pagamento","",      "",    "Só Cobranca", "",      "",    "",       "",      "",     "",      "",         "",         {},      {},      {})


return 