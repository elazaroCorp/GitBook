# HttpProxy

Este recurso nos permite abstraer las consultas hacia apis.

## Contexto&#x20;

La arquitectura actual tiene dos capas, apis externas (experiencia) e internas (negocio) y esto tipo de arquitecturas posee los siguientes retos.

* Saber organizar los enpoints de cada api.
* Al consumir un api se debe crear la URL (query, params) y el body.
* Cuando se consume una mismo endpoint en más de dos lugares se repite las definiciones.
* Cada desarrollador organiza sus apis como mejor le parezca.
* Poder consumir apis de otras celulas es complicado.

La siguiente imagen muestra una abstracción de la arquitectura de apis y células, cada vez que se crean nuevas apis se vuele complicado de mantener y escalar, generando código duplicado en muchos casos.

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption><p>Resumen de la arquitectura entre células</p></figcaption></figure>

Adicional a ello, el consumo de las apis debe ser un estándar para todas las células, donde todos deben cumplir con los requerimientos de Seguridad y Calidad.

Por ello, se propone esta sección donde presentan estructuras, clases, ejemplos, etc.

## HttpProxy

Esta clase nos ayuda a crear un intermediario entre el consumidor y la api. Esto se realiza por los siguientes motivos:

* Imprimir los datos necesarios para hacer soporte.
* Validar parámetros obligatorios como rqId, basicAuth.
* Hacer envió de datos hacia una **api\_track** para generar diagramas (solo en DEV).
* Abstraer la forma en la que se consumen las apis, es decir se en un futuro se quiere cambiar el uso de axios a fetch será más fácil y transparente para los desarrolladores.
* Enviar los datos como objetos ya sea en los parámetros y query.

### Recomendaciones

* Si la api es especifica de la célula, se recomienda crearlo en una librería de la célula.
* Si la api es compartida o general, se recomienda crearlo en una librería general para todas las células, ejemplo api de seguridad.
* Las apis deben definirse en typescript para tener los tipos de datos en el input y output de cada api.











&#x20;







&#x20;&#x20;

