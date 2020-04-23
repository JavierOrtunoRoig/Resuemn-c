###1. Configuración inicial del entorno
```bash
gcc *.c -o main -I. cabecera && main
```
Poner en el archivo que nos harán crear el mismo nombre que el de la cabecera pero con extencion '.c' y añadir las siguientes cabeceras:
```c
#include <stdio.h>
#include <stdlib.h>
#include "nombre_cabecera.h"
```

#####2. Typedef

Estructura simple:

```c
typedef struct Nodo *TListaJugadores;

struct Nodo {
    unsigned int id;
    unsigned int numeroGoles;
    TListaJugadores sig;
};
```

Estructura anidada:

```c
typedef struct Nodo *LSistema;
typedef struct NodoHebra *ListaHebra;

struct Nodo {
    unsigned int proceso;
    ListaHebra hebras;
    LSistema sig;
};

struct NodoHebra
{
    char hebra[3];
    int prioridad;
    ListaHebra sig;
};

```

#####3. Paso de parametros

:heavy_check_mark:
```c
//Bien hecho, una referencia a la estructura
AnadirProceso(&plan,1)
```
:x:	
```c
//Mal hecho, si quiero editar la estructura al pasarla por parámetro debo de asar una referencia a esa estructura, sino, no se guardarán los cambios que hagamos
AnadirProceso(plan,1)
```

#####4. Manejo de Strings
```c
//Añadir
#include <string.h>

//Métodos
strcmp(); // Compara dos strings, devuelve 0 cuando son iguales
strcpy(); // Copia el segundo string en el primero
```

#####5. Crear "FILE" (CERRAR SIEMPRE)
```c
Los pasos a seguir con los archivos son: 
1.	Declaración.
2.	Abrir el archivo.
3.	Comprobar que no es NULL (todo ha ido bien).
4.	Leer/escribir el fichero.
5.	Cerrar fichero.

FILE *file = fopen(nombre, modo);
//modo ["rb", "wb", "rt", "wb"]
//r+, w+, permite lectura/escritura
//a Abre para escritura al final del archivo
```

#####5.1. Lectura binario
```c
FILE *file = fopen(nombreFich, "rb");

if (file == NULL) {
    perror("No se ha podido leer el archivo");
}

unsigned int dato;
int leidos;
int esID = 0;


//dato, valor que se va a leer, se tiene que pasar por referencia
//sizeof(unsigned int) tamaño de lo que vamos a leer
//1 especifica el número de elementos que se van a leer (dejar siempre en uno)
//leidos retorna el número de ítems leidos.

//!Podriamos poner en el while: feof(fd), hacen lo mismo, paran al terminar de leer
while ((leidos = fread(&dato, sizeof(unsigned int), 1, file)) > 0) {

    if (esID % 3 == 1) {
        insertar(lj, dato);
    }

    esID++;
    printf ("%d ", dato);
}

fclose(file);
```



#####5.2. Escritura binario
```c
FILE *fd;

fd = fopen("ficheroBinario.dat", "wb");

if (fd == NULL) {
    perror("Error abriendo el fichero");
}


int i;

//(mejor descripcion de parametros en transparencias) Valor a escribir - Tamaño de lo que quiero escribir - Numero de veces que escribo - Manejador

for (i = 0; i < 10; i++) {
    fwrite(&i, sizeof(int),1, fd);
}

fclose(fd);
```


#####5.3. Lectura normal
```c
FILE *fd;

printf("Introduce el nombre del fichero: ");
fflush(stdout);

char fichero[30];

scanf("%s", fichero);

fd = fopen(fichero, "rt");

if (fd == NULL) {
    perror("Error creando fichero");
}

int valor;

//Mientras no lleguemos al final del fichero seguimos leyendo

//fgets para leer con espacios

//fgets(&loQueSeQuiereGuardar, int tamañoMaximo, File);
while(fscanf(fd, "%d", &valor) != EOF) {
    printf("%d ", valor);

}
```

#####5.3. Escritura normal
```c
//Tipo fichero

	FILE *fd;

	//Abrir fichero (segundo parametro indicamos el uso)

	fd = fopen("ficheroTexto.txt", "wt");

	if (fd == NULL) {
		perror("Error creando el fichero");
	}


	//Escritura (si no est� creado el fichero se crea, de lo contrario lo reemplaza)

	int i;

	for (i = 0; i<10; i++) {
		fprintf(fd,"%d ",i);
	}

	//Cerrar fichero

	fclose(fd);

```


#####Crear lista

```c
void crear(TListaJugadores *lc) {
    *lc = NULL;
}
```

#####Destruir lista

```c
void destruir(TListaJugadores *ls) {

    TListaJugadores aux = *ls;

    while (*ls != NULL) {
        *ls = (*ls) -> sig;
        free(aux);
    }
}
```



#####Destruir lista Recursivo

```c
void Destruir(TColaPrio *cp) {

    if (*cp != NULL) {
        Destruir(&(*cp) -> sig);
        free(*cp);
        *cp = NULL;
    }
}
```



#####Mostrar lista
```c
void mostrar(TListaJugadores ls) {

    while (ls != NULL) {
        printf("El jugador: %d, ha metido %d goles \n", ls -> id, ls -> numeroGoles);
        ls = ls -> sig;
    }
}
```



#####Mostrar lista recursiva
```c
void Mostrar(TColaPrio cp) {

    if (cp != NULL) {
        printf("(%d:%d) ", cp -> id, cp -> prioridad);
        Mostrar(cp -> sig);
    }
}

```

#####Insertar al final
```c
void Insertar (ListaHebra *ls, char *idhebra, int priohebra) {

    ListaHebra next = *ls;
    ListaHebra nuevo = (ListaHebra) malloc(sizeof(struct NodoHebra));

    strcpy(nuevo -> hebra, idhebra);
    nuevo -> prioridad = priohebra;
    nuevo -> sig = NULL;


    while (next != NULL) {
        next = next -> sig;
    }

    next -> sig = nuevo;
}
```
#####Insertar al principio
```c
void Insertar (ListaHebra *ls, char *idhebra, int priohebra) {

    ListaHebra next = *ls;
    ListaHebra nuevo = (ListaHebra) malloc(sizeof(struct NodoHebra));

    strcpy(nuevo -> hebra, idhebra);
    nuevo -> prioridad = priohebra;

    nuevo -> sig = *ls;
    *ls = nuevo;

}
```



#####Insertar Ordenado
```c
void InsertarOrdeando (ListaHebra *ls, char *idhebra, int priohebra) {

    ListaHebra prev = NULL;
    ListaHebra next = *ls;
    ListaHebra nuevo = (ListaHebra) malloc(sizeof(struct NodoHebra));

    strcpy(nuevo -> hebra, idhebra);
    nuevo -> prioridad = priohebra;
    nuevo -> sig = NULL;

    if (next == NULL) {
        *ls = nuevo;

    } else {

        while (next != NULL && priohebra > next -> prioridad) {
            prev = next;
            next = next -> sig;
        }

        if (prev == NULL) {

            nuevo -> sig = next;
            *ls = nuevo;

        } else if (next == NULL) {
            prev -> sig = nuevo;
        } else {
            prev -> sig = nuevo;
            nuevo -> sig = next;
        }
    }
}
```
