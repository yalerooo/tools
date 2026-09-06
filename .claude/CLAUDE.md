# Listas Acestream (list1 / list2)

Este repo contiene dos listas M3U de canales Acestream, guardadas **sin extensión**
(`list1` y `list2`). Deben permanecer sin extensión siempre — no renombrar a `.m3u`.

## Diferencia entre list1 y list2

Son idénticas en contenido (mismos canales, mismo orden, mismos `id` de stream).
La única diferencia es el host del engine Acestream en la URL:

- `list1` → `http://127.0.0.1:6878/ace/getstream?id=...`
- `list2` → `http://192.168.68.110:6878/ace/getstream?id=...`

**Cualquier cambio (añadir, borrar, reordenar) debe aplicarse a ambas listas por
igual**, cambiando solo el host correspondiente en la URL.

## Formato de cada canal

Cada canal ocupa dos líneas:

```
#EXTINF:-1 tvg-logo="URL_LOGO" group-title="GRUPO" tvg-id="ID_EPG",NOMBRE VISIBLE
http://HOST:6878/ace/getstream?id=STREAM_ID
```

- `tvg-logo`: URL de la imagen del canal (puede quedar vacío `""` si no hay logo).
- `group-title`: categoría del canal dentro del reproductor (ver lista de grupos abajo).
- `tvg-id`: identificador usado para casar la programación (EPG) contra `spain1.xml`.
  Debe coincidir **exactamente** (mayúsculas, tildes, espacios) con un
  `<channel id="...">` de `spain1.xml`. Si el canal no existe en `spain1.xml`,
  se deja el `tvg-id` tal cual (sin EPG, no pasa nada).
- Nombre visible: va después de la coma en `#EXTINF`. Las resoluciones se escriben
  siempre como `1080p` / `720p` (con la "p"), nunca `1080` / `720` a secas.

## Convención de "alt"

Cuando hay varios enlaces distintos para el mismo canal/resolución (backups,
servidores alternativos, etc.), se numeran así:

- El primero **no lleva sufijo**: `DAZN Liga 1080p`
- El segundo: `DAZN Liga 1080p alt 2`
- El tercero: `DAZN Liga 1080p alt 3`
- Y así sucesivamente (`alt 4`, `alt 5`, ...)

Formato exacto: `<nombre base> alt <n>` (con espacios, "alt" en minúscula).

## Estructura del archivo (secciones)

Los canales están agrupados en bloques separados por un comentario de cabecera:

```
############################################################
# NOMBRE DE LA SECCIÓN
############################################################
```

Y dentro de una sección, si hay varios proveedores/subgrupos, un sub-comentario:

```
# --- NOMBRE DEL SUBGRUPO ---
```

Entre cada grupo de canales del mismo nombre base (mismo canal + sus alt) se deja
una línea en blanco para que sea fácil de leer de un vistazo.

Orden actual de secciones (de arriba a abajo) y su `group-title`:

1. **LALIGA - PRIMERA DIVISION** (`group-title="Liga"`) — subgrupos `DAZN`, `MOVISTAR PLUS+` y `RFEF (FEDERACIÓN)`
2. **LALIGA HYPERMOTION - SEGUNDA DIVISION** (`group-title="LaLiga Hypermotion"`)
3. **UEFA CHAMPIONS LEAGUE** (`group-title="Champions"`)
4. **BALONCESTO** (`group-title="Baloncesto"`) — subgrupos `DAZN`, `MOVISTAR PLUS+`, `ACB - EVENTOS`
5. **DAZN - CANALES GENERALES** (`group-title="DAZN"`) — subgrupos `CANALES DAZN 1-4`, `FORMULA 1`, `MOTOGP`, `ELEVEN SPORTS`
6. **MOVISTAR - #VAMOS** (`group-title="Movistar"`)
7. **DEPORTES** (`group-title="Deportes"`) — subgrupos `MOVISTAR PLUS+`, `OTROS` e `INTERNACIONAL`
8. **EUROSPORT** (`group-title="Eurosport"`)
9. **CANALES GENERALES** (`group-title="General"`) — solo TV generalista española: Antena 3, Aragón TV, Canal Cocina, Cuatro, Discovery, La Sexta, La1, La2, Movistar Plus+

**Regla de coherencia de grupos:** `group-title` agrupa por temática real, no por dónde
pegó el usuario el enlace. En concreto:
- Canales de fútbol de la federación (Primera Federación, RFEF TV, y cualquier
  competición RFEF futura) van en `group-title="Liga"`, subgrupo `RFEF (FEDERACIÓN)`
  al final de la sección LaLiga — no en "General".
- Canales de deporte genérico/internacional sin competición propia (BeIN Sports,
  BT Sport, ESPN Argentina/Premium, Fox Sports, FSN, Esports3, Golplay, etc.) van
  en `group-title="Deportes"`, subgrupo `INTERNACIONAL` — no en "General".
- "General" se reserva para TV generalista real (autonómicas, cadenas nacionales
  no deportivas). Si llega un canal nuevo que no es deportivo ni de LaLiga/Champions/
  Baloncesto/etc., ahí es donde va. Si aparece una categoría nueva con varios
  canales que no encaja en ningún grupo existente, crear un `group-title` nuevo
  en vez de forzarlo dentro de "General".

> La sección **MUNDIAL 2026** (DAZN Mundial 1-4 + M+ Fanzone) existió pero se
> eliminó por completo a petición del usuario. Si en el futuro se vuelve a pedir
> añadir canales del Mundial, recrear la sección entre Eurosport y Canales
> Generales con `group-title="Mundial"`.

Al añadir un canal nuevo, colócalo en la sección/subgrupo que le corresponda por
temática, no por el orden en que el usuario lo pega en el chat.

## Reglas para actualizar las listas (instrucciones del usuario)

Cuando el usuario pida cambios en este chat:

- **"Borra todos los de X y añade estos nuevos"** → eliminar TODOS los canales
  existentes de esa categoría/canal (en ambas listas) y añadir los nuevos en su
  lugar, renumerando el "alt" desde 1 para los que queden.
- **"Añade estos"** (sin pedir borrar nada) → añadir los nuevos enlaces dentro de
  la misma sección/subgrupo donde ya están los canales con ese nombre, continuando
  la numeración "alt" existente (si el último era `alt 3`, el nuevo es `alt 4`).
- Siempre aplicar el cambio a **list1 y list2** (mismo `id` de stream, solo cambia el host).
- Si el canal nuevo tiene un `tvg-id` que existe en `spain1.xml`, usar ese id exacto
  para que case la programación. Si no existe, usar un `tvg-id` razonable (nombre
  del canal + `.es` si es un canal español) aunque no tenga EPG todavía.
- Aplicar siempre la corrección de resolución (`1080p`/`720p`) y el formato de
  comentarios de sección si se añade una categoría nueva que aún no existe.
- Los enlaces que pega el usuario suelen traer emojis/decoraciones en el nombre
  (banderas, números en círculo, etc.) — **quitarlos siempre**, el nombre visible
  final debe seguir el estilo limpio del resto de la lista.
- Si al reemplazar un canal (ej. "borra todos los de X y añade estos") faltan
  variantes que antes existían (p.ej. no viene un 4K y antes sí lo había), esa
  variante simplemente desaparece — no preguntar, no inventar un enlace de relleno.
- Si un enlace que pega el usuario no trae la resolución indicada en el nombre,
  asumir siempre **1080p** — no preguntar.

## spain1.xml

Es la guía EPG (`url-tvg` en la cabecera `#EXTM3U`). Contiene `<channel id="...">`
y `<programme channel="...">` para la programación. Se usa solo como referencia
para validar/completar el `tvg-id` de los canales — no se edita como parte de este
flujo de trabajo salvo que el usuario lo pida explícitamente.

**No se guarda una copia local en el repo.** Cuando haga falta comprobar o buscar
un `tvg-id`, consultarlo directamente desde
`https://www.open-epg.com/files/spain1.xml` (con WebFetch o descargándolo a un
temporal). Es un fichero grande (~1.3 MB), así que conviene usar `grep`/búsqueda
puntual sobre él en vez de leerlo entero.

## Logos

Wikimedia cambió su política de miniaturas: las URLs `/thumb/.../<ancho>px-archivo.png`
**solo aceptan estos anchos**: 20, 40, 60, 120, 250, 330, 500, 960, 1280, 1920, 3840.
Cualquier otro ancho (1200px, 2048px, 2560px, "langes-1100px", etc.) da error 400.
Si un logo de Wikimedia deja de cargar, lo primero a comprobar es el ancho del
nombre de archivo — cambiarlo a uno de la lista de arriba suele arreglarlo sin
necesidad de buscar una imagen nueva.

**Copia de seguridad local:** todos los logos usados en la lista (y unos cuantos
"de repuesto" para canales de Champions que aún no existen) están descargados en
la carpeta `logos/` en la raíz del repo, por si la web de origen desaparece o
cambia la URL. Nombre de archivo → uso:

| Archivo en `logos/` | Canal(es) que lo usan |
|---|---|
| `campeones-1.png` | CAMPEONES (Champions League 1) |
| `campeones-2.png` a `campeones-5.png` | CAMPEONES 2, 3, 4, 5 |
| `campeones-6-unused.png` a `campeones-13-unused.png` | de repuesto, sin asignar — para cuando se añadan Campeones 6+ |
| `vamos.png` | #VAMOS, #VAMOS 2, #VAMOS 3 |
| `deportes-1.png` | Deportes (1) y TELEDEPORTE |
| `deportes-2.png` a `deportes-8.png` | Deportes 2 a Deportes 8 |
| `golf-1.png` / `golf-2.png` | Movistar + Golf / Golf 2 |
| `dazn-baloncesto.png` | DAZN Baloncesto 1/2/3 y todos los ACB Eventos + DAZN ACB 1 |
| `m-baloncesto.png` | M+ Baloncesto 1/2/3 |
| `espn.png` | ESPN Argentina, ESPN Premium, ESPN Lat Deport |
| `bt-sport.png` | BT Sport 1/2/3 UK |
| `bein-sports.png` | BeIN Sports 1/2/3/4 Turquía/Ñ |
| `fox-sports.png` | FOX SPORTS |
| `antena-3.png` | ANTENA 3 |
| `la-sexta.png` | LA SEXTA |
| `la1.svg` | LA1 (720p/1080p/4k) |
| `la2.png` | LA2 |
| `cuatro.jpg` | CUATRO |
| `discovery.png` | DISCOVERY CHANNEL |
| `canal-cocina.svg` | CANAL COCINA |
| `golplay.jpg` | GOLPLAY |
| `esports3.png` | ESPORTS 3 |
| `m-plus.png` | Movistar Plus+ |
| `dazn-generico.svg` | Dazn 1, 2, 3, 4 |
| `dazn-f1.png` | DAZN F1 |
| `dazn-motogp.jpg` | Dazn MotoGP |
| `dazn-liga-1.png` a `dazn-liga-4.png` | DAZN Liga 1, 2, 3, 4 |
| `dazn-liga-5.png` | DAZN Liga 5 (logo genérico DAZN LaLiga, sin logo propio asignado) |
| `m-liga-1.png` a `m-liga-5.png` | M. Liga 1, 2, 3, 4, 5 |
| `hypermotion.png` | HYPERMOTION, HYPERMOTION 2, HYPERMOTION 3 |
| `motors-tv.png` | MOTORS TV |
| `eurosport.png` | Eurosport 1080p/720p, Eurosport 2 1080p/720p |
| `primera-federacion.png` | 1ª FEDERACIÓN |
| `rfef-tv.png` | RFEF TV |
| `aragon-tv.png` | ARAGÓN TV |

**Sin logo todavía** (`tvg-logo=""`, el usuario no ha dado enlace): ELEVEN DAZN 5,
ELEVEN DAZN 6, MOTORVISIÓN, FSN, Tennis Channel.

Si el usuario pasa un logo nuevo para un canal: 1) comprobar que la URL carga
(`curl -o /dev/null -w "%{http_code}"`, cuidado con falsos 429 de Wikimedia por
pedir demasiado rápido — reintentar con una pausa), 2) actualizar `tvg-logo` en
`list1` y `list2`, 3) descargar la imagen a `logos/` con nombre descriptivo,
4) actualizar esta tabla.

## Correcciones ya aplicadas (histórico, por si se repite el patrón)

Estos `tvg-id` estaban mal escritos respecto a `spain1.xml` y se corrigieron:

| Antes | Ahora |
|---|---|
| `DAZN1.es` / `DAZN2.es` / `DAZN3.es` / `DAZN4.es` | `DAZN 1.es` / `DAZN 2.es` / `DAZN 3.es` / `DAZN 4.es` |
| `M+ LALIGA TV.es` (y variantes 2/3/4) | `M+ LALIGA.es` (y variantes 2/3/4, sin "TV") |
| `M+ Vamos 2.e` | `M+ Vamos 2.es` (le faltaba la "s") |
| `Movistar Golf.es` | `M+ Golf.es` |
| `Movistar Plus+.es` | `Movistar Plus.es` (sin el "+") |
| `laSexta.es` | `La Sexta.es` |
| `1ª FEDERACIÓN 1080` | `Primera Federación.es` |
| `GOL PLAY.es` | `GOL.es` |
| `Movistar Deportes 1.es` (usado para Teledeporte) | `Teledeporte.es` |
| `ARAGÓN TV` | `Aragón TV.es` |
| `Discovery Channel.es` | `Discovery.es` |
| `La 1.es` | `LA 1.es` |

Algunos `tvg-id` (BeIN Sports, BT Sport, Canal Cocina, DAZN Mundial, Eleven Dazn,
ESPN Argentina/Premium/Lat, Fox Sports, FSN, M+ Fanzone, M+ Deportes 8, Motors TV,
Motorvisión) **no existen en `spain1.xml`** — se dejaron tal cual porque no hay
mejor alternativa conocida; simplemente no tendrán programación de EPG.
