#include "protheus.ch"
#include "report.ch"

/*

ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PLSR890   ºAutor  ³  VANESSA          º Data ³   14/05/18   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Melhoria para equipe verificar                             º±±
±±º          ³ qual é o seu nível de auditor                              º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User function PLSR890()

Local oCB, oDET, oReport, oResumo   

oFont8   := TFont():New("Arial",9,8 ,.T.,.F.,5,.T.,5,.T.,.F.)
oFont8b  := TFont():New("Arial",9,8 ,.T.,.T.,5,.T.,5,.T.,.F.)
oFont10  := TFont():New("Arial",9,10,.T.,.F.,5,.T.,5,.T.,.F.)
oFont10b := TFont():New("Arial",9,10,.T.,.T.,5,.T.,5,.T.,.F.)
oFont11  := TFont():New("Arial",9,11,.T.,.F.,5,.T.,5,.T.,.F.)
oFont11b := TFont():New("Arial",9,11,.T.,.T.,5,.T.,5,.T.,.F.)
oFont12  := TFont():New("Arial",9,12,.T.,.F.,5,.T.,5,.T.,.F.)
oFont12b := TFont():New("Arial",9,12,.T.,.T.,5,.T.,5,.T.,.F.)
oFont14  := TFont():New("Arial",9,14,.T.,.F.,5,.T.,5,.T.,.F.)
oFont14b := TFont():New("Arial",9,14,.T.,.T.,5,.T.,5,.T.,.F.)
oFont16  := TFont():New("Arial",9,16,.T.,.F.,5,.T.,5,.T.,.F.)
oFont16b := TFont():New("Arial",9,16,.T.,.T.,5,.T.,5,.T.,.F.)
oFont21  := TFont():New("Arial",9,21,.T.,.f.,5,.T.,5,.T.,.F.)

DEFINE REPORT oReport NAME "PLSR890" TITLE ("Operador Sistema x Nivel Auditoria") ACTION {|oReport| PrintReport(oReport)}

oReport:SetLandscape() // ou SetPortrait()
oReport:SetTotalInLine(.F.)    
oReport:cFontBody := "ARIAL"  
oReport:nFontBody := 5 
oReport:SetDevice(4) //1-Arquivo,2-Impressora,3-email,4-Planilha e 5-Html.
oReport:SetEnvironment(2) // 1-Server e 2-Cliente


DEFINE SECTION ODET OF OREPORT TITLE "Movimentos"
DEFINE CELL NAME "BX4_CODOPE" OF ODET TITLE "Cod Operador"                SIZE 08
DEFINE CELL NAME "BX4_NOMOPE" OF ODET TITLE "Nome Operador"               SIZE 60
DEFINE CELL NAME "NIVELAUD"   OF ODET TITLE "Nivel Auditoria"             SIZE 10
DEFINE CELL NAME "DESCRINIV"  OF ODET TITLE "Descricao Nivel Auditoria"   SIZE 40

oDet:nLeftMargin := 0

DEFINE SECTION oResumo OF oReport TITLE "Resumo"    
oReport:nLeftMargin := 2   
oReport:PrintDialog() 

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PLRS890   ºAutor  ³Microsiga           º Data ³  14/05/18   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function PrintReport(oReport)
Local aNiveis := {}
Local cCodOpe := ""

oReport:cFile := oReport:cDir+"niveis_operador_"+SubStr(DtoC(Date()),1,2)+SubStr(DtoC(Date()),4,2)+SubStr(DtoC(Date()),7,4)+"_"+StrTran(Time(),":","")+".xml" 
oReport:SetMeter(2)
oReport:IncMeter()   

BEGIN REPORT QUERY oReport:Section(1)
	
	BeginSql alias "CSQL"   
	    
		SELECT '0' tipo, 'Cod Operador' BX4_CODOPE, 'Nome Operador' BX4_NOMOPE, 'Nivel Auditoria' BX4_YGRAUD, 'Descricao Nivel Auditoria' X5_DESCRI FROM DUAL
		UNION ALL
		SELECT '1', BX4_CODOPE, BX4_NOMOPE, BX4_YGRAUD, ''
	    FROM %TABLE:BX4% BX4  
			WHERE BX4.D_E_L_E_T_ = ' '
			AND BX4.BX4_FILIAL = '  ' 
			AND BX4.BX4_YGRAUD <> ' ' // and BX4_CODOPE = '000029'
		ORDER BY 1,BX4_CODOPE 
		
	EndSql

END REPORT QUERY oReport:Section(1)

oReport:IncMeter()
oReport:SetMeter(50)
oReport:IncMeter()

oReport:Section(1):SetHeaderSection(.F.) 
oReport:Section(1):Init() 

oReport:EndPage()      
	
While !oReport:Cancel() .And. !CSQL->(Eof())
	If oReport:Cancel()
		Exit
	EndIf 

	oReport:IncMeter()  
	
	If CSQL->TIPO == '0'
	   	oReport:Section(1):cell("NIVELAUD"):SetValue("NIVEL AUDITORIA")
	    oReport:Section(1):cell("DESCRINIV"):SetValue("DESCRICAO NIVEL AUDITORIA")  
		oReport:Section(1):PrintLine() 
	Else
		aNiveis := Separa(AllTrim(CSQL->BX4_YGRAUD),",")  
		
		For x:=1 to len(aNiveis)  		
		
			oReport:Section(1):cell("BX4_CODOPE"):SetValue(CSQL->BX4_CODOPE)
			oReport:Section(1):cell("BX4_NOMOPE"):SetValue(CSQL->BX4_NOMOPE)
		
			cQuery := "select * from sx5010 where d_e_l_e_t_ = ' ' and x5_tabela = 'Z6' "
			cQuery += "and x5_chave = "+aNiveis[x]+" "  
			PlsQuery(cQuery,"TMPSX5")		
			
			if !TMPSX5->(Eof()) 
				oReport:Section(1):cell("NIVELAUD"):SetValue(aNiveis[x])
				oReport:Section(1):cell("DESCRINIV"):SetValue(TMPSX5->X5_DESCRI)
	        Else
				oReport:Section(1):cell("NIVELAUD"):SetValue(aNiveis[x])
				oReport:Section(1):cell("DESCRINIV"):SetValue("Nível sem descricao na SX5")
			EndIf 
			oReport:Section(1):PrintLine()	
			TMPSX5->(DBCLOSEAREA())
		next 
	EndIf
	CSQL->(DbSkip()) 
	
	//RODAPE
   	If oReport:Row() > oReport:LineHeight() * 80  
   		oReport:EndPage()   		
	endif	
	
Enddo

oReport:Section(1):Finish()
return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PLSR010   ºAutor  ³Microsiga           º Data ³  04/12/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³                                                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static function Rodape2(oReport,nPag)
oReport:SkipLine()
oReport:EndPage()
return       

Static Function ValidPerg()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}

PutSx1(cPerg,"01",OemToAnsi("Caminho p/ arquivo?")         ,"","","mv_ch1","C",60,0,0,"G","","DIR","","","mv_par01","","","","","","","","","","","","","","","")

return   