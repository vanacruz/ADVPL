#include "protheus.ch" 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSLIMPD  ºAutor  ³Vanessa Cruz         º Data ³  15/05/17   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.³   Limpar campos de data de atendimento para exclusão de  º±±
±±º      de internação cliente local - campos : BEA_DATPRO,BEA_HORPRO,    º±±
±±         BE4_DATPRO,BE4_HORPRO     º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User Function PLSLIMPD()  //Funcao de usuário maximo 8 caracteres
Local cQuery    := ""
Local cImpresso := ""  
Local cCartao   := ""
Private cPerg   := "PLSLIMPD" 

CriaSX1()
if !Pergunte(cPerg,.T.,"Limpando campos data e hora ")
       return()
endif    


if Empty(mv_par01) .And. Empty(mv_par02) //Se ambos os campos estiverm vazios
    Alert("Informe impresso e codigo cliente.")
    Return
endif 
      
if Empty(mv_par01) //Se o impresso estiver vazio 
	Alert("Informe o impresso corretamente.")
    Return
endif
if Empty(mv_par02) // Se o codigo do clinte estiver vazio
	Alert("Informe codigo do cliente.")
    Return
endif

cImpresso := AllTrim(mv_par01) 
cCartao   := AllTrim(mv_par02)  

//Verificando  os campos digitados pelo usuário: impresso + cartaõ cliente

cQuery := "select * from siga.bea010 "
cQuery += "where d_e_l_e_t_= ' ' " 
cQuery += "and bea_numimp  = '"+cImpresso+"' "
cQuery += "and bea_Opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito ='"+cCartao+"' "
Plsquery(cQuery,"TMPBEA") // Rebece o resultado da query acima 


If  TMPBEA->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não tenho dados para atualizar
    Alert("Verificar impresso: " +cImpresso+ " Verificar cartao: "+cCartao+ " Dados nao encontrados") 
    TMPBEA->( DbCloseArea() ) //Fecha tabela TMPBEA que contem o select 
    Return 
EndIf  

TMPBEA->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| PLSLIMPD1(cImpresso,cCartao)} , 'Processando' , 'Aguarde. Atualizando Impresso...')  //chamando a função que realmente vai fazer o update  

//Alert("concluido")
MSGINFO("Alteração em data de Atendimento Finalizado...", "Verifique a guia!")
Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSLIMPD1  ºAutor  ³Vanessa Cruz         º ³Data   15/05/17  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Update          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function PLSLIMPD1(cImpresso,cCartao) //Função Status estar será vista somente aqui.
Local Update := ""    
procregua(000) 

 Begin Transaction  
 	
     //update 01 -Tabela bea010
     
    Update := "update bea010 "
    Update += "set bea_datpro = '        ',bea_horpro='  ' "
    Update += "where d_e_l_e_t_ = ' ' "
    Update += "and bea_codemp <> '0001' "
    Update += "and bea_numimp = '" +cImpresso+ "' "
    Update += "and bea_opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito= '" +cCartao+ "' "
    TCSQLEXEC(Update) 
 End Transaction 

 Begin Transaction     
	//update 02 - Tabela be4010
	
    Update := "update be4010 "
    Update += "set be4_datpro='        ', be4_horpro='  '  "
    Update += "where d_e_l_e_t_ = ' ' "
    Update += "and be4_codemp <> '0001' "
    Update += "and be4_numimp = '" +cImpresso+ "' "
    Update += "and be4_opeusr||be4_codemp||be4_matric||be4_tipreg||be4_digito= '" +cCartao+ "' " 
    TCSQLEXEC(Update) 
 End Transaction   
 Return 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³CriaSX1   ºAutor  Vanessa          º Data ³  15/05/17       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³  Criando os parametros necessários para a rotina.          º±±
±±º          ³  Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 


Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


    //(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Numero Impresso ?")        ,"","", "mv_ch1","C",12,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Cartao cliente  ?")        ,"","", "mv_ch2","C",17,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})

return 