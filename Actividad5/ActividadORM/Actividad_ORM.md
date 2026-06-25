Actividad ORM
Parte 1: Diagnóstico Técnico y Brecha de Impedancia
1. La Brecha de Impedancia

La brecha de impedancia es la diferencia conceptual entre el modelo orientado a objetos utilizado en C# y el modelo relacional utilizado en bases de datos SQL. Mientras que en C# la información se representa mediante clases y objetos, en una base de datos relacional los datos se organizan en tablas, columnas y registros.

Entity Framework Core actúa como un Mapeador Objeto-Relacional (ORM), permitiendo la conversión automática entre ambos modelos.

Equivalencias de mapeo
Dominio de Objetos (C#)	Dominio Relacional (SQL)
Clase Clásica (POCO)	Tabla
Propiedad/Atributo	Columna
Instancia de Objeto	Fila o Registro
2. Mitigación de Vulnerabilidades

En ADO.NET tradicional, concatenar variables directamente dentro de una consulta SQL puede provocar ataques de Inyección SQL. Entity Framework Core previene este problema de manera automática mediante el uso de consultas parametrizadas generadas por el ORM.

Cuando se utilizan consultas LINQ, EF Core convierte los valores en parámetros SQL seguros sin necesidad de que el programador los gestione manualmente.

En ADO.NET, la forma equivalente de mitigar este riesgo era utilizar parámetros en los comandos SQL, por ejemplo mediante:

cmd.Parameters.AddWithValue("@filtro", valor);
3. Optimización de Infraestructura

El método .AsNoTracking() se utiliza en Entity Framework Core para indicar que los objetos obtenidos en una consulta son únicamente de lectura y no serán modificados posteriormente.

Al desactivar el rastreador de cambios, EF Core consume menos memoria RAM y menos recursos de procesamiento, ya que no necesita almacenar información sobre el estado de cada entidad recuperada.

Por esta razón, el uso de .AsNoTracking() representa una forma de solidaridad computacional con el hardware compartido de la universidad, permitiendo que otros usuarios y procesos dispongan de más recursos disponibles y mejorando el rendimiento general del servidor.

Parte 2: Desafío de Refactorización de Código
1. Definición del Contexto (DbContext)
using Microsoft.EntityFrameworkCore;

public class UnidadAcademicaContext : DbContext
{
    public UnidadAcademicaContext(DbContextOptions<UnidadAcademicaContext> options)
        : base(options)
    {
    }

    public DbSet<Catedratico> Catedraticos { get; set; }
}
Entidad Catedratico
public class Catedratico
{
    public int Id { get; set; }
    public string Nombre { get; set; }
}
2. Consulta Moderna con Entity Framework Core y LINQ
using Microsoft.EntityFrameworkCore;

public class CatedraticoService
{
    private readonly UnidadAcademicaContext _context;

    public CatedraticoService(UnidadAcademicaContext context)
    {
        _context = context;
    }

    public List<Catedratico> ObtenerCatedraticos()
    {
        return _context.Catedraticos
            .AsNoTracking()
            .Where(c => c.Nombre.StartsWith("Ing."))
            .ToList();
    }
}
Explicación
DbSet<Catedratico> permite mapear la tabla de catedráticos.
Where() filtra los registros cuyo nombre comienza con "Ing.".
StartsWith("Ing.") es el equivalente moderno a LIKE 'Ing.%'.
AsNoTracking() desactiva el rastreo de cambios para optimizar el uso de memoria.
ToList() devuelve una lista de objetos Catedratico.
Parte 3: Referencias Bibliográficas

Facultad de Ingeniería, USAC. (2026). Sesión 17: Conectividad con SQL Server. Acceso Estructurado a Datos mediante C# y ADO.NET. Laboratorio de Introducción a la Programación y Computación 2. Guatemala.

Facultad de Ingeniería, USAC. (2026). Sesión 18: Mapeo de Objetos Relacionales. Persistencia Automatizada con Entity Framework Core. Laboratorio de Introducción a la Programación y Computación 2. Guatemala.