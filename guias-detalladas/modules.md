# Modules

Un módulo es una carpeta que se utiliza para organizar la estructura de la aplicación. Todas las aplicaciones tienen al menos un módulo raíz.&#x20;

Los módulos son recomendables por la forma efectiva de organizar sus componentes. Además, que facilita el entendimiento del proyecto, manteniendo el código organizado y estableciendo límites entre módulos.

## Estructura Simple

Para demostrar esto, creamos la siguiente estructura.

{% tabs %}
{% tab title="Javascript" %}
```
Proyect
├── package.json
├── src
│    ├── modules
│    │    ├── client 
│    │    │    ├── client.controller.js
│    │    │    ├── client.service.js
│    │    │    ├── client.respository.js
│    │    │    ├── client.routes.js
│    │    │    ├── client.entity.js
│    │    │    ├── client.mock.js
│    │    │    └── index.js
│    │    └── setRoutes.js
│    └── application.js
└── jsconfig.json
```
{% endtab %}

{% tab title="TypeScript" %}
```
Proyect
├── package.json
├── src
│    ├── modules
│    │    ├── client 
│    │    │    ├── client.controller.ts
│    │    │    ├── client.service.ts
│    │    │    ├── client.respository.ts
│    │    │    ├── client.routes.ts
│    │    │    ├── client.entity.ts
│    │    │    ├── client.mock.ts
│    │    │    └── index.ts
│    │    └── setRoutes.ts
│    └── application.ts
└── tsconfig.json
```
{% endtab %}
{% endtabs %}

### setRoutes

Los valores que se quiera exportar estan en el archivo `index`

{% tabs %}
{% tab title="client/index.js" %}
```javascript
module.exports = {
  ...require('./client.routes'),
  ...require('./client.mock'),
}
```
{% endtab %}

{% tab title="client/index.ts" %}
```typescript
export * from './client.routes'
export * from './client.mock'
```
{% endtab %}
{% endtabs %}

Cada módulo se agrega en el archivo `setRoutes` y este es llamado desde application.js

{% tabs %}
{% tab title="setRoutes.js" %}
```javascript
const { clientRouter } = require('./client')

function setRoutes(router) {
  router.use(clientRouter)
}

module.exports = { setRoutes }
```
{% endtab %}

{% tab title="setRoutes.ts" %}
```typescript
import { express } from 'cc-backend-ts'
import { clientRouter } from './client'

export function setRoutes(router: express): void {
  router.use(clientRouter)
}
```
{% endtab %}
{% endtabs %}

## Estructura Compleja

Cuando se tiene muchos servicios y cada fichero va creciendo se vuelve difícil de mantener para ello es recomendable dividir en ficheros.

* Tener ficheros individuales para cada servicio: createClient, deleteClient, updateInfoClient, etc
* Tener ficheros para cada entidad, clase: client, account, date, etc.
* Tener interfaces para cada entidad.
* Tener mocks por cada servicio y para cada casuística, es decir cuando todo es correcto, cuando ocurre un fallo controlado, etc.

{% tabs %}
{% tab title="Javascript" %}
```
Proyect
├── package.json
├── src
│    ├── modules
│    │    ├── client 
│    │    │    ├── domain
│    │    │    │    ├── client.entity.js
│    │    │    │    ├── client.mock.js
│    │    │    │    └── client.respository.js
│    │    │    ├── infraestructure
│    │    │    │    ├── client.routes.js
│    │    │    │    └── client.controller.js
│    │    │    ├── application
│    │    │    │    └── client.service.js
│    │    │    └── index.js
│    │    └── setRoutes.js
│    └── application.js
└── jsconfig.json
```
{% endtab %}

{% tab title="TypeScript" %}
```
Proyect
├── package.json
├── src
│    ├── modules
│    │    ├── client 
│    │    │    ├── domain
│    │    │    │    ├── client.entity.ts
│    │    │    │    ├── client.mock.ts
│    │    │    │    └── client.respository.ts
│    │    │    ├── infraestructure
│    │    │    │    ├── client.routes.ts
│    │    │    │    └── client.controller.ts
│    │    │    ├── application
│    │    │    │    └── client.service.ts
│    │    │    └── index.ts
│    │    └── setRoutes.ts
│    └── application.ts
└── jsconfig.json
```
{% endtab %}
{% endtabs %}

## Arquitectura

La arquitectura base que se presenta está basado en una arquitectura limpia. Sin embargo, este no contine todos los principios como Inyección de Dependencia.

Lo esencia de este tipo de estructuras es cumplir con ciertos principios como

* Principio de Inversión de Dependencias
* Separación de Responsabilidades
* Patrón Repositorio
* Patrón Interactor
* Patrón Presentador
* Patrón Mapeador
* Unit Test

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

&#x20;Las capas que se presentan son

* Infraestura: Son responsables de manejar los datos que vienes del exterior.
* Aplicación: Aquí se maneja la lógica de negocio, donde interactúan las componentes del dominio.
* Dominio: Se crear las estructuras, clases, etc que representan al negocio,

Esta es una pequeña representación para impulsar al desarrollador a conocer y aplicar las mejores prácticas al momento de desarrollar sus servicios.

Si se quiere conocer más detalles sobre las Clean Architectures:

* [Blog Introduccion a las arquitecturas limpias](https://medium.com/@diego.coder/introducci%C3%B3n-a-las-clean-architectures-723fe9fe17fa).
* [Clean Architecture Microsoft](https://medium.com/@diego.coder/introducci%C3%B3n-a-las-clean-architectures-723fe9fe17fa)
* [Clean Architecture: La mejor forma de escalar y mantener tu código](https://www.youtube.com/watch?v=y3MWfPDmVqo)

&#x20;



