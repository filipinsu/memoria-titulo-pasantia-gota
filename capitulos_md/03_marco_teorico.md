# Marco teórico

En este capítulo se presentan los conceptos, estándares y metodologías que sustentan la evaluación de seguridad desarrollada en este trabajo. Se abordan, en orden, los fundamentos de la seguridad de aplicaciones web, las metodologías y marcos de referencia utilizados para guiar la evaluación, los riesgos propios de arquitecturas multiusuario como la de Maria.ag, la seguridad de las interfaces de programación de aplicaciones (API) dado el rol central que estas cumplen en la plataforma, y las particularidades de seguridad que surgen cuando un sistema de software tiene capacidad de incidir sobre procesos físicos, como ocurre con el riego agrícola.

## Seguridad de la información y de las aplicaciones web

La seguridad de la información se organiza tradicionalmente en torno a tres propiedades conocidas como la tríada CIA: *confidencialidad*, que garantiza que la información solo sea accesible para quienes están autorizados a conocerla; *integridad*, que asegura que los datos no sean alterados de forma no autorizada o accidental; y *disponibilidad*, que asegura que los sistemas y datos estén accesibles cuando se requieran [6]. Estas tres propiedades constituyen el criterio base para clasificar el impacto de cualquier vulnerabilidad identificada durante la evaluación.

En el caso particular de las aplicaciones web, la seguridad se ve condicionada por su arquitectura cliente-servidor y por la exposición pública de sus interfaces, lo que amplía la superficie de ataque respecto de aplicaciones de uso exclusivamente local. Los riesgos más comunes en este tipo de sistemas incluyen fallas en el control de acceso, en la validación de entradas, en la gestión de sesiones y en la configuración de los componentes que conforman la aplicación [4].

## Arquitectura de referencia de Maria.ag (modelo C4)

Para situar la evaluación de seguridad en la arquitectura concreta de la plataforma, y no solo en generalidades sobre aplicaciones cliente-servidor, se emplea el modelo C4 [7], una notación que describe un sistema de software en niveles crecientes de detalle. Este trabajo utiliza sus dos primeros niveles: el diagrama de *contexto* (C1), que sitúa a Maria.ag frente a sus usuarios y a los sistemas externos con los que se relaciona, y el diagrama de *contenedores* (C2), que muestra los grandes bloques de software –aplicaciones, servicios y almacenes de datos– que componen la plataforma y cómo se comunican entre sí.

Los diagramas que siguen se elaboraron a partir de la política de privacidad de Maria.ag y de la información recabada durante la pasantía; deben entenderse como una hipótesis de arquitectura a validar con el equipo de desarrollo de Gota SpA –en particular una vez que se obtenga acceso al repositorio– y no como una descripción certificada del sistema. Aun con esa salvedad, permiten delimitar la superficie de ataque de la plataforma y anticipar en qué componentes se concentrarán las pruebas de seguridad.

La Figura 1 presenta el diagrama de contexto (C1). En él se observa que Maria.ag es utilizada por dos tipos de actores –el personal de las empresas clientes y el personal de Gota SpA– y que se comunica con cinco sistemas externos: Agroclima, del que obtiene datos meteorológicos; Dropcontrol y Talgil, con los que intercambia telemetría y a los que además envía recomendaciones de riego para su ejecución; Galcon, al que envía recomendaciones de riego; y Hotjar/Microsoft Clarity, que recopila analítica de navegación sin que esos datos se almacenen en la base de datos de la plataforma. Las integraciones con capacidad de accionamiento –Dropcontrol, Talgil y Galcon– son, de acuerdo con lo discutido en la sección anterior, las de mayor relevancia para este trabajo, pues son las que trasladan un eventual error de software hacia el proceso físico de riego.

![Diagrama de contexto (C1) de Maria.ag](images/c4-context.pdf)

**Figura 1.** Diagrama de contexto (C1) de Maria.ag, elaborado a partir de la política de privacidad de la plataforma y de la información recabada durante la pasantía. Pendiente de validación con el equipo de desarrollo de Gota SpA.

La Figura 2 hace un acercamiento (C2) al interior de Maria.ag y distingue cuatro contenedores: una *aplicación web*, con la que interactúa el usuario desde su navegador; una *API o backend*, que concentra la autenticación, el control de acceso y la orquestación de las integraciones externas; un *motor de recomendación*, que ejecuta el modelo predictivo de riego; y una *base de datos*, que almacena las cuentas, los datos de suelos y sectores, los registros de riego manual y la información recibida de las integraciones. Bajo esta hipótesis de arquitectura, la API/backend es el componente que concentra la mayor superficie de ataque, al ser el punto donde confluyen la autenticación de los usuarios, el control de acceso entre empresas clientes y la comunicación con los sistemas externos con capacidad de accionamiento.

![Diagrama de contenedores (C2) de Maria.ag](images/c4-container.pdf)

**Figura 2.** Diagrama de contenedores (C2) de Maria.ag: bloques principales de software y sus relaciones, bajo la misma hipótesis de arquitectura que la Figura 1.

## Metodologías y estándares de referencia

### OWASP Top 10

El *Open Worldwide Application Security Project* (OWASP) es una comunidad abierta dedicada a mejorar la seguridad del software. Su proyecto OWASP Top 10 [4] identifica y describe las diez categorías de riesgo más críticas en aplicaciones web, construidas a partir de datos aportados por organizaciones de seguridad a nivel mundial. Entre estas categorías se encuentran el control de acceso roto (*broken access control*), las fallas criptográficas, la inyección de código y las fallas de diseño relacionadas con la lógica de negocio de la aplicación. Este trabajo utiliza el OWASP Top 10 como taxonomía para clasificar los hallazgos identificados.

### OWASP Web Security Testing Guide (WSTG)

La Guía de Pruebas de Seguridad Web de OWASP [3] es un marco metodológico que describe, de manera estructurada, los procedimientos para evaluar la seguridad de una aplicación web a lo largo de su ciclo de desarrollo. La guía organiza las pruebas en categorías –gestión de la configuración, autenticación, gestión de sesiones, validación de datos, lógica de negocio, entre otras– y, para cada una, define objetivos, técnicas y criterios de verificación. En este trabajo, la WSTG se utiliza como procedimiento operativo para ejecutar la evaluación de seguridad de Maria.ag.

### Common Vulnerability Scoring System (CVSS)

El Common Vulnerability Scoring System [5] es un estándar abierto que permite asignar una puntuación numérica a la severidad de una vulnerabilidad, a partir de métricas base –como el vector de ataque, la complejidad de explotación y el impacto sobre la confidencialidad, integridad y disponibilidad– que pueden complementarse con métricas temporales y de entorno. La puntuación resultante, en una escala de 0 a 10, permite comparar y priorizar hallazgos de distinta naturaleza bajo un mismo criterio cuantitativo, lo que en este trabajo se utiliza tanto para priorizar las vulnerabilidades a mitigar como para comparar el estado de la plataforma antes y después de la intervención.

### Modelado de amenazas

El modelado de amenazas es un proceso estructurado para identificar, de forma anticipada, las posibles formas en que un sistema puede ser atacado, a partir de la descomposición de su arquitectura, la identificación de sus activos y puntos de confianza, y el análisis de los flujos de datos entre sus componentes. Un marco ampliamente utilizado con este fin es STRIDE, que clasifica las amenazas en seis categorías: suplantación de identidad (*Spoofing*), alteración de datos (*Tampering*), repudio (*Repudiation*), divulgación de información (*Information disclosure*), denegación de servicio (*Denial of service*) y elevación de privilegios (*Elevation of privilege*) [8]. En este trabajo, el modelado de amenazas se utiliza como insumo previo a las pruebas dinámicas, para orientar el esfuerzo de evaluación hacia los componentes y flujos de mayor riesgo.

## Control de acceso en arquitecturas multiusuario

Maria.ag opera bajo un modelo en que múltiples empresas clientes comparten la misma instancia de la aplicación e infraestructura, cada una con sus propios datos y usuarios. Este tipo de arquitectura, conocida como *multiusuario* o *multi-tenant*, requiere que el control de acceso se aplique de forma consistente en cada operación del sistema y no únicamente a nivel de interfaz, de modo que un usuario autenticado solo pueda leer o modificar los recursos que le pertenecen [4].

Cuando esta verificación falla, se produce lo que OWASP clasifica como control de acceso roto, cuya manifestación más frecuente es la referencia directa insegura a objetos (*Insecure Direct Object Reference*, IDOR): un identificador de un recurso –por ejemplo, el de un sector de riego o un registro de riego manual– es manipulado por el usuario para acceder a recursos que pertenecen a otra cuenta o empresa, sin que el servidor verifique la propiedad del recurso solicitado. Dado el modelo multiusuario de Maria.ag, este trabajo otorga especial relevancia a la verificación del control de acceso entre clientes y entre roles dentro de una misma cuenta.

## Seguridad de interfaces de programación de aplicaciones (API)

Una parte sustancial de la funcionalidad de Maria.ag se sustenta en el intercambio de información con plataformas externas a través de API, tanto para la obtención de datos meteorológicos y de telemetría como para el envío de recomendaciones de riego hacia sistemas de terceros. Esta dependencia hace pertinente incorporar al marco de referencia de este trabajo los riesgos específicos de las API, los cuales no siempre son cubiertos en su totalidad por las guías orientadas a aplicaciones web tradicionales.

El OWASP API Security Top 10 describe los riesgos más críticos en este tipo de interfaces, entre los que se cuentan la autorización rota a nivel de objeto, la asignación masiva de propiedades no esperadas en una petición, la exposición excesiva de datos en las respuestas, y la ausencia de límites de uso (*rate limiting*) que permitan contener el abuso de un endpoint. Adicionalmente, cuando una API actúa como intermediaria hacia un sistema externo, cobra relevancia el correcto manejo de las credenciales de dicha integración –su almacenamiento, rotación y alcance de permisos– así como la validación de los datos antes de reenviarlos, para evitar que un valor manipulado por el cliente sea transmitido sin control hacia la plataforma de destino.

## Seguridad en sistemas con componentes ciberfísicos

A diferencia de una aplicación web convencional, Maria.ag forma parte de un sistema ciberfísico: sus recomendaciones y registros de riego son transmitidos a plataformas externas que gestionan equipamiento de riego en terreno (bombas, válvulas, controladores), por lo que una vulnerabilidad en el software puede manifestarse como un efecto físico sobre el cultivo o la infraestructura de riego, y no únicamente como una filtración o alteración de datos. Esta característica es análoga a la que se observa en los sistemas de control industrial y en el Internet de las Cosas (IoT) aplicado a la agricultura de precisión, donde la literatura ha documentado que errores de validación o fallas de autenticación en la capa de software pueden escalar a consecuencias operativas y económicas sobre el proceso físico que dicho software supervisa o controla [9]. En este trabajo, esta consideración se traduce en un énfasis particular sobre la validación de los datos que viajan hacia las integraciones con capacidad de accionamiento, y sobre los mecanismos que evitan la duplicación o ejecución no intencionada de una orden de riego.
