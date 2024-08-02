# Request Rq

## Rq

Rq es una clase base que valida y retorna ciertos datos usando el objeto **req** de express.&#x20;

Para hacer uso de la clase Rq es necesario instancia y pasar como parámetro req.

{% tabs %}
{% tab title="anyMiddleware.js" %}
```javascript
const { Rq } = require('cc-backend-ts')

async anyMiddleware(req, res, next) {
    const rq = new Rq(req)
    const info = {
        ip: rq.ip,
        id: rq.id,
        log: rq.log
    } 
}
```
{% endtab %}

{% tab title="anyMiddleware.ts" %}
```typescript
import { Rq } from 'cc-backend-ts'

async anyMiddleware(req: any, res: any, next: any): Promise<any> {
    const rq = new Rq(req)
        const info = {
        ip: rq.ip,
        id: rq.id,
        log: rq.log
    } 
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="186">Métodos Get</th><th>Descripcion</th></tr></thead><tbody><tr><td>ip</td><td>Retorna la ip usando req.headers o req.socket</td></tr><tr><td>id</td><td>Retorna el id de la peticion (uuid).</td></tr><tr><td>log</td><td>Retorna una instancia de log Signale.</td></tr></tbody></table>

## ExternalRq

Es una extensión de la clase Rq que es utilizada en las apis externas.

{% tabs %}
{% tab title="anyMiddlewareExternal.js" %}
```javascript
const { Rq } = require('cc-backend-ts')

async anyMiddleware(req, res, next) {
    const rq = new ExternalRq(req)
    const info = {
        tokenBearer: rq.tokenBearer,
        userId: rq.userId,
        objectId: rq.objectId
    } 
}
```
{% endtab %}

{% tab title="anyMiddlewareExternal.ts" %}
```typescript
import { Rq } from 'cc-backend-ts'

async anyMiddleware(req: any, res: any, next: any): Promise<any> {
    const rq = new Rq(req)
    const info = {
        tokenBearer: rq.tokenBearer,
        userId: rq.userId,
        objectId: rq.objectId
    } 
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="186">Métodos Get</th><th>Descripcion</th></tr></thead><tbody><tr><td>tokenBearer</td><td>Retorna el token bearer de la autenticación</td></tr><tr><td>userId</td><td>Retorna el id del usuario</td></tr><tr><td>objectId</td><td>Retorna el id del object que es extrae del tokenBearer</td></tr></tbody></table>

## InternalRq

Es una extensión de la clase Rq que es utilizada en las apis internas.

{% tabs %}
{% tab title="anyMiddlewareInternal.js" %}
```javascript
const { Rq } = require('cc-backend-ts')

async anyMiddleware(req, res, next) {
    const rq = new Rq(req)
    const info = {
        tokenBasic: rq.tokenBasic
    }
}
```
{% endtab %}

{% tab title="anyMiddlewareInternal.ts" %}
```typescript
import { Rq } from 'cc-backend-ts'

async anyMiddleware(req: any, res: any, next: any): Promise<any> {
    const rq = new InternalRq(req)
    const info = {
        tokenBasic: rq.tokenBasic
    } 
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="186">Métodos Get</th><th>Descripcion</th></tr></thead><tbody><tr><td>tokenBasic</td><td>Retorna token basic este es igual a <mark style="color:blue;"><code>req?.headers?.authorization</code></mark></td></tr></tbody></table>
