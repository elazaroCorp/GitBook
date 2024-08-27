# HttpProxy

Este recurso nos permite abstraer las consultas hacia apis.

## Contexto&#x20;

La arquitectura actual tiene dos capas, apis externas (experiencia) e internas (negocio) y esto tipo de arquitecturas posee los siguientes retos.

* Saber organizar los enpoints de cada api.
* Al consumir un api se debe crear la URL (query, params) y el body.
* Cuando se consume una mismo endpoint en más de dos lugares se repite las definiciones.
* Cada desarrollador organiza sus apis como mejor le parezca.
* Poder consumir apis de otras celulas es complicado.

La siguiente imagen muestra una abstracción de la arquitectura de apis y células, cada vez que se crean nuevas apis se vuele complicado de mantener y escalar, generando código duplicado en muchos casos.

<figure><img src=".gitbook/assets/image (3) (1).png" alt=""><figcaption><p>Resumen de la arquitectura entre células</p></figcaption></figure>

Adicional a ello, el consumo de las apis debe ser un estándar para todas las células, donde todos deben cumplir con los requerimientos de Seguridad y Calidad.

Por ello, se propone esta sección donde presentan estructuras, clases, ejemplos, etc.

### Api Name

Esta es una parte importante al momento de definir un HttpProxy, este valor es usando por detrás para hacer las consultas. Este valor del nombre es el que se le ha asignado al servicio usando `azure-pipelines` se tiene un ejemplo:

```yaml
- template: .devops/pipeline-templates/stages/defaultStage.yml@templates
  parameters:
    .....
    productName: 'portalptc'
    apiPath: 'portalptc-client'
    externalExpose: false
# Production
- template: .devops/pipeline-templates/stages/defaultStage.yml@templates
  parameters:
    ....
    productName: 'portalptc'
    apiPath: 'portalptc-client'
    externalExpose: false
```

El campo **`apiPath`** tiene un valor de `portalptc-client` este valor hace referencia a dos cosas:

* Nombre de la celula: portalptc
* Nombre del api: **client**

Este valor del nombre `client` es el que se va a usar al instanciar las propiedades de la clase HttpProxy en el parametro `apiName`.

> Por qué no se usa portalptc? este valor debe ser instanciado dentro de una url

### Base URL

Esta definición hace referencia a una parte que comparten todas las url que tienen las apis de una celula.

Por ejemplo, se tiene las siguientes urls:

* [https://bttccslab006.azurekl.net/portalptc-](https://bttccslab006.azurekl.net/portalptc-)client
* [https://bttccslab006.azurekl.net/portalptc-](https://bttccslab006.azurekl.net/portalptc-)user
* [https://bttccslab006.azurekl.net/portalptc-](https://bttccslab006.azurekl.net/portalptc-)account

Para este caso se define el valor de&#x20;

* base URL: [https://bttccslab006.azurekl.net/portalptc-](https://bttccslab006.azurekl.net/portalptc-)

> 👍 Este valor al ser confidencial va a ser definido como un secreto en Azure, por cada ambiente que se tenga DEV, QA, PROD.

Las urls de un api por ambiente pueden visualizarse en el archivo README.md de cada api, en la sección \[URLs]

## HttpProxy

Esta clase nos ayuda a crear un intermediario entre el consumidor y el api. Esto se realiza por los siguientes motivos:

* Imprimir los datos necesarios para hacer soporte.
* Validar parámetros obligatorios como rqId, basicAuth.
* Hacer envió de datos hacia una **api\_track** para generar diagramas (solo en DEV).
* Abstraer la forma en la que se consumen las apis, es decir se en un futuro se quiere cambiar el uso de axios a fetch será más fácil y transparente para los desarrolladores.
* Enviar los datos como objetos ya sea en los parámetros y query.

### Recomendaciones

* Si el api es <mark style="background-color:green;">especifica</mark> de la célula, se recomienda crearlo en una librería de la célula.
* Si el api es <mark style="background-color:yellow;">compartida</mark> o general, se recomienda crearlo en una librería general para todas las células, ejemplo api de seguridad.
* Las apis deben definirse en typescript para tener los tipos de datos en el input y output de cada api.

<figure><img src=".gitbook/assets/image (4) (1).png" alt="" width="518"><figcaption></figcaption></figure>

### Módulo

Para hacer uso de la clase HttpProxy este primero debe estar asociado a un [módulo ](guias-detalladas/modules/)de la Api a la cual hace referencia.

* Se tiene que crear un archivo \<name\_module>.api.module.ts
* Se crear la clase que extiende de HttpProxy con la nomenclatura `<Name>ApiModule`
* Se pasar tiene que pasar el nombre del módulo en el contructor super.
* Se define los métodos de ese modulo con su interfaz en el input y output.

### SendRequest

&#x20;Este método de la clase heredada nos permite hacer él envió de las peticiones donde se tiene que pasar los siguientes parámetros.

<table><thead><tr><th width="146">Campo</th><th width="229">Tipo</th><th>Descripción</th></tr></thead><tbody><tr><td>method</td><td><p></p><pre class="language-typescript"><code class="lang-typescript">'POST'
'PUT'
'GET'
'DELETE'
</code></pre></td><td>Método Http</td></tr><tr><td>resource</td><td>string</td><td>Nombre del recurso</td></tr><tr><td>rqId</td><td>uuid</td><td>Identificador de la petición</td></tr><tr><td>body?</td><td>Object</td><td>Input body </td></tr><tr><td>params?</td><td>Object</td><td>Parámetros URL</td></tr><tr><td>query?</td><td>Object</td><td>Parámentros URL query</td></tr></tbody></table>

{% tabs %}
{% tab title="name.api.module.ts" %}
```typescript
export class NameApiModule extends HttpProxy {
  constructor() {
    super('name_module')
  }

  /** @description Description */
  async methodExample(input: exampleInput, rqId: string): Promise<exampleResponse> {
    return this.sendRequest({
      method: 'GET',
      resource: 'get_example',
      body: { id }
      rqId,
    })
  }
}

export const nameApiModule = new NameApiModule()
```
{% endtab %}
{% endtabs %}

Para un ejemplo más realista usamos las rutas definidas en la [sección anterior](guias-detalladas/modules/routes.md#ejemplos) donde se tiene un api negocio de clientes.

{% tabs %}
{% tab title="client.api.module.ts" %}
```typescript
export class ClientApiModule extends HttpProxy {
  constructor() {
    super('client')
  }

  /** @description Obtine un cliente por su id */
  async getById(input: GetByIdInput, rqId: string): Promise<GetByIdResponse> {
    const { id, idv2, idParam, test } = input
    return this.sendRequest({
      method: 'POST',
      resource: 'get_by_id',
      body: { id },
      query: { idv2  },
      params: { id: idParam, test },
      rqId,
    })
  }
}

export const clientApiModule = new ClientApiModule()

interface GetByIdInput {
  id: number
  idv2: string
  idParam: string
  test: string
}

interface GetByIdResponse {
  name: string
  mail: string
}
```
{% endtab %}
{% endtabs %}

### Relación Routes

En la siguiente imagen muestra la relación entre un HttpProxy y las Rutas del api a la cual hace referencia.

<figure><img src=".gitbook/assets/image (14).png" alt=""><figcaption><p>Relación entre HttpProxy (Izquierda) con una Api (derecha)</p></figcaption></figure>

### Usar ApiModule

Existen dos formas esta es la primera cuando solo se quiere usar un módulo y no un api completo. Para usar api module en un api se tiene que pasar los siguientes parámetros usando la función **setProps**.

> 🤔 Por que usar una función en lugar del constructor? Esto se debe a que los parámetros son obtenidos como secretos por una función asíncrona. Un constructor no puede ejecutar funciones asíncronas y tampoco puede esperar a que termine.



<table><thead><tr><th width="146">Campo</th><th width="229">Tipo</th><th>Descripción</th></tr></thead><tbody><tr><td><a href="httpproxy.md#url-base">baseUrl</a></td><td>string</td><td>URL base del api, por ejemplo <br><a href="https://bttccslab006.azurekl.net/portaltest-">https://bttccslab006.azurekl.net/portalptc-</a></td></tr><tr><td>basicAuth</td><td>string</td><td>Valor de la autorización Basic</td></tr><tr><td>localUrl</td><td>string</td><td>Url para hacer pruebas en local, ejemplo <br><a href="http://localhost:3000/">http://localhost:3000/</a> </td></tr><tr><td><a href="httpproxy.md#apiname">apiName</a></td><td>string</td><td>Nombre del api</td></tr></tbody></table>

{% tabs %}
{% tab title="application.ts" %}
```typescript
async function startApplication() {
  await secrets.uploadAll()

  clientApiModule.setProps({
    apiName: 'client',
    baseUrl: 'https://bttccslab006.azurekl.net/portaltest-',
    basicAuth: secrets.value.basicAuth,
    localUrl: 'http://localhost:3000/',
  })
  
  setRoutes(routes)
  const server = new ExternalServer({
    bearerStrategyOptions: {
      clientID: secrets.value.clientID,
      isB2C: secrets.value.isB2C === 'true',
      policyName: secrets.value.policyName,
      scope: secrets.value.scope,
      tenantName: secrets.value.tenantName,
      customDomainName: secrets.value.customDomainName,
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
{% endtabs %}

Posteriormente, lo puedes usar la instancia en cualquier parte de su aplicación ya sea un controlador de un api experiencia, en un servicio interno, etc.

{% tabs %}
{% tab title="client.controller.js" %}
```javascript
const { Rq } = require('cc-backend-ts')
const { clientService } = require('./client.service')

class ClientController {
  async getById(req, res, next) {
    const rq = new Rq(req)
    return clientApiModule.getById(
      {
        id: req.body.id,
        idParam: 'id',
        test: 'test',
        idv2: 'idv2',
      },
      rq.id
    )
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
     return clientApiModule.getById(
      {
        id: req.body.id,
        idParam: 'id',
        test: 'test',
        idv2: 'idv2',
      },
      rq.id
    )
  }
}
```
{% endtab %}
{% endtabs %}

## HttpProxyApi

Esta es una clase que al extender nos permite agrupar varios ApiModule. Esto con el fin de tener definido todos los servicios que tiene un api.&#x20;

Para hacer uso de esto se tiene que definir lo siguiente:

* Se crea un archivo \<name\_module>.api.ts
* Se define los módulos que contiene el api mediante una interfaz.
* Se crea una clase con el nombre \`\<name>Api que extienda de HttpProxyApi\<T>, donde T es la interfaz de los módulos.
* En el constructor se le pasa el nombre del api y las instancias de apiModules.

{% tabs %}
{% tab title="client.api.ts" %}
```typescript
import { HttpProxyApi } from '../HttpProxyApi'
import { clientApiModule, ClientApiModule } from './Modules/Client.api.module'

interface ApiModules {
  client: ClientApiModule
}

const apiModules: ApiModules = {
  client: clientApiModule,
}

class ClientApi extends HttpProxyApi<ApiModules> {
  constructor() {
    super('client', apiModules)
  }
}

export const clientApi = new ClientApi()
```
{% endtab %}
{% endtabs %}

### Usar API

En este caso se sigue los mismos pasos que [ApiModule](httpproxy.md#usar-apimodule) donde se define las propiedades al inicio de application. Sin embargo, ya no se **define el parámetro apiName** porque este ya se asignó en el constructor.

{% tabs %}
{% tab title="application.ts" %}
```typescript
async function startApplication() {
  await secrets.uploadAll()

  clientApi.setProps({
    baseUrl: secrets.value.baseURL,
    basicAuth: secrets.value.basicAuth,
    localUrl: 'http://localhost:3000/',
  })
  
  setRoutes(routes)
  const server = new ExternalServer({
    //...
  })
  const app = await server.init(routes)
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}

module.exports = startApplication
```
{% endtab %}
{% endtabs %}

Finalmente, para usar un servicio primero se tiene que acceder al módulo y luego al método.

La clase HttpProxyApi tiene una propiedad **`module`** que te permite acceder a los módulos que se han definido.

{% tabs %}
{% tab title="client.controller.js" %}
```javascript
const { Rq } = require('cc-backend-ts')
const { clientService } = require('./client.service')

class ClientController {
  async getById(req, res, next) {
    const rq = new Rq(req)
    return clientApi.module.client.getById(  // 👈 Aqui se usa .module
      {
        id: req.body.id,
        idParam: 'id',
        test: 'test',
        idv2: 'idv2',
      },
      rq.id
    )
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
    return clientApi.module.client.getById(  // 👈 Aqui se usa .module
      {
        id: req.body.id,
        idParam: 'id',
        test: 'test',
        idv2: 'idv2',
      },
      rq.id
    )
  }
}
```
{% endtab %}
{% endtabs %}

&#x20;



&#x20;&#x20;

