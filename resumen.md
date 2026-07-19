# Proyecto Integrador - Comunicaciones Digitales 2025

Alumno:
- ANTONINO, Tadeo.

Docentes: 
- CORRAL BRIONES, Graciela. 
- AYARDE, Juan Martín.

## Resumen

A continuación se detallan los módulos que componen el trabajo integrador.

1) Diseño de Codificador-Decodificador.

En una transmisión LoRa no se envían bits sueltos por el aire, sino símbolos formados agrupando esos bits en bloques de tamaño SF (Spreading Factor). El SF define tanto el tamaño de cada bloque como la cantidad de símbolos posibles (2^SF). Entonces un codificador agrupa los bits de a SF y forma el símbolo correspondiente (equivalente a una conversión binario-decimal), y un decodificador hace el proceso inverso. El BER se mantiene en 0 porque todavía no hay ni ruido AWGN ni canal multipath que puedan alterar la información entre transmisor y receptor.

2) Diseño de Waveformer - N-Tuple Former.

Los símbolos para poder ser transmitidos deben transformarse en una señal, y en LoRa se usan señales chirp (su frecuencia varía linealmente en el tiempo). El módulo genera una up-chirp base (el símbolo 0) usando los parámetros SF y N=2^SF heredados del Módulo 1, más BW=125 kHz (el ancho de banda estándar de LoRa), Fs=BW (la frecuencia de muestreo mínima que cumple Nyquist, dando exactamente una muestra por bin de frecuencia), y T=N/BW y M=N. Para representar cualquier otro símbolo s, en vez de recalcular la fórmula desde cero, simplemente se rota esa señal s veces hacia la izquierda, esto genera que la up-chirp comience en una frecuencia más alta y, al llegar al tope, "salte" hasta el fondo y continúe subiendo durante el resto de la duración del símbolo. Este proceso se hace en el Waveform Former, y una vez generados los chirps ya pueden ser enviados.

En la recepción trabaja el proceso opuesto, el n-Tuple Former, cuyo trabajo es tomar los chirps que le llegan, determinar qué símbolo es, y a partir de ahí regenerar los bloques de SF bits que lo componen. Para eso sigue tres pasos: primero el dechirping, que consta de multiplicar la señal recibida por el down-chirp (el conjugado del up-chirp base), lo que cancela la rampa de frecuencia y da como resultado un tono puro; luego se le aplica la FFT para llevarlo al dominio de la frecuencia, donde se observa un pico en un bin específico; y por último se busca el argmax, que devuelve directamente el símbolo porque coincide con ese bin.

3) Implementación del ruido del canal.

Primero implementamos una función que, dado un SNR objetivo, agrega ruido AWGN a una señal en 3 pasos: mide la potencia de la señal, calcula la potencia de ruido necesaria para ese SNR, y genera ruido gaussiano complejo con esa potencia. Verificamos que la función es correcta comparando el SNR medido contra el pedido para varios valores. Después, visualizamos cómo un símbolo puntual (el 45) se degrada progresivamente con distintos niveles de SNR, tanto en el dominio del tiempo como en la FFT post-dechirping — mostrando que en SNR alto el pico es claramente dominante, mientras que en SNR muy bajo el ruido puede competir con él. Finalmente, se hace un barrido sistemático: para 31 valores de SNR entre −15 y +15 dB, simulamos la transmisión completa de símbolos aleatorios y medimos la BER resultante, comparando la curva obtenida contra la fórmula teórica de M-FSK no coherente citada en el paper de Vangelista

4) Implementación de un canal selectivo en frecuencia.

Venimos del Módulo 3, donde agregamos ruido AWGN al canal, y ahora se quiere modelar cómo funciona el sistema cuando además hay multipath: en la vida real, la señal rebota en paredes, techos y objetos, y llega al receptor por varios caminos con distinto retardo y atenuación. Para eso definimos el canal como su respuesta al impulso `h`, un vector donde cada índice representa un retardo en muestras y el valor complejo guardado ahí combina magnitud (cuánto se atenuó ese camino) y fase (cuánto se desfasó su ciclo respecto al camino directo); aplicar el canal a una señal es simplemente convolucionarla con `h`. Primero visualizamos la respuesta en frecuencia del canal (la FFT de `h`): un canal plano (un solo camino, sin multipath) da una línea recta en 1 porque no distorsiona ninguna frecuencia, mientras que un canal con varios caminos da picos y valles, porque el mismo retardo físico produce un desfase distinto según la frecuencia — cuando ese desfase se acerca a 0°/360° las copias se refuerzan (pico), y cuando se acerca a 180° se cancelan (valle), lo cual es la selectividad en frecuencia. Después convolucionamos cada chirp con `h` para aplicar el canal y visualizamos su efecto sobre la forma de onda. Por último armamos el canal completo (multipath + AWGN) e hicimos un barrido BER vs SNR para compararlo contra el canal solo-AWGN del Módulo 3 y contra la curva teórica M-FSK, confirmando que LoRa tolera razonablemente bien la selectividad en frecuencia gracias a que el chirp barre todo el ancho de banda.

5) Implementación de LORA en SDR.

    - Transmisión y detección de símbolos LORA con un solo SDR (loopback digital y loopback por antena)

    > Segundo paper

    - Preambulo y detector de trama

    - Alineación de ventana

    - Peak Merging (Combinación de picos)

    - Recuperación de portadora

    - Recuperación de tiempo

    - Curvas de BER vs. tiempo con 2 SDRs y transmisión-recepción de un mensaje: ¡Hola Com Dig!
