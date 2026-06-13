
## Investigación Teórica y Análisis de Casos

### 1. El Límite de las Rotaciones Simples y Desbalanceo en "Zig-Zag"

#### El Problema Cruzado

Las rotaciones simples en árboles AVL funcionan correctamente cuando el desbalance ocurre en una sola dirección. Sin embargo, cuando se presenta una configuración cruzada o tipo "Zig-Zag", una rotación simple no es suficiente para restaurar el equilibrio del árbol.

Por ejemplo, al insertar los valores:

```text
30, 10, 20
```

se obtiene la siguiente estructura:

```text
    30
   /
 10
   \
    20
```

El nodo 30 presenta un desbalance porque su subárbol izquierdo tiene mayor altura que el derecho. Sin embargo, el hijo izquierdo (10) está inclinado hacia la derecha, lo que genera un caso cruzado.

Si se aplicara únicamente una rotación simple, el problema no se resolvería completamente, ya que la estructura seguiría presentando desequilibrio. Por esta razón se utiliza una Rotación Doble Izquierda-Derecha (RID).

La condición matemática para ejecutar una RID es:

```text
FE(Padre) = -2
FE(Hijo Izquierdo) = +1
```

Donde:

* FE = Factor de Equilibrio.
* El padre está desbalanceado hacia la izquierda.
* El hijo izquierdo está desbalanceado hacia la derecha.

La corrección se realiza en dos pasos:

1. Rotación simple izquierda sobre el hijo.
2. Rotación simple derecha sobre el padre.

El resultado final es:

```text
     20
    /  \
   10  30
```

Con esta estructura todos los nodos quedan balanceados.

---

#### Principio DRY (Don't Repeat Yourself)

El principio DRY establece que el código no debe repetirse innecesariamente.

En la implementación de árboles AVL, las rotaciones dobles (RID y RDI) pueden construirse reutilizando las rotaciones simples ya existentes.

Por ejemplo:

```csharp
RotacionIzquierda(hijo);
RotacionDerecha(padre);
```

Esta estrategia presenta varias ventajas:

* Reduce la cantidad de código.
* Facilita el mantenimiento.
* Disminuye la probabilidad de errores.
* Favorece la reutilización de funciones probadas.
* Mejora la legibilidad del programa.

Por ello, desde el punto de vista de la ingeniería de software, es preferible construir las rotaciones compuestas a partir de operaciones simples ya implementadas.

---

### 2. Fundamentos de Arquitectura Web y Protocolo HTTP

#### El Modelo Cliente-Servidor

La arquitectura Cliente-Servidor es un modelo de comunicación en el que un cliente solicita servicios o recursos a un servidor.

Los componentes principales son:

##### Cliente

Es la aplicación que realiza la solicitud.

Ejemplos:

* Navegador web.
* Aplicación móvil.
* Aplicación de escritorio.

##### Servidor

Es el sistema encargado de procesar solicitudes y responder al cliente.

Ejemplos:

* Servidor ASP.NET Core.
* Servidor Apache.
* Servidor Nginx.

El flujo de comunicación es el siguiente:

```text
Cliente
   |
   | HTTP Request
   v
Servidor
   |
   | HTTP Response
   v
Cliente
```

##### Request (Petición)

La petición enviada por el cliente contiene:

* Método HTTP.
* URL del recurso.
* Encabezados (Headers).
* Datos enviados al servidor (Body, cuando aplica).

##### Response (Respuesta)

La respuesta enviada por el servidor contiene:

* Código de estado HTTP.
* Encabezados.
* Datos solicitados.

Normalmente los datos se intercambian utilizando formatos como:

* JSON.
* XML.
* Texto plano.

---

#### Semántica de Operaciones HTTP

##### Método GET

El método GET se utiliza para recuperar información almacenada en el servidor.

Características:

* No modifica datos.
* Es de solo lectura.
* Se utiliza para consultas.

Ejemplo:

```http
GET /api/arbol
```

En la práctica de la API AVL, este método se utiliza para recuperar la estructura actual del árbol.

---

##### Método POST

El método POST se utiliza para enviar datos al servidor y producir cambios en la aplicación.

Características:

* Inserta información.
* Puede modificar recursos.
* Envía datos dentro del cuerpo de la petición.

Ejemplo:

```http
POST /api/arbol/insertar
```

Con el siguiente JSON:

```json
{
  "id": 20,
  "etiqueta": "Nieto Derecho"
}
```

En la práctica de la API AVL, este método permite insertar un nuevo nodo y ejecutar la simulación de la rotación RID.

---

##### Diferencia entre GET y POST

| GET                                     | POST                                 |
| --------------------------------------- | ------------------------------------ |
| Recupera información                    | Inserta o modifica información       |
| No altera el estado del servidor        | Puede alterar el estado del servidor |
| Generalmente envía parámetros en la URL | Envía datos en el Body               |
| Utilizado para consultas                | Utilizado para inserciones           |

Aplicado a la práctica:

* GET /api/arbol → Recupera la estructura actual del árbol AVL.
* POST /api/arbol/insertar → Inserta un nodo y ejecuta el balanceo compuesto cuando corresponde.

---

## Referencias

1. Material de la Sesión 10: Rotaciones Dobles en Árboles AVL.
2. Documentación oficial de ASP.NET Core.
3. Especificación HTTP/1.1 RFC 9110.
4. Conceptos fundamentales de Arquitectura Cliente-Servidor.



# Parte 2: Implementación Práctica: API de Simulación AVL

## Inicialización del Entorno

Se creó un nuevo proyecto utilizando:

```bash
dotnet new webapi -o ApiAvlSimulacion
cd ApiAvlSimulacion
```

## Modelo del Nodo

Archivo: NodoAVL.cs

```csharp
public class NodoAVL
{
    public int Id { get; set; }
    public string Etiqueta { get; set; } = string.Empty;
    public int Altura { get; set; } = 1;
}
```

## Program.cs

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// resto del código...
```

## Pruebas

### GET /api/arbol

Respuesta obtenida:

```json
[
  {
    "id": 30,
    "etiqueta": "Nodo Raíz (Abuelo) - FE: -2"
  }
]
```