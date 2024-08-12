# Routes

Las rutas de un módulo son definidas en una clase que debe extender de **Routes\<T>**. Para ello se debe pasar como parámetros de la clase extendida el nombre del módulo y una instancia del controller.

La clase Routes necesita el tipo \<T> que representa al controller para poder llamar a los métodos usando `this`.&#x20;

{% tabs %}
{% tab title="client.controller.js" %}
```javascript
const { ClientController } = require('./client.controller')

/** @extends {Routes<ClientController>} */
class ClientRoutes extends Routes {
  constructor() {
    super('client', new ClientController())
  }

  setRoutes() {
    // Definir sus rutas aqui
  }
}

const clientRouter = new ClientRoutes().router
module.exports = { clientRouter }
```
{% endtab %}

{% tab title="client.controller.ts" %}
```typescript
import { ClientController } from './client.controller'

class ClientRoutes extends Routes<ClientController> {
  constructor() {
    super('client', new ClientController())
  }

  setRoutes(): void {
    // Definir sus rutas aqui
  }
}

export const clientRouter = new ClientRoutes().router
```
{% endtab %}
{% endtabs %}

> ✅ Todas las rutas deben estar dentro de la función setRoutes() .

## SetRoutes()

Para definir una ruta se debe usar algún método de acción heredado de la clase Routes.

* Métodos:  <mark style="color:blue;">`get`</mark>, <mark style="color:blue;">`post`</mark>, <mark style="color:blue;">`put`</mark>, <mark style="color:blue;">`delete`</mark>.

{% tabs %}
{% tab title="client.controller.js" %}
```javascript
const { ClientController } = require('./client.controller')

/** @extends {Routes<ClientController>} */
class ClientRoutes extends Routes {
  // ....
  
  setRoutes() {
    this.post({
      // Parametros
    })
    this.put({
      // Parametros
    })
    this.get({
      // Parametros
    })
    this.delete({
      // Parametros
    })
  }
}
```
{% endtab %}

{% tab title="client.controller.ts" %}
```typescript
import { ClientController } from './client.controller'

class ClientRoutes extends Routes<ClientController> {
  // ....
  
  setRoutes(): void {
    this.post({
      // Parametros
    })
    this.put({
      // Parametros
    })
    this.get({
      // Parametros
    })
    this.delete({
      // Parametros
    })
  }
}
```
{% endtab %}
{% endtabs %}

Cada ruta debe tener los siguientes parámetros definidos en un objeto `{}`:

<table><thead><tr><th width="140">Parámetros</th><th width="111">Opcional/Requerido<select><option value="0Q1ARrrBHaBM" label="Opcional" color="blue"></option><option value="6gbmhxC1uFKA" label="Requerido" color="blue"></option></select></th><th>Descripción</th></tr></thead><tbody><tr><td>resource</td><td><span data-option="6gbmhxC1uFKA">Requerido</span></td><td>Nombre del recurso</td></tr><tr><td>validator</td><td><span data-option="0Q1ARrrBHaBM">Opcional</span></td><td>Esquema de validación en request o response, ya sea en Zod o Yoi</td></tr><tr><td>swagger</td><td><span data-option="6gbmhxC1uFKA">Requerido</span></td><td>Definición de json swagger.</td></tr><tr><td>before</td><td><span data-option="0Q1ARrrBHaBM">Opcional</span></td><td>Arreglo de middlewares que se ejecutaran antes del controller.</td></tr><tr><td>controller</td><td><span data-option="6gbmhxC1uFKA">Requerido</span></td><td>Método del controller. Por detrás se encapsula en un try/catch</td></tr></tbody></table>

## Resource

El nombre del recurso debe iniciar sin `/` , esto ya lo agrega la librería.

<mark style="color:green;">`<name_module>/<resource>`</mark>

```javascript
this.get({
    resource: 'get_by_id/:id'
})

/**
* Ruta final
* /client/get_by_id/:id
*/
```

## Controller

El controlador se define usando la propiedad controller

<mark style="color:green;">`this.controller.<Method>`</mark>

```javascript
this.get({
    controller: this.controller.getById
})
```

## Before

Aquí se puede agregar un arreglo de middlewares antes de ejecutar el controller.

```javascript
this.get({
    before: [accessMiddleware],
    controller: this.controller.getById
})

const accessMiddleware = (req, res, next) => {
  if (req.headers.token === 1234) {
    return next()
  } else {
    throw new Error('Access token denied')
  }
}
```

## Validator

La validación puede realizarse en la entrada y salida de datos, por ello se tiene dos definiciones.

> ✅ En los esquemas de validación se puede usar zod o joi, eso dependerá del desarrollador.&#x20;

### Request

Se valida los datos de input como pueden ser `headers`, `params`, `query`, `body`.

```javascript
this.get({
  validator: {
    request: {
      headers: Joi.object(),
      query: Joi.object(),
      params: z.object(),
      body: z.object({
          id: z.string()
        }).strict()
    },
})
```

Si los datos que se validan son incorrectos se lanzara un error con la siguiente estructura.

```json
{
  "error": {
    "statusCode": 422,
    "message": "Invalid Input",
    "extra": "{...}"
  }
}
```

En el campo extra va los detalles del error, esto va depender de la respuesta de Zod o Joi.

```javascript
{
  "extra": {
      "data": {
        "id": "123456",
        "test": "info"
      },
      "error": {
        "issues": [
          {
            "code": "unrecognized_keys",
            "keys": [
              "test"
            ],
            "path": [],
            "message": "Unrecognized key(s) in object: 'test'"
          }
        ],
        "name": "ZodError"
      }
}
```

### Response

Se valida los datos de output este solo puede ser el parámetro `response`.

```javascript
this.get({
  validator: {
    response: Joi.object({
      nroDocument: Joi.string(),
      name: Joi.string(),
      mail: Joi.string().required()
    })
})
```

Si los datos que se validan son incorrectos se lanzara un error con la siguiente estructura.

```javascript
{
  "error": {
    "statusCode": 500,
    "message": "Invalid Output",
    "extra": "{...}"
  }
}
```

Ejemplo usando Joi

```javascript
{
  "extra": {
      "data": {
        "name": "John Doe",
        "email": "jhonDoe@credicorp.com",
        "phone": "987654321",
        "test": "test"
      },
      "error": [
        {
          "message": "\"mail\" is required",
          "path": [
            "mail"
          ],
          "type": "any.required",
          "context": {
            "label": "mail",
            "key": "mail"
          }
        }
      ]
}
```

## Swagger

La documentación se define usando un objeto con la clave `swagger` , se tiene el siguiente ejemplo donde se mapean todos los parámetros disponibles.

```javascript
this.post({
  swagger: {
        description: 'Register Client',
        consumes: ['application/json'],
        produces: ['multipart/form-data'],
        tags: ['Client'],
        summary: 'Register'
        deprecaded: false,
        ignore: false,
        parameters: {
          body: {
            in: 'body',
            required: true,
            schemaRef: 'ClientRegisterBody'
          },
          id: {
            in: 'path',
            required: true,
            type: 'string'
          }
       },
       responses: { 200: { schemaRef: 'ClientRegisterResponse' } },
  }
})
```

Sin embargo, muchos de ellos son opcionales con valores por defecto. Por ello, en general se van a definir los siguientes parámetros **description, responses y parameters**.

```javascript
this.post({
  swagger: {
        description: 'Register Client'
        parameters: {
          body: {
            in: 'body',
            required: true,
            schemaRef: 'ClientRegisterBody'
          }
       },
       responses: { 200: { schemaRef: 'ClientRegisterResponse' } },
  }
})
```

### Swagger Autogen

El desarrollo de la definición swagger está desarrolladas sobre la librería `Swagger autongen` . Esta u otras librerías usan comentarios para definir los endpoint, esto impide a que el editor de código le proporcione la ayuda al desarrollador. &#x20;

**Requerimientos:**

* Las rutas tienen que definirse en un archivo con el siguiente nombre `<name>.routes.ts` o `<name>.routes.js`
* Se tiene que tener un archivo `swagger-autogen`&#x20;

{% tabs %}
{% tab title="swagger-autogen.js" %}
```javascript
const { Swagger } = require('cc-backend-ts')
const { ClientSwagger } = require('./src/modules/client')

const doc = {
  info: {
    version: '1.0.0',
    title: 'API Clientes',
    description: 'Api negocio clientes',
  },
  host: 'localhost:3000',
  definitions: {
    ...ClientSwagger,
  },
}

const swagger = new Swagger(doc)
swagger.run()
```
{% endtab %}

{% tab title="swagger-autogen.ts" %}
```typescript
import { Swagger } from 'cc-backend-ts'
import { ClientSwagger } from './src/modules/client'

const doc = {
  info: {
    version: '1.0.0',
    title: 'API Clientes',
    description: 'Api negocio clientes',
  },
  host: 'localhost:3000',
  definitions: {
    ...ClientSwagger
  }
}

const swagger = new Swagger(doc)
swagger.run()

```
{% endtab %}
{% endtabs %}

* Agregar en los comandos npm en `package.json`

{% tabs %}
{% tab title="package.json [js]" %}
```javascript
"scripts": {
   //"...",
   "swagen": "node swagger-autogen.js",
}
```
{% endtab %}

{% tab title="package.json [ts]" %}
```typescript
"scripts": {
   //"...",
   "swagen": "ts-node --pretty --transpile-only swagger-autogen.ts"
}
```
{% endtab %}
{% endtabs %}

* Ejecutar el comando, cada vez que se cambie la definición. Se va a crear un archivo `swagger-generate.json` el cual será usado por los pipelines de Azure.

```bash
npm run swagen   
```

### Parámetros Endpoint

Los parámetros disponibles son:

<table><thead><tr><th width="145">Campo</th><th width="195">Tipo</th><th width="265">Descripción</th><th>Default Value</th></tr></thead><tbody><tr><td>description</td><td>string</td><td>Descripción</td><td>-</td></tr><tr><td>consumes?</td><td><a href="routes.md#iotype">IOType</a>[]</td><td>Tipos de entrada que consume</td><td><code>json</code></td></tr><tr><td>produces?</td><td><a href="routes.md#iotype">IOType</a>[]</td><td>Tipos de salida que produce</td><td><code>json</code></td></tr><tr><td>tags?</td><td>string[]</td><td>Etiquetas para la organización y filtrado</td><td>name_module</td></tr><tr><td>summary?</td><td>string</td><td>Resumen corto del propósito</td><td>null</td></tr><tr><td>deprecaded?</td><td>boolean</td><td>Indica si está en desuso</td><td><code>false</code></td></tr><tr><td>ignore?</td><td>boolean</td><td>Indica si se debe ignorar en la documentación</td><td><code>false</code></td></tr><tr><td>parameters?</td><td>{[K: string]: <a href="routes.md#parameterswagger">ParameterSwagger</a> }</td><td>Objeto que define los parámetros</td><td>-</td></tr><tr><td>responses?</td><td>{[K: number]: <a href="routes.md#responseswagger">ResponseSwagger</a> }</td><td>Objeto que define las respuestas</td><td>-</td></tr><tr><td>security?</td><td><a href="routes.md#authtype-1">AuthType</a></td><td>Tipo de autentificación</td><td>Se asigna dependiendo del tipo de api.</td></tr></tbody></table>

> El símbolo `?` indica que el parámetro es opcional, tal como lo define en TypeScript.

### ParameterSwagger

Cada input se debe definir un objeto con los siguientes datos.

<table><thead><tr><th width="186">Campo</th><th width="207">Tipo</th><th>Descripción</th></tr></thead><tbody><tr><td>in</td><td><a href="routes.md#paramin">ParamIn</a></td><td>Indica la ubicación del parámetro (query, header, path, etc.)</td></tr><tr><td>description?</td><td>string</td><td>Descripción del parámetro</td></tr><tr><td>required?</td><td>boolean</td><td>Indica si el parámetro es obligatorio</td></tr><tr><td>type?</td><td><a href="routes.md#paramtype">ParamType</a></td><td>Tipo de dato del parámetro (string, number, boolean, etc.)</td></tr><tr><td>format?</td><td><a href="routes.md#paramformat">ParamFormat</a></td><td>Formato del parámetro (int32, float, double, etc.)</td></tr><tr><td>schema?</td><td>Record&#x3C;string, any></td><td>Esquema del parámetro</td></tr><tr><td>schemaRef?</td><td>string</td><td>Referencia a un esquema externo</td></tr><tr><td>collectionFormat?</td><td><a href="routes.md#paramcollectionformat">ParamCollectionFormat</a></td><td>Formato de colección del parámetro</td></tr><tr><td>items?</td><td>Record&#x3C;string, any></td><td>Definición de los elementos si el parámetro es un array</td></tr></tbody></table>

> 📃 Se debe usar solo uno de los tipos **schema** o **shemaRef**.&#x20;

#### **Input Query**

```javascript
parameters: {
  date: {
    in: 'query',
    description: 'Fecha',
    required: true,
    type: 'string'
  }
}
```

#### **Input Path**

```javascript
parameters: {
  name: {
    in: 'path',
    description: 'Nombre',
    required: true,
    type: 'string'
  }
}
```

#### **Input Body Schema**

```javascript
parameters: {
  body: {
    in: 'body',
    description: 'New Client',
    schema: {
      name: 'John Doe',
      $age: 29,
      about: ''
    }
  }
}
```

> El simbolo $ indica que es un dato obligatorio

#### Input Body SchemaRef

```javascript
parameters: {
  body: {
    in: 'body',
    required: true,
    schemaRef: 'ClientGetByIdRequest'
  }
}
```

En este caso el nombre `ClientGetByIdRequest` hace referencia a una variable definida en el archivo [swagger-autogen](routes.md#swagger-autogen) en el parámetro de `definitions`.

> 👍 Como estándar se recomienda tener estas definiciones en un archivo con el sufijo&#x20;
>
> `<name>.mock.ts`. Ya que puede servir para swagger como para las pruebas unitarias.

{% tabs %}
{% tab title="client.mock.js" %}
```javascript
const input = {
  id: '123456',
}

const client = {
  name: 'John Doe',
  email: 'jhonDoe@credicorp.com',
  phone: '987654321',
}

const ClientGetByIdSwagger = {
  ClientGetByIdBody: input,
  ClientGetByIdResponse: client,
}

module.exports = {
  ClientSwagger: {
    ...ClientGetByIdSwagger,
  },
}
```
{% endtab %}

{% tab title="client.mock.ts" %}
```typescript
const input = {
  id: '123456'
}
const client = {
  name: 'John Doe',
  email: 'jhonDoe@credicorp.com',
  phone: '987654321'
}

const ClientGetByIdSwagger = {
  ClientGetByIdBody: input,
  ClientGetByIdResponse: client
}

export const ClientSwagger = {
  ...ClientGetByIdSwagger
}
```
{% endtab %}
{% endtabs %}

#### Input File

```javascript
consumes: ['multipart/form-data'] 
parameters: {
  singleFile: {
    in: 'formData',
    type: 'file',
    required: 'true',
    description: 'Archivo simple',
  }
}
```

#### Input MultiFile

```javascript
consumes: ['multipart/form-data'] 
parameters: {
  multFiles: {
    in: 'formData',
    type: 'array',
    required: true,
    description: 'Varios Archivos',
    collectionFormat: 'multi',
    items: { type: 'file' }
  }
}
```

### ResponseSwagger

Cada respuesta se debe definir un objeto llamado `responses` con los siguientes datos.

<table><thead><tr><th width="186">Campo</th><th width="207">Tipo</th><th>Descripción</th></tr></thead><tbody><tr><td>description?</td><td>string</td><td>Descripción del parámetro</td></tr><tr><td>schema?</td><td>Record&#x3C;string, any></td><td>Esquema del parámetro</td></tr><tr><td>schemaRef?</td><td>string</td><td>Referencia a un esquema externo</td></tr></tbody></table>

Donde la key de cada ítem debe ser un número que representa al **status-code.**&#x20;

#### Output Body SchemaRef

Para este caso también se define la variable en un archivo [mock](routes.md#input-body-schemaref).

```javascript
responses: { 
    200: { schemaRef: 'ClientGetByIdResponse' } 
},
```

#### **Output Body Schema**

```javascript
responses: {
  200: {
    description: 'Cliente solicitado',
    schema: {
      name: 'John Doe',
      email: 'jhonDoe@credicorp.com',
      phone: '987654321',
    }
  }
}
```

### Tipos de Datos

#### AuthType

Tipo de Autenticación

```typescript
type AuthType = 'basicAuth' | 'bearerAuth'
```

#### IOType

Tipo de entrada y salida de datos

```typescript
type IOType = 'application/json' | 'multipart/form-data'
```

#### ParamIn

Donde se encuentra el parámetro de entrada

```typescript
type ParamIn = 'query' | 'body' | 'formData' | 'path' | 'header'
```

#### ParamType

Tipo de parámetro de entrada

```typescript
type ParamType =
  | 'string'
  | 'number'
  | 'boolean'
  | 'array'
  | 'integer'
  | 'object'
  | 'file'
```

#### ParamFormat

Formato del parámetro de entrada

```typescript
type ParamFormat =
  | 'int32'
  | 'int64'
  | 'float'
  | 'double'
  | 'byte'
  | 'binary'
  | 'date'
  | 'date-time'
  | 'password'
  | 'email'
  | 'uuid'
  | 'uri'
```

#### ParamCollectionFormat

Formato de colección, solo usar cuando el tipo `IOType` es `multipart/form-data`

```typescript
type ParamCollectionFormat = 'csv' | 'ssv' | 'tsv' | 'pipes' | 'multi'
```

#### AuthType

Tipo de autenticación este se asigna de por defecto dependiendo del tipo de api.

* Internal - Negocio: basic
* External - Experiencia: bearer

```typescript
type AuthType = 'basicAuth' | 'bearerAuth'
```

## Ejemplos

{% tabs %}
{% tab title="client.routes.js" %}
```javascript
const {
  Routes,
  Zod: { z },
  Joi,
} = require('cc-backend-ts')
const { ClientController } = require('./client.controller')

/** @extends {Routes<ClientController>} */
class ClientRoutes extends Routes {
  constructor() {
    super('client', new ClientController())
  }

  setRoutes() {
    this.post({
      resource: 'get_by_id/:id/:test',
      before: [accessMiddleware],
      validator: {
        request: {
          body: z
            .object({
              id: z.string(),
            })
            .strict(),
        },
        response: Joi.object({
          nroDocument: Joi.string(),
          name: Joi.string(),
          mail: Joi.string().required(),
        }),
      },
      controller: this.controller.getById,
      swagger: {
        description: 'Obtine un cliente por su id',
        responses: { 200: { schemaRef: 'ClientGetByIdResponse' } },
        parameters: {
          body: {
            in: 'body',
            required: true,
            schemaRef: 'ClientGetByIdBody',
          },
          id: {
            in: 'path',
            required: true,
            type: 'string',
          },
          test: {
            in: 'path',
            required: true,
            type: 'string',
          },
          idv2: {
            in: 'query',
            required: true,
            type: 'string',
          },
        },
      },
    })

    this.post({
      resource: 'register',
      controller: this.controller.register,
      swagger: {
        description: 'Registra un nuevo cliente',
        responses: { 200: { schemaRef: 'ClientRegisterResponse' } },
        parameters: {
          body: {
            in: 'body',
            required: true,
            schemaRef: 'ClientRegisterBody',
          },
        },
      },
    })
  }
}

const accessMiddleware = (req, res, next) => {
  if (req.headers.token === 1234) {
    return next()
  } else {
    throw new Error('Access denied')
  }
}

const clientRouter = new ClientRoutes().router

module.exports = {
  clientRouter,
}
```
{% endtab %}

{% tab title="client.routes.ts" %}
```typescript
import { Routes, Zod, Joi } from 'cc-backend-ts'
import { ClientController } from './client.controller'
const { z } = Zod

class ClientRoutes extends Routes<ClientController> {
  constructor() {
    super('client', new ClientController())
  }

  setRoutes(): void {
    this.post({
      resource: 'get_by_id/:id/:test',
      validator: {
        request: {
          body: z
            .object({
              id: z.string()
            })
            .strict()
        },
        response: Joi.object({
          name: Joi.string(),
          mail: Joi.string().required()
        })
      },
      before: [accessMiddleware],
      controller: this.controller.getById,
      swagger: {
        description: 'Obtine un cliente por su id',
        responses: { 200: { schemaRef: 'ClientGetByIdResponse' } },
        parameters: {
          body: {
            in: 'body',
            required: true,
            schemaRef: 'ClientGetByIdRequest'
          },
          id: {
            in: 'path',
            required: true,
            type: 'string'
          },
          test: {
            in: 'path',
            required: true,
            type: 'string'
          },
          idv2: {
            in: 'query',
            required: true,
            type: 'string'
          }
        }
      }
    })

    this.post({
      resource: 'register',
      controller: this.controller.register,
      swagger: {
        description: 'Registra un nuevo cliente',
        responses: { 200: { schemaRef: 'ClientRegisterResponse' } },
        parameters: {
          body: {
            in: 'body',
            required: true,
            schemaRef: 'ClientRegisterBody',
          },
        },
      },
    })
  }
}

const accessMiddleware = (req: any, res: any, next: any): void => {
  if (req.headers.token === 1234) {
    return next()
  } else {
    throw new Error('Access denied')
  }
}

export const clientRouter = new ClientRoutes().router

```
{% endtab %}
{% endtabs %}
