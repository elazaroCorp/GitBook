---
cover: >-
  https://images.unsplash.com/photo-1532522750741-628fde798c73?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw4fHxTb2Z0d2FyZXxlbnwwfHx8fDE3MjA2MzIxNTN8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Introducción

## Que es CC-Backend?

CC-Backend es una biblioteca que facilita a los desarrolladores de backend la creación de APIs más rápidas y seguras.

Se proporciona un conjunto de herramientas para simplificar y agilizar el desarrollo. Algunos de los ejemplos son:

* Facilidad para documentar Swagger usando Json tipado.
* Middlewares integrados para la auth, handler-error, rate-limit, try-catch, etc.
* Validadores de Request y Response usando Zod y Joi.
* Conexiones a Base de datos usando mssql.
* Manejo de secretos y env.
* Intermediario de peticiones HttpProxyClient.

### ¿Por qué?

* Estandarizar las librerías y herramientas.
* Garantizar los lineamientos en seguridad y arquitectura.
* Generar diagramas de forma automática como diagramas de secuencia, C4, etc.

### Beneficios

La librería está pensada principalmente en los desarrolladores. Sin embargo, los beneficios de estos también son extensibles para otros roles como seguridad, gobierno, arquitectura, soporte, etc.

* **Seguridad:** El código desarrollado está pensado para seguir los lineamientos que exija seguridad tales como la validación de datos, autenticaciones, manejo de errores.
* **Gobierno:** El equipo de gobierno podrá gestionar de forma sencilla&#x20;
  * Las sub-dependencias que se tienen al tener una sola librería bases.
  * Poder generar diagramas de forma automática gracias al uso del **RqId.**
* **Arquitectura**: La librería va a exigir en varios puntos de la aplicación cumplir con los lineamientos de arquitectura como ejemplo
  * Api de Experiencia no se puede conectar a una base de datos.
  * Api de Negocio siempre debe tener Autenticación Básica.
  * Validación obligatoria para enviar RqId en los headers (Identificación de la petición)
* **Soporte:** En caso de tener algún error por parte de la aplicación será más fácil encontrar lo ocurrido filtrando rqId en AzureLog.
