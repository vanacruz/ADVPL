#include "protheus.ch"
#include "report.ch"

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ                              
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLSGAFAT   ºAutor   Vanessa         º Data       09/06/2019 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Localizando guias em que houve inconsistencia entre bdh x bd6      º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/  

User function PLSGAFAT()  			

Local oCB, oDET, oReport,oResumo   
                                                              
Private cPerg      := "PLSGAFAT"   
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


DEFINE REPORT oReport NAME "PLSGAFAT" TITLE cSubcon PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}

oReport:SetPortrait() // ou SetLandscape()
oReport:SetTotalInLine(.F.)
oReport:cFontBody := "ARIAL"  
oReport:nFontBody := 6     

oReport:SetLeftMargin(2)

DEFINE SECTION oDET OF oReport TITLE "Movimentos"


DEFINE CELL NAME "Impresso"        OF oDET TITLE "IMPRESSO"        SIZE 15 
DEFINE CELL NAME "Local"           OF oDET TITLE "LOCAL"           SIZE 10 
DEFINE CELL NAME "Peg"             OF oDET TITLE "PEG"             SIZE 12   
DEFINE CELL NAME "Numero"          OF oDET TITLE "NUMERO"          SIZE 10  
DEFINE CELL NAME "competencia"     OF oDET TITLE "COMPETENCIA"     SIZE 08
DEFINE CELL NAME "Titulo"          OF oDET TITLE "TITULO"          SIZE 15 



DEFINE SECTION oResumo OF oReport TITLE "Resumo"

oDet:nLeftMargin := 0
oReport:PrintDialog()

Return
/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSGAFAT   ºAutor   Microsiga           º Data    09/06/19   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
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
	cFiltro += "and a.bd6_codemp = '"+AllTrim(MV_PAR01)+"' " 
  //	cFiltro += "%"
Else
	MsgAlert("Informe a empresa.") 
    Return()
EndIf   

If !empty(MV_PAR02)
	cFiltro += "and a.bd6_matric = '"+AllTrim(MV_PAR02)+"' " 
  //	cFiltro += "%"
Else
	MsgAlert("Informe a família.") 
    Return()
EndIf  


If !empty(MV_PAR03)
	cFiltro += "and a.bd6_anopag = '"+AllTrim(MV_PAR03)+"' " 
  //	cFiltro += "%"
Else
	MsgAlert("Informe o ano.") 
    Return()
EndIf   
       
If !empty(MV_PAR04)
	cFiltro += "and a.bd6_mespag = '"+AllTrim(MV_PAR04)+"' " 
 //	cFiltro += "%"
Else
	MsgAlert("Informe o mes.") 
    Return()
EndIf  
	
	cFiltro += "%"

BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "CSQL"  
select distinct (a.bd6_numimp) as IMPRESSO,
                 a.bd6_codldp  as LOCAL,
				 a.bd6_codpeg  as PEG,
				 a.bd6_numero  as NUMERO,
				 a.bd6_anopag || bd6_mespag as COMPETENCIA, 
				 a.bd6_numtit as TITULO 
from %TABLE:bd6010% a
where a.bd6_situac = '1' //Ativa (Fixo)
and   a.bd6_fase   > '2'//conferencia (Fixo)
and   a.bd6_stafat <> '0'// em aberto (Fixo)
and   a.bd6_vlrtpf > 0 //tem valor de cooparticipação (Fixo)
and   a.bd6_blocpa <> '1' // não esta bloqueada a cobrança(Fixo) 
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
	
	aadd(aDados,{AllTrim(cSQL->IMPRESSO),;
	             AllTrim(cSQL->LOCAL),;
	             AllTrim(cSQL->PEG),;
	             AllTrim(cSQL->NUMERO),;
	             AllTrim(cSQL->COMPETENCIA),;
				 AllTrim(cSQL->TITULO)})


   	
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
local cArqTxt := alltrim(mv_par05)+".csv"   
Local nCnt    := 1
Private nHdl  := 0   
Private cCpo  := ""  

FErase(cArqTxt)
nHdl := fCreate(cArqTxt)

If nHdl == -1
    MsgAlert("O arquivo de nome "+cArqTxt+" nao pode ser executado! Verifique os parametros.","Atencao!")
    Return .F.
EndIf    


cCpo := "IMPRESSO;LOCAL;PEG;NUMERO;COMPETENCIA;TITULO"+CRLF
fWrite(nHdl,cCpo) 


while nCnt <= Len(aDados) 
 	 	
	cCpo := AllTrim(aDados[nCnt][1])+"';"      //IMPRESSO 
	cCpo += AllTrim(aDados[nCnt][2])+"';"      //LOCAL    
	cCpo += AllTrim(aDados[nCnt][3])+"';"       //PEG
	cCpo += AllTrim(aDados[nCnt][4])+"';"       //NUMERO
	cCpo += AllTrim(aDados[nCnt][5])+"';"       //COMPETENCIA
	cCpo += AllTrim(aDados[nCnt][6])+CRLF       //TITULO
	
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
±±ºDesc.                                                                  º±±
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso         AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static function Cabecalho(oReport,nPag)  
                                             
Local cTitulo := "Relatorio Localizando Guias"    
Local cAno    := "Competencia: " + mv_par03
Local cMes    :=  mv_par04

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
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso         AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function ValidPerg()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}

PutSx1(cPerg,"01",OemToAnsi("Empresa             "),"","","mv_ch01","C",04,0,0,"G","",   "","","","mv_par01",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"02",OemToAnsi("Familia             "),"","","mv_ch02","C",06,0,0,"G","",   "","","","mv_par02",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"03",OemToAnsi("Ano                 "),"","","mv_ch03","C",04,0,0,"G","",   "","","","mv_par03",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"04",OemToAnsi("Mes                 "),"","","mv_ch04","C",02,0,0,"G","",   "","","","mv_par04",     "","","","",     "","","",     "","","","","","","","","")
PutSx1(cPerg,"05",OemToAnsi("Caminho para o excel"),"","","mv_ch05","C",60,0,0,"G","",   "DIR","","","mv_par05",     "","","","",     "","","",     "","","","","","","","","")


return     