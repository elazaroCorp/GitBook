# Service

Los servicios son responsables de manejar la lógica de negocio tales como:

* Validaciones de datos
* Condicionales lógicas
* Llamadas a bases de datos usando un repositorio.
* Envió de correos.
* Notificaciones
* Lanzamiento de excepciones
* Filtración de datos

Estos archivos son los que generalmente se actualizan al cambiar los requerimientos por parte del negocio.&#x20;

Para crear un servicio se recomienda usar una clase para mantener el estándar.

{% tabs %}
{% tab title="client.service.js" %}
```javascript
const { clientRepository } = require('./client.repository')

class ClientService {
  async getById(id, rqId) {
    const client = await clientRepository.getById(id, rqId)
    return client.info
  }
}

const clientService = new ClientService()

module.exports = { clientService }
```
{% endtab %}

{% tab title="client.service.ts" %}
```typescript
import { ClientInfo } from './client'
import { clientRepository } from './client.repository'

export class ClientService {
  async getById(id: number, rqId: string): Promise<ClientInfo> {
    const client = await clientRepository.getById(id, rqId)
    return client.info
  }
}

export const clientService = new ClientService()
```
{% endtab %}
{% endtabs %}

> ✅ Todos los servicios deben recibir los parámetros necesarios y además el rqId de manera obligatoria.

