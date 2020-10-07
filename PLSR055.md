#include "protheus.ch"
#include "report.ch"

/*
	Autor:Vanessa Aparecida da Cruz
	Data : 08/11/2011
	Desc :Relatorio para mostrar Titulos NDF impostos
*/
User Function PLSR055()
Local oReport
Local oSE2
Local oBAU
Local oBMR
Local oBLR
Private cPerg :="PLSR055"

CriaSX1()
pergunte(cPerg,.F.)

DEFINE REPORT oReport NAME "PLSR055" TITLE "Relatorio Titulos NDF - Impostos" PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}


	DEFINE SECTION oSE2 OF oReport TITLE "Titulos NDF - Impostos" TABLES "SE2","BAU","BMR","BLR"
	
		DEFINE CELL NAME "E2_ANOBASE"   OF  oSE2 Alias "SE2" SIZE 12 
		DEFINE CELL NAME "E2_MESBASE"   OF  oSE2 Alias "SE2" SIZE 12
		DEFINE CELL NAME "E2_PREFIXO"   OF  oSE2 Alias "SE2" SIZE 13
		DEFINE CELL NAME "E2_NUM"       OF  oSE2 Alias "SE2" SIZE 20
		DEFINE CELL NAME "E2_TIPO"  	OF  oSE2 Alias "SE2" SIZE 08
		DEFINE CELL NAME "E2_CODRDA" 	OF  oSE2 Alias "SE2" SIZE 15
		DEFINE CELL NAME "BAU_NOME"     OF  oSE2 Alias "BAU" //SIZE 45
		DEFINE CELL NAME "BMR_CODLAN"   OF  oSE2 Alias "BMR" //SIZE 05
		DEFINE CELL NAME "BLR_DESCRI"   OF  oSE2 Alias "BLR" SIZE 20
		DEFINE CELL NAME "BMR_VLRPAG"   OF  oSE2 Alias "BMR" SIZE 15
 		oSE2:nLeftMargin:= 2 //setando Tamanho da margem no cabecalho
 		oSE2:nFontBody := 5
		
oReport:nFontBody := 9  // setando Tamanho da fonte no corpo do relatorio
oReport:SetLandscape()
oReport:PrintDialog()
Return

Static Function PrintReport(oReport)
#IFDEF TOP
	Local cAlias := GetNextAlias()
	local cSql := "%"
	
	if !empty(mv_par01) //Mes 
		cSql += " SE2.E2_MESBASE = '"+mv_par01+"' AND"
	endif
	if !empty(mv_par02) //Ano 
		cSql += " SE2.E2_ANOBASE = '"+mv_par02+"' AND"
	endif 
	
	cSql += " %"
	MakeSqlExp("REPORT")
			
	BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "REL"
	                          
	SELECT 
		SE2.E2_ANOBASE,
		SE2.E2_MESBASE, 
		SE2.E2_PREFIXO, 
		SE2.E2_NUM, 
		SE2.E2_TIPO, 
		SE2.E2_CODRDA, 
		BAU.BAU_NOME, 
		BMR.BMR_CODLAN, 
		BLR.BLR_DESCRI, 
		BMR.BMR_VLRPAG	                       
	FROM 
		%TABLE:SE2% SE2,
		%TABLE:BMR% BMR,
		%TABLE:BLR% BLR,
		%TABLE:BAU% BAU 
	WHERE 
	     SE2.%notDel% AND //=> SE2010. D_E_L_E_T_ = ' ' 
	     BMR.%notDel% AND //=> BMR010. D_E_L_E_T_ = ' '
	     BLR.%notDel% AND
	     BAU.%notDel% AND      
	     SE2.E2_CODRDA  = BMR.BMR_CODRDA AND 
	     SE2.E2_PLLOTE  = BMR.BMR_ANOLOT||BMR.BMR_MESLOT||BMR.BMR_NUMLOT AND 
		 BMR.BMR_CODLAN = BLR.BLR_PROPRI||BLR.BLR_CODLAN AND
		 BMR.BMR_CODRDA = BAU.BAU_CODIGO AND
		 %exp:cSql%
		 SE2.E2_TIPO ='NDF'AND
		 BMR.BMR_CODLAN IN('169','183','185','187','189','191','193','195','197','199')
	ORDER BY BMR.BMR_CODRDA, BMR.BMR_CODLAN
	
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
Local cArq	:= iif(!EMPTY(Alltrim(MV_PAR04)),Alltrim(MV_PAR04),'U:\')+"RelTituloNDF_";// caso o caminha esteja preenchido gere nele;caso nao gere em no U:/ com um nome pre-estabelecido
			    	+DtoS(date())+".csv"
Local nHdl	:= fCreate(cArq) // crie o arquivo
	
REL->(DbGoTop())

fWrite(nHdl,"E2_ANOBASE;E2_MESBASE;E2_PREFIXO;E2_NUM;E2_TIPO;E2_CODRDA;BAU_NOME;BMR_CODLAN;BLR_DESCRI;BMR_VLRPAG " +Chr(10)) //Estas sao as coluna que sairao no Excel+ pular para a linha de debaixo 

While !REL->(Eof())
	
	cCpo	:= Alltrim(REL->E2_ANOBASE)+";"
	cCpo	+= Alltrim(REL->E2_MESBASE)+";"
	cCpo	+= Alltrim(REL->E2_PREFIXO)+";"
	cCpo	+= Alltrim(REL->E2_NUM)+";"
	cCpo    += Alltrim(REL->E2_TIPO)+";"
	cCpo    += Alltrim(REL->E2_CODRDA)+";"
	cCpo    += Alltrim(REL->BAU_NOME)+";"
	cCpo    += Alltrim(REL->BMR_CODLAN)+";"
	cCpo    += Alltrim(REL->BLR_DESCRI)+";"
	cCpo	+= Alltrim(StrTran(Str(REL->BMR_VLRPAG),".",","))+Chr(10) // substituindo o ponto por virgula
	
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