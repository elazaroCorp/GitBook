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

### Try / Catch

Siempre se debe agregar un try/catch en cada método para poder capturar el error y tratarlo. Sin embargo, en este caso ya no es necesario porque se agrega cuando se instancia en las [Rutas](../../routes.md#controller).

Además, todos los endpoints tienen asociados un middleware de error, con lo cual el servicio no va ser interrumpido.

### Response - StatusCode

Ya no es necesario retornar una instancia de res en cada método, esto ya lo realiza la librería.

### **Normalmente**

```javascript
async getById(req, res, next) {
    // ...other
    const data = await clientService.getById(req.body.id, rq.id)
    return res.status(200).send(data)
}
```

### **Valores por defecto**

* **statusCode**: 200 por defecto.
* **data**: el valor que retorna el método en return.

```javascript
async getById(req, res, next) {
    // ...other
    const data = await clientService.getById(req.body.id, rq.id)
    return data
}
```

### **Asignación**

Para cambiar el status, se tiene que retornar la siguiente estructura `{ statusCode, data}`

```javascript
async getById(req, res, next) {
    // ...other
    const data = await clientService.getById(req.body.id, rq.id)
    return { statusCode: 201 , data }
}
```

Si no se envía el objeto data, por ejemplo cuando se registrar un cliente. Este retornara un objeto vació por defecto.

```javascript
async createClient(req, res, next) {
    // ...other
    const data = await clientService.createClient(req.body, rq.id)
    return { statusCode: 201 }
}
/**
* Code: 201
* Response body: {}
*/
```

### Sin Asignación

En caso que no exista un objeto return, se retorna `{ statusCode: 200, data: {} }`

```javascript
async deleteClient(req, res, next) {
    // ...other
    await clientService.deleteClient(req.body, rq.id)
}
/**
* Code: 200
* Response body: {}
*/
```

