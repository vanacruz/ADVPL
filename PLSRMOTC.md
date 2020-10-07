#include "protheus.ch"
#include "report.ch"

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ                              
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³ PLSRMOTC   ºAutor  ³ Vanessa         º Data ³   09/11/17   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³  Relatório de motivos de criticas tabela BCT010            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      
User function PLSRMOTC()  			

Local oCB, oDET, oReport 


Private cPerg := "PLSRMOTC"   

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

DEFINE REPORT oReport NAME "PLSRMOTC"  TITLE 'Motivos de Criticas' PARAMETER cPerg ACTION {|oReport| PrintReport(oReport)}

oReport:SetLandscape() // ou SetPortrait()
oReport:SetTotalInLine(.F.)
oReport:cFontBody := "ARIAL"  
oReport:nFontBody := 6 

DEFINE SECTION oDET OF oReport TITLE "Clientes por Data de Exclusão"

DEFINE CELL NAME "CODIGO_GLOSA "         OF oDET TITLE "Glosa_Protheus"        size 04 
DEFINE CELL NAME "DESCRICAO"             OF oDET TITLE "Descricao"             size 45
DEFINE CELL NAME "COD_PTUONLINE"         OF oDET TITLE "Ptu_Online"            size 06
DEFINE CELL NAME "COMUNICA_ONLINE"       OF oDET TITLE "Comunica_Online"       size 03
DEFINE CELL NAME "ATV_BAIXO_RISCO"       OF oDET TITLE "Baixo_Risco"           size 03
DEFINE CELL NAME "CRITICA_CONTRATUAL"    OF oDET TITLE "Critica_contratual"    size 03
DEFINE CELL NAME "COD_PTUBATCH"          OF oDET TITLE "Ptu_Batch"             size 04      
DEFINE CELL NAME "GLOSA_ATIVA"           OF oDET TITLE "Glosa_Ativa"           size 03
DEFINE CELL NAME "ATIVA_PROTHEUS"        OF oDET TITLE "Ativa_Protheus"        size 03
DEFINE CELL NAME "LIB_ESPECIAL"          OF oDeT TITLE "Libera_Especial"       size 03
DEFINE CELL NAME "PERMITE_FORCAR"        OF oDET TITLE "Permite_Forcar"        size 03
DEFINE CELL NAME "AUDITORIA"             OF oDET TITLE "Auditoria"             size 03
    
oDet:nLeftMargin := 0         

DEFINE SECTION oResumo OF oReport TITLE "Dados"

oReport:PrintDialog()

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PLSRDESL  ºAutor  ³Microsiga           º Data ³  23/06/17   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function PrintReport(oReport)
Local cFiltro := "%" ,nPag:=0 

oReport:SetMeter(2)
oReport:IncMeter()   


If mv_par01 == 2 .And. mv_par02 == 2 .And. mv_par03 ==2 .And. mv_par04==2 .And.  mv_par05==2 // Se o usuário escolher todas opções = Não
   Alert ('Para gerar o relatório deve-se escolher apenas uma opção = sim e as demais opções = Não  ')
   return
EndIf	

If mv_par01 == 1 .And. mv_par02 == 1 .And. mv_par03 ==1 .And. mv_par04==1 .And.  mv_par05==1 //Se o usuário escolher todas opções = Sim
   Alert ('Para gerar o relatório deve-se escolher apenas uma  opção = sim e as demais opções = Não ')
   return
EndIf    

//Se o uusário  escolher  mais de uma opção = Sim 

If ( mv_par01 == 1 .And. mv_par02 == 1 .And. mv_par03 == 2 .And. mv_par04== 2 .And.  mv_par05== 2) .or. ;
   ( mv_par01 == 1 .And. mv_par02 == 2 .And. mv_par03 == 1 .And. mv_par04== 2 .And.  mv_par05== 2) .or. ;
   ( mv_par01 == 1 .And. mv_par02 == 2 .And. mv_par03 == 2 .And. mv_par04== 1 .And.  mv_par05== 2) .or. ;
   ( mv_par01 == 1 .And. mv_par02 == 2 .And. mv_par03 == 2 .And. mv_par04== 2 .And.  mv_par05== 1) .or. ;
   ( mv_par01 == 2 .And. mv_par02 == 1 .And. mv_par03 == 1 .And. mv_par04== 2 .And.  mv_par05== 1) .or. ;
   ( mv_par01 == 2 .And. mv_par02 == 1 .And. mv_par03 == 2 .And. mv_par04== 1 .And.  mv_par05== 2) .or. ;
   ( mv_par01 == 2 .And. mv_par02 == 1 .And. mv_par03 == 2 .And. mv_par04== 2 .And.  mv_par05== 1) .or. ;
   ( mv_par01 == 2 .And. mv_par02 == 2 .And. mv_par03 == 1 .And. mv_par04== 1 .And.  mv_par05== 2) .or. ;
   ( mv_par01 == 2 .And. mv_par02 == 2 .And. mv_par03 == 1 .And. mv_par04== 2 .And.  mv_par05== 1) .or. ;
   ( mv_par01 == 2 .And. mv_par02 == 2 .And. mv_par03 == 2 .And. mv_par04== 1 .And.  mv_par05== 1) 

   Alert ('Para gerar o relatório deve-se escolher apenas uma opção = sim  e as e as demais opções = Não ')

  return

EndIf 


//cFiltro += "%" 


  If mv_par02 == 1 //Somente Com Depara Ptu_online = Sim 
     cFiltro += " and bct_coded2<>' ' "
  Endif
    if mv_par03 == 1 // Somente Sem Depara Ptu_online = Sim 
   cFiltro += " and bct_coded2=' ' "
    Endif	
      if mv_par04 == 1 // Somente Com Depara Ptu_batch  = sim  
    cFiltro +=" and bct_codedI<>' ' " 
  Endif		
      if mv_par05 ==  1 // Somente Sem Depara Ptu_batch  = sim  
    cFiltro +=" and bct_codedI=' ' "
  EndIf  



cFiltro += "%"    

BEGIN REPORT QUERY oReport:Section(1)
	
BeginSql alias "cSQL"

//Query basica para  todas as opções que o cliente escolher 

  SELECT  bct_propri||bct_codglo as CODIGO_GLOSA,  
     bct_descri as DESCRICAO,
     bct_coded2 as COD_PTUONLINE,
     case when bct_comonl = '0' then 'Nao' when bct_comonl ='1' then 'Sim' end as COMUNICA_ONLINE,
     case when bct_ativbr = '0' then 'Nao' when bct_ativbr ='1' then 'Sim' end as ATV_BAIXO_RISCO,
     case when bct_ycrctt = '0' then 'Nao' when bct_ycrctt ='1' then 'Sim' end as CRITICA_CONTRATUAL,                                                                                                                                                                                                                                        
     bct_codedI as COD_PTUBATCH,                                                                                                                    
     case when bct_ativo='0'   then 'Nao' when bct_ativo='1' then 'Sim'  end as GLOSA_ATIVA,                                                                                                                     
     case when bct_prot='0'    then 'Nao' when bct_prot= '1'  then 'Sim'  end as ATIVA_PROTHEUS,                                                                                                                    
     case when bct_libesp='0'  then 'Nao' when bct_libesp='1' then 'Sim' end as LIB_ESPECIAL,                                                                                                                      
     case when bct_perfor='0'  then 'Nao' when bct_perfor='1' then 'Sim' end as PERMITE_FORCAR,                                                                                                              
     case when bct_audito ='0' then 'Nao' when bct_audito='1' then 'Sim' end as AUDITORIA                                                                                                                       
  from siga.bct010
  where bct010.d_e_l_e_t_=' ' and bct_filial=' ' 
  %exp:cFiltro% 

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

if !empty(mv_par06) 
    
    fGerExcel(oReport)

EndIf         
                                       
return  

/*   
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³fGerExcel ºAutor             º Data ³  02/06/17   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³Funcao responsavel por gerar arquivo texto para utilizacao  no excel    º±±

±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static function fGerExcel(oReport)
Private cArqTxt := alltrim(mv_par06)+".csv"
Private nHdl    := fCreate(cArqTxt)
Private cEOL    := "CHR(13)+CHR(10)"

If Empty(cEOL)
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

cCpo := 'Glosa_Protheus;Descricao;Ptu_Online;Comunica_Online;Baixo_Risco;Critica_contratual;Ptu_Batch;'           
cCpo += 'Glosa_Ativa;Ativa_Protheus;Libera_Especial;Permite_Forcar;Auditoria'   
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
	   
	cCpo := '"=""'+Alltrim(cSQL->CODIGO_GLOSA)+'"""'+";"
	cCpo += AllTrim(cSQL->DESCRICAO)+";" 
	cCpo += '"=""'+AllTrim(cSQL->COD_PTUONLINE)+'"""'+";"
	cCpo += AllTrim(cSQL->COMUNICA_ONLINE)+";"
	cCpo += AllTrim(cSQL->ATV_BAIXO_RISCO)+";" 
	cCpo += AllTrim(cSQL->CRITICA_CONTRATUAL)+";" 
	cCpo += '"=""'+AllTrim(cSQL->COD_PTUBATCH)+'"""'+";"       
	cCpo += AllTrim(cSQL->GLOSA_ATIVA)+";" 
	cCpo += AllTrim(cSQL->ATIVA_PROTHEUS)+";"      
	cCpo += AllTrim(cSQL->LIB_ESPECIAL)+";"
	cCpo += AllTrim(cSQL->PERMITE_FORCAR)+";"
	cCpo += AllTrim(cSQL->AUDITORIA)
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
Local cTitulo  := "Relatório de Motivo de Criticas"
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


PutSx1(cPerg,"01",OemToAnsi("Todas as glosas?")              ,"","","mv_ch1","N",01,0,0,"C","","","","","mv_par01",    "Sim","","","",     "Nao","","",     "","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Somente Com Depara Ptu_online") ,"","","mv_ch2","N",01,0,0,"C","","","","","mv_par02",    "Sim","","","",     "Nao","","",     "","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Somente Sem Depara Ptu_online") ,"","","mv_ch3","N",01,0,0,"C","","","","","mv_par03",    "Sim","","","",     "Nao","","",     "","","","","","","","","",{},{},{})
PutSx1(cPerg,"04",OemToAnsi("Somente Com Depara Ptu_batch")  ,"","","mv_ch4","N",01,0,0,"C","","","","","mv_par04",    "Sim","","","",     "Nao","","",     "","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Somente Sem Depara Ptu_batch")  ,"","","mv_ch5","N",01,0,0,"C","","","","","mv_par05",    "Sim","","","",     "Nao","","",     "","","","","","","","","",{},{},{})
PutSx1(cPerg,"06",OemToAnsi("Caminho para o excel")          ,"","","mv_ch6","C",60,0,0,"G","",   "DIR","","","mv_par06",  "","","","",  "","","",  "","","","","","","","","")

return      