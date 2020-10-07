include "protheus.ch"
#include "report.ch"

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ                              
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSRSIP2   ºAutor  Vanessa              º Data ³  17/10/2018 º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Relatório de Geração do Sip - Internacao Obstétrica X      º±±
±±º          ³ Nascidos Vivos/Mortos/Prematuros - 999 Reg                 º±± 
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
User function PLSRSIP2()  	

Local _lExec  := .T. 
Local aDados  := {}
		
Private lOk   := .F.   
Private cHtml := ""                                                           
Private cPerg := "PLSRSIP2" 
Private cDiretorio:=" " 

ValidPerg( cPerg )
Pergunte( cPerg , .F. )

//////////////////////////////////////////////////////////
While _lExec

	nOpca := 0
	 
	If Pergunte( cPerg )
	 
	  FWMsgRun( , {|| aDados := RunCont() } ,, 'Processando os dados do relatório...' )
	  
		  If !lOk 
		      If Empty(cHtml  )
				   cHtml := '<html><body>'
				   cHtml += 'Não foram encontrados registros para processar!'
				   cHtml += '<br><hr><br>'
				   cHtml += 'Verifique os parâmetros informados e tente novamente.'
				   cHtml += '</body></html>'   
			  Else
			  EndIf				
			   
		  Else
			   cHtml := '<html><body>'
			   cHtml += 'Arquivo salvo em '+alltrim(cDiretorio)+'.csv'
			   cHtml += '</body></html>'	  
		  EndIf
		  MsgStop( cHtml , 'Verifique!' )
	 Else
	 
	 	  _lExec := .F.
	  
	 EndIf

EndDo

Return

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  PLSRSIP2   ºAutor  ³Vanessa              º Data ³  17/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function RunCont()
Local cFiltro  := "" 
Local cQuery   := ""
Private aDados := {}   


if Empty(mv_par01) .And. Empty(mv_par02) .And. Empty(mv_par03)
    Alert("Informe corretamente os campos, nenhum campo deve estar vazio.")
    Return
endif 

if Empty(mv_par01) //Se o ano  estiver vazio 
	Alert("Informe o ano para pesquisa.")
    Return
endif

if Empty(mv_par02) //Se o Mes inicial  estiver vazio 
	Alert("Informe o mês inicial para pesquisa.")
    Return
endif

if Empty(mv_par03) //Se o  Mes final estiver vazio 
	Alert("Informe o mês Final para pesquisa.")
    Return
endif

cFiltro += "and be4_anopag = '"+Alltrim(mv_par01)+"' and be4_mespag >='"+Alltrim(mv_par02)+"' and be4_mespag <='"+Alltrim(mv_par03)+"' " 

cQuery := "select distinct  "
cQuery += "be4_anopag ANOPAG,  "
cQuery += "be4_mespag MESPAG, "
cQuery += "be4_codrda CODRDA,  "  
cQuery += "bau_nome   NOME_RDA,  "
cQuery += "be4_codldp LOCAL_DIG,  "
cQuery += "be4_codpeg NRO_PEG,  "
cQuery += "be4_numero NRO_GUIA,  "
cQuery += "be4_numimp NRO_IMPRESSO,  "
cQuery += "be4_datpro DATA_INTERNACAO,  "
cQuery += "be4_dtalta DATA_ALTA,  "
cQuery += "decode(be4_grpint,'1','Internacao Clinica','2','Internacao Cirurgica','3','Internacao Obstetrica') TIPO_INTERNACAO,  "
cQuery += "ba1_nomusr NOME_BENEFICIARIA,  "
cQuery += "be4_nasviv QDE_VIVO,  "
cQuery += "be4_nrdcnv DECL_VIVO1,  "
cQuery += "be4_yndvi2 DECL_VIVO2,  "
cQuery += "be4_yndvi3 DECL_VIVO3,  "
cQuery += "be4_yndvi4 DECL_VIVO4,  "
cQuery += "be4_yndvi5 DECL_VIVO5,   "  
cQuery += "be4_nasmor QDE_MORTO,  "       
cQuery += "be4_ydobt1 DECL_MORTO1,  "   
cQuery += "be4_ycobt1 CID_MORTO1,   "  
cQuery += "be4_ydobt2 DECL_MORTO2,  "  
cQuery += "be4_ycobt2 CID_MORTO2,   "  
cQuery += "be4_ydobt3 DECL_MORTO3,  "  
cQuery += "be4_ycobt3 CID_MORTO3,   " 
cQuery += "be4_ydobt4 DECL_MORTO4,  "
cQuery += "be4_ycobt4 CID_MORTO4,   " 
cQuery += "be4_ydobt5 DECL_MORTO5,  "
cQuery += "be4_ycobt5 CID_MORTO5,   "                                   
cQuery += "be4_codemp EMPRESA,  "
cQuery += "be4_matric MATRIC,  "
cQuery += "be4_tipreg TR    "
cQuery += "from "+RetSqlName("be4")+ " be4 , "+RetSqlName("ba1")+" ba1 ,"+RetSqlName("bau")+" bau  "
cQuery += "where be4_filial = ' ' and   ba1_filial = ' ' and   bau_filial = ' '  "
cQuery += "and   be4.d_e_l_e_t_ = ' ' and   ba1.d_e_l_e_t_ = ' ' and   bau.d_e_l_e_t_ = ' '  "
cQuery += "and   be4_codemp = ba1_codemp  "
cQuery += "and   be4_matric = ba1_matric  "
cQuery += "and   be4_tipreg = ba1_tipreg  "
cQuery += "and   be4_codrda = bau_codigo  "
cQuery += "and   be4_grpint = '3'   "             //fixo
cQuery += "and   substr(be4_codrda,1,1) <> '5'   " //fixo
cQuery += "and   be4_situac = '1'  "            //fixo
cQuery += "and   be4_fase   = '4'  "           //fixo
If !Empty(cFiltro) 
   cQuery += cFiltro
EndIf
cQuery += "order by be4_nasviv, be4_nrdcnv, be4_nasmor, be4_ydobt1, be4_codldp, be4_codpeg, be4_numero   "   
plsquery(cQuery,"TMPSIP")

If !TMPSIP->(Eof())   
    lOk := .T.				

	While !TMPSIP->(Eof())	// Enquanto tem registro faça 
		aadd(aDados,{ TMPSIP->ANOPAG     ,; //01 
			TMPSIP->MESPAG     ,; //02 
			TMPSIP->CODRDA     ,; // 03
			TMPSIP->NOME_RDA   ,; //04 
			TMPSIP->LOCAL_DIG  ,; // 05 
			TMPSIP->NRO_PEG    ,;  // 06
			TMPSIP->NRO_GUIA   ,; //  07
			TMPSIP->NRO_IMPRESSO      ,; // 08
			TMPSIP->DATA_INTERNACAO   ,; // 09 
			TMPSIP->DATA_ALTA         ,; // 10
			TMPSIP->TIPO_INTERNACAO   ,; // 11  
			TMPSIP->NOME_BENEFICIARIA ,; //12  
			TMPSIP->QDE_VIVO      ,;  // 13
			TMPSIP->DECL_VIVO1    ,; // 14
			TMPSIP->DECL_VIVO2    ,; // 15 
			TMPSIP->DECL_VIVO3    ,; // 16 
			TMPSIP->DECL_VIVO4    ,; // 17 
			TMPSIP->DECL_VIVO5    ,; // 18    
			TMPSIP->QDE_MORTO     ,; //  19      
			TMPSIP->DECL_MORTO1   ,; // 20   
			TMPSIP->CID_MORTO1    ,; // 21   
			TMPSIP->DECL_MORTO2   ,;// 22  
			TMPSIP->CID_MORTO2    ,; // 23  
			TMPSIP->DECL_MORTO3   ,;// 24  
			TMPSIP->CID_MORTO3    ,;// 25  
			TMPSIP->DECL_MORTO4   ,; // 26
			TMPSIP->CID_MORTO4    ,; // 27 
			TMPSIP->DECL_MORTO5   ,; // 28
			TMPSIP->CID_MORTO5    ,; // 29                     
			TMPSIP->EMPRESA         ,; // 30
			TMPSIP->MATRIC          ,; // 31
			TMPSIP->Tr    }) // 32	 	
	   	  					
		TMPSIP->(dbSkip()) // Indo para o proximo registro  		
	EndDo
	TMPSIP->( DbCloseArea() )
	
	fGeraExcel()
Else	  
	lOk := .F.
EndIf  

return

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºFun‡„o    ³ fGerExcelº Autor ³           		 º Data ³ 27/09/2017  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescri‡„o ³ Imprime cabecalho										  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºUso       ³                                                            º±±
±±ÈÍÍÍÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¼±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/
Static function fGeraExcel() 

local cArqTxt := ""
cDiretorio := Alltrim(cGetFile("*.*","Salvar",1,"j:\",.T.,GETF_LOCALHARD + GETF_NETWORKDRIVE + GETF_RETDIRECTORY))
cArqTxt := cDiretorio + "Sip_Inter_Obs_x_Vivos_Mortos"+SubStr(DtoC(Date()),1,2)+SubStr(DtoC(Date()),4,2)+SubStr(DtoC(Date()),7,4)+"_"+StrTran(Time(),":","")+".csv"
Private nHdl  := 0
Private cCpo  := ""  

FErase(cArqTxt)
nHdl := fCreate(cArqTxt)

If nHdl == -1
    MsgAlert("O arquivo de nome "+cArqTxt+" nao pode ser executado! Verifique os parametros.","Atencao!")
    Return .F.
EndIf    

cCpo := "SIGA/PLSRSIP2.PRW"+CRLF
fWrite(nHdl,cCpo)  

cCpo := "Data/Hora Geracao: "+dtoc(DATE())+" - "+time()+CRLF
fWrite(nHdl,cCpo)    

cCpo := "Operadora"+CRLF
fWrite(nHdl,cCpo)

cCpo := "Geração do SIP - Internação Obstétrica x Nascidos Vivos / Mortos / Prematuros - 999 Reg"+CRLF+CRLF
fWrite(nHdl,cCpo) 



cCpo := "AnoPag;MesPag;Cod_RDA;Nome_Rda;Local_Dig;Nro_Peg;Nro_Guia;Impresso;Dt_Internacao;Dt_Alta;Tp_Internacao;Nome;Qtd_vivo; "
cCpo += "Decl_vivo1;Decl_vivo2;Decl_vivo3;Decl_vivo4;Decl_vivo5;Qtd_morto;Decl_Morto1;Cid_Morto1;"
cCpo += "Decl_morto2;CID_MorTo2;Decl_Morto3;Cid_Morto3;Decl_Morto4;Cid_Morto4;Decl_Morto5;Cid_Morto5;Empresa;Matric;TR; "+CRLF

fWrite(nHdl,cCpo) 

nCnt    :=1
	
while nCnt <= Len(aDados)  	  
	

	cCpo := AllTrim(aDados[nCnt][01])+";"                       //AnoPag
	cCpo += '"=""'+AllTrim(aDados[nCnt][02]+'"""')+";"          //MesPag
	cCpo += ALLTRIM(aDados[nCnt][03])+";"                       //Cod_RDA
	cCpo += AllTrim(aDados[nCnt][04])+";"                       //Nome_Rda
	cCpo += '"=""'+AllTrim(aDados[nCnt][05])+'"""'+";"          //Local_Dig  '"=""' para conservar os digitos a esquerda 
	cCpo += '"=""'+AllTrim(aDados[nCnt][06])+'"""'+";"          //Nro_Peg
	cCpo += '"=""'+AllTrim(aDados[nCnt][07])+'"""'+";"          //Nro_Guia
	cCpo += AllTrim(aDados[nCnt][08])+";"                       //Impresso
	cCpo += dtoc(stod(aDados[nCnt][09]))+";"                    //Dt_Internacao
	cCpo += dtoc(stod(aDados[nCnt][10]))+";"                    //Dt_Alta
	cCpo += AllTrim(aDados[nCnt][11])+";"                       //Tp_Internacao
	cCpo += AllTrim(aDados[nCnt][12])+";"                       //Nome 
	cCpo += cValToChar(aDados[nCnt][13])+";"                    //Qtd_vivo
	cCpo += '"=""'+AllTrim(aDados[nCnt][14])+'"""'+";"          //decl_vivo1
	cCpo += '"=""'+AllTrim(aDados[nCnt][15])+'"""'+";"          //decl_vivo2
	cCpo += '"=""'+AllTrim(aDados[nCnt][16])+'"""'+";"          //decl_vivo3
	cCpo += '"=""'+AllTrim(aDados[nCnt][17])+'"""'+";"          //decl_vivo4
	cCpo += '"=""'+AllTrim(aDados[nCnt][18])+'"""'+";"          //decl_vivo5
	cCpo += cValToChar(aDados[nCnt][19])+";"                    //Qtd_morto
	cCpo += '"=""'+AllTrim(aDados[nCnt][20])+'"""'+";"          //decl_Morto1
	cCpo += AllTrim(aDados[nCnt][21])+";"                       //cid_Morto1
	cCpo += '"=""'+AllTrim(aDados[nCnt][22])+'"""'+";"          //decl_morto2
	cCpo += AllTrim(aDados[nCnt][23])+";"                       //CID_MorTo2
	cCpo += '"=""'+AllTrim(aDados[nCnt][24])+'"""'+";"          //decl_Morto3
	cCpo += AllTrim(aDados[nCnt][25])+";"                       //cid_Morto3
	cCpo += '"=""'+AllTrim(aDados[nCnt][26])+'"""'+";"          //decl_morto4
	cCpo += AllTrim(aDados[nCnt][27])+";"                       //CID_MorTo4
	cCpo += '"=""'+AllTrim(aDados[nCnt][28])+'"""'+";"          //decl_Morto5
	cCpo += AllTrim(aDados[nCnt][29])+";"                       //cid_Morto5
	cCpo += '"=""'+AllTrim(aDados[nCnt][30])+'"""'+";"          //Empresa
	cCpo += '"=""'+AllTrim(aDados[nCnt][31])+'"""'+";"          //Matric
	cCpo += '"=""'+AllTrim(aDados[nCnt][32])+'"""'+" "          //Tr  
		
	cCpo += CRLF     
	 
	fWrite(nHdl,cCpo) 
	
	nCnt++
EndDo  
fClose(nHdl)

Return .T.

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³   ºAutor  ³Microsiga           º Data ³  15/03/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³Funcao responsavel por gerar perguntas no dicionario de     º±±
±±º          ³dados, caso as mesmas nao existam.                          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function ValidPerg()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}   

PutSx1(cPerg,"01",OemToAnsi("Ano ")   ,"","","mv_ch1","C",04,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Mes De")  ,"","","mv_ch2","C",02,0,0,"G","","","","","mv_par02","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Mes Até") ,"","","mv_ch3","C",02,0,0,"G","","","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
//PutSx1(cPerg,"04",OemToAnsi("Caminho para o excel"),"","","mv_ch04","C",60,0,0,"G","NAOVAZIO()",   "DIR","","","mv_par04",     "","","","",     "","","",     "","","","","","","")
return    