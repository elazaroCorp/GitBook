---
description: >-
  Los secretos almacenan valores confidenciales como contraseñas, usuarios,
  token, identificadores, etc.
---

# 🔐 Secret

## Key Vault

Los secretos son gestionados en el servicio KeyVault de Azure.

{% embed url="https://azure.microsoft.com/es-es/products/key-vault" %}

Para tener accesos a los Key-Vault solicitar los permisos al equipo de infraestructura, estos permisos solo serán asignados en los ambientes DEV y QA.&#x20;

## Estructura Archivos

En los ejemplos, se seguirá la siguiente estructura de archivos presentada.

{% tabs %}
{% tab title="Javascript" %}
```
Proyect
├── package.json
├── src
│    ├── application.js
│    ├── secrets 
│    │    ├── secrets.interface.js
│    │    ├── secrets.js
│    │    └── index.js
└── jsconfig.json
```
{% endtab %}

{% tab title="TypeScript" %}
```
Proyect
├── package.json
├── src
│    ├── application.ts
│    ├── secrets 
│    │    ├── secrets.interface.ts
│    │    ├── secrets.ts
│    │    └── index.ts
└── tsconfig.json
```
{% endtab %}
{% endtabs %}

## SecretBase\<T>

Clase base para manejar los secretos de la aplicación. En la clase se usa un tipo genérico T para que el desarrollador defina sus tipos.

### Tipado en los Secretos

Antes de instancia la clase `SecretBase` es necesario definir los tipos. Esto para poder obtener la ayuda por parte de editor y mostrar los errores en tiempo de desarrollo.&#x20;

* JavaScript: usamos JsDoc `typedef` para definir los tipos.
* TypeScript: usamos una interfaz `interface`.&#x20;

{% tabs %}
{% tab title="Secrets.types.js" %}
```javascript
/**
 * @typedef {Object} Secrets
 * @property {!string} clientID - Identificador de cliente
 * @property {!string} isB2C - Es B2C
 * @property {!string} policyName - Nombre de la política
 * @property {!string} scope - Alcance
 * @property {!string} tenantName - Nombre del inquilino
 * @property {!string} customDomainName - Nombre de domin
 * @property {!string} basicAuth - Basic Auth
 */

module.exports = {}
```
{% endtab %}

{% tab title="Secrets.interface.ts" %}
```typescript
export interface TSecrets {
  /** Identificador de cliente */
  clientID: string
  /** Es B2C */
  isB2C: string
  /** Nombre de la política */
  policyName: string
  /** Alcance */
  scope: string
  /** Nombre del inquilino */
  tenantName: string
  /** Nombre de domin */
  customDomainName: string
  /** Basic Auth */
  basicAuth: string
}
```
{% endtab %}
{% endtabs %}

### Instanciar SecretBase

Para instanciar creamos un nuevo archivo llamado secrets.

{% tabs %}
{% tab title="Secrets.js" %}
```javascript
const { SecretBase } = require('cc-backend-ts')
const TSecrets = require('./secret.types')
/**
 * @type {SecretBase<TSecrets.Secrets>}
 */
const secrets = new SecretBase({
  clientID: 'ADclientID',
  isB2C: 'ADisB2C',
  policyName: 'ADpolicyName',
  scope: 'ADscope',
  tenantName: 'ADtenantName',
  customDomainName: 'ADcustomDomainName',
  basicAuth: 'BASICAUTH',
})

module.exports = { secrets }
```
{% endtab %}

{% tab title="Secrets.ts" %}
```typescript
import { SecretBase } from 'cc-backend-ts'
import { TSecrets } from './secret.interface'

const secrets = new SecretBase<TSecrets>({
  clientID: 'ADclientID',
  isB2C: 'ADisB2C',
  policyName: 'ADpolicyName',
  scope: 'ADscope',
  tenantName: 'ADtenantName',
  customDomainName: 'ADcustomDomainName',
  basicAuth: 'BASICAUTH'
})

export default secrets
```
{% endtab %}
{% endtabs %}

La clase SecretBase recibe como parámetro un objeto <mark style="color:orange;">`nameToKey`</mark> este representa

* **Name**: los nombres con los cuales se van a llamar desde el código.
* **Key**: es el nombre de los KeyVault o Env.

> Esto se realiza para tener una mejor lectura de código, por ejemplo, es más sencillo leer  `apiKeyRest` que `APIKEYREST`.  Sin embargo, si el desarrollador puede el mismo nombre.&#x20;

### Extender SecretBase

Es posible ampliar la clase SecretBase para incluir métodos que faciliten la agrupación de valores y la gestión eficiente de los secretos de manera colectiva.

{% tabs %}
{% tab title="Secrets.js" %}
```javascript
const { SecretBase } = require('cc-backend-ts')
const TSecrets = require('./secret.types')
/**
 * @type {SecretBase<TSecrets.Secrets>}
 */
class SecretCustom extends SecretBase {
  constructor() {
    super({
      clientID: 'ADclientID',
      isB2C: 'ADisB2C',
      policyName: 'ADpolicyName',
      scope: 'ADscope',
      tenantName: 'ADtenantName',
      customDomainName: 'ADcustomDomainName',
      basicAuth: 'BASICAUTH',
    })
  }

  get bearerStrategyOptions() {
    return {
      clientID: this.value.clientID,
      isB2C: this.value.isB2C === 'true',
      policyName: this.value.policyName,
      scope: this.value.scope,
      tenantName: this.value.tenantName,
      customDomainName: this.value.customDomainName,
    }
  }
}

const secret = new SecretCustom()
module.exports = { secrets }
```
{% endtab %}

{% tab title="Secrets.ts" %}
```typescript
import { SecretBase, BearerStrategyOptions } from 'cc-backend-ts'
import { TSecrets } from './Secret.interface'

class SecretCustom extends SecretBase<TSecrets> {
  constructor() {
    super({
      clientID: 'ADclientID',
      isB2C: 'ADisB2C',
      policyName: 'ADpolicyName',
      scope: 'ADscope',
      tenantName: 'ADtenantName',
      customDomainName: 'ADcustomDomainName',
      basicAuth: 'BASICAUTH'
    })
  }

  get bearerStrategyOptions(): BearerStrategyOptions {
    return {
      clientID: this.value.clientID,
      isB2C: this.value.isB2C === 'true',
      policyName: this.value.policyName,
      scope: this.value.scope,
      tenantName: this.value.tenantName,
      customDomainName: this.value.customDomainName
    }
  }
}

export const secrets = new SecretCustom()
```
{% endtab %}
{% endtabs %}

### Exportar Instancia

Para exportar usamos el archivo index, para hacer un llamado más sencillo.

{% tabs %}
{% tab title="index.js" %}
```javascript
module.exports = {
  ...require('./secret'),
}
```
{% endtab %}

{% tab title="index.ts" %}
```typescript
export * from './secret'
```
{% endtab %}
{% endtabs %}

### Cargar los secretos

Los secretos luego de instanciarlos no tienen los valores para ello, es necesario ejecutar el método asíncrono `uploadAll().`&#x20;

> El método se desarrolló porque el constructor no admite la invocación de funciones asíncronas. Dado que los secretos se almacenan en KeyVault, se requiere una función asíncrona.

Los secretos hay que iniciarlos al momento de iniciar la aplicación. Con esto aseguramos que todos los valores de los secretos almacenados y listos para usarse en cualquier parte del código.

{% tabs %}
{% tab title="Application.js" %}
```javascript
const { secrets } = requiere('./secrets')

async function startApplication() {
  await secrets.uploadAll() // <--- 👈 Here
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}

module.exports = startApplication
```
{% endtab %}

{% tab title="Application.ts" %}
```typescript
import { secrets } from './secrets'
 
export async function startApplication() {
  await secrets.uploadAll() // <--- 👈 Here
  app.listen(server.port, () => {
    console.log(server.messageListening)
  })
}
```
{% endtab %}
{% endtabs %}

Para agilizar los tiempos de desarrollo, el método hace una primera búsqueda en las variables de entorno en caso de no encontrarlo va a buscar a los KeyVault de Azure.

{% tabs %}
{% tab title=".env" %}
```javascript
PORT=3002
ENV='DEV'
ID_APLICACION=1
KEY_VAULT_NAME=KVLTDSPTC001

ADscope='B2C_TEST'
ADtenantName='test.onmicrosoft.com'
ADisB2C='TRUE'
ADpolicyName='B2C_TEST_SING'
```
{% endtab %}
{% endtabs %}

En este caso, los valores de los secretos de Active Directory serán obtenidos del archivo .env.

**Beneficios**

* Reducimos las peticiones a KV y por ende reduciendo los costos.
* No es necesario tener acceso en todo momento a KV.
* Es posible modificar los valores para las pruebas de manera sencilla sin la necesidad de acceder a los KV.

### Obtener Valores

Existe dos formas para obtener el valor de un secreto. En cualquiera de los dos existe el tipado.

* get(): usar el metodo get asignando el nombre del secreto, por ejemplo, si se ha definido en los tipos `scope: 'ADscope`  scope es el nombre y ADscope es la Key.
* value: usar el atributo de la instacia secret.

{% tabs %}
{% tab title="anyFile.js" %}
```javascript
const { secrets } = requiere('./secrets')

function anyFunction() {
    const scope = secret.get('scope')
    const scope2 = secret.value.scope
    // Error test not exist in value
    // scret.value.test
}
```
{% endtab %}

{% tab title="anyFile.ts" %}
```typescript
import { secrets } from './secrets'
 
function anyFunction() {
    const scope = secret.get('scope')
    const scope2 = secret.value.scope
    // Error test not exist in value
    // scret.value.test
}
```
{% endtab %}
{% endtabs %}
