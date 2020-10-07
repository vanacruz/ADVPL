#INCLUDE "PROTHEUS.CH"
#INCLUDE "TBICONN.CH"
#INCLUDE "COLORS.CH"
#INCLUDE "RPTDEF.CH"
#INCLUDE "FWPrintSetup.ch" 

#DEFINE  cEol CHR(13)+CHR(10)

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSPEP   º Autor ³ Vanessa            º Data ³  07/06/2019 º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³ Relatorio que demosntra historico de guia no PEP           º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/

User Function PLSPEP()   
Local cSql := "" 

Private lValida := .T.
Private cPerg := "PLSPEP"  

CriaSX1(cPerg)   

if !Pergunte(cPerg,.T.,"Historico PEP")
   	return()
endif 
       
cSql := "select trim(BEA_NUMIMP)as Guia, " 
cSql += "bea_opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito as matricula, "
cSql += "bea_nomusr as cliente,trim(ZPZ_CRITIC)||' por '||trim(ZPZ_USUOPE)||' - ' "
cSql += "||case when zp1_nmusu = ' ' then (select trim(bau_nome) from bau010 where bau_codigo=trim(zp1_cnmlog)) else TRIM(ZP1_NMUSU) end||' em '||substr(ZPZ_DATA,7,2)||'/'||substr(ZPZ_DATA,5,2)||'/'|| "
cSql += "substr(ZPZ_DATA,1,4)||' às '||ZPZ_HORA  as Historico "  
cSql += "from siga.bea010,siga.zpz010,siga.zp1010 "
cSql += "where bea_filial=' ' and "
cSql += "zpz_filial =' ' and "
cSql += "bea010.d_e_l_e_t_=' ' and " 
cSql += "zpz010.d_e_l_e_t_=' ' and "
cSql += "zp1010.d_e_l_e_t_=' ' and "
cSql += "zp1_cnmlog=zpz_usuope and "
cSql += "(bea_anoaut||bea_mesaut||bea_numaut=zpz_codlib or "
cSql += "substr(bea_nrlbor,5,14)=zpz_codlib or "
cSql += "bea_numimp=zpz_codlib or zpz_codlib=bea_tracan) and "
cSql += "zpz_codlib<>' ' and "
cSql += "bea_numimp ='"+AllTrim(mv_par01)+"' "     
cSql += "ORDER BY ZPZ_DATA, ZPZ_HORA "
Plsquery(cSql,"TMPBEAP")
                     
If TMPBEAP->(EOF())
    DbCloseArea("TMPBEAP")
	MsgInfo("Guia  não localizada."+cEol+"Verifique o numero da guia.") 
    return(.F.) 
EndIf
DbCloseArea("TMPBEAP")

Processa({|lEnd|PLSPEPA()})   

Return()

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSPEP  º Autor ³ Vanessa            º Data ³  07/06/2019  º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³ monta a grid com os tercerizados                           º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/
Static Function PLSPEPA() 

Local cSql        := ""
Local oLstBEA     := nil   
Private oDlgBEA   := nil 
Private _bRet     := .F.
Private aDadosBEA := {}  

//18/03/2020 andreaj
//acrescentado à query 
//case when zp1_nmusu = ' ' then (select trim(bau_nome) from bau010 where bau_codigo=trim(zp1_cnmlog)) else TRIM(ZP1_NMUSU) end 
//não estava aparecendo o nome de quem autorizou a guia em caso de medico

cSql := "select trim(BEA_NUMIMP)as Guia, " 
cSql += "bea_opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito as matricula, "
cSql += "bea_nomusr as cliente,trim(ZPZ_CRITIC)||' por '||trim(ZPZ_USUOPE)||' - ' "
cSql += "||case when zp1_nmusu = ' ' then (select trim(bau_nome) from bau010 where bau_codigo=trim(zp1_cnmlog)) else TRIM(ZP1_NMUSU) end||' em '||substr(ZPZ_DATA,7,2)||'/'||substr(ZPZ_DATA,5,2)||'/'|| "
cSql += "substr(ZPZ_DATA,1,4)||' às '||ZPZ_HORA  as Historico "
cSql += "from siga.bea010,siga.zpz010,siga.zp1010 "
cSql += "where bea_filial=' ' and "
cSql += "zpz_filial =' ' and "
cSql += "bea010.d_e_l_e_t_=' ' and "
cSql += "zpz010.d_e_l_e_t_=' ' and "
cSql += "zp1010.d_e_l_e_t_=' ' and "
cSql += "zp1_cnmlog=zpz_usuope and "
cSql += "(bea_anoaut||bea_mesaut||bea_numaut=zpz_codlib or "
cSql += "substr(bea_nrlbor,5,14)=zpz_codlib or "
cSql += "bea_numimp=zpz_codlib or zpz_codlib=bea_tracan) and " 
cSql += "zpz_codlib<>' ' and "
cSql += "bea_numimp ='"+AllTrim(mv_par01)+"' "       
cSql += "ORDER BY ZPZ_DATA, ZPZ_HORA "
Plsquery(cSql,"TMPBEAT") 


If TMPBEAT->(Eof()) 
	 DbCloseArea("TMPBEAT")
     Aviso( " Teste","Guia Não encontrada!", {"Ok"},1 )
     Return .F.
Endif

cGuia:=AllTrim(TMPBEAT->guia)
cCliente:=AllTrim(TMPBEAT->cliente)

Do While TMPBEAT->(!Eof()) 

     aAdd( aDadosBEA,{	AllTrim(TMPBEAT->guia),;
      			   		AllTrim(TMPBEAT->Historico)}) 
     
    TMPBEAT->(DbSkip()) 

Enddo 

DbCloseArea("TMPBEAT")     

//--Montagem da Tela

cTitulo := "Guia:"+cGuia + " - Nome: " +cCliente   
                                                                                     
Define MsDialog oDlgBEA Title cTitulo From 0,0 To 500, 825 Of oMainWnd Pixel  

	@ 10,10 LISTBOX oLstBEA;
	FIELDS HEADER "Guia","Historico";
	SIZE 395,210 OF oDlgBEA PIXEL 
	
	oLstBEA:SetArray( aDadosBEA )
	oLstBEA:bLine := {|| {	aDadosBEA[oLstBEA:nAt,1],;
							aDadosBEA[oLstBEA:nAt,2]}}

DEFINE SBUTTON FROM 230,380 TYPE 1 ACTION oDlgBEA:End() ENABLE OF oDlgBEA   

Activate MSDialog oDlgBEA Centered    

Return _bRet  

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PLSR010   ºAutor  ³Microsiga           º Data ³  05/07/10   º±±
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

PutSx1(cPerg,"01",OemToAnsi("Núm. Impresso         ?"),"","","mv_ch1","C",15,0,0,"G","","","","","mv_par01",        "","","","",      "","","","","","","","","","","","")

Return