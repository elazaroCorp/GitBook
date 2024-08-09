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

### Entity

Como los repositorios reciben y retornan objetos creamos la clase `Client` como una entidad que pertenece al dominio.

{% tabs %}
{% tab title="client.entity.js" %}
```javascript
/**
 * @typedef {Object} ClientProps
 * @property {string} name - El nombre del cliente.
 * @property {string} mail - El correo electrónico del cliente.
 * @property {string} nroDocument - El número de documento del cliente.
 */

/**
 * @typedef {Object} ClientInfo
 * @property {string} name - El nombre del cliente.
 * @property {string} mail - El correo electrónico del cliente.
 */

class Client {
  /**
   * @private
   * @readonly
   * @type {ClientProps}
   */
  props

  constructor(props) {
    this.props = props
  }

  /**
   * @returns {ClientInfo}
   */
  get info() {
    return {
      name: this.props.name,
      mail: this.props.mail,
    }
  }
}

module.exports = { Client }
```
{% endtab %}

{% tab title="client.entity.ts" %}
```typescript
export interface ClientProps {
  name: string
  mail: string
  nroDocument: string
}

export interface ClientInfo {
  name: string
  mail: string
}

export class Client {
  private readonly props: ClientProps

  constructor(props: ClientProps) {
    this.props = props
  }

  get info(): ClientInfo {
    return {
      name: this.props.name,
      mail: this.props.mail
    }
  }
}
```
{% endtab %}
{% endtabs %}

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

Es una función que nos permite ejecutar la query, pasando los siguientes parametros en un objeto json:

**Input**

* request: instancia de la clase `Request` de mssql
* query: contenido de la query en `string`
* rqId: identificador de la petición

**Response**

* Retorna el valor de `recordset`

Esta función tiene la finalidad de escanear las consultas para generar

* Generar un trace o camino de todos los componentes (**tablas DB**) por el cual paso esta petición gracias a la variable `rqId`.

```javascript
const result = await runSqlQuery({ query, request, rqId }
```
