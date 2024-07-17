---
description: Clase abstracta para extender y crear una Api Experiencia o Api Negocio.
---

# Server

Para crear un server primero hay que tener ciertas consideraciones:

1.  **Crear archivo Application**

    Se debe crear un archivo Application en la estructura de preferencia en la carpeta `src`.

{% tabs %}
{% tab title="Application.js" %}
```javascript
const { express, InternalServer } = require('cc-backend-ts')
const routes = express.Router()
setRoutes(routes)

async function startApplication() {
  await secrets.uploadAll()
  const server = new Server(propsServerSecrets)
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}

module.exports = startApplication
```
{% endtab %}

{% tab title="Application.ts" %}
```typescript
import {express, InternalServer} from 'cc-backend-ts'
const routes = express.Router()

export async function startApplication() {
  const server = new Server(propsServer)
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}
```
{% endtab %}
{% endtabs %}

En el ejemplo básico que sirve como seudocódigo para instanciar un Server. En secciones posteriores se pondrá ejemplos más realistas.

<table><thead><tr><th width="190">Pasos</th><th>Descripcion</th></tr></thead><tbody><tr><td>setRoutes(..)</td><td>Esta es una función que añade las rutas usando el método <code>use</code> de express.<mark style="color:blue;"><code>Router</code></mark></td></tr><tr><td>secret.uploadAll()</td><td>Carga los secretos definidos en la instancia secret. Los valores serán usando en el constructor de la clase Server.</td></tr><tr><td>new Server(props)</td><td>Se instancia server pasando los props necesarios y ejecutando el método <code>init</code> .</td></tr></tbody></table>

## External Server

Clase para crear una Api de experiencia.&#x20;

> Una Api de experiencia es aquella que está expuesta a internet y es usado por el frontend.

**Parámetros**

Objeto con los siguientes parámetros:&#x20;

*   bearerStrategyOptions: Objeto con los valores para instanciar BearerStrategy de Azure Passport.&#x20;



    | name             | description                     | type    |
    | ---------------- | ------------------------------- | ------- |
    | clientID         | Client ID                       | string  |
    | isB2C            | Indica si es B2C                | boolean |
    | policyName       | Nombre de la política           | string  |
    | scope            | Alcance                         | string  |
    | tenantName       | Nombre del tenant               | string  |
    | customDomainName | Nombre de dominio personalizado | string  |

Usando la estructura anteriormente mencionada instanciamos ExternalServer, como valores de parametro usamos [`secrets.bearerStrategyOptions`](secret.md#extender-secretbase) que ya tiene definido los valores.

{% tabs %}
{% tab title="Application.js" %}
```javascript
const { express, InternalServer } = require('cc-backend-ts')
const routes = express.Router()

setRoutes(routes)

async function startApplication() {
  await secrets.uploadAll()
  const server = new ExternalServer({
    bearerStrategyOptions: secrets.bearerStrategyOptions
  })
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}

module.exports = startApplication
```
{% endtab %}

{% tab title="Application.ts" %}
```typescript
import { express, InternalServer } from 'cc-backend-ts'
const routes = express.Router()

setRoutes(routes)

export async function startApplication() {
  await secrets.uploadAll()
  const server = new ExternalServer({
    bearerStrategyOptions: secrets.bearerStrategyOptions
  })
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}
```
{% endtab %}
{% endtabs %}

## Internal Server

Esta clase sirve para crear Apis internas o negocio.

> Las Apis internas son aquellas que se encuentran en una red interna y no tienen exposición a internet, son usadas por las apis de experiencias.

**Parámetros**

Objeto con los siguientes parámetros:&#x20;

* bearerStrategyOptions
* basicAuthProps: Objeto que contiene username y password para la autenticación basica.
* connDBProps (Opcional): Objeto que contiene las conexiones a las bases de datos.

{% tabs %}
{% tab title="Application.js" %}
```javascript
const { express, InternalServer } = require('cc-backend-ts')
const { secrets } = require('./Secret')
const { setRoutes } = require('./modules/SetRoutes')

const routes = express.Router()
setRoutes(routes)

async function startApplication() {
  await secrets.uploadAll()
  const server = new InternalServer({
    bearerStrategyOptions: secrets.bearerStrategyOptions,
    basicAuthProps: secrets.basicAuthProps,
    connDBProps: {
      neg: secrets.dbNegProps,
      seg: secrets.dbSecProps,
    },
  })
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}

module.exports = startApplication
```
{% endtab %}

{% tab title="Application.ts" %}
```javascript
import { express, InternalServer } from 'cc-backend-ts'
import { secrets } from './Secret'
import { setRoutes } from './modules/SetRoutes'

const routes = express.Router()
setRoutes(routes)

export async function startApplication(): Promise<void> {
  await secrets.uploadAll()
  const server = new InternalServer({
    bearerStrategyOptions: secrets.bearerStrategyOptions,
    basicAuthProps: secrets.basicAuthProps,
    connDBProps: {
      neg: secrets.dbNegProps,
      seg: secrets.dbSecProps
    }
  })
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}
```
{% endtab %}
{% endtabs %}

El desarrollador puede asignar los nombres a sus conexiones de base de datos como se muestra en el ejemplo.

* **neg**: Base de datos de negocio
* **seg:** Base de datos de seguridad.



