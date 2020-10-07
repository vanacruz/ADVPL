#include "protheus.ch" 
#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   ATUBC610  ºAutor   Vanessa        º Data    19/03/20        º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.      Realizar ajustes de valores pagamento e glosa de Mat/Med/taxaº±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      

User Function ATUBC610()
               
Local cMensagem :=" "   
Local cTexto    :=""                                 
Private cRdaI:= " " //RDA Inicial
Private cRdaF:= " " //RDA Final
Private cVigI:= " " //Vigencia Inicial
Private cVigF:= " " //Vigencia Final 
Private codpadI:=" "//Codpad Inicial 
Private codpadF:=" "//Codpad Final
Private cCodI:= " " //Procedimento Inicial
Private cCodF:= " " //Procedimento Final 
Private cOpeI:= " " //Operadora Inicial
Private cOpeF:= " " //Operadora  Final  
Private cPlanI:="" //Plano Inicial
Private cPlanF:="" //Plano Final 


Private _cCaminho :=""
Private cPerg:= "ATUBC610"  //Pergunda Principal  é case sensitive 
              
 if !Pergunte(cPerg,.T.)
       Limpar() //Limpa perguntas
       return(.F.)
 endif  

//As validações para que o campo não fique  vazio fiz no configurador. 


cRdaI:= mv_par01
cRdaF:= mv_par02
cVigI:= Dtos(mv_par03) //Dtos26/03/2020 => 20200326 
cVigF:= Dtos(mv_par04) 
codpadI:=mv_par05  //Nao pode ser vazio 
codpadF:=mv_par06  //Não pode ser vazio 
cCodI:= mv_par07   //Não pode ser vazio 
cCodF:= mv_par08   //Não pode ser vazio   
cOpeI:= mv_par09
cOpeF:= mv_par10 
cPlanI:= mv_par11
cPlanF:= mv_par12 

 //Mensagem resumindo para o usuário os  parametros informados : 

//cMensagem := "Estes foram os parâmetos que você escolheu ! " + chr(13)+chr(10) 
cMensagem := "RDA De : " + cRdaI  + chr(13)+chr(10)
cMensagem += "RDA Até: " + cRdaF  + chr(13)+chr(10)   
cMensagem += "Vigência Inicial: " +cVigI+chr(13)+chr(10)  
cMensagem += "Vigência Final: " + cVigF+ chr(13)+chr(10) 
cMensagem += "Codpad De: " + codpadI+ chr(13)+chr(10) 
cMensagem += "Codpad Até :" + codpadF  + chr(13)+chr(10) 
cMensagem += "Procedimento De:" + cCodI+ chr(13)+chr(10) 
cMensagem += "Procedimento Ate:" + cCodF  + chr(13)+chr(10)  


if Empty(cOpeI) .And. Empty(cOpeF) //Se amas as operadoras  estiverem vazias                                     
    cMensagem += "Ambas as operadoras estão vazias,então serão atualizadas todas: " + chr(13)+chr(10)
    Else 
       If !Empty(cOpeI) .And. !Empty(cOpeF)//Preencheu as duas operadoras 
         cMensagem += "Operadora De:" + cOpeI+ chr(13)+chr(10) 
         cMensagem += "Operadora Até :" + cOpeF  + chr(13)+chr(10)
       Else 
          If !Empty(cOpeI) .And. Empty(cOpeF) //preencheu só Operadora Inicial
            cMensagem += "Será atualizado a partir da Operadora :" + cOpeI+ chr(13)+chr(10) 
          Else
             If Empty(cOpeI) .And. !Empty(cOpeF) //preencheu só Operadora Final
                cMensagem += "Será atualizado as Operadores Menores que:" + cOpeF+ chr(13)+chr(10) 
             Endif
          Endif
       Endif
  Endif

If Empty(cPlanI) .And. Empty(cPlanF) //Se ambos os planos  estiverem vazios                                         
    cMensagem += "Ambos os planos estão vazios,então serão atualizados todos: " + chr(13)+chr(10)
Else 
   If !Empty(cPlanI) .And. !Empty(cPlanF)//Preencheu os  dois  planos 
     cMensagem += "Plano De: " + cPlanI+ chr(13)+chr(10) 
     cMensagem += "Plano Até: " + cPlanF  + chr(13)+chr(10)
   Else 
      If !Empty(cPlanI) .And. Empty(cPlanF) //preencheu só o plano Inicial
           cMensagem += "Será atualizado a partir do Plano : " + cPlanI+ chr(13)+chr(10) 
       Else
          If Empty(cPlanI) .And. !Empty(cPlanF) //preencheu só Plano  Final
             cMensagem += "Será atualizado os planos  Menores que: " + cPlanF+ chr(13)+chr(10) 
          Endif
       Endif
   Endif
Endif

cMensagem += chr(13)+chr(10)+"Esta rotina só ira atualizar a vigência Final, "  +chr(13)+chr(10) 
cMensagem += "se vigência Final do item  estiver vazia!!!! " +chr(13)+chr(10) 

/*MSGINFO(cMensagem)

if !Pergunte("ATUBD6",.T.) //Confirma dados para alteração ? 
   Limpar()
   return()     
Else               
    NOpcao :=mv_par01
       If NOpcao==1 // Confirmou  
        //Função para buscar os dados na tabela bc6010  
       FWMsgRun( , {|| Buscar(cRdaI,cRdaF,cVigI,cVigF,codpadI,codpadF,cCodI,cCodF,cOpeI,cOpeF,cPlanI,cPlanF)} , 'Processando' , 'Aguarde. Atualizando itens...')      
       Else 
         Limpar() //Limpa perguntas
         U_ATUBC610()//chama novamente a rotina  
       Endif
Endif */ 

cTexto:="Confirma alteração para os parametros abaixo?" +chr(13)+chr(10) +chr(13)+chr(10) + cMensagem

If MsgYesNo(cTexto,"Atualização Vigência Final")  
	//pergunta se sim ou nao, se SIM faz o bloco de codigo
	FWMsgRun( , {|| Buscar(cRdaI,cRdaF,cVigI,cVigF,codpadI,codpadF,cCodI,cCodF,cOpeI,cOpeF,cPlanI,cPlanF)} , 'Processando' , 'Aguarde. Atualizando itens...') 	
Else //se marca NAO, nao faz nada ou outro bloco de codigo depende do que o fonte se propoe a fazer
	Limpar() //Limpa perguntas
	U_ATUBC610()//chama novamente a rotina  
EndIf



/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  Buscar  ºAutor   Vanessa Cruz          º  Data   25/03/21    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.       Buscando Dados na tabela Bc6010                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function Buscar (cRdaI,cRdaF,cVigI,cVigF,codpadI,codpadF,cCodI,cCodF,cOpeI,cOpeF,cPlanI,cPlanF) 

Local cQuery := " " 
Local cFiltro:= " " 

//Será que preencheu operadora ?

If !Empty(cOpeI).And.!Empty(cOpeF)  //preencheu operadora inicial e Final
     cFiltro +=" and bc6_codope >='"+cOpeI+"'  "
     cFiltro +=" and bc6_codope <='"+cOpeF+"'  "
Else
    If !Empty(cOpeI) .And. Empty(cOpeF) //preencheu só Operadora inicial.
       cFiltro +=" and bc6_codope >='"+cOpeI+"'  "
    Else
       If Empty(cOpeI) .And.!Empty(cOpeF) //preencheu só Operadora Final
         cFiltro +=" and bc6_codope <='"+cOpeF+"'  "
       Endif
   Endif
Endif

//Será que preencheu plano ?
      
If  !Empty(cPlanI).And.!Empty(cPlanF) //preencheu Plano inicial e Final
     cFiltro +=" and bc6_codpla >='"+cPlanI+"'  "
     cFiltro +=" and bc6_codpla <='"+cPlanF+"'  "
Else
    If !Empty(cPlanI).And.Empty(cPlanF) //preencheu só Plano inicial.
       cFiltro +=" and bc6_codpla >='"+cPlanI+"'  "
    Else
       If Empty(cPlanI).And.!Empty(cPlanF) //preencheu só Operadora Final
        cFiltro +=" and bc6_codpla <='"+cPlanF+"'  "
       Endif
   Endif
Endif

//Buscando Dados :    

cQuery := " select bc6_codrda,bc6_codpad,bc6_codpro,bc6_vigini,bc6_vigfim,bc6_codope,bc6_codpla,r_e_c_n_o_,'"+cVigF+"' vigfim_atu  "
cQuery +=" from " +  RetSqlName('BC6') + " " 
cQuery +=" where " + RetSqlName('BC6') + ".d_e_l_e_t_=' ' "
cQuery +=" and bc6_filial=' '    "
cQuery +=" and bc6_codrda >='"+cRdaI+"'  "
cQuery +=" and bc6_codrda <='"+cRdaF+"'  "
cQuery +=" and bc6_vigini = '"+cVigI+"'  "
cQuery +=" and bc6_codpad >='"+codpadI+"'  "
cQuery +=" and bc6_codpad <='"+codpadF+"'  "
cQuery +=" and bc6_codpro >='"+cCodI+"'  "
cQuery +=" and bc6_codpro <='"+cCodF+"'  "   
cQuery +=" and bc6_vigfim =' ' "  
cQuery +=  cFiltro

Plsquery(cQuery,"TMPBC6") // Rebece o resultado da query acima 

If  TMPBC6->(Eof()) //Não achou bc6010  com os dados acima
    Alert("Não encontrados na RDA X Tabela de Preço itens com estes parâmetros,ou algum item já tem vigência Final. ")
    TMPBC6->( DbCloseArea() ) //Fecha tabela TMPBC6 que contem o select 
    Return()
Else
   cQuery := "CREATE TABLE SIGA.TMP_BC6_UPD AS "+cQuery
   TcSqlExec(cQuery)  //Criando tabela Tempóraria para Procedure  
   TCSPExec("PR_ATU_BC6",cVigF) //Comando que chama a procedure criada pelo senhor Abnher (evitar que trave) 
   TMPBC6->( DbCloseArea() ) //Fecha tabela TMPBC6 que contem o select 

/* Comantando update devido procedure

       //update -Tabela bc6010 
     
        Update :=" update " + RetSqlName('BC6') + " "
        Update +=" set bc6_vigfim = '"+cVigF+"'  "
        Update +=" where " + RetSqlName('BC6') + ".d_e_l_e_t_=' ' "  
        Update +=" and bc6_filial=' '    "
        Update +=" and bc6_codrda >='"+cRdaI+"'  "
        Update +=" and bc6_codrda <='"+cRdaF+"'  "
        Update +=" and bc6_vigini = '"+cVigI+"'  "
        Update +=" and bc6_codpad >='"+codpadI+"'  "
        Update +=" and bc6_codpad <='"+codpadF+"'  "
        Update +=" and bc6_codpro >='"+cCodI+"'  "
        Update +=" and bc6_codpro <='"+cCodF+"'  "
        Update +=" and bc6_vigfim =' ' " 
        Update +=cFiltro 
        TCSQLEXEC(Update)
*/
          
EndIf 
 //Alert("Concluido")  

 GERLOG()
 cMensagem := "Fim de processo!!!!!" + chr(13)+chr(10) 
 cMensagem += "Log gerado em "+_cCaminho+". Verifique!" //+ chr(13)+chr(10) 
 MSGINFO(cMensagem)     
 Limpar() //Limpa perguntas
return()
/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    25/03/20       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.        Função para limpar pergunta                               º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

Static Function Limpar()  

   dbSelectArea( "SX1" ) //Posiciona na Sx1
   dbSetOrder(1) //Dentro da SX1 na Ordem 1 (x1_Grupo+x1_Ordem)
   IF dbSeek('ATUBC610  01') //x1_Grupo + x1_Ordem //O comando dbSeek faz uma busca no banco de dados através de um índice existente.
	  while alltrim(SX1->X1_GRUPO) == 'ATUBC610' 
		RecLock("SX1",.F.) 
			SX1->X1_CNT01 := ' ' //Limpa o parametro 
		MsUnlock()
		SX1->(dbskip()) //Vai para o  proximo registro
	  enddo
    EndIF   
    
return()

/*

Programa--------: GERLOG
Autor-----------: Vanessa Cruz 

Data da Criacao-: 26/03/2020

Descrição-------: Rotina de geração de log do processamento

Parametros------: Nenhum

Retorno---------: Nenhum

*/

Static Function GERLOG()

//Local _cCaminho :=''
Local _cLin		:=''
Local cdescri   :=''   
Private cCpo	:= ""


Private nHdl

_cCaminho := Alltrim(cGetFile("*.*","Selecione o Diretorio de Destino",1,"j:\",.F.,GETF_LOCALHARD + GETF_NETWORKDRIVE + GETF_RETDIRECTORY))
_cCaminho += "Atualizando_Vigencia_Final_RDAXTabelaPreço_"+DTOS( Date() ) + StrTran( Time() , ':' , '' )+".txt"
nHdl := fCreate(_cCaminho)

If nHdl == -1
	MsgAlert("O arquivo de nome "+_cCaminho+" nao pode ser executado! Verifique os parametros.","Atencao!")
	Return
Endif

//Cabeçalho do Log:

cLin := "===================================================================="+cEOL

cLin += "Usuário logado: " +cUserName+cEOL+cEOL
cLin += "Data: "+SubStr(DtoC(Date()),1,2)+'/'+SubStr(DtoC(Date()),4,2)+'/'+SubStr(DtoC(Date()),7,4)+space(01)+Time()+cEOL+cEOL
cLin += "Estes foram os parâmetos que você escolheu ao rodar a rotina!"+cEOL+cEOL
cLin += "RDA Até: "+cRdaI+" até "+cRdaF+cEOL+cEOL
//cLin += "Vigência Inicial: "+Dtoc(cVigI)+" Vigência Final para: "+Dtoc(cVigF)+cEOL+cEOL //EXEMPLO: Dtoc=22/05/2019 JÁ DtoS=20190522
cLin += "Vigência Inicial: "+cVigI+" Vigência Final para: "+cVigF+cEOL+cEOL 
cLin += "Codpad De:"+codpadI+" até "+codpadF+cEOL+cEOL
cLin += "Procedimento De:"+cCodI+" até "+cCodF+cEOL+cEOL
cLin += "Operadora De: "+cOpeI+" até "+cOpeF+cEOL+cEOL
cLin += "Plano De: "+cPlanI+" até "+cPlanF+cEOL+cEOL
cLin += cEOL+"================================================================"+cEOL

//aqui vc ja escreveu a linha
If fWrite(nHdl,cLin,Len(cLin)) != Len(cLin)
	If !MsgAlert("Ocorreu um erro na gravacao do arquivo. Continua?","Atencao!")
		return
	Endif
Endif
////aqui vc ja escreveu chamou a fWrite novamente e escreveu a linha
//fWrite(nHdl,cLin)	         		
fClose(nHdl)
                 
cLin:=" "  //limpando linha 
Return()



/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma   CriaSX1   ºAutor  Vanessa          º Data    05/10/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.        Criando os parametros necessários para a rotina.          º±±
±±º             Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß


Pergunta : ATUBC610

Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)

PutSx1(cPerg,"01",OemToAnsi("Codigo RDA De:")         ,"","", "mv_ch1","C",06,0,0,"G","RDA","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"02",OemToAnsi("Codigo RDA Até:")        ,"","", "mv_ch2","C",06,0,0,"G","RDA","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{}) 
PutSx1(cPerg,"03",OemToAnsi("Vigência Inicial igual") ,"","", "mv_ch3","D",08,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"04",OemToAnsi("Vigência Final será:")   ,"","", "mv_ch4","D",08,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Codpad.De :")            ,"","", "mv_ch5","C",02,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"06",OemToAnsi("Codpad.Até :")           ,"","", "mv_ch6","C",02,0,0,"G","","","","","mv_par06","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"07",OemToAnsi("Procedimento De: ")      ,"","", "mv_ch7","C",08,0,0,"G","","","","","mv_par07","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"08",OemToAnsi("Procedimento Ate:")      ,"","", "mv_ch8","C",08,0,0,"G","","","","","mv_par08","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"09",OemToAnsi("Operadora De: ")         ,"","", "mv_ch9","C",04,0,0,"G","","","","","mv_par09","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"10",OemToAnsi("Operadora Ate:")         ,"","", "mv_ch10","C",04,0,0,"G","","","","","mv_par10","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"11",OemToAnsi("Plano De : ")            ,"","", "mv_ch11","C",04,0,0,"G","","","","","mv_par11","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"12",OemToAnsi("Plano Ate: ")            ,"","", "mv_ch12","C",04,0,0,"G","","","","","mv_par12","","","","","","","","","","","","","","","","",{},{},{})
return()  

Pergunta : ATUBD6 

Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//(cGrupo,cOrdem,cPergunt,cPerSpa, cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)

 PutSx1(cPerg,"01",OemToAnsi("Confirma alteração?")   ,"","", "mv_ch1","N",1,0,0,"C","","","","","mv_par01","SIM","","","","Nao","","","","","","","","","","","",{},{},{})
return() 

*/