#INCLUDE "PROTHEUS.CH"
#INCLUDE "TBICONN.CH"
#INCLUDE "COLORS.CH"
#INCLUDE "RPTDEF.CH"
#INCLUDE "FWPrintSetup.ch" 

#DEFINE  cEol CHR(13)+CHR(10)

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSGEXE   º Autor  Vanessa            º Data   23/05/2018 º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao  Relatorio verifica guias executadas a partir da guia    Original    º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/

User Function PLSGEXE()   
Local cSql := " " 

Private lValida := .T.
Private cPerg := "PLSGEXE"  

CriaSX1(cPerg)
if !Pergunte(cPerg,.T.,"Verifica Executados")
   Return()
endif 
           

if Empty(mv_par01) .or. Empty(mv_par02)
	Alert("Por favor preencha todos os parametros") 
	Return()
endif    

cSql := "select * from bea010 where d_e_l_e_t_ = ' ' "
cSql += "and (bea_nrlbor = '"+AllTrim(mv_par01)+"' or bea_nrlbor = '"+AllTrim(mv_par02)+"') " 
Plsquery(cSql,"TMPBEAE")

If TMPBEAE->(EOF())     
    DbCloseArea("TMPBEAE")    
	MsgInfo("Não consta guia(s) executada(s).")  	
 Return()
EndIf
        
DbCloseArea("TMPBEAE")

Processa({|lEnd|PLSGEXEA()}) 



Return()

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSGEXEA  º Autor  Vanessa            º Data   30/05/2018 º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao  monta a grid com as execuções                              º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso        App                                                        º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/
Static Function PLSGEXEA() 

Local cSql        := ""
Local oLstBEA     := nil   
Private oDlgBEA   := nil 
Private _bRet     := .F.
Private aDadosBEA := {} 


cSql := "select decode(bea_audito,'1','SIM','NAO') as Auditoria, "
cSql += "decode(bea_cancel,'1','SIM','NAO') as Cancelado, "
cSql += "decode(bea_status,'1','AUTORIZADO','2','PARCIALMENTE AUT','3','NAO AUT') as  Situacao, "
cSql += "bea_numimp as Guia_executada, "
cSql += "bea_codrda as RDA, "
cSql += "bea_nomrda as Nome_RDA, "
cSql += "be2_sequen as Sequencia, "
cSql += "be2_codpro as Procedimento, "
cSql += "be2_despro as Descricao, "
cSql += "be2_qtdpro as Quantidade, "
cSql += "substr(bea_datpro,7,2)||'/' ||substr(bea_datpro,5,2)||'/'||substr(bea_datpro,1,4) as Dt_Atendimento, " 
cSql += "bea_nrlbor as nrlbor,bea_nomusr as cliente " 
cSql += "from siga.bea010,siga.be2010 "
cSql += "where bea010.d_e_l_e_t_=' ' and be2010.d_e_l_e_t_=' '  and   bea_filial=' ' and be2_filial=' '  "
cSql += "and bea_anoaut = be2_anoaut "
cSql += "and bea_mesaut = be2_mesaut "
cSql += "and bea_numaut = be2_numaut "
cSql += "and (bea_nrlbor= '"+Alltrim(mv_par01)+"'  or bea_nrlbor='"+Alltrim(mv_par02)+"') "
cSql += "order by bea_numimp,be2_sequen "
Plsquery(cSql,"TMPBEAI") 
    
If TMPBEAI->(Eof()) 
	 DbCloseArea("TMPBEAI")  
     MsgInfo("Não consta guia(s) executada(s).") 
     Return (.F.)
Endif   
                  
cNrtrol:=AllTrim(TMPBEAI->nrlbor)
cCliente:=AllTrim(TMPBEAI->cliente)

Do While TMPBEAI->(!Eof()) 

     aAdd( aDadosBEA,{	AllTrim(TMPBEAI->Auditoria),;
      			   		AllTrim(TMPBEAI->Cancelado),;
      			   		AllTrim(TMPBEAI->Situacao),;
      			   		AllTrim(TMPBEAI->Guia_executada),;
      			   	   	AllTrim(TMPBEAI->RDA),;
    					AllTrim(TMPBEAI->Nome_RDA),;
    					AllTrim(TMPBEAI->Sequencia),;
    					AllTrim(TMPBEAI->Procedimento),;
    					AllTrim(TMPBEAI->Descricao),;
    				    TRANSFORM(TMPBEAI->Quantidade,"@E 999"),; //este campo é numero for isso a mascara : TRANSFORM(1234.54, “@E 999.999,99”)     // Resulta: 1.234,54
    					AllTrim(TMPBEAI->Dt_Atendimento)})


​     
    TMPBEAI->(DbSkip()) 

Enddo 

DbCloseArea("TMPBEAI")     

//--Montagem da Tela
cTitulo := "Guia Original: "+cNrtrol + " - Beneficiario: "+cCliente
Define MsDialog oDlgBEA Title cTitulo From 0,0 To 500, 825 Of oMainWnd Pixel  

	@ 10,10 LISTBOX oLstBEA;
	FIELDS HEADER "Auditoria","Cancelado","Situacao","Guia.executada","Rda","Nome.RDA","Sequencia","Procedimento","Descricao","Quantidade","Dt.Atendimento";    
	SIZE 395,210 OF oDlgBEA PIXEL 

  	oLstBEA:SetArray( aDadosBEA )
	oLstBEA:bLine := {|| {	aDadosBEA[oLstBEA:nAt,1],;
							aDadosBEA[oLstBEA:nAt,2],;
							aDadosBEA[oLstBEA:nAt,3],;
							aDadosBEA[oLstBEA:nAt,4],;
							aDadosBEA[oLstBEA:nAt,5],;
							aDadosBEA[oLstBEA:nAt,6],;
							aDadosBEA[oLstBEA:nAt,7],;
							aDadosBEA[oLstBEA:nAt,8],;
							aDadosBEA[oLstBEA:nAt,9],;
							aDadosBEA[oLstBEA:nAt,10],;
							aDadosBEA[oLstBEA:nAt,11]}}


DEFINE SBUTTON FROM 230,380 TYPE 1 ACTION oDlgBEA:End() ENABLE OF oDlgBEA   

Activate MSDialog oDlgBEA Centered    

Return _bRet  

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSR010   ºAutor  Microsiga           º Data   05/07/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     Funcao responsavel por gerar perguntas no dicionario de  dados, caso as mesmas nao existam.                          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {} 

PutSx1(cPerg,"01",OemToAnsi("Núm.Impresso Original        ?"),"","","mv_ch1","C",15,0,0,"G","","","","","mv_par01",        "","","","",      "","","","","","","","","","","","")
PutSx1(cPerg,"02",OemToAnsi("Núm.Autorizacao Original     ?"),"","","mv_ch2","C",18,0,0,"G","","","","","mv_par02",        "","","","",      "","","","","","","","","","","","")
Return

