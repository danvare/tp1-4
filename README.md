#!/bin/bash

#ayuda para guiar al usuario
function ayuda(){
	echo "Nota: Este script solo fuciona con archivos del tipo texto plano (Por ejemplo, .c, .h, .txt, etc."
	echo "No responde adecuadamente con los del tipo .doc/.docx, .odt, etc"
}

#Validaciones varias
	if [ $# == 1 ]; then
		if [ "$1" == "-h" ] || [ "$1" == "-?" ] || [ "$1" == "-help" ]; then
			ayuda;
			exit 0
		fi
	fi

#Veo si el argumento del directorio es vacio
	if [ -z "$1" ]; then
		echo "esta en blanco"
		ayuda
		exit 1
	fi

#Trata de leer el directorio y sino lo manda a ayuda
	if [ ! $# == 2 ]; then
		echo "error 1"
		exit 1	
	fi

	if [ -z "$2" ]; then
		echo "esta en blanco"
		ayuda
		exit 1
	fi

	if [ "$1" == "analizar"	]; then
		awk '
function evaluarMultiLinea(linea) {
	cantComentadas= cantComentadas + 1;
	if( index(linea, "*/") ) boolSeguirMultilinea= 0 ;}
function evaluarLineaNormal(linea, primerPalabra) {
	if( index(primerPalabra, "//")) cantComentadas= cantComentadas + 1;
	else if( index(linea, "//")) { cantComentadas= cantComentadas + 1; cantCodigos= cantCodigos + 1 ; }
	else if( index(primerPalabra, "/*")) { cantComentadas= cantComentadas + 1; boolSeguirMultilinea= 1 ; }
	else if( index(linea, "/*")) { cantComentadas= cantComentadas + 1; cantCodigos= cantCodigos + 1; boolSeguirMultilinea= 1 ; }
	else cantCodigos= cantCodigos + 1 ; }
BEGIN { boolSeguirMultilinea= 0; cantCodigos= 0; cantComentadas= 0 ;} 
/./	{	if( boolSeguirMultilinea == 1 ) evaluarMultiLinea($0);
		else evaluarLineaNormal($0, $1) ;}
END {	print "Se encontraron ", cantCodigos, " linea(s) de código y", cantComentadas, " de comentario(s)." }' $2
		exit 0
	fi

#veo si existe el directorio existe, sino 
	if [ ! -d "$1" ]; then
		echo "directorio no existe"
		ayuda
		exit 1
	fi
