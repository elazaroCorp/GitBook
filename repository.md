# Repository

Los repositorios son responsables de manejar la escritura y lectura de los datos hacia memoria como:

* Base de datos Sql
* Cache
* Base de Datos NoSql

Un [repositorio ](https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)a diferencia de un DAO este no está estrechamente asociado a la base de datos, en su lugar está ligado al Dominio ([DDD](https://martinfowler.com/bliki/DomainDrivenDesign.html)).&#x20;

Para crear un repository se recomienda usar una clase para mantener el estándar.

{% tabs %}
{% tab title="client.repository.js" %}
```javascript
const { DataBaseFactory, runSqlQuery } = require('cc-backend-ts')
const { Client } = require('./client')

class ClientRepository {
  /**
   * @returns {Promise<Client>}
   */
  async getById(id, rqId) {
    const query = 'SELECT * FROM cliente WHERE id_cliente = @id'
    const request = (await DataBaseFactory.getRequest('neg')).input('id', id)
    const result = await runSqlQuery({ query, request, rqId })
    const client = result[0]
    return new Client({
      nroDocument: client.nroDocument,
      name: client.name,
      mail: client.mail
    })
  }
}

const clientRepository = new ClientRepository()

module.exports = {
  clientRepository,
}
```
{% endtab %}

{% tab title="client.repository.ts" %}
```typescript
import { DataBaseFactory, runSqlQuery } from 'cc-backend-ts'
import { Client } from './client'

export class ClientRepository {
  async getById(id: number, rqId: string): Promise<Client> {
    const query = 'SELECT * FROM cliente WHERE id_cliente = @id'
    const request = (await DataBaseFactory.getRequest('neg')).input('id', id)
    const result = await runSqlQuery({ query, request, rqId })
    const client = result[0]
    return new Client({
      nroDocument: client.nroDocument,
      name: client.name,
      mail: client.mail
    })
  }
}

export const clientRepository = new ClientRepository()
```
{% endtab %}
{% endtabs %}

En el ejemplo se puede ver los siguiente:

* Se define la query en un string.
* Se solicita una instancia de Request usando `DataBaseFactory`
* Se ejecuta la query usando la función `runSqlQuery`
* Se crea y retorna una instancia de la clase `Client` esto porque es parte del dominio.

## DataBaseFactory

Esta es una clase con métodos estáticos para obtener la conexión a la base de datos, para ello es necesario pasar como parámetro el nombre de la conexión (nameConnection).

> [Recordar ](guias-detalladas/server.md#internal-server)que el nombre de la conexión ha sido definido en la clase `Server` en el archivo `application.[ts|js]`

Se tiene los siguientes métodos:

* **getConnection**: Para obtener una instancia de la conexión.
* **getRequest**: Para obtener una instancia de request.
* **getTransaction**: Para obtener una instancia de transacción.

```javascript
const connectionDB = await DataBaseFactory.getConnection('nameConnection')
const requestDB = await DataBaseFactory.getRequest('nameConnection')
const transactionDB = await DataBaseFactory.getTransaction('nameConnection')
```

## runSqlQuery



&#x20;
