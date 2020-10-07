#INCLUDE "PROTHEUS.CH"
#INCLUDE "TBICONN.CH"
#INCLUDE "COLORS.CH"
#INCLUDE "RPTDEF.CH"
#INCLUDE "FWPrintSetup.ch" 

#define DMPAPER_LETTER 1 		// Letter 8 1/2 x 11 in
#define DMPAPER_LETTERSMALL 2 	// Letter Small 8 1/2 x 11 in
#define DMPAPER_TABLOID 3 		// Tabloid 11 x 17 in
#define DMPAPER_LEDGER 4 		// Ledger 17 x 11 in
#define DMPAPER_LEGAL 5 		// Legal 8 1/2 x 14 in
#define DMPAPER_EXECUTIVE 7 	// Executive 7 1/4 x 10 1/2 in
#define DMPAPER_A3 8 			// A3 297 x 420 mm
#define DMPAPER_A4 9 			// A4 210 x 297 mm
#define DMPAPER_A4SMALL 10 		// A4 Small 210 x 297 mm
#define DMPAPER_A5 11 			// A5 148 x 210 mm
#define DMPAPER_B4 12 			// B4 250 x 354
#define DMPAPER_B5 13 			// B5 182 x 257 mm
#define DMPAPER_FOLIO 14 		// Folio 8 1/2 x 13 in
#define DMPAPER_NOTE 18 		// Note 8 1/2 x 11 in
#define DMPAPER_ENV_10 20 		// Envelope #10 4 1/8 x 9 1/2  

#DEFINE  cEol CHR(13)+CHR(10)
                                                                                                
/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma: PLSPRGUI      º Autor ³ Vanessa Cruz º Data ³  09/10/19      º±± 
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³ Protocolo de entraga de Guias                              º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/

User Function PLSPRGUI()    

Private lValida := .T.
Private cPerg := "PLSPROCG" 
Private oPrint
Private nItem :=0  //Variável para contar guias

// Imagens do relatorio
Private cBmp1 := "topo2.Bmp" 

//Local cData  := DtoC(date())+' '+Time() 
    
CriaSX1()// Nesta forma de criar a pergunta ela nao guarda o ultimo resultado digitado no parametro. 
if !Pergunte(cPerg,.T.,"Protocolo de Entrega de Guias.",.T.)
   	return()
endif 

Private cNomArq := "Protocolo de Entrega de Guias_"+ DTOS( Date() ) +'_'+ StrTran( Time() , ':' , '' )

Processa({|lEnd|PLSPGUI()}) //Função para montar o relatório 

Return()

/*ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  |PLSPGUI   º Autor ³ Vanessa Cruz       º Data ³  09/10/19   º±± 
±±º                                                                       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDescricao ³Protocolo de Entrega de Guias                               º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß*/
Static function PLSPGUI() 

Local cDtDigi:=" "   
Local cPaciente:=" " 
Local cImpresso:=" " 
Local cTipo:=" "     
Local cPrestador:=" "
Local cNomePrest:=" "
Local cDTRec:=" "    
Local cGuias :=""

// Paramentros do objeto
Local lAdjustToLegacy := .F.
Local lDisableSetup   := .T.
	

// Fontes para impressao do relatorio
Local oFont8
Local oFont8n
Local oFont9
Local oFont11n
Local oFont13n
Local oFont14
Local oFont15n
Local oFont16
Local oFont16n
Local oFont20
Local oFont21

oFont8  := TFont():New("Arial",9,6 ,.T.,.F.,5,.T.,5,.T.,.F.)
oFont8n := TFont():New("Courier New",9,6,.T.,.F.,5,.T.,5,.T.,.F.)
oFont9  := TFont():New("Courier New",9,8,.T.,.T.,5,.T.,5,.T.,.F.) 
oFont9n := TFont():New("Courier New",9,8,.T.,.T.,5,.T.,5,.T.,.F.)
oFont11n:= TFont():New("Arial",9,9,.T.,.T.,5,.T.,5,.T.,.F.) 
oFont12 := TFont():New("Courier New",9,12,.T.,.T.,5,.T.,5,.T.,.F.)
oFont13n:= TFont():New("Arial",9,11,.T.,.F.,5,.T.,5,.T.,.F.)
oFont14 := TFont():New("Arial",9,12,.T.,.T.,5,.T.,5,.T.,.F.)
oFont15n:= TFont():New("Arial",9,13,.T.,.F.,5,.T.,5,.T.,.F.)
oFont16 := TFont():New("Arial",9,14,.T.,.T.,5,.T.,5,.T.,.F.)
oFont16n:= TFont():New("Arial",9,14,.T.,.F.,5,.T.,5,.T.,.F.)
oFont20 := TFont():New("Arial",9,16,.T.,.T.,5,.T.,5,.T.,.F.)
oFont21 := TFont():New("Arial",9,17,.T.,.T.,5,.T.,5,.T.,.F.)

if Empty(mv_par01) //Se o numero do prestador estiver vazio 
	Alert("Informe numero impresso:")
    Return
endif
      
   cGuias :=strtran(Alltrim(mv_par01),",","','")// Recebendo mais de uma informação com virgula entre elas
    // cGuias :="'"+cGuias+"'"
    // aGuias := separa(Alltrim(mv_par01),',')

  if !Empty(mv_par02) //Se o numero do prestador estiver vazio 
	cGuias :=cGuias+strtran(Alltrim(mv_par02),",","','")
endif


	Private QRY01   := " " 
	                         
	//selecionando a(s) Guia(s):
	
	QRY01 := "select substr(bea_dtdigi,7,2)||'/' ||substr(bea_dtdigi,5,2)||'/'||substr(bea_dtdigi,1,4) as dtdigitacao,  " 
	QRY01 += "substr (bea_nomusr,1,45) as Paciente,   " 
	QRY01 += "bea_numimp as guia,  " 
	QRY01 += "case when bea_tipgui='01'  then 'consulta' when bea_tipgui='02' then 'sp/sadt' when bea_tipgui='03' then 'internacao' end as  Tipo,  " 
	QRY01 += "bea_codrda as Prestador,  " 
	QRY01 += "substr (bea_nomrda,1,45) as Nmprestador,  "  
	QRY01 += "case when bea_datsol = ' ' then  " 
	QRY01 += "(select  substr (be4_yreceb,7,2)||'/' ||substr(be4_yreceb,5,2)||'/'||substr(be4_yreceb,1,4)  " 
	QRY01 += "from siga.be4010 where d_e_l_e_t_ = ' '   " 
	QRY01 += "and be4_numimp = bea_numimp)  " 
	QRY01 += "else substr(bea_datsol,7,2)||'/' ||substr(bea_datsol,5,2)||'/'||substr(bea_datsol,1,4)  " 
	QRY01 += "end as dtrecguia   " 
	QRY01 += "from siga.bea010  " 
	QRY01 += "where bea_filial = ' ' and  bea010.d_e_l_e_t_ = ' '  " 
	QRY01 += "and  bea_numimp in ('"+cGuias+"') "
	QRY01 += "order by  bea_codrda,bea_numimp "
	PlsQuery(QRY01,'cSQL1') 
	    
	DbSelectArea("cSQL1")  
	
	If !cSQL1->(Eof())
	
	    //construtor do objeto
		oPrint := FWMSPrinter():New(cNomArq, IMP_PDF, lAdjustToLegacy,, lDisableSetup, , ,/**/ , , , .F., )  
	
		// Configura Pagina
		oPrint:SetLandscape()   		//SetPortrait() ou SetLandscape()
		oPrint:SetPaperSize(9) 			// Papel A4
		oPrint:SetMargin(60,60,60,60)	// Define margens  
		
		oPrint:StartPage() // Inicia uma nova pagina 
		
	    Titulo() 
	 
		///laco para mostrar as guias
		
		cSQL1->(dbGoTop()) 
		nLin  := 181
		cPrestador:=' '  
	    While cSQL1->(!EOF()) 
	    	If Empty(cPrestador)
		   		cPrestador :=  AllTrim(cSQL1->Prestador) // Codigo RDA   
	    	EndIf
   		 If cPrestador <> cSQL1->Prestador  // Se o prestador atual é diferente do proximo 
				rodape() 
				nLin := 181  		
				oPrint:EndPage() //finaliza a pagina 
				oPrint:StartPage() // Inicia uma nova pagina 
				Titulo()
				nItem := 0 //Total de item da pagina 			
		 EndIf  


			    cDtDigi   := AllTrim(cSQL1->dtdigitacao) //Data digitação
		 		cPaciente := AllTrim(cSQL1->Paciente) //Nome do paciente
		 		cImpresso := AllTrim(cSQL1->guia) //Impresso
				cTipo     := AllTrim(cSQL1->Tipo) //Tido de Guia
		        cPrestador:= AllTrim(cSQL1->Prestador) // Codigo RDA
		        cNomePrest:= AllTrim(cSQL1->Nmprestador)//Nome do RDA
		        cDTRec    := AllTrim(cSQL1->dtrecguia)//Data de recebimento da guia		
	       	                                                                		
		     	oPrint:Box (nLin,015,nLin+20,790 )  // Retangulo linhas 
		    	//1a. coluna - Paciente
		    	oPrint:Say(nLin+ 6,205, "|",oFont12) 
				oPrint:Say(nLin+12,205, "|",oFont12)
				oPrint:Say(nLin+18,205, "|",oFont12)  
				
				//2a. coluna  - Numero Guia 
				oPrint:Say(nLin+ 6,260, "|",oFont12) 
				oPrint:Say(nLin+12,260, "|",oFont12)
				oPrint:Say(nLin+18,260, "|",oFont12) 
				
				//3a. coluna - Tipo guia 
	    		oPrint:Say(nLin+ 6,320, "|",oFont12) 
				oPrint:Say(nLin+12,320, "|",oFont12)
				oPrint:Say(nLin+18,320, "|",oFont12) 			
				
				//4a. coluna - Prestador	 
				oPrint:Say(nLin+ 6,390, "|",oFont12) 
				oPrint:Say(nLin+12,390, "|",oFont12)
				oPrint:Say(nLin+18,390, "|",oFont12)
		    	
		    	//5a. coluna - Nome Prestador 	 
				oPrint:Say(nLin+ 6,590, "|",oFont12) 
				oPrint:Say(nLin+12,590, "|",oFont12)
				oPrint:Say(nLin+18,590, "|",oFont12)
				   
				//6a. coluna - Data Digitação 	 
				oPrint:Say(nLin+ 6,680, "|",oFont12) 
				oPrint:Say(nLin+12,680, "|",oFont12)
				oPrint:Say(nLin+18,680, "|",oFont12)
	
		    	//1a. coluna - Nome do paciente 
		    	auxiliar := cPaciente 	 		  
			   	oPrint:Say(nLin+12,020,auxiliar,oFont9n)  	   		 
		   		
		   		//2a. coluna - Numero guia 
			   	auxiliar :=  cImpresso
			    oPrint:Say(nLin+12,212,auxiliar,oFont9n) 	
				    	
		       	//3a. coluna -Tipo Guia 
		    	auxiliar := cTipo
		       	oPrint:Say(nLin+12,272,auxiliar,oFont9n) 	
				
				//4a. coluna -Codigo do Prestador
				auxiliar := cPrestador	
				oPrint:Say(nLin+12,332,auxiliar,oFont9n) 
					  
			    //5a. coluna - Nome do Prestador 
				auxiliar := cNomePrest		
		   		oPrint:Say(nLin+12,402,auxiliar,oFont9n) 	
		
				//6a. coluna - Data da Digitação 
				auxiliar := cDtDigi	
		   		oPrint:Say(nLin+12,602,auxiliar,oFont9n)  
						
				//7a. coluna -	Data recebimento da guia
				auxiliar := cDTRec 
		   		oPrint:Say(nLin+12,692,auxiliar,oFont9n) 	
				
				nItem :=nItem+1  //Contando os item da pagina - Acrescentado dia 18/12/2019 chamado numero 70855  
				
	        	nLin := nLin + 20 
	        	cPrestador:= AllTrim(cSQL1->Prestador) // Codigo RDA
				cSQL1->(dbSkip()) //pega o proximo 


​						
		EndDo 
		
	//fim do laco que monta  os itens
	 
	cSQL1->( DbCloseArea() ) //fechando  select principal 	
	rodape()
	oPrint:EndPage()	// Finaliza a pagina 		
		
	//oPrint:Setup()		// Setup para usuario 
	oPrint:Preview()	// Visualiza o arquivo antes de imprimir 

   	Else
		MsgAlert('Atenção! Sem dados a exibir.'+cEol+' Verifique!!!') 
   		return()
	EndIf 
	


Return()

Static Function Titulo() //Função de cabeçalho 
     
       // Cabecalho
    	oPrint:SayBitmap (060,200,cBmp1,400,40)    
    	oPrint:Say(120,235, "PROTOCOLO DE ENTREGA DE GUIAS.",oFont12) 
    	
    	oPrint:Say(150,020, "Relação de guias :",oFont12)
      		
    	//cabecalho dos itens 
    	
        oPrint:Box (170,015,290,790 )  // Retangulo cabecalho    
    	    // linha,coluna 
         oPrint:Say(180,020, "Paciente",oFont9n)  		
    	 oPrint:Say(176,205, "|",oFont12) 
    	 oPrint:Say(180,205, "|",oFont12) 
    	
    	oPrint:Say(180,210, "Numero_Guia",oFont9n)   		
        oPrint:Say(176,260, "|",oFont12) 
       	oPrint:Say(180,260, "|",oFont12) 


​		
		oPrint:Say(180,270, "Tipo_Guia",oFont9n)
	    oPrint:Say(176,320, "|",oFont12) 
		oPrint:Say(180,320, "|",oFont12) 


​		
		oPrint:Say(180,330, "Prestador",oFont9n) 		
	    oPrint:Say(176,390, "|",oFont12) 
		oPrint:Say(180,390, "|",oFont12) 


​		
	   	oPrint:Say(180,400, "Nome_Prestador",oFont9n)  	  		
	   	oPrint:Say(176,590, "|",oFont12) 
		oPrint:Say(180,590, "|",oFont12) 


​		
		oPrint:Say(180,600, "Dt_Digitação",oFont9n) 
	    oPrint:Say(176,680, "|",oFont12) 
		oPrint:Say(180,680, "|",oFont12) 


​		
		oPrint:Say(180,690, "Dt_Rec_Guia",oFont9n)

return()     
     

Static Function rodape() //Função de rodape  

    auxiliar :="Total de Guias : "   //Acrescentado dia 18/12/2019 chamado numero 70855   
    oPrint:Say(510,015,auxiliar,oFont12)
    oPrint:Say(510,120,CValtochar(nItem),oFont12) 
    
    auxiliar := "Colaborador Unimed : _______________________________________________________"
    oPrint:Say(550,015,auxiliar,oFont12) 			
    oPrint:Say(550,500,"Data: ___/___/______",oFont12) 		
    	                                      	   				  		
    auxiliar := "Prestador : _______________________________________________________________"
    oPrint:Say(590,015,auxiliar,oFont12) 			
    oPrint:Say(590,500,"Data: ___/___/______",oFont12) 		

return()

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PLSR010   ºAutor  ³Microsiga           º Data ³  05/07/10   º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³Funcao responsavel por gerar perguntas no dicionario de     º±±
±±º          ³dados, caso as mesmas nao existam.                          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function CriaSX1()
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}   

	Aadd( aHelpPor, 'Use vírgula entre os impressos')
	Aadd( aHelpPor, '')
	Aadd( aHelpSpa, '')	
	Aadd( aHelpEng, '')

 


//(cGrupo,cOrdem,cPergunt,cPerSpa,cPerEng,cVar,cTipo ,nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Número Impresso         ?"),"","","mv_ch1","C",84,0,0,"G","","","","","mv_par01",        "","","","",      "","","","","","","","","","","","",aHelpPor,aHelpEng,aHelpSpa,"")	 
PutSx1(cPerg,"02",OemToAnsi("Continuacao Impresso    ?"),"","","mv_ch2","C",84,0,0,"G","","","","","mv_par02",        "","","","",      "","","","","","","","","","","","",aHelpPor,aHelpEng,aHelpSpa,"")	

Return 