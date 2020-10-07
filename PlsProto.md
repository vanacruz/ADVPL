#include "protheus.ch" 
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


#DEFINE  cEOL CHR(13)+CHR(10)

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma  ³PlsProto ºAutor  ³Vanessa        º Data ³    05/03/20       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³Tela para cadastro de guias para Protocolo                  º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/      


User Function PLSPROTO()  //Funcao de usuário maximo 8 caracteres  

//Variaveis para  tratar a inserção das guias:

Private cGuias:=" " 
Private cNnPac:=" "
Private Ntipo:=" "
Private cRDA :=" "
Private dtrec:=" " 
Private dtdig:=" " 
Private cTipo:=" "


 //Tratando inserção :

Private cPerg:= "PLSPROTB" //Aqui é case sensitive  


 if !Pergunte(cPerg,.T.)
       Limpar() //Limpa perguntas
       return()
 endif  

 cGuias := mv_par01 
        
 Existe(cGuias) //Verificando se já não existe na tabela z3z010

 Limpar() //Limpa perguntas   

  if !Pergunte("PLSPROTI",.T.) //Imprimir ? Sim /Nao  
	  return()     
  Else 
       NImp :=mv_par01
       
      If NImp==1 // Deseja imprimir = Sim           
      Processa({|lEnd|Imprimir()}) //Função para montar o relatório 
     
      Else
       U_PLSPROTO()//chama novamente a rotina  
      Endif
  Endif
          
Return     

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  ³Vanessa Cruz          º ³Data   17/10/18  º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Insert                                                     º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

//Função para incluir guias na tabela de protocolo (z3z010)

Static Function InsGuia(cRDA,cNnPac,ctipo,dtrec,dtdig)  //Inserindo guia     
    
            
Z3Z->(RecLock("Z3Z",.T.)) // Permite a inclusão ou alteração de um registro no alias informado

Z3Z->Z3Z_FILIAL:=' '
Z3Z->Z3Z_IMP    := alltrim(cGuias)   // Impresso
Z3Z->Z3Z_NOME   := UPPER(alltrim(cNnPac)) //convertendo para Maiusculo - Nome do paciente
Z3Z->Z3Z_TIPGUI := ctipo       //Descrição do Tipo de Guia - z3z_Tipo = Caracter   
Z3Z->Z3Z_PREST  := cRDA    //codigo do Prestador
Z3Z->Z3Z_NMPRES := BAU->(Posicione("BAU",1,xFilial("BAU")+cRDA,"BAU_NOME"))//Nome do Prestador 
z3Z->Z3Z_DTREC  := dtrec
z3Z->Z3Z_DTDIGI := dtdig

Z3Z->(MsUnLock())//Libera o travamento / bloqueio (lock) do registro posicionado no alias corrente,

//Alert("Concluido")

cMensagem := "Guia incluída,para impressao em protocolo." + chr(13)+chr(10) 

MSGINFO(cMensagem) 

Limpar() 

Return //fechando função 

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  ³Vanessa Cruz          º ³Data   23/03/20          º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Função para limpar as perguntas                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/


Static Function Limpar()  //Limpa as 03 perguntas ao final do processo. 

dbSelectArea( "SX1" ) //Posiciona na Sx1
dbSetOrder(1) //Dentro da SX1 na Ordem 1 (x1_Grupo+x1_Ordem)
IF dbSeek('PLSPROTO  01') //x1_Grupo + x1_Ordem //O comando dbSeek faz uma busca no banco de dados através de um índice existente.
	while alltrim(SX1->X1_GRUPO) == 'PLSPROTO' 
		RecLock("SX1",.F.) 
			SX1->X1_CNT01 := ' ' //Limpa o parametro 
		MsUnlock()
		SX1->(dbskip()) //Vai para o  proximo registro
	enddo
EndIF     

dbSelectArea( "SX1" ) //Posiciona na Sx1
dbSetOrder(1) //Dentro da SX1 na Ordem 1 (x1_Grupo+x1_Ordem)
IF dbSeek('PLSPROTB  01') //x1_Grupo + x1_Ordem //O comando dbSeek faz uma busca no banco de dados através de um índice existente.
	while alltrim(SX1->X1_GRUPO) == 'PLSPROTB' 
		RecLock("SX1",.F.) 
			SX1->X1_CNT01 := ' ' //Limpa o parametro 
		MsUnlock()
		SX1->(dbskip()) //Vai para o  proximo registro
	enddo
EndIF   
    
dbSelectArea( "SX1" ) //Posiciona na Sx1
dbSetOrder(1) //Dentro da SX1 na Ordem 1 (x1_Grupo+x1_Ordem)
IF dbSeek('PLSPROTC  01') //x1_Grupo + x1_Ordem //O comando dbSeek faz uma busca no banco de dados através de um índice existente.
	while alltrim(SX1->X1_GRUPO) == 'PLSPROTC' 
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
±±ºPrograma    ºAutor  ³Vanessa Cruz          º ³Data   17/10/18           ±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Função Checar Impresso se existe na tabela Bea010          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

//Função para Verificar se a guia já existe como RDA 0002


Static Function Checar(cGuias)  

    Local QRY01:=' '    
    
    QRY01 := "select substr(bea_dtdigi,7,2)||'/' ||substr(bea_dtdigi,5,2)||'/'||substr(bea_dtdigi,1,4) as dtdigi,  " 
    QRY01 += "substr(bea_nomusr,1,45) as Paciente,   " 
    QRY01 += "bea_numimp as guia,  " 
    QRY01 += "bea_tipgui as  Tipo,  " 
    QRY01 += "bea_codrda as Prestador,  " 
    QRY01 += "substr(bea_nomrda,1,45) as Nmprestador,  "  
    QRY01 += "case when bea_datsol = ' ' then  " 
    QRY01 += "(select  substr (be4_yreceb,7,2)||'/' ||substr(be4_yreceb,5,2)||'/'||substr(be4_yreceb,1,4)  " 
    QRY01 += "from siga.be4010 where d_e_l_e_t_ = ' '   " 
    QRY01 += "and be4_numimp = bea_numimp)  " 
    QRY01 += "else substr(bea_datsol,7,2)||'/' ||substr(bea_datsol,5,2)||'/'||substr(bea_datsol,1,4) end as dtrecguia   " 
    QRY01 += "from siga.bea010  " 
    QRY01 += "where bea_filial = ' ' and  bea010.d_e_l_e_t_ = ' '  " 
    QRY01 += "and  bea_numimp in ('"+cGuias+"') "
    QRY01 += "order by  bea_codrda,bea_numimp "
    
    PlsQuery(QRY01,'TMP') 
      
    If !TMP->(Eof())  // A guia existe, agora vamos verificar o RDA 
        RDA()     	   
    Else 					   
     TMP->(DbCloseArea()) //A guia não existe - Fecha tabela do select - será necessário incluir  
     
       if !Pergunte("PLSPROTO",.T.) //chama todas as demais perguntas
           Limpar()
          return( )//cancelou  a pergunta 
       Else 
           // Atribui valores : 
    	 cNnPac:=mv_par01 //Nome do Paciente(pergunta: PLSPROTO)
    	 Ntipo:= mv_par02 //Tipo da guia    (pergunta: PLSPROTO)
    	 cRDA := mv_par03 //Codigo do RDA   (pergunta: PLSPROTO)
         dtrec:= mv_par04 //Data recebimento(pergunta: PLSPROTO)
         dtdig:= mv_par05 //Data Digitação  (pergunta: PLSPROTO)
             
        if !Empty(cNnPac).And. !Empty(Ntipo).And. !Empty(cRDA).And. !Empty(dtrec).And.!Empty(dtdig) //A pergunta PLSPROTO  esta toda preenchida.
        if (Ntipo==01 .or. Ntipo==1)
   		    ctipo:='Consulta'
        else   
           if(Ntipo==02 .or. Ntipo==2) 
              ctipo:='SP/SADT'
           else   
              if(Ntipo==03 .or. Ntipo==3) 
                ctipo:='Internacao'
             Endif
          Endif    
        Endif 
           InsGuia(cRDA,cNnPac,ctipo,dtrec,dtdig)  //Inserindo guia                                                                                                                             
        Endif  	 
       Endif
	Endif 
return()

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  ³Vanessa Cruz          º ³Data   17/10/18           ±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Função Checar Impresso se existe na tabela Bea010          º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function Existe(cGuias)  

Local cQuery :=' ' 
Local cMensagem:=' '

    cQuery := "select * " 
    cQuery += "from " + RetSqlName('Z3Z') + " "
    cQuery += "where z3z_filial = ' ' and  " + RetSqlName('Z3Z') +".d_e_l_e_t_=' ' and z3z_imp="+cGuias+" " 
    cQuery += "order by  z3z_prest,z3z_imp
    PlsQuery(cQuery,'cSQL')  


​	 
	  If !cSQL->(Eof()) //Se Achou 
	    cMensagem := "Esta guia já foi digitada." + chr(13)+chr(10) 
	    MSGINFO(cMensagem)
	    cSQL->(DbCloseArea()) //Fecha    
	    U_PLSPROTO()//chama novamente a rotina    
	  Else
	      cSQL->(DbCloseArea()) //Fecha
	      Checar(cGuias)  //Verificando se já existe ( bea010)     
	  Endif              
return()

/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  ³Vanessa Cruz          º ³Data   17/10/18           ±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Função RDA- Verifica se o RDA é 0002 ou outro              º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/
Static Function RDA () 

 If TMP->Prestador=="000002"

     if !Pergunte("PLSPROTC",.T.) //Quem é o RDA desta guia ?
          Limpar() //Limpar pergunta
          return()//cancelou  a pergunta
     endif 

   	     cRDA:= mv_par01  //Codigo do RDA  (pergunta: PLSPROTC)
		 cNnPac:= TMP->Paciente
		 Ntipo:= TMP->Tipo
         dtrec:= CTOD(TMP->dtrecguia)
	     dtdig:= CTOD(TMP->dtdigi)
		 TMP->(DbCloseArea()) //Fecho o select que procura a guia 
		 
		   if !Empty(cNnPac).And. !Empty(Ntipo).And. !Empty(cRDA).And. !Empty(dtdig) //A pergunta PLSPROTO  esta toda preenchida. 
		          if Ntipo=='01'   
	                 ctipo:='Consulta'
		          else   
	                if Ntipo=='02'  
	                  ctipo:='SP/SADT'
	                else   
	                   if Ntipo=='03'   
	                     ctipo:='Internacao'
	                   Endif
	               Endif    
	            Endif     
	           InsGuia(cRDA,cNnPac,ctipo,dtrec,dtdig)  //Inserindo guia                                                                                                                               
	       endif  
		 return()	 
  Endif 
  If TMP->Prestador<>"000002" // O prestador não é RDA 00002  

   	     cRDA:=TMP->Prestador
		 cNnPac:=TMP->Paciente
		 Ntipo:=TMP->Tipo
         dtrec:=CTOD(TMP->dtrecguia)
	     dtdig:=CTOD(TMP->dtdigi)
		 TMP->(DbCloseArea())//Fecho o select que procura a guia  
		  if !Empty(cNnPac).And. !Empty(Ntipo).And.!Empty(cRDA) .And. !Empty(dtdig) //A pergunta PLSPROTO  esta toda preenchida.  
		    if Ntipo=='01'   
               ctipo:='Consulta'
		    else   
               if Ntipo=='02'  
                 ctipo:='SP/SADT'
             else   
                if Ntipo=='03'   
                   ctipo:='Internacao'
                Endif
               Endif    
            Endif     
               InsGuia(cRDA,cNnPac,ctipo,dtrec,dtdig)  //Inserindo guia                                                                                                                               
           endif  	  
		 return()
		 
 Endif

return   
/*
ÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜÜ
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
±±ÉÍÍÍÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍËÍÍÍÍÍÍÑÍÍÍÍÍÍÍÍÍÍÍÍÍ»±±
±±ºPrograma    ºAutor  ³Vanessa Cruz          º ³Data   23/03/20           ±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³ Função Para imprimir as guia                               º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß
*/

Static Function Imprimir()  

#DEFINE  cEOL CHR(13)+CHR(10)

Local cDtDigi:=" "   
Local cPaciente:=" " 
Local cImpresso:=" " 
Local cTipo:=" "     
Local cPrestador:=" "
Local cNomePrest:=" "
Local cDTRec:=" "  



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

//Variaveis para  tratar a impressão das guias:
Private QRY01   := " " 
Private lValida := .T.
Private cNomArq := "Protocolo de Entrega de Guias_"+ DTOS( Date() ) +'_'+ StrTran( Time() , ':' , '' )  

// Imagens do relatorio      

Private cBmp1 := "topo2.Bmp" 
Private oPrint
Private nItem :=0  //Variável para contar guias



// Fontes para impressao do relatorio
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


	//selecionando a(s) Guia(s):
	
	QRY01 := "select substr(z3z_dtdigi,7,2)||'/' ||substr(z3z_dtdigi,5,2)||'/'||substr(z3z_dtdigi,1,4) as dtdigitacao,  " 
	QRY01 += "substr (z3z_nome,1,45) as Paciente,   " 
	QRY01 += "z3z_imp as guia,  " 
	QRY01 += "z3z_tipgui as  Tipo,  " 
	QRY01 += "z3z_prest as Prestador,  " 
	QRY01 += "substr (z3z_nmpres,1,45) as Nmprestador,  "  
	QRY01 += "substr(z3z_dtrec,7,2)||'/' ||substr(z3z_dtrec,5,2)||'/'||substr(z3z_dtrec,1,4) as dtrecguia "
	QRY01 += "from siga.z3z010  " 
	QRY01 += "where z3z_filial = ' ' and  z3z010.d_e_l_e_t_ = ' ' and z3z_saiu<>'SIM' " 
	QRY01 += "order by  z3z_prest,z3z_imp "
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
		    cTipo    := AllTrim(cSQL1->Tipo) //Tido de Guia
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
				
	       //marcando na tabela z3z010 que aquela guia foi impressa
				
		        Update := " Update " + RetSqlName('Z3Z') + " "
		      	Update += " set z3z_saiu='SIM'"+" "    
	            Update += " where z3z_filial =' ' and " + RetSqlName('Z3Z') +".d_e_l_e_t_=' ' and z3z_saiu<>'SIM' "
	            Update += " and z3z_imp="+cImpresso+" " 
	            
	            TCSQLEXEC(Update) 
						
		EndDo 
		
	//fim do laco que monta  os itens
	 
	cSQL1->( DbCloseArea() ) //fechando  select principal 	
	rodape()
	oPrint:EndPage()	// Finaliza a pagina 		
		
	//oPrint:Setup()		// Setup para usuario 
	oPrint:Preview()	// Visualiza o arquivo antes de imprimir 

   	Else
		MsgAlert('Atenção! Sem dados para imprimir.'+cEOL+' Verifique!!!') 
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
±±ºPrograma  ³CriaSX1   ºAutor  Vanessa          º Data ³  05/10/18       º±±
±±ÌÍÍÍÍÍÍÍÍÍÍØÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÊÍÍÍÍÍÍÏÍÍÍÍÍÍÍÍÍÍÍÍÍ¹±±
±±ºDesc.     ³  Criando os parametros necessários para a rotina.          º±±
±±º          ³  Serão informados pelo usuário:                            º±±
±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±±
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß

Static Function CriaSX1() //No protheus 12 não existe mais estsa função: 

Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


//Pergunta 1 - PLSPROTB


//(cGrupo,  cOrdem,cPergunt,cPerSpa,                  cPerEng,cVar, cTipo,  nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Numero impresso ")      ,"","", "mv_ch1","C",  12,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{}) 

return 


Static Function CriaSX1() 
Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  

//Pergunta 2 - PLSPROTO

//(cGrupo,  cOrdem,cPergunt,cPerSpa,                  cPerEng,cVar, cTipo,  nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Nome do cliente ")      ,"","", "mv_ch1","C",  60,0,0,"G","","","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"02",OemToAnsi("Tipo de guia")          ,"","", "mv_ch2","N",  1,0,0,"C","","","","","mv_par02","Consulta","","","","Sp/SADT","","", "Internacao","","","","","","","","",{},{},{})
PutSx1(cPerg,"03",OemToAnsi("Codigo do prestador")   ,"","", "mv_ch3","C",  06,0,0,"G","","RDA","","","mv_par03","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"04",OemToAnsi("Data recebimento Guia"),"","",  "mv_ch4","D",  08,0,0,"G","","","","","mv_par04","","","","","","","","","","","","","","","","",{},{},{})
PutSx1(cPerg,"05",OemToAnsi("Data Digitação Guia ")  ,"","", "mv_ch5","D",  08,0,0,"G","","","","","mv_par05","","","","","","","","","","","","","","","","",{},{},{})
return 

Static Function CriaSX1() //No protheus 12 não existe mais estsa função: 

Local aHelpPor := {}
Local aHelpSpa := {}
Local aHelpEng := {}  


//Pergunta 3) PLSPROTC

//(cGrupo,  cOrdem,cPergunt,cPerSpa,                  cPerEng,cVar, cTipo,  nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Codigo do prestador")   ,"","", "mv_ch1","C",  06,0,0,"G","","RDA","","","mv_par01","","","","","","","","","","","","","","","","",{},{},{})

return

//Pergunta 4)PLSPROI - imprimir guias ?
                        
 //(cGrupo,  cOrdem,cPergunt,cPerSpa,                  cPerEng,cVar, cTipo,  nTamanho,nDecimal,nPresel,cGSC,cValid,cF3, cGrpSxg,cPyme,cVar01,cDef01,cDefSpa1,cDefEng1,cCnt01,cDef02,cDefSpa2,cDefEng2,cDef03,cDefSpa3,cDefEng3,cDef04,cDefSpa4,cDefEng4,cDef05,cDefSpa5,cDefEng5,aHelpPor,aHelpEng,aHelpSpa,cHelp)
PutSx1(cPerg,"01",OemToAnsi("Imprimir impressos cadastrados?")   ,"","", "mv_ch1","N",  1,0,0,"C","","","","","mv_par01","SIM","","","","Nao","","","","","","","","","","","",{},{},{})

*/