# Observaciones al borrador

Registro de comentarios sobre el borrador de la memoria. Cada entrada incluye el extracto observado y la observación reformulada.

---

## 1. Hipótesis — alcance del trabajo

**Sección:** Hipótesis  
**Fecha:** 2026-10-08

### Extracto

> La aplicación de un proceso sistemático de evaluación de seguridad basado en metodologías y estándares reconocidos, junto con la posterior implementación de medidas de mitigación, permite reducir el número y la severidad de las vulnerabilidades en la plataforma Maria.ag.

### Observación

Hay que delimitar hasta dónde llega la memoria: ¿solo hasta el **análisis** de vulnerabilidades, hasta el **plan** de mejora, o hasta la **implementación** y medición de efectividad?

La hipótesis debe alinearse con ese alcance. Incluir análisis, plan e implementación es demasiado ambicioso, sobre todo porque:

1. La **implementación** de las mitigaciones no estaría del lado del memorista (depende del equipo de Gota / tiempos de la empresa).
2. **Medir la efectividad** de las mitigaciones requiere tiempo que probablemente no cabe en el plazo del trabajo de título.

**Recomendación:** acotar el alcance a **Análisis + Plan**. Reformular la hipótesis (y, en consecuencia, objetivos y preguntas de investigación) para que no asuman implementación ni verificación post-mitigación.

### Implicancias

Si se adopta este alcance, revisan también:

- Pregunta de investigación 4 («¿En qué medida las mitigaciones implementadas…?»)
- Objetivo general (habla de «implementación» y «verificación de su efectividad»)
- Objetivos específicos 3 y 4 (implementar mitigaciones y comparar indicadores antes/después)

---

## 2. Historial de chat con Claude — transcripción, no enlace

**Sección:** Uso de IA / anexos (archivo `borrador2/conversacion_claude.txt`)  
**Fecha:** 2026-10-08

### Extracto

> https://claude.ai/share/e3ec0013-0acf-4b6d-8ac6-826f3ed81d29

### Observación

El historial de la conversación con Claude **debe estar transcrito** en el repositorio o en el documento (anexo), no reducido a un enlace al sitio de Claude.

Un link externo puede expirar, requerir acceso o no quedar archivado con la memoria; la evidencia del uso de IA debe quedar **en el propio trabajo**, como texto completo o extracto fiel de la conversación.

---

## 3. Orden de capítulos — hipótesis y objetivos después del marco teórico

**Sección:** Estructura del documento (Índice / cuerpo)  
**Fecha:** 2026-10-08

### Extracto (orden actual)

> Resumen → Introducción → **Hipótesis** → **Preguntas de investigación** → **Objetivo general** → **Objetivos específicos** → Marco teórico → …

### Observación

**Hipótesis**, **Preguntas de investigación**, **Objetivo general** y **Objetivos específicos** deben ir **después del marco teórico**, no antes.

El marco teórico es la explicación técnica que el lector necesita para entender de qué se está hablando. Si la hipótesis y los objetivos aparecen antes de ese fundamento, el lector aún no tiene el vocabulario ni el contexto (OWASP, CVSS, multi-tenant, API, ciberfísico, etc.) y se pierde el sentido de lo planteado.

### Orden sugerido

Resumen → Introducción → **Marco teórico** → Hipótesis → Preguntas de investigación → Objetivo general → Objetivos específicos → …

---

## 4. Diagrama C4 del sistema en el marco teórico

**Sección:** Marco teórico / figuras  
**Fecha:** 2026-10-08

### Extracto / verificación

En el LaTeX del borrador **no hay figuras técnicas**: solo se referencia el logo de portada (`logo_uoh.png`). No hay diagramas de arquitectura ni de contexto del sistema.

### Observación

Se sugiere incorporar un **diagrama C4** de Maria.ag en el **marco teórico**, con al menos los niveles:

- **C1 (Context):** el sistema en su entorno (usuarios, Gota, integraciones externas como Dropcontrol / Talgil / Galcon, etc.).
- **C2 (Containers):** contenedores principales de la solución (web, API, base de datos, servicios, etc.).

Esos dos niveles bastan para que el lector entienda la **complejidad y el tamaño** de la solución antes de la hipótesis y los objetivos.

### Herramienta

Usar algo como **[diagrams.net](https://www.diagrams.net/)** (draw.io) y exportar a imagen/PDF de calidad para LaTeX. **No** usar Mermaid ni diagramas generados como texto/código que dejen la memoria con aspecto improvisado o poco profesional.

---

## 5. El texto del marco teórico pide (implícitamente) el diagrama

**Sección:** Marco teórico — Seguridad de la información y de las aplicaciones web  
**Fecha:** 2026-10-08  
**Relacionada con:** [§4](#4-diagrama-c4-del-sistema-en-el-marco-teórico)

### Extracto

> En el caso particular de las aplicaciones web, la seguridad se ve condicionada por su arquitectura cliente-servidor y por la exposición pública de sus interfaces […]

### Observación

Ese párrafo **refuerza** la necesidad del diagrama C4: habla de arquitectura cliente-servidor y de interfaces expuestas, pero sin una figura el lector no ve *cómo* está armada Maria.ag ni *cuáles* son esas interfaces.

Justo después (o junto a) esa idea conviene insertar el diagrama C1/C2, para que la afirmación quede anclada en la arquitectura concreta del sistema y no solo en generalidades.

---

## 6. Terminología: «ciberfísico» vs IoT

**Sección:** Marco teórico — Seguridad en sistemas con componentes ciberfísicos  
**Fecha:** 2026-10-08

### Extracto

> ### Seguridad en sistemas con componentes ciberfísicos
>
> A diferencia de una aplicación web convencional, Maria.ag forma parte de un sistema ciberfísico: […] Esta característica es análoga a la que se observa en los sistemas de control industrial y en el Internet de las Cosas (IoT) aplicado a la agricultura de precisión […]

### Observación

El término técnico con el que suele conocerse (y buscarse) este tipo de escenario es **IoT** (*Internet of Things*), no tanto «ciberfísico» / CPS como etiqueta principal.

Conviene **priorizar IoT** en el título de la sección y en el lenguaje del marco teórico (y, si se mantiene «ciberfísico», usarlo como matiz o sinónimo secundario), para alinear el texto con la jerga habitual del dominio y con la literatura citada (p. ej. la referencia [8] ya habla de *IoT-Based Irrigation*).

---

## 7. Siguiente capítulo: diseño de la memoria (alcance metodológico coherente)

**Sección:** Nuevo capítulo posterior a hipótesis / objetivos (Diseño / Metodología de la evaluación)  
**Fecha:** 2026-10-08  
**Relacionada con:** [§1](#1-hipótesis--alcance-del-trabajo) (alcance), [§4](#4-diagrama-c4-del-sistema-en-el-marco-teórico) (C4)

### Observación

Con las observaciones anteriores, el memorista debe **construir el diseño de su memoria**: el capítulo siguiente, donde —a partir de la hipótesis— explique **qué tomará de cada estándar** mencionado en el marco teórico (OWASP Top 10, WSTG, CVSS, modelado de amenazas / STRIDE, API Security Top 10, criterios IoT, etc.).

Ese diseño no puede ser un menú genérico de buenas prácticas: debe ser **coherente con la arquitectura real** de Maria.ag, documentada en el C4.

### Criterio de coherencia

Lo que se evalúe (y lo que se cite del marco) tiene que mapear a componentes visibles en C1/C2. Por ejemplo:

- Si el C4 **no** muestra APIs / integraciones con ese rol, **no** corresponde montar un plan de pruebas de API «porque OWASP API Top 10 existe».
- Si **sí** hay API hacia Dropcontrol / Talgil / Galcon (u otros contenedores), entonces sí justifica seleccionar controles y pruebas de ese estándar —y decir *cuáles* y *por qué*.

En resumen: hipótesis → diseño del trabajo → selección explícita de fragmentos de cada estándar, **filtrados por la arquitectura** del diagrama C4.
