<div align="right">
<img width="32px" src="img/algo2.svg">
</div>


# TP1

## Repositorio de Marco Huamani - 95392 - mhcaballero79@gmail.com

- Para compilar y ejecutar, se utiliza el archivo  `makefile` ubicado dentro del proyecto.

---

<br>

## Funcionamiento de *pokemon.c*.

El proceso comienza verificando la  variable *string* que no sea nula y en caso de serlo termina y retorna un puntero nulo.<br>
En el caso que *string* no sea nulo, se reserva memoria para una nueva estructura *pokemon_t* utilizando la función *malloc()*. En caso que *malloc()* devuelva un puntero nulo, el proceso termina y retorna el puentoro nulo.<br> 
```c
pokemon_t *pokemon = malloc(sizeof(pokemon_t));
if (!pokemon)
	return NULL;
```
A continuación, se utiliza la función *sscanf()* para parsear la cadena *string* y guardar sus valores en la estructura *pokemon_t*. Si *sscanf()* no puede leer cuatro valores de la cadena, se libera la memoria reservada para la estructura *pokemon_t* con la función  `pokemon_destruir()` y el proceso termina devolviendo un puntero nulo.<br>
En caso que todo haya ido bien, el proceso devuelve un puntero a la estructura *pokemon_t* recién crada.

Por otro lado tenemos la función `pokemon_copiar()` el cual recibe un *poke* que en caso de ser nulo, el proceso termina y retorna **NULL**. Caso contrario se reserva memoria con *malloc()* para *pokemon* y si la asiganación de memoria es correcta, se copia los atributos de *poke* a *pokemon* y lo retorna. 

<br>

## Funcionamiento de *tp1.c*.

El programa comienza con `hospital_crear_desde_archivo`, que recibe como parámetro el nombre de un archivo que contiene información sobre pokemones y crea una estructura *hospital_t* con los datos obtenidos. <br>
Primero, se abre el archivo en modo lectura (`fopen`) y se comprueba que la apertura haya sido exitosa. Si no es así, se retorna un puntero nulo.<br>
Luego, se reserva memoria para la estructura *hospital_t*, inicializando la cantidad de pokemones y entrenadores en 0, y se reserva espacio para un puntero a *pokemon_t*. Acontinuación se empieza a leer el archivo línea por línea mediante la función `leer_line()`, la cual utiliza la funcion *fgetc()* para leer caracter por caracter. Cada línea leída se utiliza para crear un nuevo pokemon con la función `pokemon_crear_desde_string()`, la cual transforma una cadena de caracteres en una estructura de tipo *pokemon_t*. Si la creación del pokemon falla, se destruye el hospital creado hasta el momento y se retorna un puntero nulo. <br>
Si la creación del pokemon fue exitosa, se añade a la lista de pokemones del hospital y se incrementa la cantidad de pokemones en 1. <br>
Luego, se verifica si el pokemon recién creado pertenece a un entrenador distinto a los que ya se han registrado en el hospital. Para ello, se recorre la lista de pokemones previamente añadidos, comprobando el nombre del entrenadador de cada uno con el del pokemon recíen creado. En caso que no se encuentre coincidencia se suma suma 1 al contador correspondiente. <br> 
Finalmente, se verifica que se haya leído al menos un pokemon del archivo. Si no es así, se destruye el hospital y se retorna un puntero nulo.<br>
Una vez leído todo el archivo, se cierra y se retorna la estructura `hospital_t` creado con todos los datos obtenidos.


Tenemos la función `hospital_a_cada_pokemon()` la cual recibe por parámetro *hospital* y *funcion*, validamos si alguno de ellos son nulos y en caso de serlo se retorna 0. <br>
Como la *funcion* se debe aplicar a cada pokemon teniendo encuenta la salud de los pokemones, a menor salud mayor prioridad. Y es por esto que se crea la función `ordenar_prioridad()`, el cual ordena utilizando el metodo de inserción. Una vez ordenado recorremos con un `for` los elementos del arreglo de pokemones En cada itereación se llama a la función `funcion()` con el pokemon en la posición "i" En casa que retorne *false* se retorna la cantidad de veces que se aplicó dicha función. 

En la función `hospital_aceptar_emergencias()`, recibe por parametro un *hospital* y *pokemones_ambulancia* que son lo que se deben agregar a nuestro *hospital*. Primero verificaremos que nuestros parametros no sean nuelos, en caso de serlo retornamos *-1*. Posteriormente verificamos si la ambulancia está vacia y en caso de estarlo retornamos *0*. <br>
Posteriormente utilizaremos un *realloc* para redimensionar el arreglo de pokemones del hospital, para que tenga suficiente espacio para los que ingresan desde la ambulancia. En caso que *realloc* falle, se llama a la funcion `hospital_destruir()` y devuel *-1*. Caso contrario, se actualiza el puntero del puntero de pokemones del hospital y la cantidad total de pokemones en el hospital. Utilizaremos *for* para agregar los nuevos pokemones a la ambulancia a partir de la posición del viejo total de pokemones. 

<br>

```c
hospital->pokemones[hospital->cantidad_pokemon] = pokemon;
hospital->cantidad_pokemon += 1;
pokemon_t **poke_aux = realloc(
	hospital->pokemones,
	(hospital->cantidad_pokemon + 1) * sizeof(pokemon_t *));
if (poke_aux == NULL) {
	hospital_destruir(hospital);
	fclose(archivo);
	return NULL;
}
hospital->pokemones = poke_aux;
```
<br>

Finalmente tenemos la función `hospital_destruir()` el cual recibe un *hopital* y libera la memoria de mi estructura en capas. Utilizamos un ciclo *for* para recorrer el campo *pokemones* e ir liberando con *free* cada direccion de memoria al que apunta nuestro puntero *pokemones*. Luego liberamos dicho puntero que pertence a *hospital* y finalmente se libera la memoria de la estructura *hospital*.

<br>

```c
void hospital_destruir(hospital_t *hospital)
{
	for (size_t i = 0; i < hospital_cantidad_pokemones(hospital); i++) {
		free(hospital->pokemones[i]);
	}
	free(hospital->pokemones);
	free(hospital);
	hospital = NULL;
}
```