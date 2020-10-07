#include "protheus.ch"
#include "report.ch"

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ                              
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLRGUIAS   ºAutor   Vanessa         º Data       28/03/2019 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Detalhamento de movimentação  de contas          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/  

User function PLRGUIAS()  			

Local oCB, oDET, oReport,oResumo   
                                                              
Private cPerg      := "PLRGUIAS"   
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


DEFINE REPORT oReport NAME "PLRGUIAS" TITLE cSubcon PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}

oReport:SetPortrait() // ou SetLandscape()
oReport:SetTotalInLine(.F.)
oReport:cFontBody := "ARIAL"  
oReport:nFontBody := 6     

oReport:SetLeftMargin(2)

DEFINE SECTION oDET OF oReport TITLE "Movimentos"


DEFINE CELL NAME "CODRDA"               OF oDET TITLE "CODIGO_RDA"           SIZE 08 
DEFINE CELL NAME "EMPRESA"              OF oDET TITLE "EMPRESA"              SIZE 08 
DEFINE CELL NAME "CODESP"               OF oDET TITLE "Cod_ESPECIALIDADE"    SIZE 06
DEFINE CELL NAME "ESPECIALIDADE"        OF oDET TITLE "ESPECIALIDADE"        SIZE 45   
DEFINE CELL NAME "NUMIMP"               OF oDET TITLE "NUMIMP"               SIZE 25  
DEFINE CELL NAME "GUIA_ORIGEM"          OF oDET TITLE "GUIA_ORIGEM"          SIZE 04
DEFINE CELL NAME "DT_ATEND"             OF oDET TITLE "DATA_ATEND"           SIZE 05 
DEFINE CELL NAME "DT_ALTA"              OF oDET TITLE "DATA_ALTA"            SIZE 15
DEFINE CELL NAME "MATRICULA"            OF oDET TITLE "MATRICULA"            SIZE 15
DEFINE CELL NAME "NUMGUIA"              OF oDET TITLE "NUMERO_GUIA"          SIZE 15
DEFINE CELL NAME "EVENTO"               OF oDET TITLE "EVENTO"               SIZE 15
DEFINE CELL NAME "UMS"           		OF oDET TITLE "UMS"                  SIZE 05 
DEFINE CELL NAME "PARTIC"               OF oDET TITLE "PARTIC"               SIZE 15
DEFINE CELL NAME "TAB"                  OF oDET TITLE "TAB"                  SIZE 15 
DEFINE CELL NAME "CODPRO"               OF oDET TITLE "CODPRO"               SIZE 15
DEFINE CELL NAME "DT_REALIZ"            OF oDET TITLE "DATA_REALIZ"          SIZE 15
DEFINE CELL NAME "QTDPRO"               OF oDET TITLE "QTDPRO"               SIZE 15
DEFINE CELL NAME "VLR_ORCADO"           OF oDET TITLE "VALOR_ORCADO"         SIZE 15
DEFINE CELL NAME "PAG_EVENTO"           OF oDET TITLE "VLR_PAG_UNIT_EVENTO"  SIZE 15
DEFINE CELL NAME "PAG_COMP"             OF oDET TITLE "VLR_PAG_UNIT_COMP"    SIZE 15
DEFINE CELL NAME "TOTAL"                OF oDET TITLE "VALOR_PAGAR_TOTAL"    SIZE 15
DEFINE CELL NAME "COPART"               OF oDET TITLE "VLR_COPART"           SIZE 15
DEFINE CELL NAME "TX_ADM"               OF oDET TITLE "VLR_TAXA_ADM"         SIZE 15
DEFINE CELL NAME "TOT_COPART"           OF oDET TITLE "VLR_TOTAL_COPART"     SIZE 15
DEFINE CELL NAME "BLOPAG"               OF oDET TITLE "BLOPAG"               SIZE 15
DEFINE CELL NAME "MOTBLO"               OF oDET TITLE "MOTBLO"               SIZE 15
DEFINE CELL NAME "BLOCPA"               OF oDET TITLE "BLOCPA"               SIZE 15
DEFINE CELL NAME "DESPRO"               OF oDET TITLE "DESPRO"               SIZE 15

DEFINE SECTION oResumo OF oReport TITLE "Resumo"

oDet:nLeftMargin := 0
oReport:PrintDialog()

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLRGUIAS   ºAutor   Microsiga           º Data    04/13/10   º±±
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
Local cRDA         := ""  
Local cFiltro      := "%" 
Local nPag         := 0  
Private aDados     := {}	


oReport:SetMeter(2)
oReport:IncMeter()

If !empty(MV_PAR01) 

       cFiltro += "'"+AllTrim(MV_PAR01)+"'" 
Else
	MsgAlert("Informe o Codigo RDA. ") 
    Return()
EndIf 
         
If !empty(MV_PAR02)
	cFiltro += " and   bd7_anopag = '"+AllTrim(MV_PAR02)+"' " 

Else
	MsgAlert("Informe o ano.") 
    Return()
EndIf             

If !empty(MV_PAR03)
	cFiltro += " and   bd7_mespag = '"+AllTrim(MV_PAR03)+"' " 

Else
	MsgAlert("Informe o mês.") 
    Return()
EndIf 
 cFiltro += "%" 

BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "CSQL"  
		select bd5_codrda CODRDA,
	   bd5_codemp EMPRESA,
	   bd7_codesp CODESP,
	   (select baq_descri from %TABLE:baq010%  where d_e_l_e_t_ = ' ' and   baq_codesp= bd7_codesp) ESPECIALIDADE,       
	   bd7_numimp NUMIMP,
	   (select bea_guiori  from %TABLE:bea010%  where d_e_l_e_t_ = ' ' and   bea_numimp = bd5_numimp and bea_cancel <> '1') as GUIA_ORIGEM,
	   substr(bd5_datpro,7,2)||'/'||substr(bd5_datpro,5,2)||'/'|| substr(bd5_datpro,1,4) DT_ATEND,
	   substr(bd5_datpro,7,2)||'/'||substr(bd5_datpro,5,2)||'/'|| substr(bd5_datpro,1,4) DT_ALTA,
	   bd6_codope||'.'||bd6_codemp||'.'||bd6_matric||'.'||bd6_tipreg||'.'||bd6_digito MATRICULA,
	   bd7_codldp||'.'||bd7_codpeg||'.'||bd7_numero NUMGUIA,
	   bd7_sequen EVENTO,
	   bd7_codunm UMS,
	   bd7_codtpa PARTIC,
	   bd7_codpad TAB,
	   bd7_codpro CODPRO,
	   substr(bd7_datpro,7,2)||'/'|| substr(bd7_datpro,5,2)||'/'||substr(bd7_datpro,1,4) DT_REALIZ,
	   bd6_qtdpro QTDPRO,
	   bd6_vlracb VLR_ORCADO,
	   bd6_vlrapr PAG_EVENTO,
	   bd7_vlrapr PAG_COMP,
	   bd7_vlrpag TOTAL,
	   bd7_vlrtpf - bd7_vlrtad as COPART,
	   bd7_vlrtad TX_ADM,
	   bd7_vlrtpf TOT_COPART,
	   case when bd7_blopag ='0' then 'nao' when bd7_blopag ='1' then 'sim' end as BLOPAG,
	   bd7_motblo MOTBLO,
	   case when bd6_blocpa ='0' then 'nao' when bd6_blocpa ='1' then 'sim' end as BLOCPA,
	   bd6_despro DESPRO
from  %TABLE:bd5010%,
      %TABLE:bd6010%, 
      %TABLE:bd7010%      
where bd5010.d_e_l_e_t_ = ' '
and   bd6010.d_e_l_e_t_ = ' '
and   bd7010.d_e_l_e_t_ = ' '
and   bd5_codldp = bd6_codldp
and   bd5_codpeg = bd6_codpeg
and   bd5_numero = bd6_numero
and   bd5_situac = bd6_situac
and   bd5_anopag = bd6_anopag
and   bd5_mespag = bd6_mespag
and   bd6_codldp = bd7_codldp
and   bd6_codpeg = bd7_codpeg
and   bd6_numero = bd7_numero
and   bd6_sequen = bd7_sequen
and   bd6_situac = bd7_situac
and   bd6_anopag = bd7_anopag
and   bd6_mespag = bd7_mespag
and   bd7_situac = '1' //Fixo
and   bd7_fase   > '2' //Fixo  
and   bd5_codrda = %exp:cFiltro%
UNION ALL
select be4_codrda CODRDA,
       be4_codemp EMPRESA,
       bd7_codesp CODESP,
       (select baq_descri  from %TABLE:baq010%   where d_e_l_e_t_ = ' '  and   baq_codesp= bd7_codesp) ESPECIALIDADE,
       bd7_numimp NUMIMP,
       (select bea_guiori from %TABLE:bea010%  where d_e_l_e_t_ = ' ' and   bea_numimp = be4_numimp and bea_cancel <> '1') as GUIA_ORIGEM,
       substr(be4_datpro,7,2)||'/'|| substr(be4_datpro,5,2)||'/'|| substr(be4_datpro,1,4) DT_ATEND,
       substr(be4_dtalta,7,2)||'/'|| substr(be4_dtalta,5,2)||'/'|| substr(be4_dtalta,1,4) DT_ALTA,   
       bd6_codope||'.'|| bd6_codemp||'.'|| bd6_matric||'.'|| bd6_tipreg||'.'|| bd6_digito MATRICULA,
       bd7_codldp||'.'|| bd7_codpeg||'.'|| bd7_numero NUMGUIA,       
       bd7_sequen EVENTO,
       bd7_codunm UMS,
       bd7_codtpa PARTIC,
       bd7_codpad TAB,
       bd7_codpro CODPRO,
       substr(bd7_datpro,7,2)||'/'|| substr(bd7_datpro,5,2)||'/'||
       substr(bd7_datpro,1,4) DT_REALIZ,
       bd6_qtdpro QTDPRO,
       bd6_vlracb VLR_ORCADO,
       bd6_vlrapr PAG_EVENTO,
       bd7_vlrapr PAG_COMP,
       bd7_vlrpag TOTAL,
       bd7_vlrtpf - bd7_vlrtad COPART,
       bd7_vlrtad TX_ADM,
       bd7_vlrtpf TOT_COPART,
       case when bd7_blopag ='0' then 'nao' when bd7_blopag ='1' then 'sim' end as BLOPAG,
       bd7_motblo MOTBLO,
       case when bd6_blocpa ='0' then 'nao' when bd6_blocpa ='1' then 'sim' end as BLOCPA,
       bd6_despro DESPRO
from %TABLE:be4010%, 
     %TABLE:bd6010%, 
     %TABLE:bd7010%      
where be4010.d_e_l_e_t_ = ' '
and   bd6010.d_e_l_e_t_ = ' '
and   bd7010.d_e_l_e_t_ = ' '
and   be4_codldp = bd6_codldp
and   be4_codpeg = bd6_codpeg
and   be4_numero = bd6_numero
and   be4_situac = bd6_situac
and   be4_anopag = bd6_anopag
and   be4_mespag = bd6_mespag
and   bd6_codldp = bd7_codldp
and   bd6_codpeg = bd7_codpeg
and   bd6_numero = bd7_numero
and   bd6_sequen = bd7_sequen
and   bd6_situac = bd7_situac
and   bd6_anopag = bd7_anopag
and   bd6_mespag = bd7_mespag
and   bd7_situac = '1' //Fixo
and   bd7_fase   > '2' //Fixo 
and   be4_codrda =%exp:cFiltro%
//and   be4_numimp ='70010734358' para testar a query          
order by 1, 2, 3, 5, 14,15	
	EndSql
	
END REPORT QUERY oReport:Section(1)

oReport:IncMeter()
oReport:SetMeter(50)
oReport:IncMeter() 

// Executa o CodeBlock com o PrintLine da Sessao 1 toda vez que rodar o oSection1:Init()
oReport:onPageBreak( { || nPag++ , nPrinted := 0 , Cabecalho(oReport,nPag,cSubcon) })
oReport:Section(1):Init()
oReport:EndPage()  

nTotal     := 0  
nTotalDesc := 0 

While !oReport:Cancel() .And. !CSQL->(Eof())
	If oReport:Cancel()
		Exit
	EndIf    
	
	oReport:IncMeter()  
	
	oReport:Section(1):PrintLine()   
	
	aadd(aDados,{AllTrim(cSQL->CODRDA),;
	             AllTrim(cSQL->EMPRESA),;
	             AllTrim(cSQL->CODESP),;
	             AllTrim(cSQL->ESPECIALIDADE),;
	             AllTrim(cSQL->NUMIMP),;
	             AllTrim(cSQL->GUIA_ORIGEM),;
	             AllTrim(cSQL->DT_ATEND),;
	             AllTrim(cSQL->DT_ALTA),;
	             AllTrim(cSQL->MATRICULA),;    
				 AllTrim(cSQL->NUMGUIA),;
				 AllTrim(cSQL->EVENTO),;
				 AllTrim(cSQL->UMS),;
				 AllTrim(cSQL->PARTIC),;
				 AllTrim(cSQL->TAB),;    
				 AllTrim(cSQL->CODPRO),;
				 AllTrim(cSQL->DT_REALIZ),;
			             cSQL->QTDPRO,;
				         cSQL->VLR_ORCADO,;
				         cSQL->PAG_EVENTO,;
				         cSQL->PAG_COMP,;
			             cSQL->TOTAL,;
				         cSQL->COPART,;
				         cSQL->TX_ADM,;
				         cSQL->TOT_COPART,;
				 AllTrim(cSQL->BLOPAG),;
				 AllTrim(cSQL->MOTBLO),;
				 AllTrim(cSQL->BLOCPA),;
	             AllTrim(cSQL->DESPRO)})

   


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

local cArqTxt := alltrim(mv_par04)+".csv"
Local nCnt    := 1
Private nHdl  := 0
Private cCpo  := " "        


FErase(cArqTxt)
nHdl := fCreate(cArqTxt)

If nHdl == -1
    MsgAlert("O arquivo de nome "+cArqTxt+" nao pode ser executado! Verifique os parametros.","Atencao!")
    Return .F.
EndIf    
 			
cCpo := "CODRDA;EMPRESA;CODESP;ESPECIALIDADE;NUMIMP;GUIA_ORIGEM;DT_ATEND;DT_ALTA;MATRICULA;NUMGUIA;EVENTO;UMS;PARTIC;TAB;CODPRO;DT_REALIZ;"
cCpo += "QTDPRO;VLR_ORCADO;PAG_EVENTO;PAG_COMP;TOTAL;COPART;TX_ADM;TOT_COPART;BLOPAG;MOTBLO;BLOCPA;DESPRO"+CRLF  

fWrite(nHdl,cCpo) 
	
while nCnt <= Len(aDados) 

   	cCpo := AllTrim(aDados[nCnt][1])+";"              //CODRDA 
	cCpo += AllTrim(aDados[nCnt][2])+";"              //EMPRESA    
	cCpo += AllTrim(aDados[nCnt][3])+"';"       	  //CODESP
	cCpo += AllTrim(aDados[nCnt][4])+"';"             //ESPECIALIDADE
	cCpo += AllTrim(aDados[nCnt][5])+";"              //NUMIMP
	cCpo += AllTrim(aDados[nCnt][6])+";"              //GUIA_ORIGEM
	cCpo += Alltrim(aDados[nCnt][7])+";"              //DT_ATEND 
	cCpo += Alltrim(aDados[nCnt][8])+";"              //DT_ALTA 	 -Dtos(aDados[nCnt][8])+";"     
	cCpo += AllTrim(aDados[nCnt][9])+"';"             //MATRICULA     '"=""'+AllTrim(aDados[nCnt][9])+";" 
	cCpo += AllTrim(aDados[nCnt][10])+";"             //NUMGUIA
	cCpo += AllTrim(aDados[nCnt][11])+"';"           //EVENTO      '"=""'+AllTrim(aDados[nCnt][11])+";" 
	cCpo += AllTrim(aDados[nCnt][12])+";"             //UMS
	cCpo += AllTrim(aDados[nCnt][13])+";"             //PARTIC
	cCpo += AllTrim(aDados[nCnt][14])+"';"            //TAB      '"=""'+AllTrim(aDados[nCnt][14])+";" 
	cCpo += AllTrim(aDados[nCnt][15])+";"             //CODPRO
	cCpo += AllTrim(aDados[nCnt][16])+";"             //DT_REALIZ  ou Dtoc	
	cCpo += cValToChar(aDados[nCnt][17])+";"            //QTDPRO 
	cCpo += Transform(aDados[nCnt][18],"@E 999,999,999.99")+";"  //VLR_ORCADO Transform(aTotal[01],"@E 999,999,999.99")+;
	cCpo += transform(aDados[nCnt][19],"@E 999,999,999.99")+";"  //PAG_EVENTO
	cCpo += transform(aDados[nCnt][20],"@E 999,999,999.99")+";"  //PAG_COMP
	cCpo += transform(aDados[nCnt][21],"@E 999,999,999.99")+";"  //TOTAL
	cCpo += transform(aDados[nCnt][22],"@E 999,999,999.99")+";"  //COPART 
	cCpo += transform(aDados[nCnt][23],"@E 999,999,999.99")+";"  //TX_ADM
	cCpo += transform(aDados[nCnt][24],"@E 999,999,999.99")+";"  //TOT_COPART
	cCpo += AllTrim(aDados[nCnt][25])+";"                          //BLOPAG 
	cCpo += AllTrim(aDados[nCnt][26])+";"                          //MOTBLO 
    cCpo += AllTrim(aDados[nCnt][27])+";"                          //BLOCPA 
	cCpo += AllTrim(aDados[nCnt][28])+CRLF                        //DESPRO 
	
	fWrite(nHdl,cCpo)  

   	nCnt++
EndDo  
	
fClose(nHdl)

Return .T.

Static function Cabecalho(oReport,nPag)  
                                             
Local cTitulo := "Relatorio Guias"    
Local cAno    := "Competencia: " + mv_par02
Local cMes    :=  mv_par03

oReport:SkipLine(1)//Pula linha 
oReport:Say(oReport:Row(), 850,cTitulo+"    "+cAno+cMes,oFont10)
oReport:SkipLine(2)
return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLSR896   ºAutor   Microsiga           º Data    15/03/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Funcao responsavel por gerar perguntas no dicionario de     º±±
±±º           dados, caso as mesmas nao existam.                          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function ValidPerg()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}


PutSx1(cPerg,"01",OemToAnsi("Codigo RDA:         "),"","","mv_ch01","C",07,0,0,"G","",   "","","","mv_par01",     "","","","",     "","","",     "","","","","","","")
PutSx1(cPerg,"02",OemToAnsi("Ano                 "),"","","mv_ch02","C",04,0,0,"G","",   "","","","mv_par02",     "","","","",     "","","",     "","","","","","","")
PutSx1(cPerg,"03",OemToAnsi("Mes                 "),"","","mv_ch03","C",02,0,0,"G","",   "","","","mv_par03",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"04",OemToAnsi("Caminho para o excel"),"","","mv_ch04","C",60,0,0,"G","","DIR","","","mv_par04",  "","","","",  "","","",  "","","","","","","","","")
return     