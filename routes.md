# Routes

Las rutas de un modulo son definidas en una clase que debe extender de **Routes\<T>**. Para ello se debe pasar como parámetros de la clase extendida el nombre del modulo y una instancia del controller.

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

> ✅ Todas los rutas deben estar dentro de la función setRoutes() .

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

<table><thead><tr><th width="140">Parámetros</th><th width="111">Opcional/Requerido<select><option value="0Q1ARrrBHaBM" label="Opcional" color="blue"></option><option value="6gbmhxC1uFKA" label="Requerido" color="blue"></option></select></th><th>Descripción</th></tr></thead><tbody><tr><td>resource</td><td><span data-option="6gbmhxC1uFKA">Requerido</span></td><td>Nombre del recurso</td></tr><tr><td>validator</td><td><span data-option="0Q1ARrrBHaBM">Opcional</span></td><td>Esquema de validación en request o response, ya sea en Zod o Yoi</td></tr><tr><td>swagger</td><td><span data-option="6gbmhxC1uFKA">Requerido</span></td><td>Definición de json swagger.</td></tr><tr><td>before</td><td><span data-option="0Q1ARrrBHaBM">Opcional</span></td><td>Arreglo de middlewares que se ejecutaran antes del controller.</td></tr><tr><td>controller</td><td><span data-option="6gbmhxC1uFKA">Requerido</span></td><td>Método del controller.</td></tr><tr><td>after</td><td><span data-option="0Q1ARrrBHaBM">Opcional</span></td><td>Arreglo de middlewares que se ejecutaran después del controller.</td></tr></tbody></table>

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

