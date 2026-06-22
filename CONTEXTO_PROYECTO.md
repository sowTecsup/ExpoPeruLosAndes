# Expo Perú Los Andes — Sesión "Artesanía Inteligente"

## Contexto del evento

- **Evento:** Huancavelica Expo / Expo Perú Los Andes
- **Organización:** Mancomunidad de los Andes
- **Regiones:** Huancavelica, Cusco, Apurímac, Junín
- **Propósito:** Espacio de oferta-demanda con compradores internacionales; promover exportadores de la zona
- **Sesión asignada:** Domingo 28, 9:40–11:00am (~1 hora o 1.5h)
- **Logística:** Formato expositivo, laptop disponible en el domo
- **Compensación:** Viáticos + honorario por participación

## Nombre de la sesión

**"Artesanía Inteligente"** — Digitalización y optimización para micro empresas textiles

## Audiencia

- ~8,000 artesanos textiles de las regiones participantes
- Niveles mixtos: desde artesanos básicos hasta exportadores directos vinculados a mercados internacionales
- Producto estrella: fibra de camélidos (alpaca, vicuña, llama) — tejido a mano, producto de lujo premium

## Ejes principales de la sesión

Dos grandes temas definidos a partir de la llamada con el organizador:

### 1. Darse a conocer (visibilidad + comercialización con IA)
- Inteligencia comercial: descubrir nuevos mercados, entender demanda internacional
- Cómo comercializar productos andinos al mundo
- Plataformas digitales (Etsy, Amazon Handmade, Instagram, Faire)
- IA para generar descripciones, contenido y posicionamiento
- Diseños digitales y representación de calidad premium
- Industrial vs. hecho a mano — cómo competir desde la autenticidad

### 2. Optimizar procesos (eficiencia con tecnología)
- Digitalización de procesos en micro empresas textiles
- Caso Cusco: mecatrónica + IA para control de calidad de prendas en segundos
- Gestión de pedidos y clientes
- Equipos para hilar y limpiar fibra
- Packaging 3D y presentación premium para exportación
- Reducción de costos sin perder identidad artesanal

## Notas técnicas de la llamada (extracto bruto)

> "Digitalización de las empresas textiles artesanales como la IA ayudaría a agilizar los procesos, para optimizar sus procesos. En Cusco usando mecatrónica e IA para la gestión de calidad de distintas prendas y hay una máquina que hace un diagnóstico o control de calidad en segundos, ahorra tiempo y costos. Diseños digitales, packing 3D, tejido en punto hecho a mano, producto de lujo premium, cómo representamos esta calidad en el empaque. Inteligencia comercial, IA implementación para descubrir nuevos mercados, cómo comercializarlos y darlos a conocer al mundo."

---

## Herramientas creadas

Dos apps web HTML, standalone, **funcionan 100% offline** (sin internet). Abrir en navegador desde la laptop del domo.

### `vitrina_andina_ia.html` — "Darse a conocer"

Flujo de 4 pasos interactivos:

1. **Perfil del artesano** — nombre, región, producto, fibra, técnica, precio, historia
2. **Catálogo digital** — genera descripción bilingüe (ES/EN) con IA de plantillas + checklist de exportación
3. **Inteligencia comercial** — 6 mercados internacionales con precios reales (EE.UU., Japón, Alemania, Australia, Canadá, Corea) + plataformas recomendadas
4. **Contenido para redes** — captions listos para Instagram/TikTok (4 tipos: producto, proceso, historia, exportación) + hashtags + calendario de contenido

**Demo sugerida:** Invitar a un artesano del público, llenar su perfil en vivo y mostrar su catálogo generado al instante.

### `gestion_textil_ia.html` — "Optimizar procesos"

Cuatro módulos en una sola app con persistencia local (localStorage):

1. **Pedidos y clientes** — registro de órdenes con cliente, producto, valor USD, fecha de entrega y estado (kanban simple). Dashboard con estadísticas en tiempo real.
2. **Control de calidad** — checklist de 15 puntos para fibra de camélidos (4 categorías: fibra, tejido, acabado, medidas). Puntaje visual en anillo. Exporta reporte .txt.
3. **Costos y precios** — calculadora con materiales, mano de obra, empaque, overhead y margen. Precio mínimo en S/ y USD. Proyección por mercado internacional.
4. **Packaging premium** — 4 niveles (básico / estándar / premium / luxury) con guías específicas y checklist de exportación pre-envío.

---

## Decisiones de diseño

| Decisión | Elección | Razón |
|---|---|---|
| Formato principal | App web HTML | Demo en vivo + artesanos se llevan la herramienta |
| Conectividad | Offline-first | Internet incierto en el domo |
| Idiomas | Español + Inglés | Compradores internacionales también en la expo |
| Usuarios | Artesanos + expositor | Demo en vivo durante sesión |
| Procesos prioritarios | Calidad + pedidos + packaging | Los tres cuellos de botella más críticos identificados |

---

## Estructura del repositorio

```
ExpoPeruLosAndes/
├── CONTEXTO_PROYECTO.md        ← este archivo
├── vitrina_andina_ia.html      ← Herramienta 1: darse a conocer
└── gestion_textil_ia.html      ← Herramienta 2: optimizar procesos
```

---

## Posibles mejoras / siguientes pasos

- Conectar `vitrina_andina_ia.html` a la API de Claude para generación real de descripciones (cuando haya internet)
- Agregar módulo de fotografía de producto con guía de iluminación
- Integrar QR generator para tarjetas de artesano
- Versión móvil optimizada para celular (los artesanos usan más el celular que laptop)
- Módulo de seguimiento tecnológico: links a proveedores de equipos de hilado y limpieza de fibra
- Caso Cusco mecatrónica: documentar y referenciar como ejemplo concreto en la presentación
- Mini slide deck (.pptx) de respaldo para la sesión (5–8 slides máx)

---

## Contacto del proyecto

- **Usuario:** Sowtank (juanneyra1399@gmail.com)
- **Repositorio:** ExpoPeruLosAndes
- **Fecha de sesión expo:** Domingo 28 (próximo)
