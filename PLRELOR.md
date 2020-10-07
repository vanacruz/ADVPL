/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ                              
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLRELORC   ºAutor   Vanessa         º Data       28/03/2019 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.    Relatório  valor orçado x cobrado gerado em Excel.                                                          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User function PLRELORC()  			

Local oCB, oDET, oReport,oResumo   
                                                              
Private cPerg      := "PLRELORC"   
Private cSubcon    := ""


//ParÃ¢metros de TFont.New()
//1.Nome da Fonte (Windows)
//3.Tamanho em Pixels
//5.Bold (T/F)

oFont8   := TFont():New("Arial", 9, 8,.T.,.F.,5,.T.,5,.T.,.F.)
oFont10  := TFont():New("Arial", 9,10,.T.,.F.,5,.T.,5,.T.,.F.)
oFont10b := TFont():New("Arial", 9,10,.T.,.T.,5,.T.,5,.T.,.F.)
oFont11  := TFont():New("Arial", 9,11,.T.,.F.,5,.T.,5,.T.,.F.)
oFont11b := TFont():New("Arial", 9,11,.T.,.T.,5,.T.,5,.T.,.F.)
oFont11c := TFont():New("Arial",12,11,.T.,.F.,5,.T.,5,.T.,.F.)
oFont12  := TFont():New("Arial", 9,12,.T.,.F.,5,.T.,5,.T.,.F.)
oFont12b := TFont():New("Arial", 9,12,.T.,.T.,5,.T.,5,.T.,.F.)
oFont14  := TFont():New("Arial", 9,14,.T.,.F.,5,.T.,5,.T.,.F.)
oFont14b := TFont():New("Arial", 9,11,.T.,.T.,5,.T.,5,.T.,.F.)
oFont14c := TFont():New("Arial", 9,22,.T.,.T.,5,.T.,5,.T.,.F.)
oFont16  := TFont():New("Arial", 9,16,.T.,.F.,5,.T.,5,.T.,.F.)
oFont16b := TFont():New("Arial", 9,16,.T.,.T.,5,.T.,5,.T.,.F.)
oFont21  := TFont():New("Arial", 9,21,.T.,.f.,5,.T.,5,.T.,.F.)

ValidPerg()
pergunte(cPerg,.F.)     


DEFINE REPORT oReport NAME "PLRELORC" TITLE cSubcon PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}

oReport:SetPortrait() // ou SetLandscape()
oReport:SetTotalInLine(.F.)
oReport:cFontBody := "ARIAL"  
oReport:nFontBody := 6     

oReport:SetLeftMargin(2)

DEFINE SECTION oDET OF oReport TITLE "Movimentos"

DEFINE CELL NAME "NRO_IMPRESSO"         OF oDET TITLE "NRO_IMPRESSO"          SIZE 18 
DEFINE CELL NAME "OPE"               OF oDET TITLE "OP"                SIZE 05 
DEFINE CELL NAME "NOME_OP          OF oDET TITLE "NOME_OP"           SIZE 45   
DEFINE CELL NAME "NUMERO_GUIA"          OF oDET TITLE "NUMERO_GUIA"           SIZE 25  
DEFINE CELL NAME "EVENTO"               OF oDET TITLE "EVENTO"                SIZE 07
DEFINE CELL NAME "QTDE"                 OF oDET TITLE "QTDE"                  SIZE 05 
DEFINE CELL NAME "VALOR_ORCADO"         OF oDET TITLE "VALOR_ORCADO"          SIZE 15
DEFINE CELL NAME "VALOR_COBRANCA"       OF oDET TITLE "VALOR_COBRANCA"        SIZE 15
DEFINE CELL NAME "VALOR_TAXA_ADM"       OF oDET TITLE "VALOR_TAXA_ADM"        SIZE 15
DEFINE CELL NAME "VALOR_TOTAL_COBRANCA" OF oDET TITLE "VALOR_TOT_COBRANCA"    SIZE 15


DEFINE SECTION oResumo OF oReport TITLE "Resumo"

oDet:nLeftMargin := 0
oReport:PrintDialog()

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLRELORC   ºAutor   Microsiga           º Data    04/13/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.                                                                  º±±
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso         AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function PrintReport(oReport)
Local cFiltro      := "%" 
Local nPag         :=0  
Private aDados     := {}	  

oReport:SetMeter(2)
oReport:IncMeter()

If !empty(MV_PAR01)
	cFiltro += "and a.bd6_anopag = '"+AllTrim(MV_PAR01)+"' " 
  //	cFiltro += "%"
Else
	MsgAlert("Informe o ano.") 
    Return()
EndIf          
If !empty(MV_PAR02)
	cFiltro += "and a.bd6_mespag = '"+AllTrim(MV_PAR02)+"' " 
 //	cFiltro += "%"
Else
	MsgAlert("Informe o mes.") 
    Return()
EndIf  
	cFiltro += "%"

BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "CSQL"  
	select a.bd6_numimp NRO_IMPRESSO, 
	   a.bd6_opeori Op,
	   x.ba0_nomint NOME_Op,
	   a.bd6_codldp||'.'||a.bd6_codpeg||'.'||a.bd6_numero NUMERO_GUIA,
	   a.bd6_sequen EVENTO,
	   a.bd6_qtdpro QTDE,
	   a.bd6_vlracb VALOR_ORCADO,
	   (select sum(b.bd6_vlrpf)
	    from %TABLE:bd6010% b
	    where b.d_e_l_e_t_ = ' '
	    and   b.bd6_codldp = a.bd6_codldp
	    and   b.bd6_codpeg = a.bd6_codpeg
	    and   b.bd6_numero = a.bd6_numero
	    and   b.bd6_situac = a.bd6_situac
	    and   b.bd6_anopag = a.bd6_anopag
	    and   b.bd6_mespag = a.bd6_mespag
	    and   b.bd6_blocpa <> '1') as VALOR_COBRANCA,
	    (select sum(c.bd6_vlrtad)
	    from %TABLE:bd6010% c
	    where c.d_e_l_e_t_ = ' '
	    and   c.bd6_codldp = a.bd6_codldp
	    and   c.bd6_codpeg = a.bd6_codpeg
	    and   c.bd6_numero = a.bd6_numero
	    and   c.bd6_situac = a.bd6_situac
	    and   c.bd6_anopag = a.bd6_anopag
	    and   c.bd6_mespag = a.bd6_mespag
	    and   c.bd6_blocpa <> '1') as VALOR_TAXA_ADM,
	    (select sum(d.bd6_vlrtpf)
	    from %TABLE:bd6010% d
	    where d.d_e_l_e_t_ = ' '
	    and   d.bd6_codldp = a.bd6_codldp
	    and   d.bd6_codpeg = a.bd6_codpeg
	    and   d.bd6_numero = a.bd6_numero
	    and   d.bd6_situac = a.bd6_situac
	    and   d.bd6_anopag = a.bd6_anopag
	    and   d.bd6_mespag = a.bd6_mespag
	    and   d.bd6_blocpa <> '1') as VALOR_TOTAL_COBRANCA
from %TABLE:bd6010% a,
     %TABLE:ba0010% x
where a.d_e_l_e_t_ = ' '
and   x.d_e_l_e_t_ = ' '
and   substr(bd6_opeori,2,3) = x.ba0_codint
and   a.bd6_situac = '1'    //Fixo
and   a.bd6_fase   > '2'    //Fixo
and   a.bd6_codemp = '0001' //Fixo
and   a.bd6_codpad = '01'   //Fixo
and   a.bd6_codpro in ('80019005','80019013') //Fixo 
	  %exp:cFiltro%
order by a.bd6_numimp
		

	EndSql

END REPORT QUERY oReport:Section(1)

oReport:IncMeter()
oReport:SetMeter(50)
oReport:IncMeter() 

// Executa o CodeBlock com o PrintLine da Sessao 1 toda vez que rodar o oSection1:Init()
oReport:onPageBreak( { || nPag++ , nPrinted := 0 , Cabecalho(oReport,nPag,cSubcon) })
oReport:Section(1):Init()
oReport:EndPage()  


While !oReport:Cancel() .And. !CSQL->(Eof())
	If oReport:Cancel()
		Exit
	EndIf    
	
	oReport:IncMeter()  
	
	oReport:Section(1):PrintLine()   
	
	aadd(aDados,{AllTrim(cSQL->NRO_IMPRESSO),;
	             AllTrim(cSQL->op),;
	             AllTrim(cSQL->NOME_op),;
	             AllTrim(cSQL->NUMERO_GUIA),;
	             AllTrim(cSQL->EVENTO),;
	             cSQL->QTDE,;
	             cSQL->VALOR_ORCADO ,;
	             cSQL->VALOR_COBRANCA,;
	             cSQL->VALOR_TAXA_ADM,;
				 cSQL->VALOR_TOTAL_COBRANCA})


   	
   	cSQL->(dbSkip())
	
	
Enddo  

oReport:Section(1):Finish()  

fGeraExcel()
return

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºFun‡„o      fGerExcelº Autor             		 º Data   27/09/2017  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescri‡„o   Imprime cabecalho										  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso                                                                    º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/
Static function fGeraExcel() 
Local cCodTip := ""
local cArqTxt := alltrim(mv_par03)+".csv"   
Local nCnt    := 1
Private nHdl  := 0   
Private cCpo  := ""  

FErase(cArqTxt)
nHdl := fCreate(cArqTxt)

If nHdl == -1
    MsgAlert("O arquivo de nome "+cArqTxt+" nao pode ser executado! Verifique os parametros.","Atencao!")
    Return .F.
EndIf    

cCpo :="NRO_IMPRESSO;Op.;NOME_op;NUMERO_GUIA;EVENTO;QTDE;VALOR_ORCADO;VALOR_COBRANCA;VALOR_TAXA_ADM;VALOR_TOT_COBRANCA"+CRLF
fWrite(nHdl,cCpo) 


while nCnt <= Len(aDados) 
 	 	
	cCpo := AllTrim(aDados[nCnt][1])+"';"              //nro_impresso 
	cCpo += AllTrim(aDados[nCnt][2])+"';"              //Op.    
	cCpo += AllTrim(aDados[nCnt][3])+";"              //Nome_op
	cCpo += AllTrim(aDados[nCnt][4])+"';"            //numero_guia
	cCpo += AllTrim(aDados[nCnt][5])+"';"              //evento
	cCpo += AllTrim(cValToChar(aDados[nCnt][6]))+";"              //qtde       
	cCpo += transform(aDados[nCnt][7],"@E 999,999,999.99")+";" //valor_orçado
	cCpo += transform(aDados[nCnt][8],"@E 999,999,999.99")+";" //valor_cobranca
	cCpo += transform(aDados[nCnt][9],"@E 999,999,999.99")+";" //valor_taxa_adm
	cCpo += transform(aDados[nCnt][10],"@E 999,999,999.99")+CRLF //valor_total_cobrança
	
	fWrite(nHdl,cCpo) 

   	nCnt++
	
	
EndDo  
	
fClose(nHdl)

Return .T.
/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLSR896   ºAutor   Microsiga           º Data    04/12/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static function Cabecalho(oReport,nPag)  
                                             
Local cTitulo := "Relatorio Atendimento Domiciliar OrcadoXRealizado"    
Local cAno    := "Competencia: " + mv_par01
Local cMes    :=  mv_par02

oReport:SkipLine(1)//Pula linha 
oReport:Say(oReport:Row(), 850,cTitulo+"    "+cAno+cMes,oFont10)
oReport:SkipLine(2)
return
/*
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLSR896   ºAutor   Microsiga           º Data    04/12/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      

Static function Rodape2(oReport,nPag)
oReport:SkipLine()
oReport:EndPage()
return   

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLSR896   ºAutor   Microsiga           º Data    15/03/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Funcao responsavel por gerar perguntas no dicionario de     º±±
±±º           dados, caso as mesmas nao existam.                          º±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function ValidPerg()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}

PutSx1(cPerg,"01",OemToAnsi("Ano                 "),"","","mv_ch01","C",04,0,0,"G","",   "","","","mv_par01",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"02",OemToAnsi("Mes                 "),"","","mv_ch02","C",02,0,0,"G","",   "","","","mv_par02",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"03",OemToAnsi("Caminho para o excel"),"","","mv_ch03","C",60,0,0,"G","",   "DIR","","","mv_par03",     "","","","",     "","","",     "","","","","","","","","")


return     