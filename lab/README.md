# Thread API # 

En esta sección escribiremos algunos programas multi-hilo y usaremos una herramienta específica llamada ```helgrind``` para encontrar problemas en estos programas. 

## Questions ##

1. Primero codifique ```main-race.c```. Examine el código de manera que usted pueda ver (ojalá de manera obvia) un data race en el código. Ahora ejecute ```helgrind``` (al teclear ```valgrind --tool=helgrind main-race```) y vea como este programa reporta los *data races*. ¿Se muestran las líneas de código involucradas?, ¿Qué otra información entrega este programa?

    > Al examinar el código no notamos ninguna fracción que genere un ```data race```, dado a que las secciones críticas estan bajo un **mutex lock**.
    Al ejecutar el comando ```valgrind --tool=helgrind main-race```, podemos notar que efectivamente no hay condiciones de ```data race```.
    ![Solucion punto 1 con mutex](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/1.1.png)

    > Si el código dado no tuviese los **mutex lock**, si se presentarían condiciones de competencia al intentar incrementar la variable ```balance```. En este caso, al ejecutar el comando ```valgrind --tool=helgrind main-race```, podemos notar que se presentan condiciones de ```data race```.
    ![Solucion punto 1 sin mutex](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/1.2.png)

2. ¿Qué ocurre cuando usted elimina una de las líneas que generan problemas en el código? Ahora agrege un lock alrededor de las actualizaciones de la variable compartida, y entonces alrededor de ambas. ¿Qué reporta ```helgrind``` en cada uno de estos casos?

    > Como se evidenció en el punto anterior, al código tener **mutex lock** alrededor de ambas actualizaciones de la variable compartida, no se presenta condición de competencia, ```data race```.
    ![Solucion punto 2 con mutex](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/1.1.png)

3. Ahora observe ```main-deadlock.c```. Examine el código. Este código tiene un problema conocido como deadlock. ¿Puede ver que problema podrá este tener?

    > Tras examinar el código dado ```main-deadlock.c```, encontramos que se presenta un **deadlock** (Interbloqueo), cuando el hilo p1 adquiere el lock de m1 y realiza inmeadiatamente un cambio de contexto; así mismo el hilo p2 adquiere el lock de m2, luego el hilo p2 intenta adquirir el lock de m1 el cual ya se encuentra bloqueado por lo cual cambia de contexto hacia p1; y el hilo p1 procede adquirir el lock de m2, el cual ya se encuentra bloqueado por el hilo p2. Es decir, cada hilo se encuentra a la espera de un recurso que libera el otro hilo.

4. Ahora ejecute ```helgrind``` en este código. ¿Qué reporta helgrind?

    > Helgrind reporta violación en el orden de adquisición de los locks, como se aprecia a continuación:
    ![Solucion punto 4 consola](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/4.1.png)
    ![Solucion punto 4 continuación](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/4.2.png)

5. Ahora ejecute ```helgrind``` en ```main-deadlock-global.c```. Examine el código. ¿Tiene este el mismo problema que ```main-deadlock.c```? ¿Muestra ```helgrind``` el mismo reporte de error? ¿Qué dice esto a cerca de herramientas como ```helgrind```?

    > Al examinar el código podemos notar que este no presenta deadlock debido a que utiliza un mutex en la región crítica, por lo tanto, no presenta el mismo problema que ```main-deadlock.c```. Al ejecutarlo con la herramienta ```helgrind```, aparentemente reporta el mismo tipo de error del punto anterior. Esto dice a cerca de herramientas como ```helgrind```, que no son del todo confiables. La siguiente es la salida en consola al ejecutar el código ```main-deadlock-global.c``` con ```helgrind```.
    ![Solucion punto 5 consola](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/5.1.png)
    ![Solucion punto 5 continuación](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/5.2.png)

6. Ahora observe ```main-signal.c```. Este código usa una variable (```done```) para señalar que el hijo esta hecho y que el padre puede continuar. ¿Por qué este códido es ineficiente? (En que termina el padre dedicando su tiempo, si el hijo toma una gran cantidad de tiempo en completarse).

    > En este código se utiliza una variable de condición (```done```), para señalar que el hijo esta hecho y que el padre puede continuar. Es ineficiente porque si el hijo toma una gran cantidad de tiempo en completarse, el padre no hará nada, estará en una espera activa, gastando tiempo de procesamiento en la CPU (se gasta todo el quantum que le asignen). Esto se debe a que el padre mientras espera al hijo a que cambie la variable de condición, realiza el siguiente código:

    >        while (done == 0)
    >        ;

7. Ahora ejecute ```helgrind``` para este programa. ¿Qué reporta helgrind?, ¿Es correcto el código?

    > El código es correcto en términos de sintaxis, pero genera un data race, dado a que la variable **done** es una variable compartida. Helgrind reporta precisamente esa condición de competencia.
    ![Solucion punto 7 consola](https://raw.githubusercontent.com/linamariaum/thread-api/master/assets/7.png)