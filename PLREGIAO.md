#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLREGIAO  ºAutor  Vanessa        º Data   09/03/2020      º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     Buscar a regiao que o cliente tem direito                   º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

User Function PLREGIAO()  //Funcao de usuário maximo 8 caracteres  

Local ccartão:=" "
Local cMensg:=" "

Private cPerg:= "PLREGIAO" //Aqui é case sensitive 
              
 if !Pergunte(cPerg,.T.)
    return()
 endif  

if Empty(mv_par01) // Se o codigo do cliente estiver vazio
 Alert("Informe o código do cartão do cliente: exemplo:0021XXXXXXXXXX")
 Return
endif          

cMensg:= "A função desta rotina é:" + chr(13)+chr(10) 
cMensg+= "Apresentar a área de atuação do Produto do cliente,"+ chr(13)+chr(10)
cMensg+= "de acordo com o que consta cadastrado no Protheus," + chr(13)+chr(10)
cMensg+= "Qualquer divergência de informação, primeiro deverá ser analisado pelo setor de Cadastro." + chr(13)+chr(10) 
cMensg+= "Se necessário, eles acionaram a equipe de T.I." + chr(13)+chr(10)

 Msginfo(cMensg) 
 ccartao:=Alltrim(mv_par01)

//Chamando a função para buscar a Regiao que o cliente tem direito.

FWMsgRun( , {||Buscar(ccartao)} , 'Processando' , 'Aguarde. Buscando Dados...')  

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  Vanessa Cruz          º Data   09/03/20          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Função para Buscar a Região que o cliente tem direito      º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

//Função para Buscar a região.

Static Function Buscar(ccartao)

Local cCODINT  :=" "
local cCODEMP  :=" "
local cMATRIC  :=" "
Local cRegiao  :=" "
Local cQuery   :=" "
Local cMensagem:=" "  
Local aRegioes := {}  //acho que vou precisar   

Local cSubcon  := "" 
Local cCodpla  := ""

cCODINT:=substr(ccartao,1,4)
cCODEMP:=substr(ccartao,5,4)
cMATRIC:=substr(ccartao,9,6)  

//Primeiro pega o plano da pessoa.
cCodpla  := AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_CODPLA") )  
                      
cMensagem:= "cartão: "+cCODINT+'.'+cCODEMP+'.'+cMATRIC+ chr(13)+chr(10)+ chr(13)+chr(10) 

//pergunta se é nacional, se for nem pesquisa mais nada. 

If AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_ABRANG") ) =='1'   //vc ja guardou as variaveis  
	cMensagem += "Produto"+cCodpla+" com " 
   	cMensagem += "Abrangencia Nacional, sem região do cartão, cadastrada no Protheus."  + chr(13)+chr(10) 
   // 	cMensagem += "Campo: Região Cartão(BA3_YREGIA)."  + chr(13)+chr(10) 
   	Msginfo(cMensagem)
   	Limpar() 
	return()
EndIf

//não temos 2 ou 5, entao não entra aqui  

If AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_ABRANG") ) =='2'    //vc ja guardou as variaveis  
	cMensagem += "Produto"+cCodpla+" com " 
    cMensagem += "Abrangencia Grupo de Estados, Unimed Uberaba não possui esta Abrangência."  + chr(13)+chr(10) 
    Msginfo(cMensagem)
    Limpar() 
 	return()
Endif  

If AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_ABRANG") ) =='3'    //vc ja guardou as variaveis  
	cMensagem += "Produto"+cCodpla+" com " 
    cMensagem += "Abrangencia Estadual."  + chr(13)+chr(10) 
    Msginfo(cMensagem)
    Limpar() 
 	return()
Endif 

If AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_ABRANG") ) =='5'    //vc ja guardou as variaveis  
	cMensagem += "Produto"+cCodpla+" com " 
    cMensagem += "Abrangencia municipal, Unimed Uberaba não possui esta Abrangência."  + chr(13)+chr(10)   
    Limpar()  
    Msginfo(cMensagem) 
 	return()
Endif 
///////

//Trantando da Abrangência 4 :  

If AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_ABRANG") ) == '4'   //Se a abrangência for 4  grupo de municipios 
	cMensagem+= "Área de atuação do Produto "+cCodpla+": "  + chr(13)+chr(10) // ai começa a montar as cidades

   	cRegiao := AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_YREGIA") ) //posicione na região. no fonte original nao tava posionando. deveria ter usado as variaveis 
   	
   	If Empty(cRegiao)  // se não encontrar  na familia pequisa no subcontrato 
   	   
   		csubcon := AllTrim(Posicione("BA3",1,xFilial("BA3")+cCODINT+cCODEMP+cMATRIC,"BA3_SUBCON") )  //pega o subcontrato  
   		cRegiao := AllTrim(Posicione("BQC",6,xFilial("BQC")+csubcon,"BQC_YREGIA"))                   //pega a regiao do subcontrato 
   	EndIf
        
	If !Empty(cRegiao) 
		//se entrou aqui ou achou na familia ou achou no subcontrato
 		cSql := "select * from bib010 where d_e_l_e_t_ = ' ' and bib_codreg = '"+cRegiao+"'"   //bib é cadastro de regioes
   		PLSQuery(cSql,"TMPBIB")  

		If !TMPBIB->(Eof())  
			
			cSql := "select * from bic010 where d_e_l_e_t_ = ' ' and bic_codreg = '"+TMPBIB->BIB_CODREG+"' order by r_e_c_n_o_"	
  			PLSQuery(cSql,"TMPBIC")	//cadastro de cidades
     		If !TMPBIC->(Eof()) 
            	while !TMPBIC->(Eof())   
             		If TMPBIC->BIC_YUTILI == '1' .or. TMPBIC->BIC_YUTILI == '3' //pergunta se escreve no cartao
                  		cSql := "select * from bid010 where d_e_l_e_t_ = ' ' and bid_codmun = '"+TMPBIC->BIC_CODMUN+"'"	
                		PLSQuery(cSql,"TMPBID") 
	                	If !TMPBID->(Eof()) 
                  			cMensagem += Capitalace(AllTrim(TMPBID->BID_DESCRI))+ chr(13)+chr(10)  //acumula na mensagem de retorno para o usuario		 
                		EndIf
	                	TMPBID->(DBCLOSEAREA()) 
           			EndIf
             		TMPBIC->(dbSkip())
               	EndDo
   			EndIf                         
     		TMPBIC->(DBCLOSEAREA())
       	EndIf
        TMPBIB->(DBCLOSEAREA()) 
	Else 
	    //falei com colaborador se nao achou na familia e no subcontrato escreve o padrao
	    
		cSql := "select * from bic010, bid010 "
		cSql += "where bic010.d_e_l_e_t_ = ' ' "  
		cSql += "and bid010.d_e_l_e_t_ = ' ' "  
		cSql += "and bic_codreg = '001' " 
		cSql += "and bic_codmun = bid_codmun "
		cSql += "order by bic010.r_e_c_n_o_" 
	 	PLSQuery(cSql,"TMPBIC")	//cadastro de cidade
		If !TMPBIC->(Eof()) 
	 		while !TMPBIC->(Eof()) 
	 			cMensagem += Capitalace(AllTrim(TMPBIC->BID_DESCRI))+ chr(13)+chr(10)  //acumula na mensagem de retorno para o usuario
	           	TMPBIC->(dbSkip())
	       	EndDo
	 	EndIf                         
	  	TMPBIC->(DBCLOSEAREA())
	Endif 
EndIf 

Limpar()  
Msginfo(cMensagem) 
return() 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  Vanessa Cruz          º Data   09/03/20          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Função Limpar a pergunta								      º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function Limpar()  

dbSelectArea( "SX1" ) //Posiciona na Sx1
dbSetOrder(1) //Dentro da SX1 na Ordem 1 (x1_Grupo+x1_Ordem)
IF dbSeek('PLREGIAO  01') //x1_Grupo + x1_Ordem //O comando dbSeek faz uma busca no banco de dados através de um índice existente.
	while alltrim(SX1->X1_GRUPO) == 'PLREGIAO' 
		RecLock("SX1",.F.) 
			SX1->X1_CNT01 := ' ' //Limpa o parametro 
		MsUnlock()
		SX1->(dbskip()) //Vai para o  proximo registro
	enddo
EndIF 

Return    

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
PutSx1(cPerg,"01",OemToAnsi("Carteirinha: "),"","", "mv_ch1","C",14,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 

return()   

/* ----- Busca Abrangencia

Static Function Msg2(Tipo)                                                      
if Tipo=='1'
	return('NACIONAL')
endif 
if Tipo=='2'             
	return('GRUPO DE ESTADOS')
endif 
if Tipo=='3'
	return('ESTADUAL')
endif 
if Tipo=='4'
	return('GRUPO DE MUNICIPIOS')
endif 
if Tipo=='5'
	return('MUNICIPAL')
endif       
return('NAO ENCONTRADO')  */ 