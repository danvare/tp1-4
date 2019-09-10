***No probe nada de lo que esta aca, pero es para basarse****

Crear un archivo script.awk que contenga esto:---------------------------------------------


	function evaluarMultiLinea(linea, cantComentadas, boolSeguirMultilinea)
	{		
		cantComentadas= cantComentadas + 1;
	
		if(index(linea, "*/")){		//			
			boolSeguirMultilinea= "0";
		}		
	}

	function evaluarLineaNormal(linea, primerPalabra ,cantComentadas, cantCodigos, boolSeguirMultilinea)
	{
		if(index(primerPalabra, "//"))   //Si la primera palabra es comtario solamente agrego +1 a la cantidad comentadas
			cantComentadas= cantComentadas + 1;
		else if(index(linea, "//")){		//Si la linea al principio el codigo pero despues se vuelve comentario tengo que contar la linea dos veces (Segun enunciado) 
			cantComentadas= cantComentadas + 1;
			cantCodigos= cantCodigos + 1;			
		}		
		else if(index(primerPalabra, "/*")){ //Si empieza el comentario multilinea al principio de la oración entonces activo el modo de multilinea
			cantComentadas= cantComentadas + 1;
			boolSeguirMultilinea= "1";
		}
		else if(index(linea, "/*")){ //Si empieza el comentario multilinea lunego de una linea de código entonces la cuento dos veces (Segun enunciado)
			cantComentadas= cantComentadas + 1;
			cantCodigos= cantCodigos + 1;
			boolSeguirMultilinea= "1";
		}
		else{
			cantCodigos= cantCodigos + 1;
		}	
	}


	BEGIN { 
		Es_comentario_multiLinea ="0"
		Cantidad_lineas_codigo="0"
		Cantidad_lineas_comentadas="0" 
	} 
	{
		if(Es_comentario_multiLinea == "1")
			evaluarMultiLinea($0, Cantidad_lineas_comentadas, Es_comentario_multiLinea) //Le pasa toda la linea y el contador de comentarios
		else
			evaluarLineaNormal($0, $1, Cantidad_lineas_comentadas, Cantidad_lineas_codigo, Es_comentario_multiLinea) //Le pasa toda la linea y los dos contadores
	}

	END { 
		printf "Se encontraron %d linea(s) de código y %d de comentario(s).", Cantidad_lineas_codigo, Cantidad_lineas_comentadas;
	}





-------------------------------------------------------------------------------------------------------------------------------
