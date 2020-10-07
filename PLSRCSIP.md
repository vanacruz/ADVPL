#include "protheus.ch"
#include "report.ch"

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ                              
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLSRCSIP   ºAutor  Vanessa         º Data   11/10/18   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Relatório de Geração SIP - Classificação  Ambulatorial Inconsistente   º±±                             
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      
User function PLSRCSIP()  			

Local oCB, oDET, oReport 


Local cAno  :=""
Local cMesI :=""                                      
Local cMesF :=""

  

Private cPerg := "PLSRCSIP"   

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

DEFINE REPORT oReport NAME "PLSRCSIP"  TITLE 'Geração SIP - Classificação Ambulatorial Inconsistente' PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}

oReport:SetLandscape() // ou SetPortrait()
oReport:SetTotalInLine(.F.)
oReport:cFontBody := "ARIAL"  
oReport:nFontBody := 6 

DEFINE SECTION oDET OF oReport TITLE "Clientes por Data de Exclusão"

DEFINE CELL NAME "TAB"           OF oDET TITLE "Tabela"         size 7
DEFINE CELL NAME "COD_PROC"      OF oDET TITLE "Codigo"         size 10
DEFINE CELL NAME "GRUPO_AMB"     OF oDET TITLE "Grupo_AMB"      size 8
DEFINE CELL NAME "PROCEDIMENTO"  OF oDET TITLE "Procedimento"   size 180


oDet:nLeftMargin := 0         

DEFINE SECTION oResumo OF oReport TITLE "Dados"

oReport:PrintDialog()

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³ PLSRCSIP  ºAutor                       º Data ³  11/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function PrintReport(oReport)
Local cFiltro := "%" ,nPag:=0 

oReport:SetMeter(2)
oReport:IncMeter()   


if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03)
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif 

if Empty(mv_par01) //Se o ano estiver  
	Alert("Informe o ano para pesquisa.")
    Return
endif

if Empty(mv_par02) //Se o Mes Inicial estiver vazio 
	Alert("Informe o mês inicial para pesquisa.")
    Return
endif

if Empty(mv_par03) //Se o Mes final do cartão estiver vazio 
	Alert("Informe o mês Final para pesquisa.")
    Return
endif


cAno  := AllTrim(mv_par01)
cMesI := AllTrim(mv_par02)                                    
cMesF := AllTrim(mv_par03)  

//Dados informados pelo usuário: 

cFiltro += "and bd6_anopag = '"+cAno+"' and   bd6_mespag >='"+cMesI+"'  and   bd6_mespag <='"+cMesF+"' %"    

BEGIN REPORT QUERY oReport:Section(1)
	
BeginSql alias "cSQL"

//Query   para buscar os dados  

  SELECT distinct
       bd6_codpad as TAB, 
       bd6_codpro as COD_PROC, 
       br8_ygramb as GRUPO_AMB, 
       br8_descri as PROCEDIMENTO
  from %TABLE:BD6%  BD6, //siga.bd6010, 
       %TABLE:BR8%  BR8 //siga.br8010       
  where BD6.%notDel% //bd6010.d_e_l_e_t_ = ' '        
  and   BR8.%notDel% //br8010.d_e_l_e_t_ = ' '
  and   bd6_codpad = br8_codpad 
  and   bd6_codpro = br8_codpsa
  and   (br8_clasip = ' ' or  substr(br8_clasip,1,1) = 'E')    //fixo
  and   bd6_tipgui <> '03'    //fixo           
  and   br8_tpproc in ('0','6')    //fixo       
  and   bd6_situac = '1'   //fixo               
  and   bd6_fase   > '2'    //fixo              
  %exp:cFiltro% 
 order by bd6_codpad, bd6_codpro

EndSql
	
END REPORT QUERY oReport:Section(1)

oReport:IncMeter()
oReport:SetMeter(50)
oReport:IncMeter()

// Executa o CodeBlock com o PrintLine da Sessao 1 toda vez que rodar o oSection1:Init()
oReport:onPageBreak( { || nPag++ , nPrinted := 0 , Cabecalho(oReport,nPag) })
oReport:Section(1):Init()
oReport:EndPage() 

While !oReport:Cancel() .And. !CSQL->(Eof())
	If oReport:Cancel()
		Exit
	EndIf

	oReport:IncMeter()  
	
	oReport:Section(1):PrintLine()
	
	cSQL->(dbSkip())
	//RODAPE
	If oReport:Row() > oReport:LineHeight() * 100 .or. cSQL->(Eof())  
		           
		oReport:EndPage()
		
	endif	

Enddo  
                                            
oReport:Section(1):Finish() 

    fGerExcel(oReport)

return  

/*   
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   fGerExcel ºAutor               º Data   02/06/17   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     Funcao responsavel por gerar arquivo texto para utilizacao  no excel       º±±                                         
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static function fGerExcel(oReport)
Private cArqTxt := " "
Private nHdl    := 0
Private cEOL    := "CHR(13)+CHR(10)" 

 //Nome do arquivo : 
          
cArqTxt := Alltrim(cGetFile("*.*","Salvar",1,"j:\",.T.,GETF_LOCALHARD + GETF_NETWORKDRIVE + GETF_RETDIRECTORY))
cArqTxt += "Geracao_SIP_Classif_Am_Inconsistente_"+SubStr(DtoC(Date()),1,2)+SubStr(DtoC(Date()),4,2)+SubStr(DtoC(Date()),7,4)+"_"+StrTran(Time(),":","")+".csv"
     
FErase(cArqTxt)                      
nHdl :=fCreate(cArqTxt) //Criando o arquivo


If Empty(cEOL) //Quebra de linha 
    cEOL := CHR(13)+CHR(10)
Else
    cEOL := Trim(cEOL)
    cEOL := &cEOL
Endif

If nHdl == -1
    MsgAlert("O arquivo de nome "+cArqTxt+" nao pode ser executado! Verifique os parametros.","Atencao!")
    Return
Endif

// cabecalho

nTamLin := 300
cLin    := Space(nTamLin)+cEOL // Variavel para criacao da linha do registros para gravacao 

cCpo := "SIGA/PLSRCSIP.PRW"+ CRLF
fWrite(nHdl,cCpo)  

cCpo := "Data/Hora Geracao: "+dtoc(DATE())+" - "+time()+ CRLF
fWrite(nHdl,cCpo)    

cCpo := "Operadora"+CRLF
fWrite(nHdl,cCpo)

cCpo := "Relatório de Geração SIP - Classificação Ambulatorial Inconsistente "+CRLF
fWrite(nHdl,cCpo)    

cCpo := " "+CRLF                 
fWrite(nHdl,cCpo)


cCpo := 'Tabela;Codigo;Grupo_AMB;Procedimento'            
cLin := Stuff(cLin,01,nTamLin,cCpo)    


If fWrite(nHdl,cLin,Len(cLin)) != Len(cLin)
	If !MsgAlert("Ocorreu um erro na gravacao do arquivo. Continua?","Atencao!")
		return
	Endif
Endif

CSQL->(dbgotop())  

While !CSQL->(Eof())

	nTamLin := 300
	cLin := Space(nTamLin)+cEOL // Variavel para criacao da linha do registros para gravacao
	   
	cCpo := '"=""'+Alltrim(cSQL->TAB)+'"""'+";"
	cCpo += AllTrim(cSQL->COD_PROC)+";" 
	cCpo += '"=""'+AllTrim(cSQL->GRUPO_AMB)+'"""'+";"
	cCpo += AllTrim(cSQL->PROCEDIMENTO)
	cLin := Stuff(cLin,01,nTamLin,cCpo)
	                                       
	If fWrite(nHdl,cLin,Len(cLin)) != Len(cLin)
		If !MsgAlert("Ocorreu um erro na gravacao do arquivo.","Atencao!")
			Exit
		Endif	
	Endif
	
	CSQL->(dbskip())
Enddo 

fClose(nHdl)

return


Static function Cabecalho(oReport,nPag)                                                
Local cTitulo  := "Geração SIP - Classificação Ambulatorial Inconsistente"
oReport:SkipLine(1)

oReport:Say(oReport:Row(),100,space(50)+cTitulo,oFont14b)    

oReport:SkipLine(4)
return


Static function Rodape2(oReport,nPag)
oReport:SkipLine()
oReport:EndPage()
return    

Static Function ValidPerg()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}


PutSx1(cPerg,"01",OemToAnsi("Ano ")   ,"","","mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Mes De")  ,"","","mv_ch2","C",02,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Mes Até") ,"","","mv_ch3","C",02,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
//PutSx1(cPerg,"04",OemToAnsi("Caminho para o excel") ,"","","mv_ch4","C",60,0,0,"G","","DIR","","","mv_par04",  "","","","",  "","","",  "","","","","","","","","") Nao é mais necessário porque já estando dsando o caminho para o usuário

return    