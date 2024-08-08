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

{% tab title="client.repository.ts" %}
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





&#x20;
