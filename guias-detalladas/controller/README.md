# Controller

Los controladores son responsables de manejar **las solicitudes (Request)** entrantes y devolver **las respuestas** (Response) al cliente, este no debe manejar ninguna lógica de negocio.

Un controlador será definido mediante una clase en la cual los métodos son las acciones y están asociadas a una ruta.

{% tabs %}
{% tab title="client.controller.js" %}
```javascript
const { Rq } = require('cc-backend-ts')
const { clientService } = require('./client.service')

class ClientController {
  async getById(req, res, next) {
    const rq = new Rq(req)
    return clientService.getById(req.body.id, rq.id)
  }
}

module.exports = { ClientController }
```
{% endtab %}

{% tab title="client.controller.ts" %}
```typescript
import { Rq } from 'cc-backend-ts'
import { clientService } from './client.service'

export class ClientController {
  async getById(req: any, res: any, next: any): Promise<any> {
    const rq = new Rq(req)
    return clientService.getById(req.body.id, rq.id)
  }
}
```
{% endtab %}
{% endtabs %}

> ✅ El controlador solo deben extraer los datos necesarios del objeto **req** para enviarlos al servicio, cualquier detalle extra debe ser manejado en otro archivo **service**.
