#INCLUDE "PROTHEUS.CH"
#include "Fileio.ch"
#include "tbiconn.ch"
#include 'rwmake.ch'
#include 'apwebex.ch'

/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  Ajustbe2  º Autor : Vanessa Ap.Cruz    º Data :  06/11/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao : Ajustar divergencia de itens entre BE2010 X BEJ010         º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso:        Job                                                       º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/

User Function Ajustbe2

Prepare Environment Empresa '01' Filial '01' Modulo 'PLS'     


Procurar() //Procurar recnos que precisar ser ajustadas.

Return()
       
/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma: Procurar Autor: Vanessa Ap.Cruz                Data 06/11/2019 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao : Procurando recnos que necessitam de ajuste                 º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/
                                                
Static Function Procurar() 

Local cQuery:= " "

cQuery := " select be2_anoaut,be2_mesaut,be2_numaut,be2_sequen,be2_codpad,be2_codpro,be2_seqptu,be2_nrtrol, be2_codemp,be2_audito, r_e_c_n_o_ as be2rec"
cQuery += " from be2010 "
cQuery += " where be2010.d_e_l_e_t_=' ' and be2_filial=' '  "  
cQuery += " and be2_anoaut>='2019' and be2_seqptu=' ' and be2_codemp='0001' and be2_codpad='16' and be2_tipgui='03' "
cQuery += " and not exists (select * from siga.bej010 "
cQuery += " where bej010.d_e_l_e_t_=' '  and bej_filial=' ' " 
cQuery += " and bej_anoint = be2_anoaut "
cQuery += " and bej_mesint = be2_mesaut "
cQuery += " and bej_numint = be2_numaut "
cQuery += " and bej_sequen = be2_sequen"
cQuery += " and bej_codpad ='16') "


Plsquery(cQuery,"TMP") // Rebece o resultado da query acima 

If  TMP->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para mostrar
    TMP->(DbCloseArea() ) //Fecha tabela TMP que contem o select 
    Return(.F.)
Else
    Alterar() 
	TMP->(DbCloseArea() ) //Fecha tabela TMP que contem o select 
EndIf  


Return()		

/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  Alterar  º Autor ³ Vanessa Ap.Cruz     ºData ³  06/11/2019   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³ Aterando o numero impresso da guia tabelas be4010 e bd7010 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso       ³ AP6 IDE                                                    º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/                                                  

Static Function Alterar() 

Local Update:= " "  

while TMP->(!Eof())

  Begin Transaction  

       Update := " update "  + RetSqlName('BE2') + " "
       Update += " set be2_filial='VA',d_e_l_e_t_='*',be2_audito='0',be2_resaut='Job Ajustbe2'  "  
       Update += " where " +RetSqlName('BE2')+".d_e_l_e_t_ =' ' and be2_filial=' ' "
       Update += " and r_e_c_n_o_ = '"+CValToChar(TMP->be2rec)+"' " //be2010.r_e_c_n_o_  
      TCSQLEXEC(Update)      
  End Transaction
  TMP->(DbSkip()) //vai para o proximo  
enddo     

Return()     

/*  Tabela para dizer quando e que horário este job devera rodar.

select * from siga.tb_Workflow 
where rownum = 1

select *  from siga.tb_Workflow 
order by id

Insert into SIGA.TB_WORKFLOW
   (ID, INICIO, ATIVO, ROTINA, HORA_EXECUTA, 
    INTERVALO, SEGUNDOS_EXECUCAO, DATA_EXECUCAO, HORA_EXECUCAO)
 Values
   (66, '20191106',1, 'U_AJUSTBE2', '14', 
    0, 0, ' ', '00:00:00')

/*