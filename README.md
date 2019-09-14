#!/bin/bash

#Manejo de errores
function errores(){
	echo ""
	echo "Error fatal en la ejecucion del script."
	echo "Por favor, ejecute -help para abrir la ayuda (comandos -h ; -? ; -help)."
	exit 1
}

#Funcion de ayuda, siempre presente para guiar al usuario
function ayuda(){
	echo "Trabajo Practico n°1 - Ejercicio 4"
	echo ""
	echo "Este script realiza un testeo de un archivo para contar la cantidad de lineas del texto que son codigo o comentario."
	echo "Ejemplo del modo de ejecucion para un archivo: ./Ejercicio4 analizar ./nombre archivo"
	echo ""
	echo "Nota: Este script solo fuciona con archivos del tipo texto plano (Por ejemplo, .c, .h, .txt, etc."
	echo "Si ve el error:  awk: cannot oen ./<nombre archivo> Tendria que agregar la carpeta donde se encuentre el archivo; ej:  ./Ejercicio4 analizar ./nombre de carpeta/nombre archivo"
	exit 0
}

#Validaciones de ayuda
	if [ $# == 1 ]; then
		if [ "$1" == "-h" ] || [ "$1" == "-?" ] || [ "$1" == "-help" ]; then
			ayuda;
		fi
	fi

#Veo si el argumento del directorio es vacio
	if [ -z "$1" ]; then
		echo "El primer parametro esta en blanco."
		errores
	fi

#Veo si el argumento del directorio es vacio
	if [ -z "$2" ]; then
		echo "El segundo parametro esta en blanco."
		errores
	fi

#Seccion de analisis
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
BEGIN { cantArchivos= 1; cantLineasTotal= 0; boolSeguirMultilinea= 0; cantCodigos= 0; cantComentadas= 0 ;} 
/./	{	cantLineasTotal= cantLineasTotal + 1; if( boolSeguirMultilinea == 1 ) evaluarMultiLinea($0); else evaluarLineaNormal($0, $1) ;}
END {	print "Se ha analizado", cantArchivos, "archivo(s)."; print "Hay un total de", cantLineasTotal, "linea(s) en total."; print "Se encontraron ", cantCodigos, " linea(s) de código, lo que corresponde a un", cantCodigos/cantLineasTotal*100,"% del total, y", cantComentadas, " lineas de comentario, que corresponde a un", cantComentadas/cantLineasTotal*100,"% del total." }' $2
		exit 0
	fi

#Si el archivo no es leible, lo deribo a errores
	if [ ! -r "$1" ]; then
		echo "El archivo no es leible o la direccion esta incompleta."
		errores
	fi

#veo si existe el directorio existe, sino 
	if [ ! -d "$1" ]; then
		echo "directorio no existe"
		ayuda
		exit 1
	fi
