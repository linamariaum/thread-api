# Introducción al thread API #

## Parte teórica ##

Lea y haga un resumen de una hoja (ni más ni menos) del capítulo  [Interlude: Thread API](http://pages.cs.wisc.edu/~remzi/OSTEP/threads-api.pdf) del libro OSTEP de Remzi.

    > Apuntes:
    
    ![Apuntes acerca de Thread API](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/Notes_Thread-API.jpg)

## Parte práctica ##

Los siguientes ejemplos (tomados del siguiente [enlace](https://github.com/remzi-arpacidusseau/ostep-code/tree/master/threads-api)) muestran como usar el API POSIX para hilos:
1. ```thread_create.c```: Programa simple para la creación de un hilo donde los argumentos son pasados al hilo desde línea de comandos.
2. ```thread_create_with_return_args.c```: programa que retorna valores desde un hilo hijo a el hilo padre.
3. ```thread_create_simple_args.c```: Modo simple de paso de valores de argumento y retorno (para los más holgazanes).

Ejecute el makefile; cada archivo llamado foo.c genera un ejecutable con foo el cual usted puede ejecutar; por ejemplo:

```bash
prompt> ./thread_create
```

**Nota**: Esta se analizará en clase.
