# Hito 100.000 bookings: celebración para la TV

Prototipo de diseño para la TV de KPIs del Marketplace (CHRONOS-2086). 28-10-2026.

## Cómo verlo

https://carolinaaranibar.github.io/tv-hito-100k/?demo

Aprieta "Probar 100.000" (el clic habilita el sonido). Tecla H repite, M silencia, Esc corta. Debajo
corre la v57 del rediseño solo como fondo, con cifras inventadas: los números reales los pone la TV.

## Qué hace

Alerta a pantalla completa de unos 42 s, a 128 BPM, y vuelve sola al tablero.

| Segundo | Fase | Qué pasa |
|---|---|---|
| 0 | Alerta | Sirena de dos tonos, banda ALERTA con balizas, viñeta roja que late |
| 3,75 | Subida | Redoble y barrido; la cifra cuenta de 99.900 a 100.000 y cae justo en el drop |
| 7,5 | Drop 1 | Bombo, bocinas, golpe blanco, láseres, bola de espejos, público, gifs y confeti |
| 22,5 | Quiebre | Pads sin bombo; los gifs se quedan |
| 26,25 | Drop 2 | Igual, con arpegio |
| 37,5 | Salida | El filtro se cierra, los gifs salen, última ráfaga de confeti |
| 41,25 | Fundido | 1,2 s y vuelve el tablero |

## Para incorporarlo

1. **Disparo único y compartido.** Un evento cuando el acumulado del año de Bookings Chile cruce
   100.000, por el canal `tv-dashboard` de las celebraciones (V3), para que todas las TVs lo
   muestren a la vez. Idempotente: una sola vez (por ejemplo `dedup_key = hito-100k-2026` en
   `tv_events`). En el prototipo lo simula `window.TVHito.alCambiarTotal(total)` con
   `localStorage`; en producción conviene que lo decida el servidor. Necesita el acumulado del año,
   el mismo dato que falta para el bloque Año.
2. **Sonido.** Chrome bloquea el audio si nadie tocó la página. La TV tiene que abrir Chrome con
   `--autoplay-policy=no-user-gesture-required`. Sin eso, la animación corre igual y aparece un
   aviso para tocar la pantalla.
3. **Música.** Se genera en el navegador con Web Audio (funciones `song()` y los instrumentos del
   script). Es una composición original: no hay archivo de audio ni licencia.
4. **Gifs.** Usa la misma bolsa de `config('tv.gifs')` con el mismo shuffle bag (Fisher-Yates, sin
   repetir el último al rebarajar). En el prototipo la lista está copiada en `GIFS`. Diez lugares
   fijos alrededor de la cifra (`SLOTS`); entra uno cada dos pulsos y reemplaza al más antiguo.
5. **Qué portar.** El bloque `#hito` de `index.html` (marcado, CSS y script) encima de la página de
   la TV, en vez del iframe. Al recibir el evento, `TVHito.celebrar()`.

## Límites que conviene mantener

- Fotosensibilidad (WCAG 2.3.1, máximo 3 destellos por segundo): el estrobo blanco y la viñeta
  roja van cada dos pulsos (1,07 por segundo). Ningún efecto pasa de 2 en un segundo.
- `prefers-reduced-motion`: sin estrobo, sin sacudida, sin confeti y sin rebotes; la música sigue.
- Equipos con poca memoria (`navigator.deviceMemory <= 4`): el confeti baja al 40 %.
- Todo el movimiento anima solo `transform` y `opacity`.
