#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   PLCobniv  ºAutor  Vanessa        º Data   30/03/20       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Permitir alterar o campo ba3_cobniv de um cliente          º±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function PLCOBNIV()  //Funcao de usuário maximo 8 caracteres

Local cQuery     :=" "                                  
Local cOperadora :=" "
Local cEmpresa   :=" "
Local cMatric    :=" " 
Local NOpcao     :=" "
Local ctipo      :=" "
Private cPerg:= "PLCOBNIV"   

CriaSX1()  
             
if !Pergunte(cPerg,.T.,"Alterando campo ba3_cobniv de cliente ",.T.)
       return()
endif  

if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03) //Se ambos os campos estiverm vazios
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif   

if Empty(mv_par01) // Se a operadora estiver vazia  
	Alert("Informe a Operadora")
 Return
endif

if Empty(mv_par02) // Se a empresa estiver vazia 
	Alert("Informe a empresa.")
 Return
endif

if Empty(mv_par03) // Se a matricula estiver vazia
	Alert("Informe a matricula.")
    Return
endif  

cOperadora:= AllTrim(mv_par01)
cEmpresa  := AllTrim(mv_par02)
cMatric   := AllTrim(mv_par03)
NOpcao    := mv_par04  

cQuery:= " select ba3_cobniv,ba3_codint,ba3_codemp,ba3_matric,r_e_c_n_o_ "
cQuery +=" from " +  RetSqlName('BA3') + " " 
cQuery +=" where " + RetSqlName('BA3') + ".d_e_l_e_t_=' ' "
cQuery +=" and ba3_filial = ' ' "
cQuery +=" and ba3_codint= '"+cOperadora+"' "
cQuery +=" and ba3_codemp= '"+cEmpresa+"' "
cQuery +=" and ba3_matric= '"+cMatric+"' "

Plsquery(cQuery,"TMPBA3") // Rebece o resultado da query acima   

If  TMPBA3->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Por favor verifique os dados digitados pois não encontramos família com estes dados. " ) 
    TMPBA3->( DbCloseArea() ) //Fecha tabela TMPBA3 que contem o select 
    Return 
EndIf  

TMPBA3->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

if (NOpcao==1) //Opção 1 no combo 
      ctipo:='SIM'
else   
   if(NOpcao==2) //Opção 2  no combo 
      ctipo:='NAO'        
   Endif    
Endif 

cMensagem := "Esta rotina irá alterar o campo ba3_cobniv:  " + chr(13)+chr(10) 
cMensagem += "Ira ser alterado para :"+ctipo+ chr(13)+chr(10) 
cMensagem += "Na matricula : " + cOperadora+"."+" "+cEmpresa+"."+" "+cMatric + chr(13)+chr(10) 
MSGINFO(cMensagem)

FWMsgRun( , {||Alterar(cOperadora,cEmpresa,cMatric,NOpcao)} , 'Processando' , 'Aguarde. Alterando...')  //chamando a função que realmente vai fazer o update 

//Alert("Concluido")

cMensagem := "Dado alterado! " + chr(13)+chr(10) 

MSGINFO(cMensagem) 
Limpar() 
                   
Return     

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  LIMPCAMP  ºAutor  Vanessa Cruz          º Data   05/07/19  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Update propriamente dito.                                  º±±
±±º                                                                      º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso        AP                                                         º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/


Static Function Alterar(cOperadora,cEmpresa,cMatric,NOpcao) //Função Static  será vista somente aqui.

Local Update := "" 
Local cvalor :=" "
     
If NOpcao==1 // Se o combo for = 1 valor SIM  
    cvalor:='1'
Else                                             
    cvalor:='0'
End     

//ba3_cobniv = 1=Sim;0=Nao  

 Begin Transaction  
 	
     //update - Tabela ba3010 
     
    Update := " update " + RetSqlName('BA3') + " " 
    Update += " set ba3_cobniv = '"+cvalor+"' "
    Update += " where  " + RetSqlName('BA3') +".d_e_l_e_t_=' ' and ba3_filial = ' '  "
    Update += " and ba3_codint= '"+cOperadora+"' "
    Update += " and ba3_codemp= '"+cEmpresa+"' "
    Update += " and ba3_matric= '"+cMatric+"' "
    TCSQLEXEC(Update) 

 End Transaction 


Return //fechando função   


Static Function Limpar()  //Limpar pergunta no final  do processo. 

dbSelectArea( "SX1" ) //Posiciona na Sx1
dbSetOrder(1) //Dentro da SX1 na Ordem 1 (x1_Grupo+x1_Ordem)
IF dbSeek('PLCOBNIV  01') //x1_Grupo + x1_Ordem //O comando dbSeek faz uma busca no banco de dados através de um índice existente.
	while alltrim(SX1->X1_GRUPO) == 'PLCOBNIV' 
		RecLock("SX1",.F.) 
			SX1->X1_CNT01 := ' ' //Limpa o parametro 
		MsUnlock()
		SX1->(dbskip()) //Vai para o  proximo registro
	enddo
EndIF     
Return() 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  CriaSX1   ºAutor  Vanessa          º Data   05/10/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Criando os parametros necessários para a rotina.          º±±
±±º            Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Informe Operadora: ") ,"","","mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Infome Empresa: ")    ,"","","mv_ch2","C",04,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Informe Matricula: ") ,"","","mv_ch3","C",06,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"04",OemToAnsi("O novo Cobniv será:" ),"","","mv_ch4","N",03,0,0, "C","","","","","mv_par04","SIM","","","","NAO","","","","","","","","","","","",{},{},{}) 
return 