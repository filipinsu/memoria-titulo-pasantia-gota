# Metodología

En este capítulo se describe el procedimiento seguido para evaluar la seguridad de Maria.ag y para formular el plan de mitigación que resulta de dicha evaluación. Se detallan el enfoque general del trabajo, su alcance, las condiciones de autorización y las consideraciones éticas, las fases que estructuran el trabajo y su correspondencia con los objetivos específicos, las herramientas empleadas, los criterios de clasificación y priorización de hallazgos, la forma en que se evalúa el plan de mitigación propuesto y, por último, las limitaciones del estudio.

## Enfoque general

Este trabajo es de carácter aplicado y evaluativo. Consiste en una evaluación de seguridad de tipo *caja gris*: el evaluador cuenta con cuentas de usuario legítimas, con información de arquitectura entregada por la empresa y, en la medida en que Gota SpA lo autorice, con acceso de lectura al código fuente, lo que permite combinar pruebas dinámicas sobre la aplicación en ejecución con análisis estático del código y de sus dependencias.

El producto del trabajo es un informe de hallazgos clasificados y priorizados, acompañado de un plan de medidas de mitigación propuestas para cada uno. Las mitigaciones se *proponen* y se evalúan en términos de su factibilidad y de la reducción de riesgo que se estima lograrían, pero no se implementan sobre la plataforma; la implementación queda a cargo del equipo de desarrollo de Gota SpA y se plantea como trabajo futuro.

## Alcance

El alcance de la evaluación se delimita a partir de la hipótesis de arquitectura presentada en el marco teórico y de las áreas de mayor riesgo identificadas en la introducción. Quedan dentro del alcance:

- la aplicación web y la API/backend de Maria.ag;

- la autenticación y la gestión de sesiones;

- el control de acceso, tanto entre empresas clientes como entre roles dentro de una misma cuenta;

- la validación de los datos ingresados por el usuario y de los datos transmitidos hacia las integraciones con capacidad de accionamiento;

- el manejo de las credenciales de las integraciones externas y los mecanismos que evitan la duplicación o ejecución no intencionada de una orden de riego;

- las dependencias de software y la configuración de los componentes de la plataforma.

Quedan fuera del alcance los sistemas de terceros (Agroclima, Dropcontrol, Talgil, Galcon y Hotjar/Microsoft Clarity), de los cuales solo se analiza la interacción que Maria.ag mantiene con ellos; los equipos de riego en terreno; las pruebas de ingeniería social; y las pruebas de denegación de servicio volumétrico.

## Autorización, entorno de pruebas y consideraciones éticas

Dado que las pruebas de seguridad pueden afectar el funcionamiento de un sistema real, la evaluación se realiza bajo las siguientes condiciones:

- **Autorización escrita** de Gota SpA, que establece el alcance permitido y designa una persona de contacto técnica.

- **Entorno de pruebas** separado de producción y con datos ficticios. Cuando no sea posible contar con un ambiente de este tipo, las pruebas dinámicas se limitan a técnicas no destructivas y se coordinan previamente con la empresa.

- **Integraciones sin efecto físico**: las pruebas se ejecutan con credenciales de *sandbox* o con las integraciones deshabilitadas o simuladas, de modo que ninguna prueba pueda generar una orden de riego sobre equipos reales.

- **Confidencialidad**: la información y los accesos entregados se utilizan únicamente para los fines de este trabajo.

- **Divulgación responsable**: las vulnerabilidades críticas se comunican de inmediato al contacto técnico de la empresa, sin esperar la entrega del informe final.

## Fases del trabajo

El trabajo se organiza en seis fases secuenciales, cada una asociada a uno de los objetivos específicos, como se resume en la Tabla 1.

*Tabla 1. Fases del trabajo y su relación con los objetivos específicos.*

| **Fase**                     | **OE** | **Actividades**                                                                                                                                                   | **Producto**                                             |
|:-----------------------------|:-------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------|
| 1\. Caracterización          | 1      | Levantamiento de información con Gota SpA; validación de los diagramas C4; identificación de activos, dependencias y mecanismos de seguridad existentes.          | Arquitectura validada y superficie de ataque delimitada. |
| 2\. Modelado de amenazas     | 1–2    | Análisis STRIDE sobre los componentes y flujos de datos de los diagramas C4, con énfasis en la API/backend y en las integraciones con accionamiento.              | Catálogo de amenazas y lista de pruebas a ejecutar.      |
| 3\. Evaluación técnica       | 2      | Análisis estático del código, revisión de dependencias y pruebas dinámicas según las categorías de la WSTG, utilizando cuentas de al menos dos empresas clientes. | Hallazgos documentados con evidencia.                    |
| 4\. Clasificación            | 2      | Asignación de categoría OWASP Top 10 (o API Security Top 10) y de puntaje CVSS a cada hallazgo.                                                                   | Registro de hallazgos clasificados.                      |
| 5\. Priorización y propuesta | 3      | Aplicación de los criterios de priorización y formulación de una medida de mitigación para cada hallazgo priorizado.                                              | Plan de mitigación priorizado.                           |
| 6\. Evaluación del plan      | 4      | Validación de factibilidad con el equipo de Gota SpA y estimación del riesgo residual.                                                                            | Indicadores de reducción de riesgo proyectada.           |

En la fase de evaluación técnica, las pruebas dinámicas siguen las categorías de la WSTG [3], priorizando aquellas relacionadas con el alcance definido: gestión de la identidad y de la autenticación, autorización, gestión de sesiones, validación de entradas, lógica de negocio y pruebas sobre API. La verificación del aislamiento entre clientes se realiza con cuentas de dos empresas distintas, intentando acceder, desde cada una, a recursos pertenecientes a la otra mediante la manipulación de identificadores.

## Herramientas

Las herramientas se seleccionarán según la tecnología efectivamente utilizada por la plataforma, que se confirma en la fase de caracterización. Se consideran, por categoría: un proxy de interceptación y escáner de aplicaciones web para las pruebas dinámicas (como OWASP ZAP o Burp Suite Community); una herramienta de análisis estático de código (como Semgrep); una herramienta de análisis de composición de software para revisar dependencias con vulnerabilidades conocidas; y una herramienta de detección de secretos expuestos en el repositorio. Los resultados automáticos se validan manualmente para descartar falsos positivos, y las pruebas de control de acceso entre clientes y de lógica de negocio se realizan de forma manual, ya que las herramientas automáticas no las cubren adecuadamente.

## Clasificación de hallazgos

Cada hallazgo se registra con su descripción, el componente afectado, los pasos para reproducirlo, la evidencia y el impacto sobre la confidencialidad, integridad y disponibilidad. Posteriormente se clasifica según:

- la **categoría de riesgo** del OWASP Top 10 [4] o, si corresponde a una API, del OWASP API Security Top 10 [6];

- la **severidad**, mediante el puntaje base de CVSS v4.0 [5], que se agrupa en los niveles bajo (0,1–3,9), medio (4,0–6,9), alto (7,0–8,9) y crítico (9,0–10,0). Cuando el contexto de Maria.ag lo justifica, se ajusta el puntaje mediante las métricas de entorno, por ejemplo ante un hallazgo que pueda traducirse en una orden de riego no deseada.

## Criterios de priorización

Dado que no es posible proponer mitigaciones con el mismo nivel de detalle para todos los hallazgos dentro del tiempo disponible, estos se priorizan con los criterios de la Tabla 2.

*Tabla 2. Criterios de priorización de hallazgos.*

| **Criterio**                                 | **Descripción**                                                                                                             | **Uso**                                                               |
|:---------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------|
| Severidad                                    | Puntaje CVSS del hallazgo.                                                                                                  | Determina el nivel de prioridad.                                      |
| Impacto sobre el negocio y el proceso físico | Si el hallazgo permite acceso entre empresas clientes o el envío no autorizado de órdenes de riego hacia las integraciones. | Eleva el hallazgo a prioridad alta, independientemente de su puntaje. |
| Factibilidad de intervención                 | Esfuerzo estimado de la corrección y riesgo de afectar funcionalidades existentes (alta, media o baja).                     | Ordena los hallazgos dentro de un mismo nivel de prioridad.           |

Con ellos se definen tres niveles de prioridad. Es de **prioridad alta** todo hallazgo con severidad alta o crítica, o que afecte el aislamiento entre clientes o el accionamiento de equipos de riego; de **prioridad media**, el de severidad media que no presente esos impactos; y de **prioridad baja**, el de severidad baja. Dentro de cada nivel se atienden primero los de mayor factibilidad de intervención, de modo de obtener la mayor reducción de riesgo con el menor esfuerzo.

## Evaluación del plan de mitigación

Para cada hallazgo priorizado se propone una medida de mitigación que describe la corrección sugerida, el componente sobre el que se aplicaría y una estimación del esfuerzo. Como las medidas no se implementan, su evaluación se basa en dos mecanismos:

1.  **Validación de factibilidad con el equipo de Gota SpA.** Las propuestas se presentan al equipo de desarrollo, que valora su factibilidad técnica, el esfuerzo requerido y el riesgo de afectar funcionalidades existentes. De ese análisis se identifican, además, las funcionalidades que deberían cubrirse con pruebas de regresión al momento de implementar cada medida.

2.  **Estimación del riesgo residual.** Cada hallazgo se vuelve a puntuar con CVSS bajo el supuesto de que la mitigación se aplica correctamente, modificando las métricas que la medida alteraría. El resultado es una *proyección* de la reducción de riesgo y no una medición.

Con esta información se construyen los indicadores de la Tabla 3, que permiten comparar el estado actual de la plataforma con el estado proyectado.

*Tabla 3. Indicadores para evaluar el plan de mitigación.*

| **Indicador**           | **Descripción**                                                                                    |
|:------------------------|:---------------------------------------------------------------------------------------------------|
| Hallazgos por severidad | Cantidad de hallazgos en cada nivel, en el estado actual y en el estado proyectado.                |
| Puntaje CVSS            | Puntaje de cada hallazgo priorizado, actual y proyectado, y su reducción.                          |
| Cobertura del plan      | Proporción de los hallazgos de prioridad alta que cuentan con una medida de mitigación propuesta.  |
| Factibilidad validada   | Proporción de las medidas propuestas que el equipo de Gota SpA considera factibles de implementar. |

## Limitaciones

Los resultados de este trabajo deben interpretarse considerando las siguientes limitaciones:

- La efectividad de las mitigaciones se estima, no se mide: al no implementarse, no es posible repetir las pruebas ni ejecutar pruebas de regresión.

- La evaluación corresponde a un momento puntual y a un tiempo acotado; no garantiza la ausencia de vulnerabilidades no detectadas.

- El entorno de pruebas puede diferir de producción en su configuración y en sus datos.

- La arquitectura se basa en información pública y en la recabada durante la pasantía, validada parcialmente con la empresa.

- No se evalúan los sistemas de terceros ni las vulnerabilidades asociadas a la ingeniería social.
