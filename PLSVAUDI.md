#INCLUDE "PROTHEUS.CH"
#INCLUDE "TBICONN.CH"
#INCLUDE "COLORS.CH"
#INCLUDE "RPTDEF.CH"
#INCLUDE "FWPrintSetup.ch" 

#DEFINE  cEol CHR(13)+CHR(10)

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSVAUDI   º Autor ³ Vanessa            º Data ³  17/07/2019 º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³ Verifica registro da auditoria  º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/

User Function PLSVAUDI()   
Local cSql := " " 
Local cGuia:= " " 
Local cNome:= " " 
Local  cAut:= " "


Private lValida := .T.
Private cPerg := "PLSVAUDI"  

CriaSX1(cPerg)
if !Pergunte(cPerg,.T.,"Demostra quantas vezes houve auditoria.")
   Return()
endif 
           

if Empty(mv_par01) 
	Alert("Por favor preencha o parametro com o numero impresso:  ") 
	Return()
endif    

cGuia:=AllTrim(mv_par01)

cSql := "select bea_anoaut||bea_mesaut||bea_numaut as autorizacao, "
cSql += "bea_nomusr as nome  "
cSql += "from " +  RetSqlName('BEA') + " " 
cSql +=" where " + RetSqlName('BEA') +".d_e_l_e_t_=' ' "
cSql += "and bea_numimp =  '"+cGuia+"'"

Plsquery(cSql,"TMPBEA")

If TMPBEA->(EOF())
	MsgInfo("Guia não encontrada!.") 
 Return()
EndIf

cAut :=AllTrim(TMPBEA->autorizacao) 
cNome:=AllTrim(TMPBEA->nome)

DbCloseArea("TMPBEA")

FWMsgRun( , {||PLSPROVA(cGuia,cAut,cNome)} , 'Processando' , 'Aguarde....') 


Return()

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSPROVA  º Autor ³ Vanessa            º Data ³  17/07/2019 º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³ monta a grid com os registros da auditoria                 º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/

Static Function PLSPROVA(cGuia,cAut,cNome) 

Local cQuery      :=" "
Local cSqlT       :=" "
Local cTotal      :=" "
Local oLstBVX     := nil   
Private oDlgBVX   := nil 
Private _bRet     := .F.
Private aDadosBVX := {} 



cQuery := "select decode(bvx_filial,'DP','Inativa','  ','Ativo') as Filial, "
cQuery += "bvx_sequen as sequencia, "
cQuery += "bvx_codpad as tabela, "
cQuery += "bvx_codpro as codigo, "
cQuery += "bvx_despro as descricao, "
cQuery += "bvx_qtdpro as QTD, "
cQuery += "decode(bvx_parece,'0','Liberado','1','Negado','3','Pendente') as Parecer, " 
cQuery += "bvx_codaud as cod_auditor, "
cQuery += "bvx_nomaud as Auditor, "
cQuery += "substr(bvx_datpar,7,2)||'/' ||substr(bvx_datpar,5,2)||'/'||substr(bvx_datpar,1,4) as dt_parecer, "
cQuery += "bvx_msg01 as mensagem, "
cQuery += "decode(d_e_l_e_t_,'*','Item_inativo',' ','Item_ativo') as item "
cQuery += "from  bvx010 "
cQuery += "where  bvx_anoaut||bvx_mesaut||bvx_numaut= '"+cAut+"'" 
cQuery += "order by r_e_c_n_o_ "


Plsquery(cQuery,"TMPBVX") 
    
If TMPBVX->(Eof()) 
	 DbCloseArea("TMPBVX")  
     MsgInfo("Não foi encontrada auditoria!") 
     Return .F.
Endif   

Do While TMPBVX->(!Eof()) 

     aAdd( aDadosBVX,{	AllTrim(TMPBVX->Filial),;
      			   		AllTrim(TMPBVX->sequencia),;
      			   		AllTrim(TMPBVX->tabela),;
      			   		AllTrim(TMPBVX->codigo),;
      			   	   	AllTrim(TMPBVX->descricao),;
    					TRANSFORM(TMPBVX->QTD,"@E 999"),; //este campo é numero for isso a mascara : TRANSFORM(1234.54, “@E 999.999,99”)     // Resulta: 1.234,54
    					AllTrim(TMPBVX->Parecer),;
    					AllTrim(TMPBVX->cod_auditor),;
    					AllTrim(TMPBVX->Auditor),;
    					AllTrim(TMPBVX->dt_parecer),;
    					AllTrim(TMPBVX->mensagem),;
    					AllTrim(TMPBVX->item)})


​     
    TMPBVX->(DbSkip()) 

Enddo 

DbCloseArea("TMPBVX")

cSqlT :="select count(*) as contador "
cSqlT +="from bvx010 "
cSqlT +="where bvx_anoaut||bvx_mesaut||bvx_numaut= '"+cAut+"'"

Plsquery(cSqlT,"TMPCONT") 


If TMPCONT->(Eof()) 
	 DbCloseArea("TMPCONT")  
     MsgInfo("Quantidade zerada de registros!") 
     Return .F.
Endif   

cTotal :=TMPCONT->contador
     
DbCloseArea()	 

//--Montagem da Tela
              
cTitulo := "Guia: "+AllTrim(cGuia) + " - Beneficiario: "+AllTrim(cNome) + " - Total registros: "+cvaltochar(cTotal)
Define MsDialog oDlgBVX Title cTitulo From 0,0 To 500, 825 Of oMainWnd Pixel  

	@ 10,10 LISTBOX oLstBVX;
	FIELDS HEADER "Filial","Sequencia","Tabela","Codigo","Descricao","QTD","Parecer","Cod_Auditor","Auditor","Dt.Parecer","Mensagem","Item";    
	SIZE 395,210 OF oDlgBVX PIXEL 

  	oLstBVX:SetArray( aDadosBVX )
	oLstBVX:bLine := {|| {	aDadosBVX[oLstBVX:nAt,1],;
							aDadosBVX[oLstBVX:nAt,2],;
							aDadosBVX[oLstBVX:nAt,3],;
							aDadosBVX[oLstBVX:nAt,4],;
							aDadosBVX[oLstBVX:nAt,5],;
							aDadosBVX[oLstBVX:nAt,6],;
							aDadosBVX[oLstBVX:nAt,7],;
							aDadosBVX[oLstBVX:nAt,8],;
							aDadosBVX[oLstBVX:nAt,9],;
							aDadosBVX[oLstBVX:nAt,10],;
							aDadosBVX[oLstBVX:nAt,11],;
							aDadosBVX[oLstBVX:nAt,12]}}


DEFINE SBUTTON FROM 230,380 TYPE 1 ACTION oDlgBVX:End() ENABLE OF oDlgBVX   

Activate MSDialog oDlgBVX Centered    

Return _bRet  

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³   ºAutor  ³Microsiga           º Data ³  05/07/10          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³Funcao responsavel por gerar perguntas no dicionario de     º±±
±±º          ³dados, caso as mesmas nao existam.                          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {} 

PutSx1(cPerg,"01",OemToAnsi("Núm.Impresso: ?"),"","","mv_ch1","C",15,0,0,"G","","","","","mv_par01",        "","","","",      "","","","","","","","","","","","")
Return