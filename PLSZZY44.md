#include "PROTHEUS.CH"
#include "PLSMGER.CH"
#include "FILEIO.CH"
#include 'tbiconn.ch'         

#DEFINE cEOL CHR(13)+CHR(10)   
                                                                                                                  

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºFun‡„o     ³ PLSZZY44 º Autor ³ Vanessa           º Data ³ 24/03/2020  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescri‡„o  ³ Gerando Workflow para atendimento quanto guia tem em seu   º±±
±±º           ³ historio o Status = 44            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

User Function PLSZZY44()  

Local cSubject    := " "  
Private cDataBase := DTOS(date())      
                     
Prepare environment empresa '01' filial '01' modulo 'pls' //È esse trecho que ira enviar o  e-mail. 
                            
cDataBase := DTOC(date())

PLSZZY() //Buscando Dados BEA010 X ZZY010

Return()                                                                        

////////////// Buscando Dados/////////////

Static Function PLSZZY()   
         

Local cSQL := ""  
            
    cSQL :="select bea_numimp as impresso, "
    cSQL +="bea_opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito as matricula, "
    cSQL +="bea_nomusr as nome, "
    cSQL +="case when bea_tipgui='01' then 'Consulta' when bea_tipgui='02' then 'SP/SADT' when bea_tipgui='03' then 'Internacao' end as tipo, "
    cSQL +="bea_codrda as rda, " "
    cSQL +="bea_nomrda as nmrda,
    cSQL +="case when bea_audito='1' then 'Sim' when bea_audito='0' then 'Não' end as audito , "
    cSQL +="case when bea_status='1' then 'Autorizado' when bea_status='2' then 'Parcialmente Autorizado' when bea_status='3' then 'Não Autorizado' end as situacao, "
    cSQL +="bea_dtdigi as dt_digi "
    cSQL +="from siga.bea010,siga.zzy010 "
    cSQL +="where bea010.d_e_l_e_t_=' '  "
    cSQL +="and zzy010.d_e_l_e_t_=' '  "
    cSQL +="and bea_filial=' '  "
    cSQL +="and zzy_filial=' ' "
    cSQL +="and bea_numimp = zzy_guia "
    cSQL +="and zzy_status='44' "
    cSQL +="and bea_dtdigi>='20200325' "
      	   	 
    PlsQuery(cSQL, 'TMPZZY') 

   If  !TMPZZY->(Eof()) 
      EnvEmail()
      TMPZZY->(DbCloseArea()) 
   Else
      TMPZZY->(DbCloseArea())  
   endif 
    
return()

////////////// Enviando e-mail /////////////

Static Function EnvEmail()

	cSubject	:= "Guias com Status: 44 - INTERCAMBIO  - " + cDataBase + StrTran( Time(), ':', '')
	cBody		:= "======================================================================================================" + cEOL + cEOL
	cBody		+= "Dados da Guia: " + cEOL
	cBody		+= "======================================================================================================" + cEOL + cEOL
	cBody		+= "Número guia......: " + Alltrim((TMPZZY->impresso)) + cEOL 
	cBody		+= "Matricula........: " + Alltrim((TMPZZY->matricula)) + cEOL 
	cBody		+= "Nome.............: " + Alltrim((TMPZZY->nome)) + cEOL 
	cBody		+= "Tipo de guia.....: " + Alltrim((TMPZZY->tipo)) + cEOL  
	cBody		+= "Prestador........: " + Alltrim((TMPZZY->rda)) + cEOL  
	cBody	    += "Nome Prestador...: " + Alltrim((TMPZZY->nmrda)) + cEOL 
	cBody		+= "Dt.Digitacao.....: " + DTOC(STOD(TMPZZY->dt_digi)) + cEOL 
	cBody    	+= "Em Auditoria.....: " + Alltrim((TMPZZY->audito)) + cEOL 
	cBody    	+= "Status da guia...: " + Alltrim((TMPZZY->situacao)) + cEOL   
	cBody		+= "======================================================================================================" + cEOL + cEOL
	cBody    	+= "Este e-mail atende ao chamado número zzzzz - Rotina: PLSZZY44 " 		
	cTo         := 'xxxxx@xxxxxxxx.com.br;yyyyyyyyy.@yyyyyyyyyy.com.br;bbbbbbbb@bbbbbbbb.com.br' 
	cCC         := ''  
	cCO         := ''
	cAttach     := ''
	cSource     := ''			 	

   U_FCFG001(cSubject, cBody, cTo, cCC, cCO, cAttach, cSource)   			
return     

/*  Tabela para dizer quando e que horário este job devera rodar.

select * from tb_Workflow 
where rownum = 1


select *  from tb_Workflow 


Insert into SIGA.TB_WORKFLOW
   (ID, INICIO, ATIVO, ROTINA, HORA_EXECUTA, 
    INTERVALO, SEGUNDOS_EXECUCAO, DATA_EXECUCAO, HORA_EXECUCAO)
 Values
   (61, '20191104',1, 'U_AJUSTIMP', '15', 
    0, 0, ' ', '00:00:00')

update siga.tb_Workflow set HORA_EXECUTA='06'
where ID =60 
/*