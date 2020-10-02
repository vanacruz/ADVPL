#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
±±ºPrograma  : AJUSCART  - Autor  :Vanessa  - Data :  01/07/2019     º±±
±±ºDesc:     Realizar correçaõ do cartao do cliente no  numero impresso     da solicitacao armazem     º±±
*/  


User Function AJUSCART()  //Funcao de usuário maximo 8 caracteres

Local cQuery    :=""
Local cArmazem  :=""
Local nImpresso :="" 
Local cMatric   :=""   
Local cNMatri   :=""  //nova matricula informada  
Private cFiguia :=""


Private cPerg:= "AJUSCART" 

CriaSX1(cPerg)   // Nesta forma de criar a pergunta ela guarda o ultimo resultado digitado no parametro     

if !Pergunte(cPerg,.T.,"Ajustando carteirinha de guia em Solicitação Armazem",.T.)
       return()
endif  

if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03) //Se ambos os campos estiverm vazios
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif   

if Empty(mv_par01) //Solicitaçao Armazem estiver vazia 
	Alert("Informe o Numero de solicitação Armazem.")
    Return
endif
if Empty(mv_par02) // Se o impresso estiver vazio  
	Alert("Informe o impresso da solicitacao ")
 Return
endif

if Empty(mv_par03) // Se o Cartão   estiver vazio  
	Alert("Informe a Matricula correta, este campo não pode ficar vazio")
 Return
endif

cArmazem := AllTrim(mv_par01) 
nImpresso:= AllTrim(mv_par02) 
cNMatri  := AllTrim(mv_par03) //nova matricula  

cQuery :=" select cp_filial,cp_num,cp_u_nugui,cp_u_nucli,cp_ynugui "
cQuery +=" from " +  RetSqlName('SCP') + " " 
cQuery +=" where " + RetSqlName('SCP') +".d_e_l_e_t_=' ' "
cQuery +=" and cp_num = '"+AllTrim(cArmazem)+"' "
cQuery +=" and cp_u_nugui = '"+AllTrim(nImpresso)+"' "

Plsquery(cQuery,"TMPSCP") // Rebece o resultado da query acima  scp010

If  TMPSCP->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar a solicitação armazem : " +cArmazem + " e também o impresso : " +nimpresso  )
    TMPSCP->( DbCloseArea() ) //Fecha tabela TMPSCP que contem o select 
    Return 
EndIf  

If TMPSCP->cp_ynugui<>' '
     Alert (" Esta guia já sofreu vinculo, este ajuste não é possível. Desfaça o Vinculo antes de fazer este ajuste.")
	TMPSCP->( DbCloseArea() ) //Fecha tabela TMPSCP que contem o select 
    Return
EndIf
	
cMatric:=TMPSCP->cp_u_nucli //matricula que já esta na  solicitação Armazem 	
cFiguia:=Alltrim(TMPSCP->cp_filial)	
	
TMPSCP->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta   

/*  Após alinhamento com a equipe nao fazer mais esta verificação. 

cQuery :=" select bea_opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito as cliente, bea_numimp as impresso "
cQuery +=" from " +  RetSqlName('BEA') + " " 
cQuery +=" where " + RetSqlName('BEA') +".d_e_l_e_t_=' ' "
cQuery +=" and bea_filial = '  ' "
cQuery +=" and bea_cancel <>'1' 
cQuery +=" and bea_numimp ='"+AllTrim(nImpresso)+"' "


Plsquery(cQuery,"TMPBEA") // Rebece o resultado da query acima  BEA010

If  TMPBEA->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    Alert("Verificar o impresso: ele não foi encontrado na autorizacao,ou esta cancelado : " +nimpresso  )
    TMPBEA->( DbCloseArea() ) //Fecha tabela TMPBEA que contem o select 
    Return 
EndIf  

if cNMatri <> TMPBEA->cliente   // A Nova matricula informada é diferente da Matricula da Autorizacao da guia.
    Alert("A Nova Matricula informada: " +cNMatri +" é diferente da matricula da autorizacao do impresso: " +TMPBEA->cliente+ ",favor verificar" )
	TMPBEA->( DbCloseArea() ) //Fecha tabela TMPBEA que contem o select 
    Return
EndIf         

TMPBEA->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

*/

FWMsgRun( , {|| AJUSCART(cArmazem,nImpresso,cNMatri,cMatric)} , 'Processando' , 'Aguarde. Atualizando carterinha da solicitacao Armazem...')  //chamando a função que realmente vai fazer o update 

//Alert("concluido")

cMensagem := "Atualização completa! " + chr(13)+chr(10) 
cMensagem += "Solicitação Armazem : " + cArmazem  + chr(13)+chr(10)
cMensagem += "Impresso: " + nImpresso + chr(13)+chr(10)
cMensagem += "Estava na guia o cartão: " + cMatric + chr(13)+chr(10)
cMensagem += "Cartão Atual: " + cNMatri + chr(13)+chr(10)
MSGINFO(cMensagem)

Return     

/*
±±ºPrograma  AJUSCART  - Autor  Vanessa Cruz    -      Data   02/10/18  º±±
±±ºDesc.      Update propriamente dito da scp010                         º±±
*/

Static Function AJUSCART(cArmazem,nImpresso,cNMatri,cMatric) //Função Static  será vista somente aqui.

Local Update := ""  


 Begin Transaction  
 	
     //update -Tabela scp010
     
    Update := "update " + RetSqlName('SCP') + " " 
    Update += "set cp_u_nucli = '"+AllTrim(cNMatri)+"'  "
    Update += "where  " + RetSqlName('SCP') +".d_e_l_e_t_=' ' and cp_filial ='"+cFiguia+"'  "
    Update += "and cp_num ='"+cArmazem+"' "
    Update += "and cp_u_nugui='"+nImpresso+"' "
    Update += "and cp_u_nucli='"+AllTrim(cMatric)+"'  "
    Update += "and cp_ynugui=' ' "
    
    TCSQLEXEC(Update) 

 End Transaction 


Return//fechando função do update

/*
±±ºPrograma  CriaSX1   Autor  Vanessa  º±±

±±ºDesc. Criando os parametros necessários para a rotina.    º±±
±±º            Serão informados pelo usuário.  º±±
*/ 
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Solicitaçao Armazem: "),"","", "mv_ch1","C",06,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Impresso:"),"","", "mv_ch2","C",11,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Matricula Correta:"),"","", "mv_ch3","C",17,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})

return 