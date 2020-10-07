#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : AJUSIMP  ºAutor  : Vanessa        º Data :  02/10/18         º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.: Realizar correçaõ no numero impresso da solictação armazem  º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±D±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function AJUSIMP()  //Funcao de usuário maximo 8 caracteres

Local cQuery    :=""
Local cArmazem  :=""
Local nImpresso :="" 
Local nNovoimp  :=""  
Local cMatric   :=""   
Local cNMatri   :=""     

Local _aSays	:= {}
Local _aBtn		:= {}
Local _nOpca	:= 0
Local _lExec	:= .T. 
Local lOk  

Private cFiguia :="" 
Private _cHtml	:= ''   
Private _cPerg	:= "AJUSIMP2"      

aAdd( _aSays , 'Rotina para alterar o número impresso de Solicitação Armazém.' )

aAdd( _aBtn , { 05 , .T. , {|  | Pergunte( _cPerg )         	} } )
aAdd( _aBtn , { 01 , .T. , {|_o| _nOpca := 1 , _o:oWnd:End()   	} } )
aAdd( _aBtn , { 02 , .T. , {|_o| _nOpca := 0 , _o:oWnd:End()   	} } )

While _lExec
	
	_nOpca := 0
	
	FormBatch( "Altera Impresso" , _aSays , _aBtn ,, 170 , 500 ) 	
	
	If _nOpca == 1
		_cHtml := ""
		lOk    := .T.
		
		FWMsgRun( , {|| lOk := fValida() } ,, 'Validando parâmetros...' ) 
		
		If !lOk					
			MsgStop( _cHtml , 'Atenção!') 
		Else 
			MsgInfo( _cHtml , 'Verifique')		
		EndIf


​	
​	Else
​	
		_lExec := .F.
		
	EndIf

EndDo   


Return()



Static Function fValida()

if Empty(mv_par01) .or. Empty(mv_par02) //Se ambos os campos estiverm vazios
    _cHtml := "Informe corretamente os campos, nenhum campo deve estar vazio."
    Return(.F.)
endif      
cArmazem := AllTrim(mv_par01) 
nNovoimp := AllTrim(mv_par02) 

cQuery :=" select cp_filial,cp_num,cp_u_nugui,cp_u_nucli,cp_ynugui "
cQuery +=" from " +  RetSqlName('SCP') + " " 
cQuery +=" where " + RetSqlName('SCP') +".d_e_l_e_t_=' ' "
//cQuery +=" and cp_filial = '03' "    //Comentado dia 24/04/2019 porque antes somente a filial 03 utilizava este recurso e agora outras também usam
cQuery +=" and cp_num = '"+AllTrim(cArmazem)+"' "
//cQuery +=" and cp_u_nugui = '"+AllTrim(nImpresso)+"' "

Plsquery(cQuery,"TMPSCP") // Rebece o resultado da query acima  scp010

If  TMPSCP->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    _cHtml := "Solicitação armazém " +cArmazem +" não encontrada." 
    TMPSCP->( DbCloseArea() ) //Fecha tabela TMPSCP que contem o select 
    Return(.F.) 
EndIf  

If TMPSCP->cp_ynugui<>' ' .and. TMPSCP->cp_ynugui<>'7001'
	_cHtml := " Esta guia já sofreu vinculo, este ajuste não é possível. Desfaça o Vinculo antes de fazer este ajuste."
	TMPSCP->( DbCloseArea() ) //Fecha tabela TMPSCP que contem o select 
    Return(.F.)
EndIf 

If AllTrim(TMPSCP->cp_u_nugui) == AllTrim(nNovoimp)
	_cHtml := "Requisição armazém "+cArmazem+" já está cadastrada para o impresso "+nNovoimp+"."
	TMPSCP->( DbCloseArea() ) //Fecha tabela TMPSCP que contem o select 
    Return(.F.)
EndIf
	
cMatric:=TMPSCP->cp_u_nucli	
cFiguia:=Alltrim(TMPSCP->cp_filial)	
	
	
TMPSCP->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta   

cQuery :=" select bea_opeusr||bea_codemp||bea_matric||bea_tipreg||bea_digito as cliente, bea_numimp as impresso "
cQuery +=" from " +  RetSqlName('BEA') + " " 
cQuery +=" where " + RetSqlName('BEA') +".d_e_l_e_t_=' ' "
cQuery +=" and bea_filial = '  ' "
cQuery +=" and bea_cancel <>'1' 
cQuery +=" and bea_numimp ='"+AllTrim(nNovoimp)+"' "


Plsquery(cQuery,"TMPBEA") // Rebece o resultado da query acima  BEA010

If  TMPBEA->(Eof()) //Verificando se a tabela esta vazia (pois se esta vazia não encontrei dados 
    _cHtml := "Verificar o novo impresso: ele não foi encontrado na autorizacao,ou esta cancelado : " +nimpresso  
    TMPBEA->( DbCloseArea() ) //Fecha tabela TMPBEA que contem o select 
    Return(.F.) 
EndIf  
        
cNMatri:=TMPBEA->cliente

if cNMatri <> cMatric
    _cHtml := "A Matricula do cliente do novo impresso: " +cNMatri +" é diferente da matricula do cliente que já esta na solicitação armazem: " +cMatric+ ",favor verificar"
	TMPBEA->( DbCloseArea() ) //Fecha tabela TMPBEA que contem o select 
    Return(.F.)
EndIf         

TMPBEA->( DbCloseArea() )//fechar a tabela apos cria-la pois ela fica aberta 

FWMsgRun( , {|| AJUSIMP(cArmazem,nNovoimp )} , 'Processando' , 'Aguarde. Atualizando numero da guia...')  //chamando a função que realmente vai fazer o update 

//Alert("concluido")

_cHtml := "Atualização completa! " + chr(13)+chr(10) 
_cHtml += "Solicitação Armazem : " + cArmazem  + chr(13)+chr(10)
_cHtml += "Impresso atual : " + nNovoimp + chr(13)+chr(10)   
lOk    := .T.

Return     

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  AJUSIMP  ºAutor  : Vanessa Cruz          º Data   02/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     : Update propriamente dito da scp010                         º±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function AJUSIMP(cArmazem,nNovoimp ) //Função Static  será vista somente aqui.

Local Update := ""  


 Begin Transaction  
 	
     //update -Tabela scp010
     
    Update := "update " + RetSqlName('SCP') + " " 
    Update += "set cp_u_nugui = '"+AllTrim(nNovoimp)+"'  "
    Update += "where  " + RetSqlName('SCP') +".d_e_l_e_t_=' ' and cp_filial ='"+cFiguia+"'  "
    Update += "and cp_num ='"+cArmazem+"' "
    
    TCSQLEXEC(Update) 

 End Transaction 


Return//fechando função do 



/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  : CriaSX1   ºAutor : Vanessa          º Data : 02/10/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. : Criando os parametros necessários para a rotina.          º±±
±±º            Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 
/*Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Solicitaçao Armazem: "),"","", "mv_ch1","C",06,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("impresso Atual:"),"","", "mv_ch2","C",11,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Novo impresso:"),"","", "mv_ch3","C",11,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})

return       */

/*