# HANDOFF — Artesanía Inteligente (Expo Perú Los Andes)

> Documento de traspaso para preparar la **presentación final** (pensado para continuar en Claude cowork).
> Última actualización: **2026-06-26**. Versión de las apps: **v2026.06.26.1**.

---

## 1. Qué es el proyecto (en una frase)

Un **toolkit de 2 web-apps + una portada**, hecho para la sesión en vivo **"Artesanía Inteligente"** de **Expo Perú Los Andes**, dirigido a **~8,000 artesanos textiles andinos** (fibra de alpaca / vicuña / llama) de **Huancavelica, Cusco, Apurímac, Junín** (y aledaños). Se demuestra en vivo desde una laptop en el domo y **se les entrega para que lo conserven**.

- **Evento / sesión:** domingo **28 de junio de 2026, 9:40–11:00 am**, en el domo de la expo.
- **Usuario primario:** artesano/a hispanohablante, **baja alfabetización técnica**. Toda la UI es en **español**, con salidas **bilingües ES/EN** (y FR/DE/JA) donde sirve al comprador internacional.
- **Tesis de producto:** dos procesos complementarios →
  - **Proceso 1 · "Darse a conocer"** = `vitrina_andina_ia.html` (crecer hacia afuera: marca, redes, certificados, trazabilidad).
  - **Proceso 2 · "Optimizar procesos"** = `gestion_textil_ia.html` (administrar hacia adentro: clientes, mensajes, pedidos, precios).

---

## 2. Los tres archivos

| Archivo | Rol | Estado |
|---|---|---|
| `index.html` | Portada: une las dos apps + QR que se auto-genera de su propia URL para compartir | Estable |
| `vitrina_andina_ia.html` | App "Darse a conocer" — **foco de esta sesión de trabajo** | Pulida 2026-06-26 |
| `gestion_textil_ia.html` | App "Optimizar procesos" (CRM/maqueta) | Cerrada por el usuario el 2026-06-25 |

Cada archivo es **autocontenido**: `<style>` + `<script>` inline, **vanilla JS, sin framework, sin build, sin dependencias**. Se abre con doble clic y corre.

---

## 3. Decisiones de arquitectura (importantes para explicar en la presentación)

1. **Un archivo por app, sin build.** Deliberado: cualquiera puede abrirlo, copiarlo o alojarlo sin herramientas. Cero fricción para el artesano.
2. **Motor de IA = Google Gemini (Google AI Studio).** Se eligió porque las **claves son gratis sin tarjeta** y se llaman **desde el navegador sin CORS especial**. Cada usuario genera su clave en `aistudio.google.com/apikey` y la pega en la app; se guarda **solo en su navegador** (`localStorage`: `va_key` para Vitrina, `gti_key` para Gestión).
   - Modelo: constante `GEMINI_MODEL = 'gemini-2.0-flash'` (ajustable a `gemini-2.5-flash`/`gemini-1.5-flash` si una clave lo requiere).
3. **Despliegue estático, "cada usuario su propia clave".** Sin backend, sin proxy. Hosting = **GitHub Pages** desde `main`/root → `https://sowtecsup.github.io/ExpoPeruLosAndes/`. La clave **nunca** se publica.
4. **Conectividad (ACTUALIZADO 2026-06-26):** el organizador confirmó que **habrá internet en el evento**, así que **el offline-first dejó de ser requisito duro**. Ahora se permiten recursos online (ver §6, fotos reales). La app conserva **degradación elegante**: si la IA o la red fallan, los textos caen a **plantillas offline** y el **Pasaporte QR funciona sin internet** (los datos viajan dentro del QR).

---

## 4. Vitrina Andina IA — recorrido por las 4 pestañas

Estado en memoria en el objeto `D` (sin persistencia entre recargas). Navegación con `goStep(n)`. Barra de **progreso** (`progress` + `markDone(key)`) con 4 hitos: `perfil` / `redes` / `certificados` / `pasaporte`; al completar los 4 dispara `confetti()` + toast.

### Tab 1 · Perfil (`saveProfile`)
Formulario: nombre/marca, región, producto, fibra, técnica, años, capacidad/mes, precio ref. (S/), **asociación/cooperativa** (`f_assoc`, clave para la ruta de certificación), qué lo hace especial, contacto. Botón **"✨ Ver ejemplo"** (`loadExample`) rellena un perfil realista (María Quispe, Cusco, chal de alpaca baby) para demo.

### Tab 2 · Redes que sí venden — (la sección reforzada esta sesión)
Tres bloques:
- **Cronograma semanal** (`genPlan`): la IA (o plantilla `PLAN_TPL`) propone 6 posts lun–sáb por ángulo; cada tarjeta tiene **"✍️ Crear este post"** (`usePlanAngle` → genera directo).
- **Crear una publicación** (`genContent`): elige **ángulo** (producto / proceso / historia / exportación / descripción) e **idioma** (ES/EN/FR/DE/JA). La IA escribe el texto (**sin hashtags**, máx. 65 palabras); sin IA usa `tplContent(lang,type)` con plantillas propias en los 5 idiomas. Render = **tarjeta de post estilo red social** con **foto real representativa** + botones de compartir (`shareWhatsApp`, `copyAndOpenIG`, `copyPost`) y **"📷 Usar mi foto"** (`setPostPhoto` sube la foto real del producto como dataURL).
- **Guía de foto** (`PHOTO_TIPS`, estática) + **"Referencias de ángulos"** (`SHOTS`/`renderShots`): 6 ángulos de toma con **foto real de ejemplo** y botón "Más ejemplos" que abre una búsqueda en Pexels.

### Tab 3 · Ruta de certificaciones (`renderCerts`)
6 certificaciones (`CERTS`) **ordenadas por dificultad**, cada una con: **sello/medalla SVG** propio, medidor de dificultad, **costo aproximado**, **% de aumento de precio potencial**, quién aplica (individual/asociación), "por qué te conviene", "cómo conseguirla" y **fuentes oficiales** (enlaces a INDECOPI, SUNAT, Alpaca del Perú, FLOCERT/WFTO, OEKO-TEX, GOTS, Textile Exchange). Mensaje clave: **certificarse en asociación abarata** y el premium **paga hasta 3×**. Ruta recomendada personalizable: `ruleRoute()` (offline, según asociación/fibra) o `aiRoute()` (Gemini).
> Las 6: INDECOPI+marca · Alpaca del Perú · Comercio Justo (Fairtrade/WFTO) · OEKO-TEX 100 · GOTS · GRS. **Cifras orientativas** (se confirman con la entidad).

### Tab 4 · Pasaporte Digital (`generatePassport`)
Ficha de **trazabilidad** con **QR escaneable que funciona sin internet** (los datos viajan dentro del QR). La IA redacta una frase de marca de lujo (o plantilla). `composePassportImage` dibuja toda la ficha a un canvas; `sharePassport` usa Web Share API (`navigator.share({files})`) para adjuntar el PNG a WhatsApp en móvil (en escritorio: descarga + `wa.me`).

---

## 5. Gestión Textil IA — resumen (app ya cerrada)
CRM / "Centro de clientes" (maqueta), 6 pestañas: **Panel** (dashboard con gráfico de líneas costo vs ganancia) · **Mensajes** (bandeja unificada WhatsApp/IG/Messenger con **traducción IA en vivo** y respuestas sugeridas) · **Pedidos** (estados con camioncito 🚚 animado + recordatorios) · **Precios** (precio justo S/+USD + grilla por país) · **Packaging** (unboxing visual) · **Canales** (integraciones mock). Persiste en `localStorage` (`gti_*`). Tiene `seedDemo()`/`clearDemo()` para presentar.

---

## 6. Cambio reciente clave: imágenes reales en Redes (2026-06-26)
- **Antes:** placeholder con emoji / loremflickr (CDN poco fiable que se veía roto).
- **Ahora:** **fotos reales de Pexels** (CDN estable, sin clave, libre de derechos), **full online**, una **representativa por proceso** en el post y **una por ángulo** en la galería. Se verificaron con `curl` (200 image/jpeg) y revisión visual.
- Mapa post (`POST_IMG`): producto=`38011984` · proceso=`20453594` · historia=`5498791` · exportación=`4440800` · descripción=`5603009`.
- Galería (`SHOTS[].img`): plano=`33027862` · detalle=`5710046` · en uso=`27167269` · flat lay=`5603009` · taller=`17649814` · paisaje=`17015841`.
- El botón **"📷 Usar mi foto"** sigue permitiendo reemplazar por la foto real del artesano.

---

## 7. Integración con Gemini — casos a conocer (verificado)
El helper `AI` (en ambas apps) hace `POST` a:
`https://generativelanguage.googleapis.com/v1beta/models/${GEMINI_MODEL}:generateContent?key=…`
Cuerpo: `system_instruction` + `contents[].parts`. (En Gestión, la visión QC añade un `inline_data`; salida estructurada con `responseMimeType:"application/json"` + `parseJSON` defensivo.)

`AI.available()` exige **clave + `navigator.onLine`**. **Todos los callers tienen fallback**: si no hay clave / no hay internet / la API falla → devuelve `null` y se usa la **plantilla local**, marcando el resultado con `srcTag()` (● IA real vs ● plantilla offline).

**Comportamiento de error verificado (curl real):**
| Caso | Respuesta | Qué hace la app |
|---|---|---|
| Clave inválida | `400` · `API_KEY_INVALID` | cae a plantilla; `console.warn('[Gemini] 400 …')` |
| Modelo inexistente | `404` NOT_FOUND | cae a plantilla; warn |
| Cuota agotada | `429` | cae a plantilla; warn |
| Respuesta sin texto (bloqueo de seguridad) | `200` sin `parts.text` | cae a plantilla; warn |
| Sin internet | fetch lanza | cae a plantilla; warn |

> **Mejora aplicada esta sesión:** antes el error se tragaba en silencio; ahora queda un `console.warn` con el código y mensaje, útil para diagnosticar en la demo sin romper la experiencia.

**Para demostrar la IA real en vivo:** genera una clave gratis en `aistudio.google.com/apikey`, pégala en **"IA: configurar"** (el punto del header pasa a verde "IA real activa"), genera contenido (verás el tag **● IA real**), y **bórrala al terminar**. Si no hay clave/red, todo sigue funcionando con **● plantilla offline**.

---

## 8. Identidad visual (mantener consistencia en slides)
- Tema oscuro **índigo profundo** (`--night #14121f`) con glows radiales terra/jade/oro.
- Paleta fibra natural: `--vicuna #c9a26b` · `--gold #e0b352` · `--terra #c85a3c` · `--cochi #9e2b4e` · `--jade #2f8f80` · `--cream #f4efe4`.
- Logo **chakana** (cruz andina) SVG con degradado oro→terra→cochinilla; **banda textil de rombos** en el borde superior.
- Microinteracciones: `.btn:active` scale, hover-lift, **hover-zoom** en imágenes (añadido hoy), reveal de paneles, confetti al completar.

---

## 9. Limitaciones / caveats honestos (decir en la presentación si preguntan)
- **Codificador QR casero** (sin librería, para offline): tiene un **bug en versión 7+** (lectores estrictos lo rechazan). Mitigado con **tope duro en v6** y **payload de texto corto** (`Nombre - Región / Producto en Fibra / Código`). **Confirmado que escanea en Google Lens.** No meter URLs ni textos largos en el QR.
- **Cifras de certificación** (costos, % de aumento) son **orientativas**, basadas en `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md`; se confirman con la entidad. No inventar cifras nuevas.
- **Imágenes de Redes** ahora **requieren internet** (decisión consciente; el organizador confirmó conexión). Lo offline real es: textos por plantilla + Pasaporte QR.
- **Gestión** es una **maqueta** (datos sembrados); las integraciones de canales son demostrativas.

---

## 10. Estado de despliegue
- **GitHub Pages:** verificar que esté activo → `https://sowtecsup.github.io/ExpoPeruLosAndes/`.
- **Pendiente de commit/push:** los cambios de esta sesión (fotos reales en post y galería, diagnóstico Gemini, pulido, bump a `v2026.06.26.1`, este HANDOFF) están en local. **Subir antes del evento.** El string de versión en el footer sirve para confirmar que Pages cargó el commit nuevo (descarta caché).

---

## 11. Sugerencia de narrativa para la presentación final
Arco propuesto (3 actos):
1. **El problema** — el artesano andino crea piezas de clase mundial pero vende barato: barreras = foto + idioma + falta de marca/trazabilidad/certificación (puntos de dolor del estudio de mercado).
2. **La solución, en vivo** — abrir Vitrina y recorrer el ejemplo (María Quispe): perfil → **generar un post en inglés con IA real** (mostrar el tag ● IA real) y ponerle una foto real → **ruta de certificación personalizada** → **Pasaporte QR** y **escanearlo con un celular** en vivo (efecto "wow"). Luego Gestión: traducir un mensaje de un comprador y ver el dashboard.
3. **El legado** — es gratis, funciona en su propio celular, cada quien con su clave gratis de IA; se lo llevan a casa. Escala a 8,000 artesanos sin costo de infraestructura.

**Momentos "wow" para el escenario:** (a) post bilingüe generado por IA con foto real; (b) escanear el Pasaporte QR con un teléfono del público; (c) el camioncito de pedidos y la traducción en vivo en Gestión.

---

## 12. Archivos de referencia en el repo
- `CONTEXTO_PROYECTO.md` — fuente de verdad de intención, evento y decisiones de diseño.
- `INVESTIGACION_TECNOLOGIA_TEXTIL_PERU.md` — investigación de mercado y "puntos de dolor" que justifican cada feature (y citan fuentes). **Leer antes de tocar precios/mercados/datos.**
- `CLAUDE.md` — guía técnica para asistentes de código (arquitectura, convenciones).
