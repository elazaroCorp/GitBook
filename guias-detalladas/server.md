---
description: Como crear un servidor ya sea para una Api Experiencia o Api Negocio.
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

## Internal Server

Esta clase sirve para crear Apis internas o negocio.

> Las Apis internas son aquellas que se encuentran en una red interna y no tienen exposición a internet.



