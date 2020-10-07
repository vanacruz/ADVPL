#include "protheus.ch"
#include "report.ch"
/*
	Autor:Vanessa Aparecida da Cruz
	Data : 04/11/2011
	Desc : Relatorio para pegar os impostos lancados como Debito 
	
*/
User Function PLSR054()
Local oReport
Local oBMS
Local oBBB
Private cPerg := "PLSR054"

CriaSX1()
pergunte(cPerg,.F.)

DEFINE REPORT oReport NAME "PLSR054" TITLE "Relatorio Impostos como Debito" PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}


	DEFINE SECTION oBMS OF oReport TITLE "Impostos como Debito" TABLES "BMS","BBB"
	
		DEFINE CELL NAME "BMS_ANOLOT"  OF  oBMS Alias "BMS" SIZE 10
		DEFINE CELL NAME "BMS_MESLOT"  OF  oBMS Alias "BMS" SIZE 10 
		DEFINE CELL NAME "BMS_CODRDA"  OF  oBMS Alias "BMS" SIZE 15
		DEFINE CELL NAME "BMS_CODSER"  OF  oBMS Alias "BMS" SIZE 10
		DEFINE CELL NAME "BBB_DESCRI"  OF  oBMS Alias "BBB" SIZE 30
		DEFINE CELL NAME "BMS_VLRPAG"  OF  oBMS Alias "BMS" SIZE 25
 		oBMS:nLeftMargin:= 4 //setando Tamanho da margem no cabecalho
		
oReport:nFontBody := 9  // setando Tamanho da fonte no corpo do relatorio
oReport:PrintDialog()
Return

Static Function PrintReport(oReport)
#IFDEF TOP
	Local cAlias := GetNextAlias()
	local cSql := "%"
	
	if !empty(mv_par01) //Mes 
		cSql += " BMS.BMS_MESLOT = '"+mv_par01+"' AND"
	endif
	if !empty(mv_par02) //Ano 
		cSql += " BMS.BMS_ANOLOT = '"+mv_par02+"' AND"
	endif 
	
	cSql += " %"
	MakeSqlExp("REPORT")
			
	BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "REL"
	                          
	SELECT 
		BMS.BMS_ANOLOT, 
	    BMS.BMS_MESLOT, 
	    BMS.BMS_CODRDA, 
	    BMS.BMS_CODSER, 
	    BBB.BBB_DESCRI, 
	    BMS.BMS_VLRPAG	                       
	FROM 
		%TABLE:BMS% BMS,
		%TABLE:BBB% BBB
	
	WHERE 
	     BMS.%notDel%  AND //=> BMS010. D_E_L_E_T_ = ' ' 
	     BBB.%notDel%  AND //=> BBB010. D_E_L_E_T_ = ' ' 
	     %exp:cSql%     
	     BMS.BMS_CODSER = BBB.BBB_CODSER  AND
	     BBB.BBB_YIMP <>'7'        
	ORDER BY BMS_CODSER, BMS_CODRDA
	
	EndSql
		
	END REPORT QUERY oReport:Section(1)
	
	oReport:Section(1):Print()

if MV_PAR03 == 1// Verifica se o parametro gerar em Excel esta como SIM
	GeraExcel()
endif

REL->(DbCloseArea())

#ENDIF

MS_FLUSH()
Return   

// Detalhamento da funcao para gerar em  Excel:

Static Function GeraExcel()
Local cCpo	:= ""
Local cArq	:= iif(!EMPTY(Alltrim(MV_PAR04)),Alltrim(MV_PAR04),'U:\')+"RelDebImpost_";// caso o caminha esteja preenchido gere nele;caso nao gere em no U:/ com um nome pre-estabelecido
			    	+DtoS(date())+".csv"
Local nHdl	:= fCreate(cArq) // crie o arquivo
	
REL->(DbGoTop())

fWrite(nHdl,"BMS_ANOLOT;BMS_MESLOT;BMS_CODRDA;BMS_CODSER;BBB_DESCRI;BMS_VLRPAG" +Chr(10)) //Estas sao as coluna que sairao no Excel+ pular para a linha de debaixo 

While !REL->(Eof())
	
	cCpo	:= Alltrim(REL->BMS_ANOLOT)+";"
	cCpo	+= Alltrim(REL->BMS_MESLOT)+";"
	cCpo    += Alltrim(REL->BMS_CODRDA)+";" 
	cCpo	+= Alltrim(REL->BMS_CODSER)+";"
	cCpo	+= Alltrim(REL->BBB_DESCRI)+";"
	cCpo	+= Alltrim(StrTran(Str(REL->BMS_VLRPAG),".",","))+Chr(10) // substituindo o ponto por virgula
	
	fWrite(nHdl,cCpo)
	REL->(DbSkip())	   //PROXIMO REGISTRO
enddo

fClose(nHdl)// fecha o arquivo
Return

Static Function CriaSX1()  // CASO A PERGUNTA JA EXISTA APAGUE E CRIE OUTRA
LOCAL aRegs	:=	{}

If  SX1->(MsSeek(cPerg))
	While .T.
		If  SX1->(MsSeek(cPerg))
			SX1->(RecLock("SX1",.F.))
			SX1->(DbDelete())
			SX1->(MsUnLock())
		Else
			Exit
		Endif
	Enddo
Endif  

//Parametros das perguntas : 

aadd(aRegs,{cPerg,"01","Mes	    	    ","",""    	,"mv_ch1","C",2,0,0,"G",    "","mv_par01",""          ,"","","","",""            ,"","","","",""		            ,"","","","",""				    ,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"02","Ano	    		","",""	    ,"mv_ch2","C",4,0,0,"G",    "","mv_par02",""          ,"","","","",""            ,"","","","",""		            ,"","","","",""					,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"03","Gera Excel	   ?","",""	    ,"mv_ch3","N",1,0,4,"C",	"","mv_par03","Sim"       ,"","","","","Nao"    	 ,"","","","",""    		        ,"","","","",""     	        ,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"04","Local arquivo  ?","",""	    ,"mv_ch4","C",90,0,0,"G",	"","mv_par04",""	      ,"","","","",""			 ,"","","","",""			        ,"","","","",""			        ,"","","","","","","","","",""})

PlsVldPerg(aRegs)
return