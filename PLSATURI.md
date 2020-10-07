#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    PLSATURI  ºAutor   Vanessa        º Data    22/10/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.  Atualização em Regime de Internacao de acordo  com as regras de negócio estabelecidas.   º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function PLSATURI()  //Funcao de usuário maximo 8 caracteres

Local cQuery   := ""
Local cAno  := ""
Local cMes1  := ""
Local cMes2  := ""

Private cPerg:= "PLSATURI" 

if !Pergunte(cPerg,.T.,"Atualizando Regime de Internacao",.T.)
       return()
endif  

// Validação nenhum campo pode estar em branco

if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03) //Se ambos os campos estiverm vazios
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif   

if Empty(mv_par01) //Se o ano de pagamento estiver vazio 
	Alert("Informe o Ano de Pagamento para Atualizacao.")
    Return
endif

if Empty(mv_par02) // Se o mes de Inicio estiver vazio 
	Alert("Informe o Mes de Inicio para a Atualização.")
 Return
endif

if Empty(mv_par03) // Se o mes de final estiver vazio 
	Alert("Informe o Mes final para a Atualização.")
 Return
endif

//Atribuindo a variaveis os parametros informados 

cAno  := Alltrim(mv_par01)
cMes1 := Alltrim(mv_par02)
cMes2 := Alltrim(mv_par03)


//chamando função que executa os updates 
FWMsgRun( , {|| PLSATURI(cAno,cMes1,cMes2)} , 'Processando' , 'Aguarde. Atualizando Registros...')   

//Alert("concluido")

cMensagem := "Atualização dos Regimes de Internação Finalizada ! " + chr(13)+chr(10) 

MSGINFO(cMensagem)


//Limpando a tela quando abrir novamente a rotina   

   dbSelectArea("SX1")
   dbSetOrder(1)
  If dbSeek(Padr(cPerg, 10) + "01")
	RecLock("SX1", .F.)
	SX1->X1_CNT01 := " "
	SX1->(MsUnLock())
  Endif

Return 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    PLSATURI ºAutor   Microsiga           º Data    22/10/2018 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Criado seguindo Scrip do Senhor Eduardo Viana              º±±
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso         AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±º                                                                       º±±
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/                    

Static Function PLSATURI(cAno,cMes1,cMes2) 
Local Update := ""  
procregua(000) 


Begin Transaction 

//UPDATE 01  ---Atualizar Regime Internação - BE4_REGINT = Hospitalar 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set  be4_regint = '1' "
Update += "where  be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and  be4_situac = '1' " 
Update += "and  be4_fase   > '2' "
Update += "and  be4_regint <> '1' "
Update += "and  be4_anopag ='"+cAno+"' " 
Update += "and  be4_mespag >='"+cMes1+"' " 
Update += "and  be4_mespag <='"+cMes2+"' " 

    TCSQLEXEC(Update)   

/*    	if TCSQLEXEC(Update) >= 0
			Alert("Atualizar Regime Internação - BE4_REGINT = Hospitalar  com sucesso Update 1")
		else
			Alert("Atualizar Regime Internação - BE4_REGINT = Hospitalar Falha Update 1")
        endif  */
    
    
End Transaction   

Begin Transaction 

//UPDATE 02 ---Atualizar Regime Internação - BE4_REGINT = Hospital Dia 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set  be4_regint = '2' " 
Update += "where be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and   be4_situac = '1' "
Update += "and   be4_fase   > '2' "
Update += "and   be4_regint = '1' "
Update += "and   be4_anopag = '"+cAno+"' " 
Update += "and   be4_mespag >='"+cMes1+"' " 
Update += "and   be4_mespag <='"+cMes2+"' " 
Update += "and   exists "
Update += "(select * " 
Update += "from " +  RetSqlName('BD7') + " BD7 " 
Update += "where  BD7.d_e_l_e_t_=' ' "
Update += "and   bd7_codldp = be4_codldp "
Update += "and   bd7_codpeg = be4_codpeg "
Update += "and   bd7_numero = be4_numero "
Update += "and   bd7_anopag = be4_anopag "
Update += "and   bd7_mespag = be4_mespag "
Update += "and   bd7_situac = be4_situac "
Update += "and   bd7_blopag <> '1' "
Update += "and   bd7_vlrglo = 0 "
Update += "and   bd7_codpad = '01' "
Update += "and   bd7_codpro in ('60000775','60000783')) "

    TCSQLEXEC(Update)

   /* 	if TCSQLEXEC(Update) >= 0
			Alert("Atualizar Regime Internação - BE4_REGINT = Hospital Dia com sucesso Update 2")
		else
			Alert("Atualizar Regime Internação - BE4_REGINT = Hospital Dia com Falha Update 2")
        endif  */
    
    
    
End Transaction    


Begin Transaction 

//UPDATE 03 --Atualizar Regime Internação - BE4_REGINT = Domiciliar 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set    be4_regint = '3' "
Update += "where be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and   be4_situac = '1' "
Update += "and   be4_fase   > '2' "
Update += "and   be4_regint = '1' "
Update += "and   be4_codrda = '220090' "
Update += "and   be4_anopag ='"+cAno+"' " 
Update += "and   be4_mespag >='"+cMes1+"' " 
Update += "and   be4_mespag <='"+cMes2+"' " 

    TCSQLEXEC(Update)   

   

      /* if TCSQLEXEC(Update) >= 0
    		Alert("Atualizar Regime Internação - BE4_REGINT = Domiciliar com sucesso Update 3")
    	 else
    		Alert("Atualizar Regime Internação - BE4_REGINT = Domiciliar com falha Update 3")
         endif */


​    
End Transaction   
​     

Begin Transaction 

//UPDATE 04--Atualizar Tipo Internação - BE4_GRPINT = 1-Clínica 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set    be4_grpint = '1' "
Update += "where be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and   be4_situac = '1' "
Update += "and   be4_fase   > '2' "
Update += "and   be4_anopag ='"+cAno+"' " 
Update += "and   be4_mespag >='"+cMes1+"' " 
Update += "and   be4_mespag <='"+cMes2+"' " 

    TCSQLEXEC(Update)    
       
     	/*if TCSQLEXEC(Update) >= 0
    		Alert("Atualizar Tipo Internação - BE4_GRPINT = 1-Clínica com sucesso Update 4")
    	else
    		Alert("Atualizar Tipo Internação - BE4_GRPINT = 1-Clínica com falha Update 4")
         endif  */


​    
End Transaction   

Begin Transaction 

//UPDATE 05--- Atualizar Tipo Internação - BE4_GRPINT = 5-Psiquiátrica 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set    be4_grpint = '5' "
Update += "where be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and   be4_grpint = '1' "
Update += "and   be4_situac = '1' "
Update += "and   be4_fase   > '2' "
Update += "and   be4_anopag ='"+cAno+"' " 
Update += "and   be4_mespag >='"+cMes1+"' "
Update += "and   be4_mespag <='"+cMes2+"' " 
Update += "and exists "
Update += "(select * "
Update += "from " +  RetSqlName('BD7') + " BD7 " 
Update += "where bd7_filial = ' ' and BD7.d_e_l_e_t_=' ' "
Update += "and  bd7_codldp = be4_codldp "
Update += "and  bd7_codpeg = be4_codpeg "
Update += "and  bd7_numero = be4_numero "
Update += "and  bd7_anopag = be4_anopag "
Update += "and  bd7_mespag = be4_mespag "
Update += "and  bd7_situac = be4_situac "
Update += "and  bd7_fase   = be4_fase " 
Update += "and bd7_codpro in ('60000538','60000570','60000600','60000678','60000791','60001119', "
Update +=                    "'60001151','60001186','60034092', '80011071','80011080','80013040', '80013058','80014011','80014020')) "

    TCSQLEXEC(Update)   
    
     /*	if TCSQLEXEC(Update) >= 0
    		Alert("Atualizar Tipo Internação - BE4_GRPINT = 5-Psiquiátrica com sucesso Update 5")
    	else
    		Alert("Atualizar Tipo Internação - BE4_GRPINT = 5-Psiquiátrica com falha Update 5")
       endif */

End Transaction   

Begin Transaction 

//UPDATE 06 --Atualizar Tipo Internação - BE4_GRPINT = 3-Obstétrica 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set  be4_grpint = '3' "
Update += "where be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and  be4_grpint = '1' "
Update += "and  be4_situac = '1' "
Update += "and  be4_fase   > '2' "
Update += "and  be4_anopag ='"+cAno+"' " 
Update += "and  be4_mespag >='"+cMes1+"' "
Update += "and  be4_mespag <='"+cMes2+"' "
Update += "and exists "
Update += "(select * "
Update += "from " + RetSqlName('BD7') + " BD7, " +  RetSqlName('BR8') + " BR8 " 
Update += "where bd7_filial = ' ' "
Update += "and  br8_filial = ' '  "
Update += "and   BD7.d_e_l_e_t_=' ' "
Update += "and   BR8.d_e_l_e_t_=' ' "
Update += "and  bd7_codldp = be4_codldp "
Update += "and  bd7_codpeg = be4_codpeg "
Update += "and  bd7_numero = be4_numero "
Update += "and  bd7_anopag = be4_anopag "
Update += "and  bd7_mespag = be4_mespag "
Update += "and  bd7_situac = be4_situac "
Update += "and  bd7_fase   = be4_fase "
Update += "and  bd7_codpad = br8_codpad "
Update += "and  bd7_codpro = br8_codpsa "
Update += "and  br8_clasp2 = 'E13' "
//Update += "and  br8_clasp2 in ('E131','E132')) " Foi retirado em Dezembro 26/12/2018 

    TCSQLEXEC(Update)    

   /*	if TCSQLEXEC(Update) >= 0
			Alert("Atualizar Tipo Internação - BE4_GRPINT = 3-Obstétrica com sucesso Update 6")
		else
			Alert("Atualizar Tipo Internação - BE4_GRPINT = 3-Obstétrica com falha Update 6")
     endif */

End Transaction    


Begin Transaction 

//UPDATE 07 --Atualizar Tipo Internação - BE4_GRPINT = 2-Cirúrgica 

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set be4_grpint = '2' "
Update += "where be4_filial = ' '
Update += "and  BE4.d_e_l_e_t_ = ' '
Update += "and  be4_grpint = '1' "
Update += "and  be4_situac = '1' "
Update += "and  be4_fase   > '2' "
Update += "and  be4_anopag ='"+cAno+"' " 
Update += "and  be4_mespag >='"+cMes1+"' "
Update += "and  be4_mespag <='"+cMes2+"' "
Update += "and  exists "
Update += "(select * "
Update += "from " + RetSqlName('BD7') + " BD7, " +  RetSqlName('BR8') + " BR8 " 
Update += "where bd7_filial = ' ' "
Update += "and  br8_filial = ' ' "
Update += "and  BD7.d_e_l_e_t_=' ' "
Update += "and  BR8.d_e_l_e_t_=' ' "
Update += "and   bd7_codldp = be4_codldp " 
Update += "and   bd7_codpeg = be4_codpeg " 
Update += "and   bd7_numero = be4_numero " 
Update += "and   bd7_anopag = be4_anopag " 
Update += "and   bd7_mespag = be4_mespag " 
Update += "and   bd7_situac = be4_situac " 
Update += "and   bd7_fase   = be4_fase " 
Update += "and   bd7_codpad = br8_codpad " 
Update += "and   bd7_codpro = br8_codpsa " 
Update += "and   br8_tipeve in ('2','3')) " 

    TCSQLEXEC(Update)  

   /*	if TCSQLEXEC(Update) >= 0
			Alert("Atualizar Tipo Internação - BE4_GRPINT = 2-Cirúrgica com sucesso Update 7")
		else
			Alert("Atualizar Tipo Internação - BE4_GRPINT = 2-Cirúrgica com falha Updae 7")
     endif  */ 
     
End Transaction 
                      
Begin Transaction 

//UPDATE 08--Atualizar Tipo Internação - BE4_GRPINT = 4-Pediátrica – 370 Reg

Update := "update " + RetSqlName('BE4') + " BE4 " 
Update += "set  be4_grpint = '4' "
Update += "where be4_filial = ' ' and BE4.d_e_l_e_t_=' ' "
Update += "and  be4_datnas <> ' ' " 
Update += "and  be4_datpro <> ' ' "
Update += "and  be4_grpint = '1' "
Update += "and  be4_situac = '1' "
Update += "and  be4_fase   > '2' "
Update += "and  be4_anopag ='"+cAno+"' " 
Update += "and  be4_mespag >='"+cMes1+"' "
Update += "and  be4_mespag <='"+cMes2+"' "
Update += " and (To_Date(be4_datpro,'yyyymmdd') - To_Date(be4_datnas,'yyyymmdd'))/365 <= 18 "
         
    TCSQLEXEC(Update)  
    
     /*	if TCSQLEXEC(Update) >= 0
    		Alert("Atualizar Tipo Internação - BE4_GRPINT com sucesso Ultimo Update")
    	else
    		Alert("Atualizar Tipo Internação - BE4_GRPINT com falha Ultimo Update")
       endif */


End Transaction 

return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    22/10/2018      º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.   Criando os parametros necessários para a rotina.          º±±
±±º             Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)

PutSx1(cPerg,"01",OemToAnsi("Ano Pagamento ") ,"","", "mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Mês De ") ,"","", "mv_ch2","C",02,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Mês Ate ") ,"","", "mv_ch3","C",02,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
return 