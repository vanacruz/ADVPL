#INCLUDE "PROTHEUS.CH"
#include "Fileio.ch"
#include "tbiconn.ch"
#include 'rwmake.ch'
#include 'apwebex.ch'

/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  Ajustimp  º Autor : Vanessa Ap.Cruz    º Data :  01/11/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao : Ajustar divergencia de numero impresso BEA010 X BE4010     º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso        Job                                                       º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/

User Function Ajustimp

Prepare Environment Empresa '01' Filial '01' Modulo 'PLS'     


Procurar()//Procurar guias que precisar ser ajustadas.

Return()
       
/*/
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma:Procurar Autor:Vanessa Ap.Cruz                Data 01/11/2019 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao : Procurando guias que necessitam de ajuste                  º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/
                                                
Static Function Procurar() 

Local cQuery:= " "

cQuery := " select bea_numimp guia ,be4_numimp as impresso,bea_nomusr,bea_codldp as loc,bea_codpeg as peg,bea_numgui as numero,  "
cQuery += " bea010.r_e_c_n_o_ as bea_rec,be4010.r_e_c_n_o_  as be4_rec "
cQuery += " from bea010, be4010 "
cQuery += " where bea010.d_e_l_e_t_=' ' and be4010.d_e_l_e_t_=' '  "
cQuery += " and bea_filial =' ' "
cQuery += " and be4_filial =' ' "
cQuery += " and bea_codldp = be4_codldp "
cQuery += " and bea_codpeg = be4_codpeg "
cQuery += " and bea_numgui = be4_numero "
cQuery += " and bea_codrda = be4_codrda "
cQuery += " and bea_codemp = be4_codemp "
cQuery += " and bea_matric = be4_matric "
cQuery += " and bea_tipreg = be4_tipreg "
cQuery += " and bea_digito = be4_digito "
cQuery += " and bea_numimp <> be4_numimp "
cQuery += " and bea_tipgui ='03' "
cQuery += " and bea_anoaut >='2019' "
cQuery += " and bea_cancel ='0' "
cQuery += " order by bea_nomusr "

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
±±ºPrograma  Alterar  º Autor : Vanessa Ap.Cruz     ºData :  01/11/2019   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao : Alterando o numero impresso da guia tabelas be4010 e bd7010 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso       : AP6 IDE                                                    º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
/*/                                                  

Static Function Alterar() 

Local Update:= " "  

while TMP->(!Eof())
  Begin Transaction  

       Update := " update "  + RetSqlName('BE4') + " "
       Update += " set be4_numimp='X"+AllTrim(TMP->guia)+"' " //bea_numimp 
       Update += " where " +RetSqlName('BE4')+".d_e_l_e_t_ =' ' and be4_filial=' ' "
       Update += " and be4_numimp = '"+AllTrim(TMP->impresso)+"' "  //be4_numimp
       Update += " and be4_codldp = '"+TMP->loc+"' " //bea_codldp
       Update += " and be4_codpeg = '"+TMP->peg+"' " //bea_codpeg
       Update += " and be4_numero = '"+TMP->numero+"' " //bea_numgui
       TCSQLEXEC(Update) 
  End Transaction   

  Begin Transaction  
       Update := " update "  + RetSqlName('BD7') + " "
	   Update += " set bd7_numimp='X"+AllTrim(TMP->guia)+"' " //bea_numimp 
	   Update += " where " +RetSqlName('BD7')+".d_e_l_e_t_ =' ' and bd7_filial=' ' "
	   Update += " and bd7_numimp = '"+AllTrim(TMP->impresso)+"' "  //be4_numimp
	   Update += " and bd7_codldp = '"+TMP->loc+"' " //bea_codldp
	   Update += " and bd7_codpeg = '"+TMP->peg+"' " //bea_codpeg
	   Update += " and bd7_numero = '"+TMP->numero+"' " //bea_numgui

      TCSQLEXEC(Update)      
  End Transaction
  TMP->(DbSkip())   
enddo     

Return()     

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