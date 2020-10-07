#include "protheus.ch"
#include "report.ch"
/*
	Autor: Vanessa Aparecida da Cruz
	Data: 30/05/2011
	Desc: Gerar relatorio de faturas do intercambio 
	
*/
User Function PLSR051()
Local oReport
Local oSE2
Local oBD7
Private cPerg := "PLSR051"

CriaSX1()
pergunte(cPerg,.F.)

DEFINE REPORT oReport NAME "PLSR051" TITLE "Faturas de Intercambio" PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}


	DEFINE SECTION oSE2 OF oReport TITLE "Faturas de Intercambio" TABLES "SE2"
	
		DEFINE CELL NAME "E2_CODRDA"  OF  oSE2 Alias "SE2"             SIZE 20
		DEFINE CELL NAME "E2_NOMFOR"  OF  oSE2 Alias "SE2"             SIZE 45
	    DEFINE CELL NAME "cTIPGUI"    OF  oSE2 TITLE "TIPO DE GUIA" ;
	    block {|| iif(mv_par07=1,"Consulta",iif(mv_par07=2,"Exame/Amb",iif(mv_par07=3,"Internacão" ,"Todos")))} SIZE 25	 
	 	oSE2:nLeftMargin := 4 


  	    
    DEFINE SECTION oITENS OF oSE2 TITLE "ITENS" TABLES "SE2","BD7"
       
     	DEFINE CELL NAME "E2_NUM"	   OF oITENS TITLE "NUM.TITULO" SIZE 15
        DEFINE CELL NAME "E2_PREFIXO"  OF oITENS Alias "SE2"        SIZE 10
        DEFINE CELL NAME "BD7_CODPEG"  OF oITENS Alias "BD7"        SIZE 7
        DEFINE CELL NAME "QTDGUIAS"    OF oITENS TITLE "QTDGUIAS"   SIZE 8
    	oITENS:nLeftMargin := 7


​	  
	 //	DEFINE FUNCTION FROM oBD6:Cell("QTD") FUNCTION SUM TITLE "Total Qtd."   


​		 


oReport:nFontBody := 09  // seta Tamanho da fonte no corpo do relatorio
oReport:PrintDialog()
Return

Static Function PrintReport(oReport)
#IFDEF TOP
	Local cAlias := GetNextAlias()
	local cSql := "%"// BD7_TIPGUI = '0"+ Alltrim(STR(mv_par07)) +"' AND"
	
	if !empty(mv_par01)  // Unimed de - Ate
		cSql += " SE2.E2_CODRDA >= '"+mv_par01+"' AND"
		cSql += " SE2.E2_CODRDA <= '"+mv_par02+"' AND"
	endif	
	if !empty(mv_par03) //Mes de - Ate
		cSql += " SE2.E2_MESBASE >= '"+mv_par03+"' AND"
		cSql += " SE2.E2_MESBASE <= '"+mv_par04+"' AND"
	endif
	if !empty(mv_par05) //Ano de - Ate
		cSql += " SE2.E2_ANOBASE >= '"+mv_par05+"' AND"
		cSql += " SE2.E2_ANOBASE <= '"+mv_par06+"' AND"
	endif 
	if !empty(mv_par07)  //Tipo= 01 Consulta - 02 Exame/Amb -03 Internacao -04 Todos
		if mv_par07 < 4
			cSql += " BD7.BD7_TIPGUI = '0"+Alltrim(str(mv_par07))+"' AND"  
		endif
	endif
	cSql += " %"
	MakeSqlExp("REPORT")
			
	BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "REL"
	
	SELECT DISTINCT 
		BD7.BD7_TIPGUI,
		BD7.BD7_CODPEG,
		SE2.E2_PREFIXO,
		SE2.E2_NUM,
		SE2.E2_CODRDA,
		SE2.E2_NOMFOR,
		SE2.E2_MESBASE,
		SE2.E2_ANOBASE,	
 		(CASE WHEN (BD7_TIPGUI = '01') THEN 'Consulta' ELSE 
	 		(CASE WHEN (BD7_TIPGUI = '02') THEN 'Exame/Ambulatório' ELSE 
				(CASE WHEN (BD7_TIPGUI = '03') THEN 'Internação' END)
			END)
	  	END)  AS TIPGUI,
	  	(CASE WHEN BD7_TIPGUI <> '03' // IF DIFERENTE DE 03 OU SEJA (CONSULTA OU AMB)
       		THEN (SELECT COUNT(*) FROM BD5010 BD5 WHERE // CONTANDO PELO CABECALHO DA GUIA EM BD5
            				BD5.D_E_L_E_T_=' ' AND BD5.BD5_CODLDP=BD7.BD7_CODLDP AND // AMARRANDO POR CODLDP E CODPEG CONTO AS GUIAS POR PEG
            				BD5.BD5_CODPEG=BD7.BD7_CODPEG) 
        	ELSE (SELECT COUNT(*) FROM BE4010 BE4 WHERE   // SENDO INTERNCAO CONTE PELO CABECALHO DA GUIA EM  BE4
            				BE4.D_E_L_E_T_=' ' AND BE4.BE4_CODLDP=BD7.BD7_CODLDP AND 
            				BE4.BE4_CODPEG=BD7.BD7_CODPEG)  
        END)  AS QTDGUIAS
	FROM 
		%TABLE:SE2% SE2,
		%TABLE:BD7% BD7
	WHERE
	    BD7.%notDel% AND //=>	BD7.D_E_L_E_T_ = ' ' AND
	    SE2.%notDel% AND 
		%exp:cSql% 
		SE2.E2_CODRDA=BD7.BD7_CODRDA AND
		SE2.E2_PLLOTE=BD7.BD7_NUMLOT 

   ORDER BY BD7.BD7_TIPGUI,	BD7.BD7_CODPEG,SE2.E2_PREFIXO,
            SE2.E2_NUM,SE2.E2_CODRDA,
            SE2.E2_NOMFOR,SE2.E2_MESBASE,SE2.E2_ANOBASE  
	EndSql
		
	END REPORT QUERY oReport:Section(1) PARAM mv_par08
	
	oReport:Section(1):Section(1):SetParentQuery()
	oReport:Section(1):Section(1):SetParentFilter({|cParam| REL->E2_CODRDA == cParam},{|| REL->E2_CODRDA})
	
	oReport:Section(1):Print()


#ENDIF
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
//Parametros das perguntas
aadd(aRegs,{cPerg,"01","Unimed De  ?","",""  	,"mv_ch1","C",6,0,0,"G",	"","mv_par01",""	   		    ,"","","","",""			    ,"","","","",""			        ,"","","","",""					,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"02","Unimed Ate ?","",""	    ,"mv_ch2","C",6,0,0,"G",	"","mv_par02",""	   		    ,"","","","",""			    ,"","","","",""			        ,"","","","",""		   			,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"03","Mes	De	    ?","",""	,"mv_ch3","C",2,0,0,"G",	"","mv_par03",""	   		    ,"","","","",""			    ,"","","","",""			        ,"","","","",""					,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"04","Mes	Ate	    ?","",""	,"mv_ch4","C",2,0,0,"G",	"","mv_par04",""	   		    ,"","","","",""			    ,"","","","",""			        ,"","","","",""					,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"05","Ano De	    ?","",""	,"mv_ch5","C",4,0,0,"G",	"","mv_par05",""	   		    ,"","","","",""			    ,"","","","",""			        ,"","","","",""					,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"06","Ano Ate	    ?","",""	,"mv_ch6","C",4,0,0,"G",	"","mv_par06",""	   		    ,"","","","",""			    ,"","","","",""			        ,"","","","",""					,"","","","","","","","","",""})
aadd(aRegs,{cPerg,"07","Tipo	    ?","",""	,"mv_ch7","N",1,0,0,"C",	"","mv_par07","Consulta"		,"","","","","Exame/AMB"	,"","","","","Internacao"		,"","","","","Todos"	        ,"","","","","","","","","",""})

PlsVldPerg(aRegs)
return