#INCLUDE 'PROTHEUS.CH'
#DEFINE SIMPLES Char( 39 )
#DEFINE DUPLAS  Char( 34 )             

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma: PLSRAJUS  ºAutor  ³Vanessa Cruz         º Data ³  27/11/19    º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc. Relatório de reajustes                                          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso        Baseado no fonte: MPLS108                                  º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/ 

User Function PLSRAJUS()  //Funcao de usuário maximo 8 caracteres    

Local cImpresso := ""  
Local Codope:= ""
Local Codemp:= ""
Local Codcont:= ""
Local Codcont:= ""
Local CodSubI:= ""
Local CodSubF:= ""
Private nOpca	:= 1
Private aCores 	:= {} 
Private cFiltro :="" 
Private cCompl  :=""  
Private cPerg   := "PLSRAJUS"  
Private aVetor  := {}


CriaSX1()                 

if !Pergunte(cPerg,.T.,"Montando dados do Relatório de Reajustes.",.T.)
       return()
endif			
	
if Empty(mv_par01) //Se o código da Operadora estiver vazio 
	Alert("Informe o código da Operadora, este campo é obrigatório.")
    Return
endif
	
if Empty(mv_par02) //Se o código do Grupo Empresa estiver vazio  
	Alert("Informe o Grupo empresa, este campo é obrigatório.")
    Return
endif	


if Empty(mv_par03) //Se o numero do contrato estiver vazio   
	Alert("Informe o número do contato, este campo é obrigatório.")
    Return
endif	

 Codope :=AllTrim(mv_par01)
 Codemp :=AllTrim(mv_par02)   
 Codcont:=AllTrim(mv_par03) //Recebe o que o usuário digitou na tela
 Codcont:=Meustrzero(Codcont,12)//Funcao para acrescentar zeros a esquerda e retonar a string para a variável  Codcont
 CodSubI:=AllTrim(mv_par04)
 CodSubI:=Meustrzero(CodSubI,9) //Funcao para acrescentar zeros a esquerda e retonar a string para a variável  CodSubI
 CodSubF:=AllTrim(mv_par05) 
 CodSubF:=Meustrzero(CodSubF,9)//Funcao para acrescentar zeros a esquerda e retonar a string para a variável  CodSubF


if !empty(mv_par04)
	cFiltro  := "and bt7_codigo>= '" +CodSubI+ "' "
	cCompl   := "and bt8_subcon>= '" +CodSubI+ "' "
endif

if !empty(mv_par05)
	cFiltro += "and bt7_codigo<= '" +CodSubF+ "' "
	cCompl  += "and bt8_subcon<= '" +CodSubF+ "' "
endif


 FWMsgRun( , {||Buscar(Codope,Codemp,Codcont,CodSubI,CodSubF)} , 'Processando' , 'Aguarde.Pesquisando Dados ...')  //chamando a função quer irá pesquisar os dados    

 

return()

/*
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±± Função para acrescentar zeros a esquerda do contrato e subcontrato      ±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
*/
                
Static Function Meustrzero(cString,Ntamanho)
 Local cont:=0
 Local cAux :=''
 Ntamanho :=  Ntamanho-len(cString)
 For cont:=1 to Ntamanho
   cAux +='0'   
 Next
 cString:= cAux+cString
return(cString)      
/*
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±± Função que busca os dados para montar o relatório                       ±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
*/

Static Function Buscar(Codope,Codemp,Codcont,CodSubI,CodSubF) //Função Static  será vista somente aqui.

Local   lAchou   := .T. 
Local   cQuery     := ""  
Local   aRet     := {}
Local   oDlg     := NIL
Local   oChkMar  := NIL
Local   oLbx     := NIL
Local   oMascEmp := NIL
Local   oButMarc := NIL
Local   oButDMar := NIL
Local   oSay     := NIL
Local   oOk      := LoadBitmap( GetResources(), 'LBOK' )
Local   oNo      := LoadBitmap( GetResources(), 'LBNO' )
Local   lChk     := .F.
Local   lOk      := .F.
Local   lTeveMarc:= .F.
Local   aMarcadas  := {}
Local   nCnt

//ÚÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄ¿
//³ Monta Query para buscar os dados. 															   ³
//ÀÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÄÙ

cQuery := "select bt7_codigo as Empresa,'grupo'as Origem, "
cQuery += "(select "
cQuery += "  trim(bg9_descri) "//subselect para pegar o nome da empresa
cQuery += "  from " + RetSqlName("BG9")+ " "
cQuery += "  where " + RetSqlName("BG9") + ".d_e_l_e_t_=' ' "
cQuery += "    and bg9_filial =' ' "
cQuery += "    and bg9_codigo=bt7_codigo) as Descricao, " 
cQuery += "bt7_numcon as Contrato, "
cQuery += "bt7_subcon as Subcontrato, "
cQuery += "(select  "//subselect para pegar o nome do subcontrato
cQuery += "   trim(bqc_descri)  "
cQuery += "   from " + RetSqlName("BQC")+ " " 
cQuery += "   where " + RetSqlName("BQC") + ".d_e_l_e_t_=' ' "
cQuery += "     and bqc_filial =' '  "
cQuery += "     and bqc_codemp=bt7_codigo "
cQuery += "     and bqc_numcon=bt7_numcon "
cQuery += "     and bqc_subcon=bt7_subcon) as Descri_sub, "
cQuery += "bt7_codpro as Produto, "
cQuery += "(select   " //subselect para pegar o nome do Produto(plano)
cQuery += "   trim(bi3_descri)  "
cQuery += "   from " + RetSqlName("BI3")+ " "
cQuery += "   where " + RetSqlName("BI3") + ".d_e_l_e_t_=' ' "
cQuery += "     and bi3_filial=' ' "
cQuery += "     and bi3_codigo=bt7_codpro ) as Descri_prod, "
cQuery += "bt7_codgru as Tp_Tab_Padrao, "
cQuery += "decode(bhf_tipo,'1','Internacao','2','Ambulatorial','3','Ambos') as Tipo_Atend, "
cQuery += "substr(bhf_vigini,7,2)||'/' ||substr(bhf_vigini,5,2)||'/'||substr(bhf_vigini,1,4) as Vig_Inicial,  "
cQuery += "substr(bhf_vigfin,7,2)||'/' ||substr(bhf_vigfin,5,2)||'/'||substr(bhf_vigfin,1,4) as Vig_Fim,   "  
cQuery += "bhf_anomes as Ano_Mes, "
cQuery += "bhf_qtd    as Qtd, "
cQuery += "bhf_percop as Per_Co_part, "
cQuery += "bhf_valcop as Vl_Co_part, "
cQuery += "bhf_valus  as Valor_Us, "
cQuery += "bhf_txadm  as Vl_tx_Adm, "
cQuery += "bhf_limfra as Vl_franquia, "
cQuery += "bhf_vlrant as Vl_anterior, "
cQuery += "bhf_yvlde  as Valor_De, "
cQuery += "bhf_yvlate as Valor_Ate "
cQuery += "from " + RetSqlName("BT7")+ " "
cQuery += "left join " + RetSqlName("BHF")+ " "
cQuery += " on "  + RetSqlName("BT7") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BHF") + ".d_e_l_e_t_=' ' "
cQuery += " and bt7_filial=' '  "
cQuery += " and bhf_filial=' '  "
cQuery += " and bt7_codigo = bhf_codigo "
cQuery += " and bt7_numcon = bhf_numcon "
cQuery += " and bt7_subcon = bhf_subcon "
cQuery += " and bhf_somcom='0'  " //Somente cobrança = Não 
cQuery += "where bt7_codint='"+Codope+"' "       
cQuery += " and bt7_codigo='"+Codemp+"' "
cQuery += " and bt7_numcon='"+Codcont+"' "  
cQuery += cFiltro  
cQuery += "group by bt7_codigo,bt7_numcon,bt7_subcon,bt7_codpro,bt7_codgru,bhf_tipo,bhf_vigini,bhf_vigfin,bhf_anomes,bhf_qtd,bhf_percop,bhf_valcop,  "
cQuery += "bhf_valus,bhf_txadm,bhf_limfra,bhf_vlrant,  "
cQuery += "bhf_yvlde,bhf_yvlate,bhf_somcom "
cQuery += "union all "
cQuery += "select  bt8_codigo as Empresa,'procedimento' as Origem,  "
cQuery += "(select "//subselect para pegar o nome da empresa
cQuery += "    trim(bg9_descri)  "
cQuery += "    from " + RetSqlName("BG9")+ " "
cQuery += "    where  " + RetSqlName("BG9") + ".d_e_l_e_t_=' ' "
cQuery += "      and bg9_filial =' '  "
cQuery += "      and bg9_codigo=bt8_codigo) as Descricao, "
cQuery += "bt8_numcon as Contrato, "
cQuery += "bt8_subcon as Subcontrato, "
cQuery += "(select " //subselect para pegar o nome do subcontrato
cQuery += "  trim(bqc_descri)  "
cQuery += "  from " + RetSqlName("BQC") + " "
cQuery += "  where " + RetSqlName("BQC") + ".d_e_l_e_t_=' ' "
cQuery += "  and bqc_filial =' '  "
cQuery += "  and bqc_codemp=bt8_codigo "
cQuery += "  and bqc_numcon=bt8_numcon "
cQuery += "  and bqc_subcon=bt8_subcon) as Descri_sub, "
cQuery += "bt8_codpro as Produto, "
cQuery += "(select  " //subselect para pegar o nome do Produto(plano)
cQuery += "  trim(bi3_descri) 
cQuery += "  from " + RetSqlName("BI3")+ " "
cQuery += "  where " + RetSqlName("BI3") + ".d_e_l_e_t_=' ' "
cQuery += "    and bi3_filial=' ' "
cQuery += "    and bi3_codigo=bt8_codpro) as Descri_prod, "
cQuery += "bt8_codpad||bt8_codpro as Tp_Tab_Padrao, "
cQuery += "decode (bhe_tipo,'1','Internacao','2','Ambulatorial','3','Ambos') as Tipo_Atend, "
cQuery += "substr(bhe_vigini,7,2)||'/' ||substr(bhe_vigini,5,2)||'/'||substr(bhe_vigini,1,4) as Vig_Inicial,  "
cQuery += "substr(bhe_vigfin,7,2)||'/' ||substr(bhe_vigfin,5,2)||'/'||substr(bhe_vigfin,1,4) as Vig_Fim,  "
cQuery += "bhe_anomes as Ano_Mes, "
cQuery += "bhe_qtd as Qtd, "
cQuery += "bhe_percop as Per_Co_part, "
cQuery += "bhe_vlrcop as VL_Co_part, "
cQuery += "bhe_valus  as Valor_Us, "
cQuery += "bhe_txadm  as vl_tx_Adm, "
cQuery += "bhe_limfra as VL_franquia, "
cQuery += "bhe_vlrant as Vl_anterior, "
cQuery += "bhe_yvlde  as Valor_De, "
cQuery += "bhe_yvlate as Valor_Ate "
cQuery += " from " + RetSqlName("BT8")+ " "
cQuery += " left join " + RetSqlName("BHE")+ " "
cQuery += " on " + RetSqlName("BT8") + ".d_e_l_e_t_=' ' "
cQuery += " and " + RetSqlName("BHE") + ".d_e_l_e_t_=' ' "
cQuery += " and bt8_filial=' '  "
cQuery += " and bhe_filial=' '  "
cQuery += " and bt8_codigo = bhe_codigo "
cQuery += " and bt8_numcon = bhe_numcon "
cQuery += " and bt8_subcon = bhe_subcon "
cQuery += " and bhe_somcom='0'  " //Somente cobrança = Não   
cQuery += " where bt8_codint='"+Codope+"' "
cQuery += " and bt8_codigo='"+Codemp+"' "
cQuery += " and bt8_numcon='"+Codcont+"' "
cQuery += cCompl 
cQuery += "group by bt8_codigo,bt8_numcon,bt8_subcon,bt8_codpad,bt8_codpro,bhe_tipo,bhe_vigini,bhe_vigfin,bhe_anomes,bhe_qtd,bhe_percop,bhe_vlrcop,  "
cQuery += "bhe_valus,bhe_txadm,bhe_limfra,bhe_vlrant,bhe_yvlde,bhe_yvlate  "   
cQuery += "order by 1,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21 "

Plsquery(cQuery,"TMPREL") // Recebe o resultado da query acima 

If  TMPREL->(Eof()) //Verificando se a tabela TMPREL esta  vazia 
    Alert("Dados não foram encontrados!")
    TMPREL->( DbCloseArea() ) //Fecha tabela TMPREL 
    Return(.F.)//Fecha a rotina não faça mais nada  
EndIf 
		While !TMPREL->(Eof()) //Se o relatório não estiver vazio alimente o vetor com os dados : 
		    aAdd( aVetor,{ aScan( aMarcadas, {|x| x[1] == TMPREL->Empresa } ) > 0,;   
		                                                  TMPREL->Empresa,;
		                                                  TMPREL->Origem,;
													      TMPREL->Descricao,;
													      TMPREL->Contrato,;
													      TMPREL->Subcontrato,;
													      TMPREL->Descri_sub,;
													      TMPREL->Produto,;
													      TMPREL->Descri_prod,;
													      TMPREL->Tipo_Atend,;
													      TMPREL->Vig_Inicial,;
													      TMPREL->Vig_Fim,;
													      TMPREL->Ano_Mes,;
													      TMPREL->Qtd,;
													      TMPREL->Per_Co_part,;
													      TMPREL->Vl_Co_part,;
													      TMPREL->Valor_Us,;
													      TMPREL->Vl_tx_Adm,;
													      TMPREL->Vl_franquia,;
													      TMPREL->Vl_anterior,;
													      TMPREL->Valor_De,;
													      TMPREL->Valor_Ate   } )
		    TMPREL->(DbSkip()) // pega o próximo registro   
			
	    EndDo
	    TMPREL->(dbCloseArea())   
	
	     Define MSDialog  oDlg Title '' From 0, 0 To 500, 900 Pixel //Caixa de Dialogo Altura X Largura 
	          
	    oDlg:cToolTip := ''
				
		oDlg:cTitle := 'Relatório de Reajustes'
		
		//Cabeçalho das colunas do relatório:
		
		@ 10, 00 Listbox  oLbx Var  cVar Fields Header '[X]', 'Empresa','Origem','Descricao','Contrato','Subcontrato',;
	                                                          'Descri_sub','Produto','Descri_prod','Tipo_Atend','Vig_Inicial',;
															  'Vig_Fim','Ano_Mes','Qtd','Per_Co_part','Vl_Co_part','Valor_Us',;
															  'Vl_tx_Adm','Vl_franquia','Vl_anterior','Valor_De','Valor_Ate' Size 810, 200 Of oDlg Pixel //Listbox Largura X Altura
		
		oLbx:SetArray( aVetor )    
		
		oLbx:bLine := {|| {IIf( aVetor[oLbx:nAt, 1], oOk, oNo ), ; //Empresa
								aVetor[oLbx:nAt, 2], ; //Origem
								aVetor[oLbx:nAt, 3], ; //Descricao
								aVetor[oLbx:nAt, 4], ; //Contrato
								aVetor[oLbx:nAt, 5], ; //Subcontrato
								aVetor[oLbx:nAt, 6], ; //Descri_sub
								aVetor[oLbx:nAt, 7], ; //Produto
								aVetor[oLbx:nAt, 8], ; //Descri_prod
								aVetor[oLbx:nAt, 9], ; //Tipo_Atend
								aVetor[oLbx:nAt, 10], ; //Vig_Inicial
								aVetor[oLbx:nAt, 11], ; //Vig_Fim
								aVetor[oLbx:nAt, 12], ; //Ano_Mes
								aVetor[oLbx:nAt, 13], ; //Qtd
								aVetor[oLbx:nAt, 14], ; //Per_Co_part
								aVetor[oLbx:nAt, 15], ; //Vl_Co_part
								aVetor[oLbx:nAt, 16], ;	//Valor_Us
								aVetor[oLbx:nAt, 17], ;	//Vl_tx_Adm
								aVetor[oLbx:nAt, 18], ; //Vl_franquia
								aVetor[oLbx:nAt, 19], ;	//Vl_anterior
								aVetor[oLbx:nAt, 20], ;	//Valor_De						
								aVetor[oLbx:nAt, 21] }} //Valor_Ate
										
		oLbx:BlDblClick := { || aVetor[oLbx:nAt, 1] := !aVetor[oLbx:nAt, 1], VerTodos( aVetor, @lChk, oChkMar ), oChkMar:Refresh(), oLbx:Refresh()}
		oLbx:cToolTip   :=  oDlg:cTitle
		oLbx:lHScroll   := .T. // NoScroll habilita barra de rolagem horizontal
		oLbx:Refresh()
		
		@ 01, 01 CheckBox oChkMar Var  lChk Prompt 'Todos'   Message 'Marca / Desmarca Todos' Size 40, 007 Pixel Of oDlg;
		on Click MarcaTodos( lChk, @aVetor, oLbx ) 
		
		Define SButton From 215, 235 Type 1 Action ( lTeveMarc:=RetSelecao( @aRet, aVetor ), oDlg:End() ) OnStop 'Confirma a Seleção'  Enable Of oDlg //Linha x coluna
	    Define SButton From 215, 200 Type 2 Action ( IIf( lTeveMarc, aRet :=  aMarcadas, .T. ), oDlg:End() ) OnStop 'Abandona a Seleção' Enable Of oDlg//Linha x coluna
	Activate MSDialog  oDlg Center
	
	If lTeveMarc // se marcou algum escreve o csv  
		fGeraExcel() 
	EndIf

Return()    


Static Function MarcaTodos( lMarca, aVetor, oLbx )
Local  nI := 0

For nI := 1 To Len( aVetor )
	aVetor[nI][1] := lMarca
Next nI

oLbx:Refresh()

Return NIL


Static Function RetSelecao( aRet, aVetor )
Local  nI    := 0

aRet := {}
For nI := 1 To Len( aVetor )
	If aVetor[nI][1]
		aAdd( aRet, { aVetor[nI][2] , aVetor[nI][3], aVetor[nI][2] +  aVetor[nI][3] } )
	EndIf
Next nI 

If len(aRet) == 0 
	return .F.
EndIf

Return .T. //NIL


Static Function VerTodos( aVetor, lChk, oChkMar ) 
Local lTTrue := .T.
Local nI     := 0

For nI := 1 To Len( aVetor )
	lTTrue := IIf( !aVetor[nI][1], .F., lTTrue )
Next nI

lChk := IIf( lTTrue, .T., .F. )
oChkMar:Refresh()

Return NIL


Static function fGeraExcel() //Função que irá gerar o Relatório em Excel. 
Local cArqTxt := ""
Local nCnt    :=1 

Private nHdl  := 0
Private cCpo  := ""  

cDiretorio := Alltrim(cGetFile("*.*","Salvar",1,"j:\",.T.,GETF_LOCALHARD + GETF_NETWORKDRIVE + GETF_RETDIRECTORY))
cArqTxt    := cDiretorio+"Relatorio de Reajustes "+SubStr(DtoC(Date()),1,2)+SubStr(DtoC(Date()),4,2)+SubStr(DtoC(Date()),7,4)+"_"+StrTran(Time(),":","")+".csv"

FErase(cArqTxt)
nHdl := fCreate(cArqTxt)

If nHdl == -1
    MsgAlert("O arquivo de nome "+cArqTxt+" nao pode ser executado! Verifique os parametros.","Atencao!")
    Return .F.
EndIf    

cCpo := "SIGA/PLSRAJUS.PRW"+CRLF
fWrite(nHdl,cCpo)  

cCpo := "Data/Hora Geracao: "+dtoc(DATE())+" - "+time()+CRLF
fWrite(nHdl,cCpo)    

cCpo := "Unimed Uberaba"+CRLF
fWrite(nHdl,cCpo)


cCpo := "Operador"+space(02)+cUsername+CRLF+CRLF
fWrite(nHdl,cCpo) 

cCpo := "Empresa;"     //01
cCpo += "Origem;"      //02 
cCpo += "Descricao;"   //03 
cCpo += "Contrato;"    //04 
cCpo += "Subcontrato;" //05 
cCpo += "Descri_sub;"  //06 
cCpo += "Produto;"     //07 
cCpo += "Descri_prod;" //08 
cCpo += "Tipo_Atend;"  //09 
cCpo += "Vig_Inicial;" //10 
cCpo += "Vig_Fim;"     //11 
cCpo += "Ano_Mes;"     //12 
cCpo += "Qtd;"         //13 
cCpo += "Per_Co_part;" //14 
cCpo += "Vl_Co_part;"  //15
cCpo += "Valor_Us;"    //16
cCpo += "Vl_tx_Adm;"   //17
cCpo += "Vl_franquia;" //18
cCpo += "Vl_anterior;" //19
cCpo += "Valor_De;"    //20
cCpo += "Valor_Ate"    //21
cCpo += CRLF
fWrite(nHdl,cCpo) 


while nCnt <= Len(aVetor)  	  
	
	cCpo := AllTrim(aVetor[nCnt][02])+"';"  //02 "Empresa;"
	cCpo += AllTrim(aVetor[nCnt][03])+";"   //03 "Origem"
	cCpo += AllTrim(aVetor[nCnt][04])+";"   //04 "Descricao;"
	cCpo += AllTrim(aVetor[nCnt][05])+"';"  //05 "Contrato;" 
	cCpo += AllTrim(aVetor[nCnt][06])+"';"  //06 "Subcontrato;"
	cCpo += AllTrim(aVetor[nCnt][07])+";"   //07 "Descri_sub;"
	cCpo += AllTrim(aVetor[nCnt][08])+"';"  //08 "Produto;"
	cCpo += AllTrim(aVetor[nCnt][09])+";"   //09 "Descri_prod;"
	cCpo += AllTrim(aVetor[nCnt][10])+";"   //10 "Tipo_Atend;"
	cCpo += AllTrim(aVetor[nCnt][11])+";"   //11 "Vig_Inicial;"
	cCpo += AllTrim(aVetor[nCnt][12])+";"   //12 "Vig_Fim;"  
	cCpo += AllTrim(aVetor[nCnt][13])+";"   //13 "Ano_Mes;"  
	cCpo +=cValToChar(aVetor[nCnt][14])+";"  //14 "Qtd;"  
	cCpo += AllTrim(aVetor[nCnt][15])+";"   //15 "Per_Co_part;" 
	cCpo += Transform(aVetor[nCnt][15],"@E 999,999,999.99")+";"   //16 "Vl_Co_part;"  
	cCpo += Transform(aVetor[nCnt][16],"@E 999,999,999.99")+";"   //17 "Valor_Us;"    
	cCpo += Transform(aVetor[nCnt][17],"@E 999,999,999.99")+";"   //18 "Vl_tx_Adm;"   
	cCpo += Transform(aVetor[nCnt][18],"@E 999,999,999.99")+";"   //19 "Vl_franquia;" 
	cCpo += Transform(aVetor[nCnt][19],"@E 999,999,999.99")+";"   //20 "Vl_anterior;" 
	cCpo += Transform(aVetor[nCnt][20],"@E 999,999,999.99")+";"   //21 "Valor_De;"    
	cCpo += Transform(aVetor[nCnt][21],"@E 999,999,999.99")       //22 "Valor_Ate"    
	cCpo += CRLF     
	 
	fWrite(nHdl,cCpo) 
	
	nCnt++
EndDo  
fClose(nHdl)  

MSGalert("Arquivo gerado em: "+Chr(10)+cArqTxt+'\nome_arquivo'+DTOS( Date() ) + '_' + StrTran( Time() , ':' , '' )+'.csv')
Return       

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³CriaSX1   ºAutor  Vanessa          º Data ³  01/10/19       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³  Criando os parametros necessários para a rotina.          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


//(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Operadora ?")      ,"","","mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Grupo Empresa ?")  ,"","","mv_ch2","C",04,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Contrato?")        ,"","","mv_ch3","C",12,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"04",OemToAnsi("Subcontrato De?")  ,"","","mv_ch4","C",09,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Subcontrato Ate?") ,"","","mv_ch5","C",09,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
return 