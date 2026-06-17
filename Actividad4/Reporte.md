# Fundamentación Teórica y Análisis Crítico

## 1. El Tránsito hacia los Sistemas Distribuidos y Multi-Capa

### La Limitación del Monolito Local

Cuando la interfaz de usuario, la lógica del negocio y la base de datos se encuentran en una sola computadora, el sistema presenta problemas de escalabilidad y sincronización. Si muchos usuarios desean acceder simultáneamente, los recursos de la máquina pueden agotarse rápidamente, disminuyendo el rendimiento. Además, el crecimiento del sistema se vuelve difícil, ya que todo depende de un único equipo físico, creando un punto único de falla.

### Distinción Crítica: Layers vs. Tiers

**Layers (Capas):**
Son divisiones lógicas del software que organizan las responsabilidades del código. Por ejemplo: presentación, negocio y acceso a datos.

**Tiers (Niveles):**
Son divisiones físicas del sistema, es decir, dónde se ejecutan los componentes. Cada nivel puede estar en una máquina o servidor diferente.

En resumen:

- Layers = Organización lógica del código.
- Tiers = Distribución física del sistema.

### Responsabilidades en la Arquitectura de 3 Niveles

#### Nivel 1: Presentation Tier

Su función es interactuar con el usuario, mostrar información y capturar datos de entrada.

Tecnologías comunes:
- HTML
- CSS
- JavaScript
- Razor Views
- ASP.NET Core MVC

#### Nivel 2: Application Tier

Contiene la lógica del negocio y las reglas del sistema. Procesa la información recibida desde la presentación y coordina el acceso a los datos.

Tecnologías comunes:
- C#
- ASP.NET Core
- Servicios y APIs

#### Nivel 3: Data Tier

Se encarga del almacenamiento y administración de los datos del sistema.

Tecnologías comunes:
- SQL Server
- MySQL
- PostgreSQL
- Entity Framework Core

### Seguridad Perimetral

Exponer directamente una base de datos a Internet representa un riesgo crítico de seguridad, ya que un atacante podría intentar acceder a la información, modificarla o eliminarla.

La buena práctica consiste en:

- Mantener la base de datos en una red privada.
- Permitir únicamente conexiones desde el servidor de aplicaciones.
- Utilizar firewalls y autenticación segura.
- Nunca exponer el puerto de la base de datos al público.

## 2. Desacoplamiento Lógico con el Patrón MVC

### La Crisis del Código Espagueti

Cuando las consultas SQL, la lógica del negocio y la interfaz gráfica se mezclan en un mismo archivo, el mantenimiento del sistema se vuelve complejo.

Las consecuencias son:

- Código difícil de entender.
- Mayor cantidad de errores.
- Dificultad para reutilizar componentes.
- Problemas para realizar pruebas unitarias.
- Bajo mantenimiento y escalabilidad.

### Separación de Preocupaciones (SoC)

#### Modelo (Model)

Representa los datos y las reglas del negocio del sistema.

El modelo no debe conocer cómo se muestran los datos al usuario ni depender de la interfaz gráfica.

#### Vista (View)

Es la encargada de presentar la información al usuario.

Se considera una entidad pasiva porque solo muestra datos y recibe acciones del usuario.

La vista tiene prohibido contener:

- Consultas SQL.
- Reglas del negocio.
- Procesamientos complejos.

#### Controlador (Controller)

Actúa como intermediario entre la Vista y el Modelo.

Sus funciones principales son:

- Recibir solicitudes HTTP.
- Validar datos de entrada.
- Invocar operaciones del Modelo.
- Enviar los resultados a la Vista correspondiente.

### Métricas de Ingeniería de Software

El patrón MVC favorece:

**Alta Cohesión:**
Cada componente tiene una única responsabilidad claramente definida.

**Bajo Acoplamiento:**
Los componentes son independientes entre sí, facilitando:

- El mantenimiento.
- Las pruebas unitarias.
- La reutilización del código.
- La escalabilidad del sistema.

 

# Parte 2. Modelado del Ciclo de Vida y Enrutamiento Semántico

## 1. Mapeo Analítico de URLs

La plantilla de enrutamiento utilizada por ASP.NET Core MVC es:

{controller=Home}/{action=Index}/{id?}

| URL Entrante del Cliente | Clase Controladora Buscada | Método (Acción) Ejecutado | Parámetro id |
|--------------------------|----------------------------|---------------------------|-------------|
| https://ingenieria.usac.edu.gt/ControlAcademico/Login | ControlAcademicoController | Login | Ninguno |
| https://ingenieria.usac.edu.gt/Estudiante/Historial/20260123 | EstudianteController | Historial | 20260123 |
| https://ingenieria.usac.edu.gt/Asignacion/Detalle/10 | AsignacionController | Detalle | 10 |
| https://ingenieria.usac.edu.gt/Home | HomeController | Index | Ninguno (Opcional) |

## 2. Diagramación del Flujo Interactivo

### Paso 1

El usuario realiza una acción en su navegador web, por ejemplo, hace clic en un botón o accede a una dirección URL. El navegador envía una solicitud HTTP al servidor donde está alojada la aplicación.

### Paso 2

El sistema de enrutamiento de ASP.NET Core analiza la URL recibida y determina qué controlador y qué acción deben ejecutarse.

Por ejemplo:

/Estudiante/Historial/20260123

se dirige a:

- Controlador: EstudianteController
- Acción: Historial
- id: 20260123

### Paso 3

El Controlador recibe la solicitud y procesa los parámetros enviados por el usuario. Si necesita datos, solicita la información al Modelo.

El controlador actúa como intermediario entre la Vista y el Modelo.

### Paso 4

El Modelo realiza las operaciones necesarias, como obtener datos desde una base de datos o aplicar reglas de negocio, y devuelve los resultados al Controlador.

### Paso 5

Finalmente, el Controlador envía los datos a la Vista. La Vista genera dinámicamente el código HTML y lo devuelve al navegador del usuario, donde se muestra la página web final.




# Parte 3. Implementación Práctica - Sistema de Control Académico

## 1. Creación del espacio de trabajo




# Parte 4. Auditoría y Control de Calidad

## 1. Prueba de Cohesión (GET)

Se ejecutó la acción:

GET /Estudiante/Listar

El resultado fue satisfactorio, ya que el controlador únicamente se encarga de obtener la información almacenada en la lista de estudiantes y enviarla a la vista correspondiente.

No se encontraron:

- Consultas SQL escritas directamente en el controlador.
- Cálculos complejos dentro de los métodos.
- Código HTML mezclado con la lógica del negocio.
- Acceso directo a una base de datos desde la vista.

Por lo tanto, el controlador cumple con el principio de responsabilidad única y mantiene una alta cohesión.

## 2. Evaluación de Antipatrones

Se revisó el archivo:

Controllers/EstudianteController.cs

Los métodos implementados son:

- Listar()
- Registrar()

Ambos métodos tienen una cantidad reducida de líneas de código y una responsabilidad específica:

- Listar(): Obtiene la lista de estudiantes y la envía a la vista.
- Registrar(): Realiza una validación básica y agrega un estudiante a la colección en memoria.

Debido a esto, el proyecto evita el antipatrón conocido como "Fat Controller" (Controlador Gordo), ya que la lógica de negocio permanece separada y el controlador actúa únicamente como intermediario entre el Modelo y la Vista.

## Conclusión

La aplicación desarrollada cumple con los principios del patrón MVC y las buenas prácticas de ingeniería de software, promoviendo:

- Alta cohesión.
- Bajo acoplamiento.
- Separación de responsabilidades.
- Facilidad de mantenimiento.
- Mayor escalabilidad del sistema.



# Parte 5. Referencias Bibliográficas

1. Facultad de Ingeniería, USAC. (2026). *Sesión 11: Modelado Base y Arquitecturas de Despliegue. Evolución de Sistemas Distribuidos, Fundamentos del Modelo Cliente-Servidor y Diseño Físico Multi-Capas (N-Tier).* Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala.

2. Facultad de Ingeniería, USAC. (2026). *Sesión 12: Arquitectura y Componentes del Patrón MVC. Desacoplamiento Lógico de Software, Ciclo de Vida de las Peticiones y Enrutamiento en Aplicaciones Interactivas Modernas.* Laboratorio del curso Introducción a la Programación y Computación 2. Guatemala.

3. Microsoft. *ASP.NET Core MVC Overview*. Disponible en: https://learn.microsoft.com/en-us/aspnet/core/mvc/overview

4. Microsoft. *Routing in ASP.NET Core*. Disponible en: https://learn.microsoft.com/en-us/aspnet/core/fundamentals/routing