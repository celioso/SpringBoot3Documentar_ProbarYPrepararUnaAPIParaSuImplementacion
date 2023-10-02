# Spring Boot 3: documentar, probar y preparar una API para su implementación

### Proyecto del curso anterior

¿Comenzando en esta etapa? [Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el curso anterior](https://github.com/alura-es-cursos/1979-spring-boot-buenas-practicas-security/tree/clase-5 "Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el curso anterior").

## Preparando el ambiente

Para preparar el ambiente, necesitamos tener algunas cosas instaladas en la computadora, como:

### IntelliJ
Para instalar IntelliJ, clique en el link y descargue la versión actual.

[IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows "IntelliJ IDEA")

![Intellij IDEA](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/5.png "Intellij IDEA")

Alternativamente:

1. Podemos trabajar con editores de código como Eclipse, visual studio code, pero estos tienen configuraciones diferentes a las que serán utilizadas en el video, sin embargo, es posible trabajar con ellos dejando como desafío usar otro editor de código.

![eclipse](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/8.png "eclipse")

[The Eclipse Foundation](https://www.eclipse.org/downloads/download.php?file=/oomph/epp/2022-12/R/eclipse-inst-jre-win64.exe&mirror_id=576 "The Eclipse Foundation")

![Code editing](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/12.png "Code editing")

[Visual Studio Code](https://code.visualstudio.com/Download "Visual Studio Code")

Para comenzar esta tercera parte del curso necesitamos descargar la versión del curso anterior que podemos encontrar en el siguiente link [click aqui](https://github.com/alura-es-cursos/1979-spring-boot-buenas-practicas-security/tree/clase-5 "click aqui").

![git](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/4.png "git")

![git HTTPS](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/10.png "git HTTPS")

En la imagen superior podemos ver como se encuentra el repositorio vamos a descargar el archivo .zip

![IdeaProjects](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/9.png "IdeaProjects")

Luego vamos a extraer este archivo en la carpeta donde se encuentra el resto de las informaciones de spring para este curso.

![Open File or Project](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/6.png "Open File or Project")

Luego de haber descargado la información del curso anterior vamos a abrir la IDE de eclipse, seleccionar en la pestaña de nuevo abrir un proyecto existente, seleccionamos el archivo del directorio y finalizar.

Alternativamente podemos clonar el repositorio desde la consola de IntelliJ

![](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/7.png)

O utilizando git bash console.

![cmd](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/11.png "cmd")

En estos links podemos encontrar el soporte visual de la aplicación mobile front end y las diferentes actividades que fueron realizada

[Aplicación mobile Front End](https://www.figma.com/file/vgn35i1ErivIN8LJYEqxGZ/Untitled?node-id=0-223&t=YNrx4H2YyxEWXQFb-0 "Aplicación mobile Front End")

[Solicitud del cliente para la API](https://trello.com/b/yGQuuyVV/api-voll-med "Solicitud del cliente para la API")

### Para saber más: anotacion @JsonAlias

Aprendimos que los nombres de los campos enviados en JSON a la API deben ser idénticos a los nombres de los atributos de las clases DTO, ya que de esta manera Spring puede completar correctamente la información recibida.

Sin embargo, puede ocurrir que un campo se envíe en JSON con un nombre diferente al atributo definido en la clase DTO. Por ejemplo, imagine que se envíe el siguiente JSON a la API:

```java
{
"producto_id": 12,
"fecha_compra": "01/01/2022"
}
```

Y la clase DTO creada para recibir esta información se define de la siguiente manera:

```java
public record DatosCompra(
Long idProducto,
LocalDate fechaCompra
){}
```

Si esto ocurre, tendremos problemas porque Spring instanciará un objeto del tipo DatosCompra, pero sus atributos no se completarán y quedarán como null debido a que sus nombres son diferentes a los nombres de los campos recibidos en JSON.

Tenemos dos posibles soluciones para esta situación:

1. Renombrar los atributos en el DTO para que tengan el mismo nombre que los campos en JSON;
2. Solicitar que la aplicación cliente que envía las solicitudes a la API cambie los nombres de los campos en el JSON enviado.
La primera opción anteriormente mencionada no es recomendable, ya que los nombres de los campos en JSON no están de acuerdo con el estándar de nomenclatura de atributos utilizado en el lenguaje Java.

La segunda opción sería la más indicada, pero no siempre será posible "obligar" a los clientes de la API a cambiar el estándar de nomenclatura utilizado en los nombres de los campos en JSON.

Para esta situación, existe una tercera opción en la que ninguno de los lados (cliente y API) necesita cambiar los nombres de los campos/atributos. Para ello, solo es necesario utilizar la anotación @JsonAlias:

```java
public record DatosCompra(
@JsonAlias("producto_id") Long idProducto,
@JsonAlias("fecha_compra") LocalDate fechaCompra
){}
La anotación @JsonAlias sirve para mapear "alias" alternativos para los campos que se recibirán del JSON, y es posible asignar múltiples alias:
public record DatosCompra(
@JsonAlias({"producto_id", "id_producto"}) Long idProducto,
@JsonAlias({"fecha_compra", "fecha"}) LocalDate fechaCompra
){}
```

De esta manera, se resuelve el problema, ya que Spring, al recibir el JSON en la solicitud, buscará los campos considerando todos los alias declarados en la anotación @JsonAlias.

### Para saber más: formatacion de fechas

Como se demostró en el video anterior, Spring tiene un patrón de formato para campos de tipo fecha cuando se asignan a atributos de tipo LocalDateTime. Sin embargo, es posible personalizar este patrón para utilizar otros formatos que prefiramos.

Por ejemplo, imagine que necesitamos recibir la fecha/hora de la consulta en el siguiente formato: dd/mm/yyyy hh:mm. Para que esto sea posible, debemos indicar a Spring que este será el formato en el que se recibirá la fecha/hora en la API, lo que puede hacerse directamente en el DTO utilizando la anotación @JsonFormat:

```java
@NotNull
@Future
@JsonFormat(pattern = "dd/MM/yyyy HH:mm")
LocalDateTime data
```

En el atributo pattern indicamos el patrón de formato esperado, siguiendo las reglas definidas por el estándar de fechas de Java. Puede encontrar más detalles en esta página del JavaDoc.

Esta anotación también se puede utilizar en las clases DTO que representan la información que devuelve la API, para que el JSON devuelto se formatee de acuerdo con el patrón configurado. Además, no se limita solo a la clase LocalDateTime, sino que también se puede utilizar en atributos de tipo LocalDate y LocalTime.

### Para saber más: Service Pattern
El patrón Service es muy utilizado en la programación y su nombre es muy conocido. Pero a pesar de ser un nombre único, Service puede ser interpretado de varias maneras: puede ser un caso de uso (Application Service); un Domain Service, que tiene reglas de su dominio; un Infrastructure Service, que utiliza algún paquete externo para realizar tareas; etc.

A pesar de que la interpretación puede ocurrir de varias formas, la idea detrás del patrón es separar las reglas de negocio, las reglas de la aplicación y las reglas de presentación para que puedan ser fácilmente probadas y reutilizadas en otras partes del sistema.

Existen dos formas más utilizadas para crear Services. Puede crear Services más genéricos, responsables de todas las asignaciones de un Controller; o ser aún más específico, aplicando así la S del SOLID: Single Responsibility Principle (Principio de Responsabilidad Única). Este principio nos dice que una clase/función/archivo debe tener sólo una única responsabilidad.

Piense en un sistema de ventas, en el que probablemente tendríamos algunas funciones como: Registrar usuario, Iniciar sesión, Buscar productos, Buscar producto por nombre, etc. Entonces, podríamos crear los siguientes Services: RegistroDeUsuarioService, IniciarSesionService, BusquedaDeProductosService, etc.

Pero es importante estar atentos, ya que muchas veces no es necesario crear un Service y, por lo tanto, agregar otra capa y complejidad innecesarias a nuestra aplicación. Una regla que podemos utilizar es la siguiente: si no hay reglas de negocio, simplemente podemos realizar la comunicación directa entre los controllers y los repositories de la aplicación.

### Haga lo que hicimos en aula

Ha llegado el momento de que sigas todos los pasos que he dado durante esta lección. En caso de que ya lo hayas hecho, excelente. Si aún no lo ha hecho, es importante que realice lo que se vio en los videos para que pueda continuar con la siguiente lección.

____

¡Vamos a empezar el proyecto! En la columna "DONE" en Trello, podemos ver que ya hemos implementado las funcionalidades de CRUD para médicos y pacientes. En la columna "TO DO" a la izquierda, faltan las funcionalidades de programación y cancelación de citas.

Comenzaremos implementando la programación. Por lo tanto, moveremos la tarjeta "Agendamiento de consultas" a la columna "DOING". Con un clic en la tarjeta, abriremos su información y, como de costumbre, leeremos la descripción de la funcionalidad para entender lo que necesitamos hacer.

La descripción dice lo siguiente:

"El sistema debe tener una funcionalidad que permita la programación de citas, en la que se deben completar la siguiente información: Paciente, Médico, Fecha/Hora de la consulta".

"Se deben validar las siguientes reglas de negocio por parte del sistema: (sigue una lista con reglas de negocio)".

Esta es una funcionalidad similar a las que ya hemos implementado. Nuestra API recibirá una solicitud con el ID del paciente o del médico y la fecha en que se realizará la consulta.

Con esta información, realizaremos las validaciones y las guardaremos en la base de datos. La diferencia es que ahora tenemos reglas de negocio más complejas que las que hemos visto anteriormente para médicos y pacientes.

Abrimos nuestro diseño de Figma con un modelo de aplicación móvil para nuestro proyecto.

![](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/image1.jpg)

En la aplicacion móvil, la persona tendrá una pantalla para agendar una nueva consulta, que contiene el nombre del paciente y del médico o médica. Estas informaciones serán extraídas de nuestra base de datos. Además, habrá dos campos para la elección del día y de la hora de la consulta, así como un botón para confirmar la programación.

Por lo tanto, la aplicación móvil necesita hacer validaciones de los campos obligatorios, siendo opcional solamente el campo "Nombre del médico". Cuando la persona complete todos los campos obligatorios y haga clic en "Agendar consulta", se disparará una solicitud a nuestra API. Necesitaremos recibir estos datos y tratarlos como ya hemos hecho en las otras funcionalidades.

Volviendo a la tarjeta en Trello, la única diferencia son algunas de las validaciones, que requerirán la escritura de un algoritmo, consulta en la base de datos, etc.

Así, estas no son sólo validaciones de formulario como aquellas con las que trabajamos anteriormente con BEAN validation (campo obligatorio, campo número o texto, tamaño mínimo y máximo, etc.). Ahora, tendremos que aprender a trabajar con validaciones un poco más complejas.

Códigos para nuevas funcionalidades

Para implementar esta o cualesquiera otras funcionalidades, seguimos una especie de paso a paso. Necesitamos crear siempre los siguientes tipos de códigos:

- Controller, para mapear la solicitud de la nueva funcionalidad;
- DTOs, que representan los datos que llegan y salen de la API;
- Entidad JPA;
- Repository, para aislar el acceso a la base de datos;
- Migration, para hacer las alteraciones en la base de datos.

Estos son los cinco tipos de código que siempre desarrollaremos para una nueva funcionalidad. Esto también se aplica al agendamiento de las consultas, incluyendo un sexto elemento a la lista, las reglas de negocio. En esta clase, entenderemos cómo implementar las reglas de negocio con algoritmos más complejos.

Implementando la funcionalidad

Ahora que ya entendemos lo que necesita ser hecho, desarrollaremos la funcionalidad por partes. Empezaremos por los primeros cinco elementos de la lista, que son más básicos. Luego, abordaremos la parte de reglas de negocio.

Abriré el IntelliJ con el proyecto importado en la IDE. Es el mismo proyecto del curso anterior, que finalizamos con la parte de seguridad y tratamiento de errores.

Como la primera parte seguirá un mismo patrón, dejé esas clases listas en el código. Creé un nuevo ConsultaController en el paquete "src > main > java > med.voll.api > controller".

Ya que no estaremos más registrando pacientes, sino consultas, la idea es tener un Controller para recibir esas solicitudes relacionadas con la programación de consultas.

Es una clase Controller, con las anotaciones del Spring: @Controller,@ResponseBody, @RequestMapping("consultas") o @RestController. Mapea las solicitudes que llegan con la URI "/consultas", sabiendo que debe llamar a este controller y no a los otros.

Luego, tenemos un método anotado con @PostMapping. Entonces, la solicitud para programar una consulta será del tipo Post, como observamos en las otras funcionalidades.

```java
@Controller
@ResponseBody
@RequestMapping("/consultas")
public class ConsultaController {

   @PostMapping
   @Transactional   
   public ResponseEntity agendar(@RequestBody @Valid DatosAgendarConsulta datos) {

System.out.println(dados);
           return ResponseEntity.ok(new DatosDetalleConsulta(null, null, null, null));

   }
}
```

Al abrir DatosAgendarConsulta, notaremos que se trata de un registro similar a los que vimos anteriormente. Tiene los campos que provienen de la API (Long idMedico, Long idPaciente y LocalDateTime fecha) y las anotaciones de BEAN validation @NotNull para el ID de la persona paciente y para la fecha, además de que la fecha debe ser en el futuro (@Future), es decir, no podemos programar una consulta para días que ya han pasado.

```java
public record DatosAgendarConsulta(

       @NotNull
       Long idPaciente,
       Long idMedico,
       @NotNull
       @Future
       LocalDateTime fecha,
       Especialidad especialidad) {

}
```

Al volver al Controller, nuestro otro DTO es el de respuesta, llamado DatosDetalleConsulta. Devuelve el ID de la consulta creada, del médico, del paciente y la fecha de la consulta registrada en el sistema.

Además, en el paquete "src > main > java > med.voll.api > domain", creamos el subpaquete "consulta", que abarca las clases relacionadas con el dominio de consulta.

Entre ellas, tenemos la entidad JPA "Consulta.java", que contiene las anotaciones de JPA y Lombok, así como la información de la consulta: médico, paciente y fecha.

```java
@Table(name = "consultas")
@Entity(name = "Consulta")
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode(of = "id")
public class Consulta {

   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   private Long id;

   @ManyToOne(fetch = FetchType.LAZY)
   @JoinColumn(name = "medico_id")
   private Medico medico;

   @ManyToOne(fetch = FetchType.LAZY)
   @JoinColumn(name = "paciente_id")
   private Paciente paciente;

   private LocalDateTime fecha;

}
```

En este caso, médico y paciente son relaciones con las otras entidades Medico y Paciente.

También creamos "ConsultaRepository.java", que está vacío por el momento.

```java
@Repository
public interface ConsultaRepository extends JpaRepository<Consulta, Long> {

}
```

Por último, tenemos la migración en "src > main > java > med.voll.api > resources", en la carpeta "db.migration". Teníamos cinco migraciones, pero ahora creé la migración número 6 ("V6"), que crea la tabla de consultas.

```java
create table consultas(

   id bigint not null auto_increment,
   medico_id bigint not null,
   paciente_id bigint not null,
   fecha datetime not null,

   primary key(id),

   constraint fk_consultas_medico_id foreign key(medico_id) references medicos(id),
   constraint fk_consultas_paciente_id foreign key(paciente_id) references pacientes(id)

);
```

los campos de ID de consulta, ID de paciente, ID de médico y fecha, donde medico_id y paciente_id son claves foráneas que apuntan a las tablas de médicos y pacientes.

Estos son los códigos estándar para cualquier funcionalidad, con sus respectivos cambios de acuerdo con el proyecto. Cada uno creará un controlador o una entidad distinta, pero el funcionamiento es el mismo.

El proyecto se inició sin errores. Ahora podemos intentar enviar una solicitud a la dirección "/consultas" y verificar si se redirige al "ConsultaController.java" y nos muestra el System.out que muestra los datos que llegaron en el JSON de la solicitud.

Para enviar la solicitud, continuaremos utilizando Postman.

```java
{
"idPaciente": 1,
"idMedico": 1,
"fecha": 
}
```

el atributo "fecha" está representado por LocalDateTime, la clase de fechas que se introdujo en Java 8. ¿Cómo enviamos una fecha en la solicitud JSON de manera que Spring pueda crear correctamente el objeto LocalDateTime? ¿Qué formato usamos para la fecha?

Es necesario que la fecha esté entre comillas, como si fuera una cadena, y tenga el formato de fecha americano: AAAA-MM-DD. Para separar la fecha de la hora, escribimos la letra "T" en mayúscula y la hora en formato HH:MM:SS. Los segundos son opcionales.

Así que la solicitud se verá de la siguiente manera:

```java
{
"idPaciente": 1,
"idMedico": 1,
"fecha": "2023-10-10T10:00"
}
```

Ya hemos implementado el esqueleto de la funcionalidad. Ahora necesitamos implementar las reglas de negocio.

Nuestro trabajo será un poco diferente a lo que ya hicimos con la validación de campos de formulario vía BEAN validation. Ahora, las validaciones son más complejas. ¿Pero cómo las implementamos?

Volviendo a IntelliJ y observando "ConsultaController.java", podríamos hacer todas las validaciones en el método agendar(), antes del retorno. Sin embargo, esa no es una buena práctica.

La clase controller no debe traer las reglas de negocio de la aplicación.

Es solo una clase que controla el flujo de ejecución: cuando llega una solicitud, llama a la clase X, devuelve la respuesta Y. Si la condición es Z, devuelve otra respuesta y así sucesivamente. Es decir, solo controla el flujo de ejecución y, por lo tanto, no debería tener reglas de negocio.

Así, aislando las reglas de negocio, los algoritmos, los cálculos y las validaciones en otra clase que será llamada por el Controller.

Expandiremos el menú "Project" en el lado izquierdo de la interfaz, seleccionaremos el paquete "consulta" . De esta manera, crearemos una clase para contener las reglas de agendamiento de consultas. La llamaremos "AgendaDeConsultasService".

El nombre es muy autoexplicativo: esta clase contendrá la agenda de consultas. Podemos tener en esta clase otras funcionalidades aún relacionadas con el agendamiento de consultas.

Como acabamos de crear la clase y aún no tiene ninguna anotación, el Spring Boot no puede cargarla automáticamente. Por lo tanto, necesitamos insertar alguna anotación primero.

Sin embargo, esta no es una clase Controller ni una clase de configuraciones. Esta clase representa un servicio de la aplicación, el de agendamiento de consultas. Por lo tanto, será una clase de servicios (Service) y llevará la anotación @Service. El objetivo de esta anotación es declarar el componente de servicio al Spring Boot.

Dentro de esta clase, crearemos un método public void agendar(), que recibe como parámetro el DTO DatosAgendarConsulta.

```java
@Service
public class AgendaDeConsultaService {
   public void agendar(DatosAgendarConsulta datos){


   }
}
```

La clase Service ejecuta las reglas de negocio y las validaciones de la aplicación.

Necesitaremos utilizar esta clase en "ConsultaController.java". Necesitamos declarar un atributo del tipo AgendaDeConsultas, llamándolo agenda. Para pedirle a Spring que instancie este objeto, usaremos @Autowired encima del atributo.

```java
@Autowired
private AgendaDeConsultaService service;
```

Con esto, hemos inyectado la clase AgendaDeConsultas en el Controller. En el método agendar del Controller, obtendremos el objeto agenda y llamaremos al método agendar(), pasando como parámetro los datos que llegan al Controller. Todo esto antes del retorno.

```java
@PostMapping
   @Transactional   
   public ResponseEntity agendar(@RequestBody @Valid DatosAgendarConsulta datos) {

service.agendar(datos);
return ResponseEntity.ok(new DatosDetalleConsulta(null, null, null, null));

   }
```

El Controller recibe la información, hace solo la validación de BIN validation y llama a la clase Service AgendaDeConsultas, que ejecutará las reglas de negocio. Esta es la forma correcta de manejar las reglas de negocio.

Ahora abriremos la clase AgendaDeConsultas y escribiremos todas las validaciones que figuran en el cartão do Trello dentro del método agendar().

Al final del día, nuestro objetivo es guardar el agendamiento en la base de datos: recibimos la solicitud con los datos de agendamiento y necesitamos guardarlos en la tabla de consultas.

Por lo tanto, necesitamos acceder a la base de datos y a la tabla de consultas en esta clase. Así que declararemos un atributo ConsultaRepository, llamándolo consultaRepository.

Justo encima del atributo, usaremos la anotación @Autowired para que el Spring Boot inyecte este repository en nuestra clase Service.

Al final del método agendar(), insertaremos consultaRepository.save() y pasaremos un objeto del tipo consulta, la entidad JPA. Obviamente, solo podemos llamar a este método si todas las validaciones se han ejecutado de acuerdo con las reglas de negocio.

```java
@Service
public class AgendaDeConsultaService {

@Autowired
private ConsultaRepository consultaRepository;

   public void agendar(DatosAgendarConsulta datos){

    consultaRepository.save(consulta);

   }
}
```

Ya hemos llamado "save", pero está dando un error de compilación porque la variable "consulta" aún no ha sido creada. Por lo tanto, necesitamos ejecutar esta acción.

```java
@Service
public class AgendaDeConsultaService {

@Autowired
private ConsultaRepository consultaRepository;

   public void agendar(DatosAgendarConsulta datos){

    var consulta = new Consulta();
consultaRepository.save(consulta);

   }
}
```

La entidad Consulta está anotada con @AllArgsConstructor de Lombok, que genera un constructor con todos los atributos. Podemos usar este mismo constructor en "AgendamientoDeConsultas". El primer parámetro es el ID null, ya que es la base de datos la que pasará el ID. El segundo es médico, paciente y fecha. Esta última vendrá en el DTO, a través del parámetro datos.

Sucede que el médico y el paciente no llegan en la solicitud, sino el ID del médico y el ID del paciente. Por lo tanto, necesitamos establecer el objeto completo en la entidad y no solo el ID.

Por lo tanto, necesitamos cargar médico y paciente desde la base de datos. Necesitaremos inyectar, entonces, dos Repositories más en nuestro Service: MedicoRepository y PacienteRepository.

En el método agendar(), necesitamos crear un objeto paciente también. Usaremos pacienteRepository.findById() para buscar el objeto por ID, que está dentro del DTO datos.

En la solicitud solo viene el ID, pero necesitamos cargar el objeto completo. Por lo tanto, usamos el Repository para cargar por el ID de la base de datos. El médico seguirá la misma dinámica.

```java
@Service
public class AgendaDeConsultaService {

@Autowired
private PacienteRepository pacienteRepository;
@Autowired
private MedicoRepository medicoRepository;
@Autowired
private ConsultaRepository consultaRepository;

   public void agendar(DatosAgendarConsulta datos){
var paciente = pacienteRepository.findById(datos.idPaciente()).;

var medico = medicoRepository.findById(datos.idMedico()).;

    var consulta = new Consulta(null,medico,paciente,datos.fecha());
consultaRepository.save(consulta);

   }
}
```

Aparecerá un error de compilación porque el método findById() no devuelve la entidad, sino un Optional. Por lo tanto, al final de la línea, antes del punto y coma, necesitamos escribir .get() junto a findById(). Esto hace que tome la entidad cargada.

```java
var paciente = pacienteRepository.findById(datos.idPaciente()).get();

var medico = medicoRepository.findById(datos.idMedico()).get();

    var consulta = new Consulta(null,medico,paciente,datos.fecha());
consultaRepository.save(consulta);
```

El método agendar() en la clase Service hará lo siguiente: obtenemos el ID y cargamos el paciente y el médico desde la base de datos; creamos una entidad consulta pasando el médico, el paciente y la fecha que viene en el DTO; y lo guardamos en la base de datos.

Pero antes de eso, necesitamos escribir el código para realizar todas las validaciones que forman parte de las reglas de negocio.

El código de las reglas de negocio aparecerá antes del último fragmento que implementamos en este video. A continuación, abordaremos cómo realizar las validaciones de la mejor manera posible.

---

Para verificar el ID del paciente, usaremos un if. La idea es comprobar si el ID del paciente existe. El Repository es el que consulta la base de datos.

Podemos lanzar una excepción dentro del if, que muestre un mensaje que indique el problema. Incluso podemos crear una excepción personalizada para nuestro proyecto llamada ValidacaoException().

Dentro de los paréntesis, escribimos el mensaje como una cadena ("El ID del paciente proporcionado no existe" o algo similar).

```java
public DatosDetalleConsulta agendar(DatosAgendarConsulta datos){

   if(!pacienteRepository.findById(datos.idPaciente()).isPresent()){
       throw new ValidacionDeIntegridad("este id para el paciente no fue encontrado");
   }
}
```

Recibiremos un error de validación porque la clase exception no existe. Con el cursor del mouse sobre ella, usaremos el atajo "ALT + Enter" y seleccionaremos la opción "Create Class" (Crear clase).

En "Destination package" (Paquete de destino), dejaremos la clase en el paquete "domain" ("med.voll.api.domain"), ya que no es específica de ninguna funcionalidad. A continuación, seleccionaremos el botón "OK".

Con esto, IntelliJ creará la excepción automáticamente. Heredará de Throwable, pero lo cambiaremos a RuntimeException.

Ya creará un constructor que recibe la cadena, pero coloca el parámetro con el nombre "s". Renombraremos este parámetro como "mensaje". Dentro del constructor, llamaremos al constructor de la clase madre usando la palabra "super" y pasando el parámetro "mensaje".

```java
public class ValidacionDeIntegridad extends RuntimeException {
   public ValidacionDeIntegridad(String s) {
       super(s);
   }
}
```

Continuando con la clase "AgendaDeConsulta", haremos la verificación con el if creado. Primero, verificaremos si existe un paciente con el ID que está llegando a la base de datos. Si no existe, se lanzará una excepción con un mensaje de error.

Existe un método en la interfaz Repository en Spring Data llamado existsById. Realiza una consulta a la base de datos para verificar si existe un registro con un determinado ID. Devuelve un booleano: true si existe y false si no existe.

Usaremos este método existsById, pasando como parámetro datos.idMedico(). Negaremos la expresión agregando un signo de exclamación al inicio. Con esto, si no hay un paciente con el ID en la base de datos, debemos detener la ejecución del programa.

```java
if( !medicoRepository.existsById(datos.idMedico())){
   throw new ValidacionDeIntegridad("este id para el medico no fue encontrado");
}
```

uestra última regla de negocio en Trello dice lo siguiente:

"La elección del médico es opcional, y en ese caso el sistema debe elegir aleatoriamente algún médico disponible en la fecha/hora indicada".

Por lo tanto, es posible que un ID de médico no llegue en la solicitud. Volviendo a IntelliJ, no podemos llamar a existsById si el ID del médico está nulo. Esto resultará en un error para JPA.

Solo podemos llamar al if si el ID no es nulo. Por lo tanto, agregaremos una condición al if antes de la condición actual:

```java
if(datos.idMedico()!=null && !medicoRepository.existsById(datos.idMedico())){
   throw new ValidacionDeIntegridad("este id para el medico no fue encontrado");
}
```

Los dos "ifs" se utilizan para asegurarse de que llegue un ID existente en la base de datos. La excepción devuelve un mensaje que describe el error de manera más apropiada para el cliente de la aplicación.

El código sólo llegará a la sección que crea las variables si el ID que llega está en la base de datos. En el caso del médico, al ser un campo opcional, puede ser que la línea "var medico = medicoRepository.findById(dados.idMedico()).get()" tenga un idMedico nulo.

De acuerdo con la regla de negocio que acabamos de analizar, necesitamos escribir un algoritmo que elija aleatoriamente un médico en la base de datos. Por lo tanto, la línea anterior necesita ser reemplazada. Como todavía no tenemos el algoritmo, crearemos un método privado en la clase para llamarlo:

```java
var medico = seleccionarMedico(datos);
```

Obtendremos un error indicando que el método 'seleccionarMedico' no existe. Presionaremos el atajo "ALT + Enter" y elegiremos la opción "Crear método 'seleccionarMedico' en 'AgendaDeConsultas'". El método se crea como privado, pero su tipo de retorno es void, lo cual reemplazaremos por un objeto de tipo Medico.

```java
private Medico seleccionarMedico(DatosAgendarConsulta datos) {

}
```

Esto sirve para aislar el algoritmo y evitar que esté suelto dentro del método de programación de citas. En escolherMedico, necesitamos verificar si está llegando el ID del médico en la solicitud o no.

Si lo está, obtendremos el médico correspondiente de la base de datos. Si no es así, debemos elegir aleatoriamente un profesional de la salud, según lo indica la regla de negocio. A continuación, abordaremos la implementación de este algoritmo y cómo podemos elegir un médico aleatorio de la base de datos.

Ahora, necesitamos implementar el algoritmo que elige al médico de manera aleatoria en caso de que la persona que realiza la solicitud no haya elegido un profesional de la salud para atenderla al programar la cita.

Tenemos que cubrir todos los posibles escenarios. La primera comprobación que haremos es si la persona eligió un médico al hacer la solicitud, usando if (dados.idMedico() != null).

En este caso, cargaremos la información de la base de datos con return medicoRepository.getReferenceById(dados.idMedico()). En lugar de usar findById(), podemos usar getReferenceById() y no necesitamos llamar al .get() que usamos anteriormente.

También podemos cambiar findById() por getReferenceById() en la variable paciente, ya que no queremos cargar el objeto para manipularlo, sino solo para asignarlo a otro objeto.

Volviendo al método 'seleccionarMedico' (), lo primero que estamos haciendo es verificar si la persona que realiza la solicitud solicitó un médico específico para su atención. Si es así, simplemente cargamos la información del médico que viene de la base de datos.

```java
private Medico seleccionarMedico(DatosAgendarConsulta datos) {
   if(datos.idMedico()!=null){
       return medicoRepository.getReferenceById(datos.idMedico());
   }

  return medicoRepository.getReferenceById(datos.idMedico);
}
```

Por el momento, la información que se encuentra en el DTO es idMedico, idPaciente y LocalDateTime fecha. Añadiremos una coma después de fecha y declararemos la especialidad como Especialidad especialidad. Sin embargo, no podemos agregar la anotación @NotNull ya que es opcional. Solo es obligatoria cuando no tenemos el ID del médico.

```java
public record DatosAgendarConsulta(
       @NotNull
       Long idPaciente,
       Long idMedico,
       @NotNull
       @Future
       LocalDateTime fecha,
       Especialidad especialidad) {
}
```

Volveremos a la clase "AgendamientoDeConsultas" para editar el método seleccionarMedico(). Si el registro no entró en el if (no se solicitó un médico específico), esto significa que el ID del médico es nulo. En este caso, la especialidad debe haber sido completada. Si no está completada, lanzaremos una excepción.

```java
if(datos.especialidad()==null){
   throw new ValidacionDeIntegridad("debe seleccionarse una especialidad para el medico");
}
```

Si ninguno de estos if fue aplicado, está todo bien. Esto significa que no se eligió un médico, pero se eligió una especialidad. Ahora, necesitamos seleccionar un médico aleatorio que atienda la especialidad elegida.

Entonces, necesitamos hacer una consulta a la base de datos. ¿Pero cómo elegimos un médico aleatorio de la especialidad elegida que esté disponible en la fecha y hora seleccionadas?

Existen varias formas de hacer esto: podríamos cargar los médicos, filtrarlos por especialidad y comprobar la fecha de la consulta en Java.

Lo ideal es cargar un profesional aleatorio directamente de la base de datos. Sin embargo, esta consulta es específica para nuestro proyecto, es decir, no está lista en Spring Data JPA.

Necesitaremos crear un método para hacer esto:

```java
return medicoRepository.seleccionarMedicoConEspecialidadEnFecha(datos.especialidad(),datos.fecha());
```

Obtendremos un error de compilación porque este método aún no existe en nuestro medicoRepository. Usaremos el atajo "ALT + Enter" y elegiremos la primera opción sugerida por IntelliJ: "Create method 'seleccionarMedicoConEspecialidadEnFecha' in 'MedicoRepository'".

Sin embargo, escribimos este método en portugués. No estamos siguiendo el estándar de nomenclatura en portugués, como el findAllByAtivoTrue(). De esta manera, Spring Data no podrá construir el SQL automáticamente. La idea es precisamente esa: para este método, nosotros escribiremos el comando SQL.

Para hacerlo, usaremos la anotación @Query() justo encima del método. Viene del paquete "org.springframework.data.jpa". Importaremos la anotación y, entre paréntesis, construiremos la consulta utilizando la sintaxis del Java Persistence Query Language (JPQL).

En nuestro caso, construiremos una consulta. Para facilitar la visualización, dejaremos la consulta dividida por líneas y usaremos el Text block. Esto nos permite escribir el código y dividir las líneas de manera más simple, sin tener que concatenar todo con "+".

¿Por dónde empezamos? Por lo obvio: escribiendo select m from Medico m dentro de la consulta. Si lo dejamos así, se cargarán todos los médicos de la base de datos.

Eso no es lo que queremos. Por lo tanto, debemos filtrar usando m.activo = 1. Recuerda que solo queremos cargar médicos activos. Esta es una de las reglas de negocio que están en nuestro Trello.

Sin embargo, todavía estamos trayendo todos los médicos activos. Por eso, filtraremos una vez más centrándonos en la especialidad: m.especialidad = :especialidad. El : sirve para indicar que se trata del mismo parámetro del método.

```java
@Query("""
       select m from Medico m
       where m.activo= 1
       and
       m.especialidad=:especialidad
            """)
```

Con esto, estamos pidiendo que el código nos traiga todos los médicos activos que sean de la especialidad seleccionada por el usuario.

Sin embargo, no queremos que traiga todos los médicos de esa especialidad, sino solo uno. ¿Cómo lo hacemos? Escribiendo "limit 1". De esta manera, solo se traerá un registro.

Sin embargo, de esta manera siempre tendremos el mismo médico para la especialidad. ¿Cómo obtenemos un médico elegido aleatoriamente? Escribiendo "order by rand()", donde "rand()" significa "aleatorio".

Ahora, se traerán todos los médicos, ordenados de manera aleatoria. Luego, pedimos que solo se traiga el primer registro.

```java
@Query("""
       select m from Medico m
       where m.activo= 1
       and
       m.especialidad=:especialidad
       order by rand()
       limit 1
       """)
```

Aún no hemos terminado. Por ahora, estamos trayendo un médico de una especialidad determinada de manera aleatoria. Sin embargo, necesitamos un profesional que esté disponible en el día y horario seleccionado por el usuario. Con el código actual, es posible que estemos trayendo a un médico con la agenda completa en la fecha seleccionada. ¿Cómo filtramos esto? Necesitamos consultar la tabla de consultas. Después de la especialidad, agregaremos otro "and" y escribiremos "m.id not in()". Con esto, estamos pidiendo que se traigan los médicos cuyo ID no esté en esta subselección. Dentro de la subselección, escribiremos:

```java
select c.medico.id from Consulta c
where
c.fecha=:fecha
```

Este subselect está trayendo el ID de los médicos que tienen una consulta en la fecha seleccionada. Con el not in(), evitaremos los médicos de esta lista. El código completo quedaría de la siguiente forma:

```java
@Query("""
       select m from Medico m
       where m.activo= 1
       and
       m.especialidad=:especialidad
       and
       m.id not in( 
           select c.medico.id from Consulta c
           where
           c.fecha=:fecha
       )
       order by rand()
       limit 1
       """)
```

### Lo que aprendimos

Lo que aprendimos en esta aula:

- Implementar una nueva funcionalidad en el proyecto;
- Evaluar cuando es necesario crear una clase de Servicio en la aplicación;
- Crear una clase de Servicio, con el objetivo de aislar códigos de reglas de negocio, utilizando para ello la anotación @Service;
- Implementar un algoritmo para la funcionalidad de programación de consultas;
- Realizar validaciones de integridad de la información que llega a la API;
- Implemente una consulta JPQL (Lenguaje de consulta de persistencia de Java) compleja en una interfaz de repositorio, utilizando la anotación @Query.

### Proyecto del aula anterior

¿Comenzando en esta etapa? [Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior](https://github.com/alura-es-cursos/Spring-Boot-3/tree/stage-1 "Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior").

### Para saber más: principios SOLID

SOLID es un acrónimo que representa cinco principios de programación:

- Principio de Responsabilidad Única (Single Responsibility Principle)
- Principio Abierto-Cerrado (Open-Closed Principle)
- Principio de Sustitución de Liskov (Liskov Substitution Principle)
- Principio de Segregación de Interfaces (Interface Segregation Principle)
- Principio de Inversión de Dependencia (Dependency Inversion Principle)

Cada principio representa una buena práctica de programación que, cuando se aplica en una aplicación, facilita mucho su mantenimiento y extensión. Estos principios fueron creados por Robert Martin, conocido como Uncle Bob, en su artículo Design Principles and Design Patterns.

### Haga lo que hicimos en aula

Para cada validación de las reglas de negocio, crearemos una clase específica para cada una de ellas. La primera regla trata sobre el horario de la clínica: "El horario de funcionamiento de la clínica es de lunes a sábado, de 07:00 a 19:00". Si llega una solicitud a nuestra API con una fecha programada para una consulta, ¿qué sucede si el cliente intenta programar una consulta para un domingo? ¿O para un lunes a las 4 de la mañana? Por eso necesitamos validar el horario de la consulta. Crearemos una clase para realizar esta validación.

En el IntelliJ abriremos el menú lateral del proyecto y, para organizar esta clase, crearemos un subpaquete. Con la carpeta "consulta" seleccionada, presionamos la combinación de teclas "Alt + insert" y seleccionaremos la opción "Paquete". Crearemos un nuevo paquete llamado "validaciones".

Dentro del paquete "validaciones", crearemos las clases. Primero, crearemos una nueva clase llamada "HorarioDeFuncionamientoClinica". La idea es crear un método dentro de esta clase para realizar la validación del horario de funcionamiento de la clínica. Crearemos el método "validar()" y recibiremos el DTO "DatosAgendarConsulta" como parámetro.

```java
public class HorarioDeFuncionamientoClinica{

   public void validar(DatosAgendarConsulta datos) {

       }

}
```
Escribiremos var domingo igual a DayOfWeek.getDayOfWeek(), usaremos este método para obtener el día de la semana, y para verificar si es igual usaremos equals con el parámetro que indica el día de la semana que queremos comparar, en este caso el domingo, equals(DayOfWeek.SUNDAY). Esto nos devolverá un booleano, true si es domingo y false si no lo es.
```java
public class HorarioDeFuncionamientoClinica{
   public void validar(DatosAgendarConsulta datos) {

var domingo = DayOfWeek.SUNDAY.equals(datos.fecha().getDayOfWeek());

     }
}
```
Seguiremos la misma lógica, será un booleano. Crearemos una variable llamada "antesDeApertura", igual a "datos.fecha().getHour())" y verificaremos si es menor que 7:

```java
var antesDeApertura=datos.fecha().getHour()<7;
```

Realizaremos lo mismo para verificar si el horario es después del cierre. Crearemos una variable llamada "despuesDeCierre", igual a "datos.fecha().getHour()" y comprobaremos si es menor que las 18:00.

```java
var despuesDeCierre=datos.fecha().getHour()>19;
```
El horario de funcionamiento de la clínica es de 7h a 19h. No puede haber una consulta a las 19h, la última consulta debe ser a las 18h. Además, una de las reglas de negocio dice que "Las consultas tienen una duración de 1 hora".

A continuación, haremos un if. Si es domingo, antes de la apertura de la clínica o después del cierre de la clínica, lanzaremos una excepción para indicar que es inválido, throw new ValidationException("Consulta fuera del horario de funcionamiento de la clínica").

```java
if(domingo || antesdDeApertura || despuesDeCierre){
   throw  new ValidationException("El horario de atención de la clínica es de lunes a sábado, de 07:00 a 19:00 horas");
}
```

El codigo quedo asi

```java
public class HorarioDeFuncionamientoClinica{
   public void validar(DatosAgendarConsulta datos) {

       var domingo = DayOfWeek.SUNDAY.equals(datos.fecha().getDayOfWeek());

       var antesdDeApertura=datos.fecha().getHour()<7;
       var despuesDeCierre=datos.fecha().getHour()>19;
       if(domingo || antesdDeApertura || despuesDeCierre){
           throw  new ValidationException("El horario de atención de la clínica es de lunes a sábado, de 07:00 a 19:00 horas");
       }
   }
}
```
¡Listo! Tenemos nuestra primera validación. El único objetivo de esta clase es ejecutar esa única validación. El código queda pequeño, simple y fácil de dar mantenimiento y de probar de manera automatizada.

Ahora tenemos que hacer el código de las otras validaciones. La siguiente validación de la lista es "Las consultas tienen una duración fija de 1 hora". Esta no necesitamos verificar, ya será implícita, la aplicación estará disponible de una en una hora para agendar la consulta.

Siguiente validación: "Las consultas deben ser agendadas con un mínimo de 30 minutos de antelación". Vamos a crear un validador para esto. En nuestro proyecto, dentro del paquete de validaciones, vamos a insertar una nueva clase llamada HorarioDeAnticipacion.

Crearemos un método similar al que hicimos anteriormente.
```java
public class HorarioDeAnticipacion{

   public void validar(DatosAgendarConsulta datos) {

   }
}
```
Vamos a agregar las variables horaDeConsulta y ahora:

```java
public void validar(DatosAgendarConsulta datos) {

   var ahora = LocalDateTime.now();
   var horaDeConsulta= datos.fecha();

}
```

Ahora compararemos la diferencia en minutos entre esas fechas para lanzar una excepción en caso de que sea inferior a treinta.

Crearemos una variable llamada diferenciaDe30Minigual a Duration.between pasando como parámetro ahora y horaDeConsulta, seguido de .toMinutes() para obtener la duración en minutos.

```java
public void validar(DatosAgendarConsulta datos) {
   var ahora = LocalDateTime.now();
   var horaDeConsulta= datos.fecha();

   var diferenciaDe30Min= Duration.between(ahora,horaDeConsulta).toMinutes()<30;

}
```

Ahora solo queda hacer un if. Si diferenciaDe30Min es menor que 30, lanzaremos una excepción con el mensaje: "La consulta debe ser agendada con una antelación mínima de 30 minutos".

```java
public void validar(DatosAgendarConsulta datos) {
   var ahora = LocalDateTime.now();
   var horaDeConsulta= datos.fecha();

   var diferenciaDe30Min= Duration.between(ahora,horaDeConsulta).toMinutes()<30;
   if(diferenciaDe30Min){
       throw new ValidationException("Las consultas deben programarse con al menos 30 minutos de anticipación");
   }
}
```

En la clase MedicoActivo, la única diferencia es que usamos el Repositorio, que aún no se está inyectando, pronto hablaremos de eso. Estamos buscando solo el atributo activo del médico, filtrando por el ID. Y si el médico no tiene ID, lanza una excepción.

Se creó el método findAtivoByID. En este caso, no quiero cargar el objeto completo del médico solo para verificar si el atributo activo está en true. Entonces, podemos hacer una consulta personalizada trayendo solo un atributo único, select m.ativo.

Luego tenemos la clase MedicoConConsulta. Aquí también es necesario consultar la base de datos para verificar si hay una consulta con este médico en la misma fecha.

Hicimos una consulta usando el patrón de nomenclatura de SpringData: existsByMedicoIdAndFecha(idMedico, fecha).

El PacienteActivo se parece al ValidadorMedicoActivo.

Y tenemos la validación para que el paciente no tenga consulta en la misma fecha, PacienteSinConsulta. También consulta la base de datos para ver si hay una consulta para este paciente, colocando la fecha de inicio y la fecha de finalización de ese día, tomando la primera y la última hora del día.

---

Vamos a crear una interfaz en el proyecto. En el panel lateral, con el paquete de validaciones seleccionado, usaremos el atajo "Alt + Insert", elegiremos la opción "Java Class", la nombraremos como ValidadorDeConsultas y elegiremos la opción "Interface".

Una vez creada la interfaz, dentro de ella declararemos el método que las clases tienen en común, validar(DatosAgendarConsulta datos). No es necesario usar la palabra clave "public" ya que es implícito que todos los métodos de una interfaz son públicos.

```java
public interface ValidadorDeConsultas {
   public void validar(DatosAgendarConsulta datos);
}
```

Aquí no es necesario usar "public" porque es implícito que todos los métodos de una interfaz son públicos. Ahora, vamos a cambiar cada una de las clases para implementar esta interfaz. Empezando por la clase HorarioDeAnticipacion, vamos a agregar "implements ValidadorDeConsultas " en la firma de la clase, antes de abrir las llaves:

```java
public class HorarioDeAnticipacion implements ValidadorDeConsultas
```

Haremos lo mismo con los otros validadores. Tenemos 6 validadores, haremos esto para cada una de las 6 clases.

De esta manera, podemos estandarizar el proyecto. Cada validador debe implementar esta interfaz ValidadorAgendamentoDeConsulta y, obligatoriamente, debe implementar el método validar(DatosAgendarConsulta). Solo el cuerpo del método de cada clase será diferente.

Otra cosa importante: para poder inyectar estas clases en algún lugar, Spring necesita conocerlas. Entonces, encima de ellas debe haber alguna anotación de Spring.

Podríamos poner la anotación @Service para indicar que es un servicio, una validación. Pero vamos a usar la anotación @Component que es para componentes genéricos. Porque a veces tenemos una clase que no es ni una clase de configuración, ni un controlador ni un servicio. Entonces podemos poner el @Component para indicarle a Spring que esta clase es un componente genérico y lo cargará en la inicialización del proyecto.

Podría ser el @Service también, pero dejaré el @Component para mostrar algo nuevo para ustedes.

Vamos a insertar @Component en todas las clases de validación.

```java
@Component
public class HorarioDeAnticipacion implements ValidadorDeConsultas
```

Ahora, con el @Component el Spring reconocerá estas clases y podremos inyectar el repository.

Podemos colocar @Autowired en las clases que tienen el repository como atributo, para que el Spring inyecte el repository automáticamente. El MedicoActivo quedará así, por ejemplo:

```java
@Service
public class MedicoActivo implements ValidadorDeConsultas{
   @Autowired
   private MedicoRepository repository;

}
```

En la interfaz no es necesario colocar ninguna anotación porque el Spring la carga automáticamente.

Para inyectar estos validadores en la clase service, la clase AgendaDeConsultaService, usaremos un esquema del Spring que nos facilitará la vida.

Podrías pensar que necesitamos inyectar cada uno de los validadores de la misma manera que estamos inyectando los repositorios. Pero ese es el problema que queremos evitar, no queremos declararlos uno por uno. Así que haremos un truco que el Spring nos permite hacer.

En el código de AgendaDeConsultaService, declararemos un atributo, lo anotaremos con @Autowired, pero este atributo será declarado como una lista de java.util.List y, dentro de la lista, declararemos la interfaz ValidadorDeConsultas y llamaremos a este atributo validadores.

```java
@Autowired
private PacienteRepository pacienteRepository;
@Autowired
private MedicoRepository medicoRepository;
@Autowired
private ConsultaRepository consultaRepository;
@Autowired
List<ValidadorDeConsultas> validadores;
```

Spring va a identificar automáticamente que estamos inyectando una lista y buscará todas las clases que implementan la interfaz ValidadorDeConsultas. Así, no importa la cantidad de validadores, el Spring inyectará uno por uno. Es mucho más práctico hacerlo de esta manera.

Ahora, en el método agendar(), antes de crear las variables del paciente y del médico, vamos a obtener esta lista de validadores y recorrerla con un forEach(v -> v.validar(datos)).

```java
if(datos.idMedico()!=null && !medicoRepository.existsById(datos.idMedico())){
   throw new ValidacionDeIntegridad("este id para el medico no fue encontrado");
}

validadores.forEach(v-> v.validar(datos));

var paciente = pacienteRepository.findById(datos.idPaciente()).get();

var medico = seleccionarMedico(datos);

if(medico==null){
   throw new ValidacionDeIntegridad("no existen medicos disponibles para este horario y especialidad");
}
```

De esta forma logramos inyectar todos los validadores y el código está bastante flexible.

Si queremos excluir un validador, basta con eliminar la clase de ese validador que queremos eliminar. No es necesario modificar la clase AgendaDeConsultaService, la lista simplemente quedará con una clase menos.

Observe cómo el código se ha vuelto flexible. Si surgen nuevas validaciones, si cambiamos una validación o si una validación deja de existir, no necesitaremos modificar la clase de servicio.

Ya hemos implementado la funcionalidad de programación de citas utilizando la clase de servicio y los principios SOLID. Pero aún no hemos probado. Ahora es el momento de probar, vamos a usar Postman para averiguar si todo funciona como se espera. En Postman , vamos a enviar una solicitud para programar una cita.

```Json
POST http://localhost:8080/consultas
{
    "idPaciente": 1,
    "idMedico": 1,
    "fecha": "2023-10-10T10:00,
}
```

Se produjo un error y apareció un mensaje diciendo que no se pudo conectar al servidor. Tal vez haya ocurrido algún problema en el proyecto y se haya detenido. Verifiquemos en la terminal "Run ApiApplication" del proyecto.

Está mostrando que hay alguna consulta con problem

```java
from Medico m
where
m.id = :id
```

Verifiquemos los repositories para localizar ese error. Empezaremos por MedicoRepository.

La última consulta que hicimos fue la findAtivoById.

```java
@Query ("""
    select m.activo
    from Medico m
    where
    m.id = :id
    """)
    Boolean findActivoById(Long id Medico);
}
```

Fue hasta bueno que ocurriera este problema para mostrarles algo importante. Esta consulta tiene m.id = :id. Spring Data asume que :id es el parámetro que viene en el método.

Pero en mi método, el parámetro no está como id, lo dejé como idMedico. Este problema ocurrió porque el nombre del parámetro en la query no coincide con el nombre del parámetro en el método, y por eso se produce este error.

El nombre del parámetro debe ser id, como está en la query:

```java
   @Query("""
            select m.activo
            from Medico m
            where
            m.id = :id
            """)
    Boolean findActivoById(Long id);
}
```

Guardemos y verifiquemos el código del archivo PacienteRepository. También tiene el mismo problema, en el findAtivoById. En lugar de idPaciente, dejaremos solo id.

Corregiremos el mismo error en PacienteRepository. Guardaremos el proyecto y supervisaremos la reinicialización del proyecto en la pestaña "Run".

En principio, la aplicación se inició normalmente. Volvamos a Postman y disparemos nuevamente la solicitud para programar una consulta.

```Json
POST http://localhost:8080/consultas
{
    "idPaciente": 1,
    "idMedico": 1,
    "fecha": "2023-10-10T10:00,
}
```

Fue devuelto un 403 Forbidden. Tal vez ocurrió alguna excepción, vamos verificar en la pestaña "Run ApiApplication".

Se produjo la siguiente excepción:

java.lang.RuntimeException: ¡Token JWT inválido o caducado!

Esto sucedió porque ya hace algún tiempo que se hizo el inicio de sesión. Creo que el token de inicio de sesión tiene una validez de aproximadamente dos horas. Vamos a iniciar sesión de nuevo. En Postman, haga clic en "Iniciar sesión" y disparemos la solicitud.

```Json
POST http://localhost:8080/login
{
    "login": "maria@voll.med",
    "senha": "123456",

}
```

Mostró un nuevo token aleatorio, que vamos a copiar y pegar en la pestaña "Bearer" de la solicitud "Agendar Consultas". Vamos a borrar el token anterior y pegar este nuevo token en el campo "Token".

Luego vamos a enviar la solicitud para agendar una consulta, haciendo clic en "Send". Ahora ha devuelto un código de respuesta 200 OK, pero las informaciones son nulas.

```Json
{
    "id": null,
    "idMedico": null,
    "idPaciente": null,
    "fecha": null,
}
```

Esto está sucediendo porque no hemos cambiado el controlador. El controlador sigue con el return new pasando los parámetros como nulo. Vamos a abrir el paquete de controladores y vamos al ConsultaController para arreglar esto.

En el return del ResponseEntity estamos pasando new DatosDetalleConsulta, por lo que todo se está pasando como nulo. Ya no debemos pasar esta información nula, debemos recibir el objeto de programación que se creó.

En la clase AgendaDeConsultaService, el método agendar() es void. Vamos a quitar este void e insertar el objeto DatosDetalleConsulta. Y al final del método agendar() pondremos return new DatosDetalleConsulta() pasando como parámetro el objeto consulta.

```java
public DatosDetalleConsulta agendar(DatosAgendarConsulta datos){

   if(!pacienteRepository.findById(datos.idPaciente()).isPresent()){
       throw new ValidacionDeIntegridad("este id para el paciente no fue encontrado");
   }

   if(datos.idMedico()!=null && !medicoRepository.existsById(datos.idMedico())){
       throw new ValidacionDeIntegridad("este id para el medico no fue encontrado");
   }

   validadores.forEach(v-> v.validar(datos));

   var paciente = pacienteRepository.findById(datos.idPaciente()).get();

   var medico = seleccionarMedico(datos);

   if(medico==null){
       throw new ValidacionDeIntegridad("no existen medicos disponibles para este horario y especialidad");
   }

   var consulta = new Consulta(medico,paciente,datos.fecha());

   consultaRepository.save(consulta);

   return new DatosDetalleConsulta(consulta);

}
```

Va a dar un error en "consulta", porque todavía no existe ese constructor, vamos usar el atajo "Alt + Enter" para crear el constructor que recibe el objeto consulta y llamamos a this(consulta.getId(), consulta.getMedico().getId(), consulta.getPaciente().getId(), consulta.getFecha()):

```java
public record DatosDetalleConsulta(Long id, Long idPaciente, Long idMedico, LocalDateTime fecha) {
   public DatosDetalleConsulta(Consulta consulta) {
       this(consulta.getId(),consulta.getPaciente().getId(),consulta.getMedico().getId(),consulta.getFecha());
   }
}
```

Ahora tenemos un DTO con los datos siendo devueltos correctamente.

En ConsultaController, el método agendar() devuelve el DTO. Vamos guardar el DTO en una variable y en ResponseEntity.ok pasaremos el DTO, que fue devuelto por la service.

```java
@PostMapping
@Transactional
public ResponseEntity agendar(@RequestBody @Valid DatosAgendarConsulta datos) {
   var dto= service.agendar(datos);
   return ResponseEntity.ok(dto);
}
```

En la peticion Agendar consulta, vamos a testar el envio de un paciente que no existe en la base de datos:

```Json
POST http://localhost:8080/consultas
{
    "idPaciente": 88888,
    "idMedico": 1,
    "fecha": "2023-10-10T10:00"
}
```

Al enviar la solicitud, devolvió el código 403 Forbidden. No debería devolver 403. Volviendo a la terminal de la IDE, podemos ver que está enviando la excepción ValidationException"Id do paciente informado no existe". Pero no está apareciendo para el usuario porque aún no hemos agregado esta validación en la clase que maneja las excepciones. Vamos a corregir eso.

En el paquete "errores", accedemos a la clase TratadorDeErrores y creamos un nuevo método para ValidationException:

```java
@ExceptionHandler(ValidationException.class)
public ResponseEntity errorHandlerValidacionesDeNegocio(Exception e){
   return ResponseEntity.badRequest().body(e.getMessage());
}
```

Entonces, si ocurre un error de regla de negocio, queremos devolver 400 Bad Request y en el cuerpo queremos enviar el mensaje de error, body(ex.getMessage()). Se ha realizado el tratamiento para la excepción.

En Postman, reenviemos la solicitud POST para programar una consulta para un paciente sin ID en nuestra base de datos

```json
POST http://localhost:8080/consultas
{
    "idPaciente": 88888,
    "idMedico": 1,
    "fecha": "2023-10-10T10:00"
}
```

Ahora funcionó correctamente. Devolvió el código 400 Bad Request y en el cuerpo el mensaje "¡Id del paciente informado no existe!". Si pasamos un idMedico que no existe, muestra el mensaje "¡Id del médico informado no existe!".

Ahora podemos añadir un paciente que fue excluido, al enviar la solicitud muestra el mensaje "No se puede programar una consulta con un paciente excluido". Y si se inserta un médico que fue excluido, mostrará el mensaje "No se puede programar una consulta con un médico excluido".

Vamos a probar una regla más, podemos intentar programar una consulta un domingo o fuera del horario de atención. Mostrará el mensaje: "Consulta fuera del horario de atención de la clínica".

Al intentar pasar una consulta sin médico, sólo con la especialidad, se produjo un error. En la terminal de IntelliJ apareció el mensaje:

Column 'medico_id' no puede ser nulo trató de guardar una consulta con un médico que no existe. No debería haber intentado guardar la consulta en la base de datos. Vamos a verificar el código de la clase AgendaDeConsultaService.

Faltó un detalle importante en la lógica de elegir un médico aleatorio. Faltó una verificación. Estamos verificando si viene el id del médico. Si lo tiene, consultamos ese médico en la base de datos. Si no lo tiene, verificamos si se está eligiendo la especialidad y, si se está eligiendo, buscamos un médico que esté libre en esa fecha.

¿Pero qué pasa si no hay ningún médico libre en la fecha elegida? Falta ese escenario. En este caso, el return medicoRepository.seleccionarMedicoConEspecialidadEnFecha devolverá nulo.

Entonces, si esta consulta devuelve nulo, es porque no hay ningún médico libre en esa fecha y la consulta no puede realizarse.

En la línea en la que estamos eligiendo un médico, necesitamos insertar un if para insertar el mensaje "¡No hay médico disponible en esta fecha!".

```java
var paciente = pacienteRepository.findById(datos.idPaciente()).get();

var medico = seleccionarMedico(datos);

if(medico==null){
   throw new ValidacionDeIntegridad("no existen médicos disponibles para este horario y especialidad");
}
```

### Lo que aprendimos

Lo que aprendimos en esta aula:

- Aislar los códigos de validación de reglas comerciales en clases separadas, utilizando la anotación @Component de Spring;
- Finalizar la implementación del algoritmo de programación de consultas;
- Usar los principios SOLID para hacer que el código de la funcionalidad de programación de citas sea más fácil de entender, evolucionar y probar.

### Proyecto del aula anterior

¿Comenzando en esta etapa? [Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior](https://github.com/alura-es-cursos/Spring-Boot-3/tree/stage-2 "Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior").

### Para saber más: OpenAPI Initiative

La documentación es algo muy importante en un proyecto, especialmente si se trata de una API Rest, ya que en este caso podemos tener varios clientes que necesiten comunicarse con ella y necesiten documentación que les enseñe cómo realizar esta comunicación de manera correcta.

Durante mucho tiempo no existió un formato estándar para documentar una API Rest, hasta que en 2010 surgió un proyecto conocido como Swagger, cuyo objetivo era ser una especificación open source para el diseño de APIs Rest. Después de un tiempo, se desarrollaron algunas herramientas para ayudar a los desarrolladores a implementar, visualizar y probar sus APIs, como Swagger UI, Swagger Editor y Swagger Codegen, lo que lo convirtió en un proyecto muy popular y utilizado en todo el mundo.

En 2015, Swagger fue comprado por la empresa SmartBear Software, que donó la parte de la especificación a la fundación Linux. A su vez, la fundación renombró el proyecto a OpenAPI. Después de esto, se creó la OpenAPI Initiative, una organización centrada en el desarrollo y la evolución de la especificación OpenAPI de manera abierta y transparente.

OpenAPI es actualmente la especificación más utilizada y también la principal para documentar una API Rest. La documentación sigue un patrón que puede ser descrito en formato YAML o JSON, lo que facilita la creación de herramientas que puedan leer dichos archivos y automatizar la creación de documentación, así como la generación de código para el consumo de una API.

Puede obtener más detalles en el [sitio web oficial de la OpenAPI Initiative](https://www.openapis.org/ "sitio web oficial de la OpenAPI Initiative").

### Para saber más: personalizando la documentación

En el video anterior, vimos que es posible personalizar la documentación generada por SpringDoc para incluir el token de autenticación. Además del token, podemos incluir otras informaciones en la documentación que forman parte de la especificación OpenAPI, como la descripción de la API, información de contacto y su licencia de uso.

Estas configuraciones se deben hacer en el objeto OpenAPI, que se configuró en la clase SpringDocConfigurations de nuestro proyecto:

```java
@Bean
public OpenAPI customOpenAPI() {
return new OpenAPI()
.components(new Components()
.addSecuritySchemes("bearer-key",
new SecurityScheme()
.type(SecurityScheme.Type.HTTP)
.scheme("bearer")
.bearerFormat("JWT")))
.info(new Info()
.title("API Voll.med")
.description("API Rest de la aplicación Voll.med, que contiene las funcionalidades de CRUD de médicos y pacientes, así como programación y cancelación de consultas.")
.contact(new Contact()
.name("Equipo Backend")
.email("backend@voll.med"))
.license(new License()
.name("Apache 2.0")
.url("http://voll.med/api/licencia")));  }
```

En el código anterior, después de la configuración del token JWT, se agregaron las informaciones de la API. Al acceder nuevamente a la página de Swagger UI, se mostrarán estas informaciones, como se muestra en la imagen a continuación:

![OpenAPI definition](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/2.png "OpenAPI definition")

Para obtener más detalles sobre qué informaciones se pueden configurar en la documentación de la API, consulte la especificación OpenAPI en el sitio web oficial de la iniciativa.

### Haga lo que hicimos en aula

Lo ideal sería proporcionar documentación para que los equipos puedan leer y entender cómo funciona el back-end y construir sus soluciones integradas correctamente.

No lo haremos manualmente para enviarlo como un PDF, por ejemplo, porque es laborioso y, si tenemos algún cambio en la API, tendríamos que actualizar constantemente el documento.

Es decir, nuestra idea es usar alguna herramienta que lo haga de forma automatizada, y existen varias que pueden leer el código de la aplicación del back-end para ponerlo a disposición de los equipos de front-end y móviles, que aún pueden usar automatizaciones en la integración.

En nuestro caso, como estamos usando Java con Spring Boot, tendremos que usar una herramienta compatible, que será SpringDoc. En el navegador, buscaremos "springdoc" para buscar la biblioteca y entender cómo funciona. Como resultado, encontraremos la documentación de SpringDoc en este enlace [https://springdoc.org/](https://springdoc.org/ "https://springdoc.org/").
En la página, tendremos explicaciones sobre lo que es, cómo funciona y cómo aplicar la biblioteca en el proyecto. En la sección "Introduction" o introducción, tendremos más información sobre las tecnologías involucradas que soporta, conteniendo una parte destacada por una alerta de "importante":

Para soporte de spring-boot v3, asegúrese de usar springdoc-openapi v2 [https://springdoc.org/v2/](https://springdoc.org/v2/ "https://springdoc.org/v2/")

Es decir, si estamos usando spring-boot en la versión 3, se recomienda usar la versión 2 de springdoc-openapi, que es nuestro caso.

Haciendo clic en el enlace de springdoc-openapi v2 aquí, tendremos la documentación según la versión correcta que necesitaremos.

Más adelante, tendremos la sección "Getting Started" o "Dando inicio" en portugués, donde tenemos las instrucciones para usar la biblioteca, comenzando con la descarga de la dependencia de Maven con el siguiente código:

```xml
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>2.0.2</version>
</dependency>
```

Copiaremos el bloque y lo agregaremos a nuestro proyecto.

Abriendo IntelliJ, abriremos el archivo pom.xml y navegaremos hasta la última dependencia que es la de java-jwt para pegar el código a continuación antes del cierre de `</dependencies>`.

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>4.2.1</version>
</dependency>

<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.0.2</version>
</dependency>
```

Guardaremos y habremos descargado la biblioteca. Cada vez que incluyamos una nueva, es interesante detener el proyecto haciendo clic en el icono "pausa" representado por un cuadrado rojo en la esquina superior derecha de la pantalla, y luego hacer clic en la pestaña central "Maven" en el extremo derecho. Ampliándolo, seleccionaremos "Dependencies" y luego, en la esquina superior izquierda de esta pestaña, haremos clic en el icono "reload" representado por dos flechas circulares para recargar las dependencias y ver si se descargaron correctamente al final de la lista. Para usar la biblioteca, volveremos a la documentación y, justo después del bloque de código, nos dice que al instalar la dependencia en el proyecto, se crearán dos direcciones en la API: [http://server:port/context-path/swagger-ui.html](http://server:port/context-path/swagger-ui.html "http://server:port/context-path/swagger-ui.html") y [http://server:port/context-path/v3/api-docs](http://server:port/context-path/v3/api-docs "http://server:port/context-path/v3/api-docs"). El primero es una documentación en una página web que podemos navegar y hacer pruebas, similar a lo que hacemos en Postman. La segunda URL genera un JSON con la documentación, y podemos usar herramientas que automatizan la creación del cliente de esta API basado en este JSON. Sin embargo, como estamos usando Spring Security, por defecto bloquea todas las URLs que tenemos en el proyecto, y necesitaremos estar logeados para acceder a ellas, excepto la URL de inicio de sesión. En este caso, queremos que ambas URL sean públicas y estén disponibles sin necesidad de iniciar sesión. Haremos esta configuración en el proyecto. Abriendo IntelliJ, cerraremos el pom.xml y abriremos "src > main > java", y en el paquete med.voll.api, accederemos a "infra > security" y abriremos la clase SecurityConfigurations.java que contiene las configuraciones de seguridad del proyecto. En el método securityFilterChain() donde configuramos las URLs del proyecto, encontraremos la línea de requestMatchers() con .permitAll() que permite la URL de inicio de sesión. Al copiarlos, abriremos una nueva línea debajo y colocaremos las dos direcciones, eliminando el método HttpMethod.POST de los paréntesis de la copia, ya que en este caso la URL será para cualquier método, no necesitaremos filtrar por esto. Cambiaremos la URL `a /v3/api-docs seguida de /**` con los subdirecciones que también deben ser liberadas. Después de cerrar las comillas de la cadena, colocaremos otra en la misma línea con la segunda URL "/swagger-ui.html" en la documentación. También hay otra dirección de Swagger, que es la página HTML que tiene algunos CSS y JavaScript que deben descargarse para cargar la página correctamente. Entonces escribiremos `"/swagger-ui/**"` para que lo que está después de esta dirección sea liberado.

```java
//código omitido

@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    return http.csrf().disable()
        .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
        .and().authorizeHttpRequests()
        .requestMatchers(HttpMethod.POST, "/login").permitAll()
        .requestMatchers("/v3/api-docs/**", "/swagger-ui.html", "/swagger-ui/**").permitAll()
        .anyRequest().authenticated()
        .and().addFilterBefore(securityFilter, UsernamePasswordAuthenticationFilter.class)
        .build();
}
//código omitido
```

De esta forma, guardaremos y tendremos las tres direcciones que necesitaremos dejar públicas para acceder a la documentación generada por SpringDoc.

Ejecutaremos el proyecto haciendo clic en el icono de "play" representado por un triángulo verde en la esquina superior derecha de la pantalla y veremos si aparece algún error.

Si se inicia correctamente, accederemos a la documentación directamente desde el navegador. En el navegador, abrimos una nueva pestaña, accederemos a la dirección de nuestro proyecto "localhost:8080/v3/api-docs" para ver la respuesta.

Recibiremos un JSON que describe todos los endpoints de nuestra API. En el tema llamado paths: en la lista lateral izquierda, tendremos cada uno de ellos.

"Detrás de escena", la biblioteca y los controladores, los métodos y toda la información se utilizan para crear un JSON que describe las funcionalidades.

Este documento de API puede ser utilizado por equipos y clientes para automatizar la generación del código que consumirá la API.

En una nueva pestaña del navegador, accederemos a `"localhost:8080/swagger-ui/index.html"` y abriremos la versión web como un sitio que representa la API.

Es lo mismo que el JSON, pero en una página HTML más agradable y organizada, de modo que más personas puedan comprenderlo fácilmente.

---
Ya hemos generado la documentación, pero aún no podemos "disparar" la solicitud enviando el encabezado con el token JWT.

Deberemos crear un @Bean con la configuración y anotar los métodos y controladores que queremos proteger con el encabezado bearer. Crearemos una clase de configuración de estructura que recibirá el código proporcionado.

En este nuevo paquete, crearemos una nueva clase Java llamada "SpringDocConfigurations" para contener las configuraciones de SpringDoc.

```java
public class SpingDocConfigurations {
}
```

Es una clase Java desconocida, por lo que no podemos cargarla.

Crearemos una anotación @Configuration para dejar claro que la clase hará configuraciones. Dentro de ella, pegaremos el código que copiamos de la documentación.

```java
@Configuration
public class SpingDocConfigurations {

    @Bean
     public OpenAPI customOpenAPI() {
         return new OpenAPI()
                        .components(new Components()
                            .addSecuritySchemes("bearer-key",
                            new SecurityScheme().type(SecurityScheme.Type.HTTP).scheme("bearer").bearerFormat("JWT")));
    }
}
```

Este método está exponiendo el objeto @Bean del tipo OpenAPI que será cargado por SpringDoc y seguirá las configuraciones definidas.

Después de instanciar OpenAPI, llamamos al método que configura componentes que contiene .addSecuritySchemes() pasando el parámetro "bearer-key" y especificando que es del protocolo HTTP del esquema "bearer" en formato "JWT".

Por lo tanto, es una configuración de acuerdo con la biblioteca, que contiene un encabezado para pasar la clave del token. Pero todavía no hemos dicho qué controladores también necesitamos pasar.

Volviendo a la documentación, veremos que hay @Operation() en la que vamos a la operación donde queremos restringir y colocamos la anotación @SecurityRequirement(name = "bearer-key").

La copiaremos y volveremos a IntelliJ para colocar la anotación en los controladores.

Abriendo el paquete de controladores, accederemos a PacienteController.java y la colocaremos encima de un método para hacerlo restringido.

Si todos lo son, para evitar mucha repetición en cada uno, podemos colocar la anotación encima de la clase para que se considere que todos los métodos necesitan "bearer-key".

En el caso de PacienteController, todos los métodos deben tener autenticación.

```java
@RestController
@RequestMapping("pacientes")
@SecurityRequirement(name = "bearer-key")
public class PacienteController {

//código omitido
}

```

```java
//código omitido

@RestController
@RequestMapping("medicos")
@SecurityRequirement(name = "bearer-key")
public class MedicoController {

//código omitido
}

```

```java
//código omitido

@RestController
@RequestMapping("consultas")
@SecurityRequirement(name = "bearer-key")
public class ConsultaController {

//código omitido
}
```
No haremos esto en AutenticationController.java porque no tiene token, ya que es el controlador para iniciar sesión. Notaremos que en autenticacao-controller no hay un ícono de candado porque no hay un encabezado en el token. Ejecutaremos nuevamente la solicitud de inicio de sesión haciendo clic en "Try Out". Pasaremos` "maria@voll.med"` y la contraseña 123456 que ya tenemos registrada. Haciendo clic en el botón azul "Execute", ejecutaremos y enviaremos la solicitud. Copiaremos el valor del token que aparece en el cuerpo de la respuesta y lo pegaremos en el ícono de candado de "/pacientes" en paciente-controller. También podemos hacer clic en el botón "Authorize" en la esquina superior derecha encima de paciente-controller, antes de todas las solicitudes. Al completar y guardar el campo "value", guardaremos el token y, en la solicitud GET de "/pacientes", haremos clic en "Try Out" y no tendremos que ingresar el token nuevamente, ya que ya está guardado y se enviará automáticamente por Swagger.

```java
{
"page": 0,
"size": 20
}
```

Al hacer clic en "Execute", veremos que devuelve el código 200 con todos los pacientes registrados en la base de datos.

### Lo que aprendimos

Lo que aprendimos en esta aula:

- Agregar la biblioteca SpringDoc al proyecto para que genere automáticamente la documentación de la API;
- Analizar la documentación de SpringDoc para comprender cómo configurarlo en un proyecto;
- Acceder a las direcciones que brindan la documentación de la API en formatos yaml y html;
- Utilizar la interfaz de usuario de Swagger para visualizar y probar una API Rest;
- Configurar JWT en la documentación generada por SpringDoc.

### Proyecto del aula anterior

¿Comenzando en esta etapa? [Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior](https://github.com/alura-es-cursos/Spring-Boot-3/tree/stage-3 "Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior").

### Para saber más: pruebas con in-memory database

Como se mencionó en el video anterior, es posible realizar pruebas de interfaces de repositorio utilizando una base de datos en memoria, como H2, en lugar de utilizar la misma base de datos de la aplicación.

Si desea utilizar esta estrategia para ejecutar las pruebas con una base de datos en memoria, será necesario incluir H2 en el proyecto, agregando la siguiente dependencia en el archivo pom.xml:

```xml
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>runtime</scope>
</dependency>
También debe eliminar las anotaciones @AutoConfigureTestDatabase y @ActiveProfiles en la clase de prueba, dejándola solo con la anotación @DataJpaTest:
@DataJpaTest
class MedicoRepositoryTest {
//resto del código permanece igual
}
```

También puede eliminar el archivo application-test.properties, ya que Spring Boot realiza la configuración de la URL, el nombre de usuario y la contraseña de la base de datos H2 de manera automática.

### Haga lo que hicimos en aula

Una de las pruebas que haremos es la de las interfaces Repository.

Necesitamos escribir una prueba automatizada y, en el caso de Java, la biblioteca estándar es JUnit. La primera acción lógica a tomar es agregar JUnit al proyecto como una dependencia.

Con IntelliJ abierto, abriremos el archivo "pom.xml". Si observamos las dependencias del proyecto, tenemos las de Spring Boot, Lombok, MySQL, Flyway, pero no JUnit. La verdad es que no necesitamos agregar esta dependencia porque ya está integrada en el proyecto.

En el primer curso, cuando creamos el proyecto en el sitio de Spring Initializr, automáticamente agregó la siguiente dependencia a nuestro proyecto:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

Por lo tanto, sería interesante crear una prueba para nuestra interfaz MedicoRepository y escribir escenarios de prueba para este método seleccionarMedicoConEspecialidadEnFecha.

Haremos esto seleccionando el método deseado y utilizando el atajo "ALT + Insert". De las opciones que aparecen en la pantalla, seleccionaremos la opción "Test".

Se abrirá una nueva ventana. Allí, tendremos el nombre de la nueva clase de pruebas siguiendo el estándar de JUnit ("MedicoRepositoryTest"). IntelliJ ya insertará esta clase en el paquete "src > test > java", específico para clases de prueba automatizadas.

Justo debajo, la ventana pide que confirmemos el método que queremos probar (debajo de "Generate test methods for:"). Hacemos clic en el cuadro de diálogo junto al método seleccionarMedicoConEspecialidadEnFecha y seleccionamos el botón "OK".

Se ha creado nuestra clase de prueba. Al expandir el menú Project, verá que se creó la carpeta en "src > test > java > med.voll.api > domain medico".

Si ha explorado el proyecto del curso, habrá notado que, al crearlo con Spring Initializr, incluso crea una clase de ejemplo de pruebas automatizadas llamada "ApiApplicationTests". Podemos eliminarla, ya que no se utilizará.

Para probar una clase Repository, utilizaremos algunos recursos de Spring Boot destinados a pruebas automatizadas. Como queremos probar un Repository, necesitamos insertar la anotación @DataJpaTest en ella.

En nuestro caso, podemos solicitar que Spring no use la base de datos en memoria, sino la propia base de datos de la aplicación. Esto resultará en una prueba que sea lo más fiel posible a nuestro entorno. Usaremos esta opción. La desventaja es que la prueba será un poco más lenta, aunque más fidedigna.

Para usar este enfoque, necesitamos insertar la anotación @AutoConfigureTestDatabase(). Como parámetro, escribiremos replace = y presionaremos "CTRL + Espacio" para recibir sugerencias automáticas. Una de ellas es "Replace.NONE". Seleccionamos esto presionando "Enter" y veremos que el parámetro cambió a replace = AutoConfigureTestDatabase.Replace.NONE.

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@ActiveProfiles("test")
class MedicoRepositoryTest {

   @Test
   void seleccionarMedicoConEspecialidadEnFechaEscenario1() {
   }
}
```

Sin embargo, tendremos otro problema: nuestra base de datos ya tiene muchos registros registrados. Esto puede interferir en la prueba automatizada. Lo ideal es que usemos la misma base de datos (MySQL), pero con otra base de datos exclusiva para las pruebas.

Con esto, los datos que ya tenemos en el entorno de desarrollo no afectarán a los datos utilizados para las pruebas automatizadas.

Para realizar este cambio, accederemos a "src > main > java > resources > application.properties". Este es el archivo de propiedades de la aplicación que queremos usar en el entorno de desarrollo, pero queremos otra configuración para el entorno de pruebas.

Para ello, duplicaremos este archivo haciendo clic en "application.yml" y presionando el atajo "CTRL + C", seguido de "CTRL + V". Luego, renombraremos la copia a "application-test.properties".

Podemos tener varios archivos de propiedades y "-test" funciona como un perfil. Así, "application.yml" sigue siendo el archivo predeterminado y "application-test.yml" se utiliza solo para el entorno de pruebas.

Abriremos el archivo "-test" y eliminaremos todas las propiedades, excepto la primera, que corresponde a la URL de la base de datos. En ella, modificaremos solo el nombre de la base de datos. En lugar de ser "vollmed_api", será "vollmed_api_test".
```yml
spring:
 datasource:
   url: jdbc:mysql://localhost/vollmed_api_test?createDatabaseIfNotExist=true&serverTimezone=UTC
   username: root
   password: 2812
```

Por ahora, no está compilando porque el método seleccionarMedicoConEspecialidadEnFecha() necesita dos parámetros: la especialidad y la fecha. Como especialidad, pasamos el parámetro Especialidad.CARDIOLOGIA.

En cuanto a la fecha, no podemos tomar la fecha actual como parámetro del método, ya que cada vez que se ejecute el test, tendremos un día diferente y, por lo tanto, el comportamiento del test también será diferente.

Por eso, crearemos una variable llamada proximoLunes10H. En la línea de arriba, definiremos esta variable como var proximoLunes10H= LocalDate.now().with(TemporalAdjusters.next(DayOfWeek.MONDAY)). Con esto, el próximo lunes se calcula en función de la fecha actual. Luego, pasamos la hora utilizando .atTime(10, 0), indicando que son las 10 de la mañana.

```java
@Test
@DisplayName("deberia retornar nulo cuando el medico se encuentre en consulta con otro paciente en ese horario")
void seleccionarMedicoConEspecialidadEnFechaEscenario1() {

   var proximoLunes10H = LocalDate.now()
           .with(TemporalAdjusters.next(DayOfWeek.MONDAY))
           .atTime(10,0);

   var medicoLibre = medicoRepository.seleccionarMedicoConEspecialidadEnFecha(Especialidad.CARDIOLOGIA,proximoLunes10H);

   assertThat(medicoLibre).isNull();
}
```

Podríamos ejecutar la prueba ahora, pero ¿qué pasa con la base de datos? Anteriormente, definimos que el banco utilizado será el mismo que el de la aplicación, pero con una base de datos específica para pruebas. Flyway ejecuta las migraciones y crea todas las tablas normalmente en la base de datos de prueba, pero esta está vacía.

Esta es otra desventaja de este tipo de prueba automatizada: toda la información que debe estar registrada previamente en la base de datos debe hacerse manualmente.

Por lo tanto, antes de llamar al método seleccionarMedicoConEspecialidadEnFecha(), necesitaremos registrar un médico, un paciente y una consulta en la base de datos, ya que son la información necesaria para este escenario.

```java
// Trecho de código suprimido

var medico =
var paciente =
var consulta =

// Trecho de código suprimido
```

Sin embargo, necesitamos un método para registrar cada una de estas informaciones (médico, paciente y consulta). Podríamos inyectar el pacienteRepository y el consultaRepository en la clase, pero también es posible utilizar otro enfoque: usar el Entity Manager de la propia JPA.

```java
@Autowired
private TestEntityManager em;
```

Crearemos un nuevo atributo anotado con @Autowired y usaremos el TestEntityManager, utilizado específicamente para pruebas automatizadas. A continuación, utilizaremos el Entity Manager para guardar el médico, el paciente y la consulta.

Una buena práctica es organizar el código de prueba con métodos privados. De esta manera, no dejamos el escenario de pruebas demasiado largo. Además, esta información deberá ser utilizada en otros escenarios de prueba. Por lo tanto, los métodos privados contienen información reutilizable.

```java
private void registrarConsulta(Medico medico, Paciente paciente, LocalDateTime fecha) {
   em.persist(new Consulta(null, medico, paciente, fecha, null));
}

private Medico registrarMedico(String nombre, String email, String documento, Especialidad especialidad) {
   var medico = new Medico(datosMedico(nombre, email, documento, especialidad));
   em.persist(medico);
   return medico;
}

private Paciente registrarPaciente(String nombre, String email, String documento) {
   var paciente = new Paciente(datosPaciente(nombre, email, documento));
   em.persist(paciente);
   return paciente;
}

private DatosRegistroMedico datosMedico(String nombre, String email, String documento, Especialidad especialidad) {
   return new DatosRegistroMedico(
           nombre,
           email,
           "61999999999",
           documento,
           especialidad,
           datosDireccion()
   );
```

Por ahora, necesitamos un método para registrar cada una de estas informaciones (médico, paciente y consulta). Podríamos inyectar el pacienteRepository y el consultaRepository en la clase, pero también es posible utilizar otra aproximación: usar el Entity Manager de la propia JPA. Crearemos un nuevo atributo anotado con @Autowired y utilizaremos el TestEntityManager, utilizado específicamente para pruebas automatizadas. Después, utilizaremos el Entity Manager para guardar el médico, el paciente y la consulta.

Una buena práctica es organizar el código de prueba con métodos privados. De esta forma, no dejamos el escenario de pruebas demasiado largo. Además, esta información necesitará ser utilizada en otros escenarios de prueba. Por lo tanto, los métodos privados contienen información reutilizable.

Los métodos anteriores sirven para crear los DTOs que representan al médico, al paciente y la consulta, además de métodos para guardarlos en la base de datos usando el Entity Manager.

Volviendo al escenario de prueba, llamaremos a registrarMedico en la línea var medico=. Tendremos que pasar un nombre ("Medico"), un correo electrónico ("medico@voll.med"), un documento("123456") y la especialidad del médico (Especialidad.CARDIOLOGIA).

Haremos algo similar con el paciente: llamaremos al método registrarPaciente, con un nombre ("Paciente"), un correo electrónico (paciente@email.com) y un documento (00000000000).

Por último, llamaremos al método para registrar la consulta (registrarConsulta). Los parámetros son el médico y el paciente que acabamos de registrar. La fecha es el próximo lunes a las 10 de la mañana (proximoLunes10H).

```java
var medico=registrarMedico("Jose","j@mail.com","123456",Especialidad.CARDIOLOGIA);
var paciente= registrarPaciente("antonio","a@mail.com","654321");
registrarConsulta(medico,paciente,proximoLunes10H);
```

Con eso, hicimos las llamadas y registramos todo en la base de datos. No necesitamos guardar la consulta en una variable, así que podemos eliminar la línea var consulta = que estaba presente en el código anterior.

Observa por la Consola que se inicia el servidor y Spring. Además, la inserción del médico, paciente y consulta se realizó. A continuación, llamamos al método para hacer la consulta de un médico aleatorio disponible en ese horario y la prueba se realizó con éxito. Por lo tanto, esta consulta devuelve un número.

Un factor importante en las pruebas de Repositorio: todos los registros que insertamos se eliminarán al final de la ejecución del método para que un método de prueba no interfiera en los demás. Por lo tanto, cada método de prueba se ejecuta de manera aislada.

Este es el comportamiento estándar de Spring: al finalizar una prueba, hace el rollback automáticamente. Por lo tanto, cuando se ejecuta una nueva prueba, la base de datos se restablecerá a cero.

Una vez creado el primer escenario de prueba, es más fácil crear los demás. Ahora haremos el escenario "Debería devolver médico cuando esté disponible en la fecha". En este escenario, tenemos un médico registrado, pero está disponible para la consulta. Podemos mantener la misma fecha (proximoLunes10H).

Crearemos un médico en la base de datos. Sin embargo, no necesitamos registrar pacientes ni consultas. Podemos eliminar las dos líneas que se refieren a ellas.

Por otro lado, el assert de este escenario no debe ser nulo, en su lugar, reemplazamos el .isNull() por isEqualTo(medico), es decir, debe ser igual al médico registrado anteriormente.

El médico está registrado y se guardará en una variable. Cuando hagamos la consulta para elegir al médico disponible aleatoriamente, medicoLibre debe ser el resultado, ya que es el único disponible en la fecha.

```java
@Test
@DisplayName("deberia retornar un medico cuando realice la consulta en la base de datos  en ese horario")
void seleccionarMedicoConEspecialidadEnFechaEscenario2() {

   //given
   var proximoLunes10H = LocalDate.now()
           .with(TemporalAdjusters.next(DayOfWeek.MONDAY))
           .atTime(10,0);

   var medico=registrarMedico("Jose","j@mail.com","123456",Especialidad.CARDIOLOGIA);

   //when
   var medicoLibre = medicoRepository.seleccionarMedicoConEspecialidadEnFecha(Especialidad.CARDIOLOGIA,proximoLunes10H);

   //then
   assertThat(medicoLibre).isEqualTo(medico);
}
```

Recuerda que ya no estamos trabajando con un Repository, así que no vamos a utilizar la anotación @DataJpaTest, sino la anotación @SpringBootTest en la clase. Esta anotación se utiliza cuando queremos levantar el contexto completo de Spring para simular nuestra clase Controller.

```java
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class ConsultaControllerTest {

}
```

Ahora, el esquema es el mismo que antes: queremos probar el método agendar(), así que comenzaremos creando un método de prueba: void agendarEscenario1(). Encima del método, colocamos la anotación @Test seguida de la anotación @DisplayName para describir mejor el escenario de prueba.

Nuestra API está usando Bean Validation, así que si llega una solicitud con datos inválidos, debe devolver el código 400. Queremos que este sea nuestro escenario de prueba, así que lo definimos en la anotación @DisplayName:

Hay varios escenarios de prueba posibles; lo que incluimos en el código a continuación será el primero de ellos.

```java
// Trecho de código suprimido

@SpringBootTest
class ConsultaControllerTest {

    @Test
    @DisplayName("deberia retornar estado http 400 cuando los datos ingresados sean invalidos")
    void agendarEscenario1() {
    }
}
```

Para poder simular una solicitud HTTP en el Controller cuando no hay una solicitud real, se utiliza la clase de Spring llamada MockMvc. Para hacer esto, declaramos un atributo llamado "mvc" y le añadimos la anotación "@Autowired". La clase MockMvc simula solicitudes HTTP utilizando el patrón MVC.

Para poder inyectar MockMvc en la clase ConsultaControllerTest, además de la anotación "@SpringBootTest" en la clase, necesitamos la anotación "@AutoConfigureMockMvc".

```java
// Trecho de código suprimido

@SpringBootTest
@AutoConfigureMockMvc
class ConsultaControllerTest {

    @Autowired
    private MockMvc mvc;

    @Test
    @DisplayName("deberia retornar estado http 400 cuando los datos ingresados sean invalidos")
    void agendarEscenario1() {
    }
}
```

Ahora, vamos a disparar la solicitud: simplemente utilizamos MockMvc y declaramos el método perform(). Este método realizará una solicitud a nuestra API.

Entre los paréntesis, tendremos varios métodos estáticos, como GET, POST, PUT, DELETE. Uno de los métodos que queremos llamar desde nuestro Controlador es agendar(), un método POST (@PostMapping). Entonces, llamamos al método estático POST en perform(), abrimos y cerramos paréntesis.

A continuación, queremos el retorno, ya que necesitamos guardar lo que se devolverá. Para ello, llamamos al método .andReturn() para obtener el retorno, seguido del método .getResponse(). Lo guardaremos en una variable llamada response.

La idea es llamar a response, porque necesitamos guardarlo en algún lugar.

Sin embargo, hubo un error de compilación. Al posicionar el cursor sobre el método perform(), vemos la advertencia de que hay una excepción no tratada.

Este método puede lanzar una excepción; como no queremos hacer su tratamiento, colocamos un throws en la firma del método. Con el cursor posicionado sobre perform(), pulsamos "Alt + Enter"; se sugerirá como primera opción "Agregar excepción a la firma del método".

```java

// Trecho de código suprimido

    @Test
 @DisplayName("deberia retornar estado http 400 cuando los datos ingresados sean invalidos")

    void agendarEscenario1() throws Exception
{
       var response = mvc.perform(post(urlTemplate:"/consultas"))
            .andReturn().getResponse();
    }
}
```

Ahora haremos la afirmación: vamos a verificar si el código de estado es 400, porque dispararemos la solicitud pero no llevamos ningún cuerpo en ella.

La solicitud no tiene ningún parámetro, pero recuerde que, en la solicitud de programar una cita, el médico es opcional, mientras que el paciente y la fecha son obligatorios.

Como no estamos proporcionando ni la ID ni la fecha, debería devolver "error 400". Primero haremos las aserciones para eso.

Después de disparar la solicitud, declararemos el método assertThat() e importaremos el estático de la misma manera que hicimos anteriormente, con el atajo "Alt + Enter". En este caso, seleccionamos la penúltima opción Assertions.assertThat, que proviene del paquete org.assertj.core.api.

Entre paréntesis, colocaremos response.getStatus() seguido de isEqualTo(), donde pasaremos 400. Para hacer esto, usaremos la clase de utilidad de Spring HttpStatus, del paquete org.springframework.http. Después de eso, agregamos la constante BAD_REQUEST seguida del método value().

```java
{
       var response = mvc.perform(post(urlTemplate:"/consultas"))
            .andReturn().getResponse();
//then
assertThat(response.getStatus()).isEqualTo(HttpStatus.BAD_REQUEST.value());
    }
```

De esta forma, será devuelto el código 400.

El bloque de código anterior es el método utilizado para probar el código 400: primero, se dispara una solicitud a la dirección "/consultas" mediante el método POST, sin llevar ningún cuerpo; a continuación, guardamos el resultado (.andReturn()) y el getResponse() en una variable y verificamos si el estado de la respuesta es 400, ya que en este escenario, este es el error que debe ocurrir.

Una vez finalizada nuestra prueba, podemos ejecutarla haciendo clic con el botón derecho en la clase ConsultaControllerTest y seleccionando la opción "Ejecutar". Observe que Spring levanta el servidor, pero no se dispara una solicitud real, sino una simulada.

En este caso, mi prueba falló: tenemos la información de que se esperaba el error 400, pero se devolvió el error 403.

```java
org.opentest4j.AssertionFailedError:
expected: 400
 but was: 403
Expected :400
Actual   :403
```

Esto sucede debido a Spring Security. Para hacer una reserva a través de la URL, necesitamos estar conectados y no lo hemos hecho en esta prueba.

Se puede argumentar lo siguiente: en esta prueba, no estamos probando seguridad, autenticación y autorización. Dicho esto, consideraremos que se ha iniciado sesión para probar la llamada a la API independientemente de si estamos o no conectados.

¿Cómo informar a Spring que se debe ignorar la autenticación?

Encima del método, además de las anotaciones @Test y @DisplayName, pondremos una tercera anotación de Spring: @WithMockUser.

```java
 @Test
 @DisplayName("deberia retornar estado http 400 cuando los datos ingresados sean invalidos")
@WithMockUser

    void agendarEscenario1() throws Exception
{
       var response = mvc.perform(post("/consultas"))
            .andReturn().getResponse();
//then
assertThat(response.getStatus()).isEqualTo(HttpStatus.BAD_REQUEST.value());
    }
}
```

Esta anotación sirve para indicarle a Spring que debe hacer una simulación de un usuario, considerando que estamos logueados al ejecutar el test, para hacer una llamada con autenticación. Si queremos llamar un método con autenticación, pero sin pasar el usuario, y hacer una prueba de seguridad, usamos esta anotación para que Spring simule el inicio de sesión. Después de hacer esto, guardamos y volvemos a ejecutar el test, y se devolverá el error 400.

---

En ConsultaControllerTest.java, donde ya tenemos el primer escenario @Test, simplemente tendremos que copiar y pegar para realizar las modificaciones de los siguientes escenarios.

Primero, renombraremos el método de programación para agendarEscenario2() y el contenido de @DisplayName() a "Debería devolver el código http 200 cuando la información es válida".

El assetThat() con response.getStatus() ya no será BAD_REQUEST, sino OK para devolver el código 200. Sin embargo, en este método necesitaremos enviar un JSON válido.

Para esto, el .perform() recibirá post() en el que pasaremos .content() para enviar el JSON. Podríamos escribirlo manualmente como una cadena, pero esto no es recomendable porque es muy trabajoso, o podríamos usar una clase de Spring para hacer la creación del JSON más fácil.

Antes de eso, tendremos que llamar a .contentType() para enviar la cabecera HTTP que indica que se están enviando datos en formato JSON. Esto se hacía automáticamente en Postman, pero aquí tendremos que hacerlo manualmente. Dentro de los parámetros, pasaremos como parámetro la clase MediaType de Spring que proviene del paquete org.springframework.http, seguida de .APPLICATION_JSON.

Inyectamos otra clase de utilidad además de MockMvc llamada private JacksonTester<> que tiene genéricos con el tipo de objeto con el que trabajará. En nuestro caso, deberá ser del mismo tipo DatosAgendarConsulta recibido en el método agendar() del ConsultaController.

Copiaremos esta clase y la añadiremos dentro de los genéricos de JacksonTester<>. Luego, llamaremos al atributo DatosAgendarConsulta.

Duplicaremos este atributo porque necesitaremos dos JSONs, el que llega a la API y el que se devuelve por ella. En este último, lo que llegará será el genérico DatosDetalleConsulta y el atributo será DatosDetalleConsulta.

```java
//trecho de código suprimido

class ConsultaControllerTest {

 @Autowired
private MockMvc mvc;

@Autowired
private JacksonTester<DatosAgendarConsulta> agendarConsultaJacksonTester;

@Autowired
private JacksonTester<DatosDetalleConsulta> detalleConsultaJacksonTester;

//trecho de código suprimido

}
```

En el archivo ConsultaController.java, podemos observar que el método agendar() recibe DatosAgendarConsulta pero devuelve otro dato, que es DatosDetalleConsulta. Por lo tanto, en nuestro test, debemos tener dos JacksonTester<> para simular el JSON de entrada y salida que se devuelve.

Volviendo a nuestro test, dentro de .content(), pasaremos DatosAgendarConsulta con .write() recibiendo el objeto new DatosAgendarConsulta, donde pasaremos la identificación de un médico y un paciente, como 2l y 5l por ejemplo. También necesitaremos pasar una fecha y la especialidad. Lo guardaremos en variables antes de response, donde var fecha será igual a LocalDateTime.now(). La fecha puede ser actual porque estamos llamando al controller directamente, por lo que no haremos validación, simplemente cambiaremos la hora con .plusHours(1) para agregar una hora más, dejando claro que es una fecha en el futuro. En una nueva línea siguiente, crearemos la var especialidad que será igual a Especialidad.CARDIOLOGIA, por ejemplo.

Al instanciar DatosAgendarConsulta, pasaremos los ids del médico, del paciente, la fecha y la especialidad. Sin embargo, no podemos pasar .write() directamente a .content() porque devuelve otro tipo de objeto. Por lo tanto, justo después de su paréntesis de cierre, llamaremos a .getJson() para convertirlo en una cadena JSON.

El objeto JacksonTester<> toma lo que pasamos como parámetro y lo convierte en JSON en formato de cadena sin necesidad de crearlo manualmente.

```java
@Test
@DisplayName("deberia retornar estado http 200 cuando los datos ingresados son validos")
@WithMockUser
void agendarEscenario2() throws Exception {
   //given
   var fecha = LocalDateTime.now().plusHours(1);
   var especialidad = Especialidad.CARDIOLOGIA;
   var datos = new DatosDetalleConsulta(null,2l,5l,fecha);

   // when

   when(agendaDeConsultaService.agendar(any())).thenReturn(datos);

   var response = mvc.perform(post("/consultas")
           .contentType(MediaType.APPLICATION_JSON)
           .content(agendarConsultaJacksonTester.write(new DatosAgendarConsulta(2l,5l,fecha, especialidad)).getJson())
   ).andReturn().getResponse();

   //then
   assertThat(response.getStatus()).isEqualTo(HttpStatus.OK.value());

}
```

Ya hemos hecho la solicitud, llevando la cabecera contentType() y el JSON creado por JacksonTester<>.

En las afirmaciones assertThat(), solo estamos verificando si el código HTTP está siendo devuelto como 200. Pero además de eso, deberemos verificar si el JSON que se está devolviendo es el que esperamos.

Crearemos una variable jsonEsperado que será igual a DatosDetalleConsulta.Json con .write() recibiendo como parámetro new DatosDetalleConsulta().

Esta clase recibirá un objeto del tipo Consulta o las identificaciones de la consulta, del médico, del paciente y de la fecha.

El ID de la consulta, como no llamaremos a la base de datos, no recibiremos un identificador correcto, por lo que en principio lo pasaremos como nulo.

La identificación del médico deberá ser 2l, del paciente 5l, seguida de la misma fecha. Después de crear el objeto, aplicaremos .getJson() para devolver un JSON.

En la línea siguiente, llamaremos al assertThat() que contiene response.getContentAsString() para tener el contenido como cadena. Luego, aplicaremos isEqualTo(jsonEsperado).

```java
//then
assertThat(response.getStatus()).isEqualTo(HttpStatus.OK.value());

var jsonEsperado = detalleConsultaJacksonTester.write(datos).getJson();

assertThat(response.getContentAsString()).isEqualTo(jsonEsperado);
```

Ejecutaremos esta prueba y esperaremos a que termine la ejecución. Tendremos un error indicando que JacksonTester<> no fue encontrado, ya que para poder inyectarlo, es necesario tener otra anotación encima de la clase llamada @AutoConfigureJsonTesters.


```java
@SpringBootTest
@AutoConfigureMockMvc
@AutoConfigureJsonTesters
class ConsultaControllerTest {

//trecho de código suprimido

}
```

Al ejecutar nuevamente, no habrá errores, pero la prueba fallará. Se realizó la selección de la base de datos y se disparó la consulta, pero eso no es lo que queríamos, ya que queríamos acceder solo al controlador de manera aislada, y por eso falló.

Falta indicarle a Spring que no use la base de datos real. En ConsultaController.java, veremos que se está inyectando la clase AgendaDeConsultaService que está accediendo a la base de datos.

Para solicitar un mock de este objeto, volvemos a la clase de prueba y declaramos otro atributo private AgendaDeConsultaService agendaDeConsultaService y, encima de él, colocamos la anotación @MockBean.

Con esto, le decimos a Spring que cuando necesite inyectar este objeto en el controlador, debe aplicar el mock y no inyectar una agendaDeConsultaService real.

```java
//trecho de código suprimido

class ConsultaControllerTest {

 @Autowired
private MockMvc mvc;

@Autowired
private JacksonTester<DatosAgendarConsulta> agendarConsultaJacksonTester;

@Autowired
private JacksonTester<DatosDetalleConsulta> detalleConsultaJacksonTester;

@MockBean
private AgendaDeConsultaService agendaDeConsultaService;

//trecho de código suprimido

}
```

Guardaremos y volveremos a ejecutar la prueba. Veremos que no se ejecutó la consulta ni se accedió a la base de datos, pero la prueba falló de nuevo. El retorno dice que el JSON devuelto debería ser la identificación nula, la del médico 2 y la del paciente 5, además de la fecha actual, pero lo que llegó fue un JSON vacío "". Entonces, por alguna razón, no devolvió nada, es decir, recibió el código 200 pero el cuerpo llegó vacío.

Esto sucedió porque, en nuestro controlador, llamamos a agendaDeConsultaService.agendar(), pero nuestra agenda es un mock, que por defecto devuelve nulo. Entonces, nuestro dto está nulo, y al momento de hacer la conversión, este dto devuelto por Spring es null.

Es un mock pero necesitaremos simular diciendo que, cuando el método agendar() sea llamado, devolveremos la información proporcionada. Para hacer esta configuración, volveremos al archivo de prueba y, antes de disparar la solicitud de verdad, usaremos Mockito, que es la biblioteca de mocks que Spring usa .

Después de var especialidad y antes de var response, llamaremos al método when() y haremos la importación automática de la biblioteca. Entonces, cuando se llame a agendaDeConsultaService.agendar(), tendremos el thenReturn() para devolver el nuevo objeto DatosDetalleConsulta esperado.

Más adelante, en jsonEsperado, recortaremos la línea de new DatosDetalleConsulta() y escribiremos datos, que se pasará en el thenReturn(). A continuación, lo lanzaremos a una nueva variable llamada datosDetalleConsulta, que es igual a new DatosDetalleConsulta() con null, 2l, 5l, fecha.

En when(), el contenido de .thenReturn() será solo datos. Por lo tanto, cuando la agenda que es el mock tenga el método .agendar() llamado, no importará el parámetro para devolver los datos.

Con esto, dispararemos la solicitud y, finalmente, verificaremos si el JSON devuelto es igual al objeto esperado.

```java
@Test
@DisplayName("deberia retornar estado http 200 cuando los datos ingresados son validos")
@WithMockUser
void agendarEscenario2() throws Exception {
   //given
   var fecha = LocalDateTime.now().plusHours(1);
   var especialidad = Especialidad.CARDIOLOGIA;
   var datos = new DatosDetalleConsulta(null,2l,5l,fecha);

   // when

   when(agendaDeConsultaService.agendar(any())).thenReturn(datos);

   var response = mvc.perform(post("/consultas")
           .contentType(MediaType.APPLICATION_JSON)
           .content(agendarConsultaJacksonTester.write(new DatosAgendarConsulta(2l,5l,fecha, especialidad)).getJson())
   ).andReturn().getResponse();

   //then
   assertThat(response.getStatus()).isEqualTo(HttpStatus.OK.value());

   var jsonEsperado = detalleConsultaJacksonTester.write(datos).getJson();

   assertThat(response.getContentAsString()).isEqualTo(jsonEsperado);

}
```

### Lo que aprendimos

Lo que aprendimos en esta aula:

- Escribir pruebas automatizadas en una aplicación con Spring Boot;
- Escribir pruebas automatizadas para una interfaz de Repositorio, siguiendo la estrategia de utilizar la misma base de datos que utiliza la aplicación;
- Sobreescribir las propiedades del archivo application.properties, creando otro archivo llamado application-test.properties que se carga solo cuando se ejecutan pruebas, usando la anotación @ActiveProfiles para eso;
- Escribir pruebas automatizadas para una clase de controlador, utilizando la clase MockMvc para simular solicitudes de API;
- Probar los escenarios de error 400 y código 200 al probar una clase de controlador.

### Proyecto del aula anterior

¿Comenzando en esta etapa? [Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior](https://github.com/alura-es-cursos/Spring-Boot-3/tree/stage-4 "Aquí puedes descargar los archivos del proyecto que hemos avanzado hasta el aula anterior").

### Para saber más: build con arquivo .war

Proyectos que utilizan Spring Boot generalmente usan el formato jar para empaquetar la aplicación, como se demostró durante esta lección. Sin embargo, Spring Boot brinda soporte para empaquetar la aplicación en formato war, que era ampliamente utilizado en aplicaciones Java antiguas.

Si desea que el build del proyecto empaquete la aplicación en un archivo en formato war, deberá realizar los siguientes cambios:

1. Agregue la etiqueta `<packaging>war</packaging>` al archivo pom.xml del proyecto, y esta etiqueta debe ser hija de la etiqueta raíz `<project>`:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>3.0.0</version>
<relativePath/> <!-- lookup parent from repository -->
</parent>
<groupId>med.voll</groupId>
<artifactId>api</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>api</name>
<packaging>war</packaging>
```

2. En el archivo pom.xml, agregue la siguiente dependencia:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-tomcat</artifactId>
  <scope>provided</scope>
</dependency>
```

3. Cambie la clase principal del proyecto (ApiApplication) para heredar de la clase SpringBootServletInitializer, y sobrescriba el método configure:

```java
@SpringBootApplication
public class ApiApplication extends SpringBootServletInitializer {
@Override
protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
return application.sources(ApiApplication.class);
}
public static void main(String[] args) {
SpringApplication.run(ApiApplication.class, args);
}
}
```

¡Listo! Ahora, al realizar el build del proyecto, se generará un archivo con la extensión .war en el directorio target, en lugar del archivo con la extensión .jar.

### Para saber más: GraalVM Native Image

Una de las características más destacadas de la versión 3 de Spring Boot es el soporte para imágenes nativas, algo que reduce significativamente el consumo de memoria y el tiempo de inicio de una aplicación. Algunos otros frameworks competidores de Spring Boot, como Micronaut y Quarkus, ya ofrecían soporte para esta función.

De hecho, era posible generar imágenes nativas en aplicaciones con Spring Boot antes de la versión 3, pero esto requería el uso de un proyecto llamado Spring Native que agregaba soporte para ello. Con la llegada de la versión 3 de Spring Boot, ya no es necesario utilizar este proyecto.

### Native Image

Una imagen nativa es una tecnología utilizada para compilar una aplicación Java, incluyendo todas sus dependencias, generando un archivo binario ejecutable que puede ser ejecutado directamente en el sistema operativo sin necesidad de utilizar la JVM. Aunque no se ejecute en una JVM, la aplicación también contará con sus recursos, como la gestión de memoria, el recolector de basura y el control de la ejecución de hilos.

Para obtener más detalles sobre la tecnología de imágenes nativas, consulte la documentación en el sitio web: [https://www.graalvm.org/native-image](https://www.graalvm.org/native-image "https://www.graalvm.org/native-image")

### Native Imagem com Spring Boot 3
Una forma muy sencilla de generar una imagen nativa de la aplicación es mediante un plugin de Maven, que debe incluirse en el archivo pom.xml:

```xml
<plugin>
  <groupId>org.graalvm.buildtools</groupId>
  <artifactId>native-maven-plugin</artifactId>
</plugin>
```

¡Listo! Esta es la única modificación necesaria en el proyecto. Después de esto, la generación de la imagen debe hacerse a través de la terminal, con el siguiente comando de Maven que se ejecuta en el directorio raíz del proyecto:

```cmd
./mvnw -Pnative native:compile
```

Este comando puede tardar varios minutos en completarse, lo cual es completamente normal.

¡Atención! Para ejecutar el comando anterior y generar la imagen nativa del proyecto, es necesario que tenga instalado en su computadora [GraalVM](https://www.graalvm.org/22.0/docs/getting-started/ "GraalVM") (una máquina virtual Java con soporte para la función Native Image) en una versión igual o superior a 22.3.

Después de que el comando anterior termine, se generará en la terminal un registro como el siguiente:

```cmd
Top 10 packages in code area:           Top 10 object types in image heap:
   3,32MB jdk.proxy4                      19,44MB byte[] for embedded resources
   1,70MB sun.security.ssl                16,01MB byte[] for code metadata
   1,18MB java.util                        8,91MB java.lang.Class
 936,28KB java.lang.invoke                 6,74MB java.lang.String
 794,65KB com.mysql.cj.jdbc                6,51MB byte[] for java.lang.String
 724,02KB com.sun.crypto.provider          4,89MB byte[] for general heap data
 650,46KB org.hibernate.dialect            3,07MB c.o.s.c.h.DynamicHubCompanion
 566,00KB org.hibernate.dialect.function   2,40MB byte[] for reflection metadata
 563,59KB com.oracle.svm.core.code         1,30MB java.lang.String[]
 544,48KB org.apache.catalina.core         1,25MB c.o.s.c.h.DynamicHu~onMetadata
  61,46MB for 1482 more packages           9,74MB for 6281 more object types
--------------------------------------------------------------------------------
    9,7s (5,7% of total time) in 77 GCs | Peak RSS: 8,03GB | CPU load: 7,27
--------------------------------------------------------------------------------
Produced artifacts:
 /home/rodrigo/Desktop/api/target/api (executable)
 /home/rodrigo/Desktop/api/target/api.build_artifacts.txt (txt)
================================================================================
Finished generating 'api' in 2m 50s.
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  03:03 min
[INFO] Finished at: 2023-01-17T12:13:04-03:00
[INFO] ------------------------------------------------------------------------
```

La imagen nativa se genera en el directorio target, junto con el archivo .jar de la aplicación, como un archivo ejecutable de nombre api, como se muestra en la siguiente imagen:

![target](https://caelum-online-public.s3.amazonaws.com/1996+-spring-boot-3/3.png "target")

texto alternativo: Lista de archivos y directorios ubicados dentro del directorio target del proyecto, incluido el archivo de la imagen nativa, cuyo nombre es api.

La diferencia del archivo .jar, que se ejecuta en la JVM mediante el comando java -jar, la imagen nativa es un archivo binario y debe ejecutarse directamente desde la terminal:

```cmd
target/api
```

Al ejecutar el comando anterior se generará el registro de inicio de la aplicación, que al final muestra el tiempo que tardó la aplicación en iniciarse:

```cmd
INFO 127815 --- [restartedMain] med.voll.api.ApiApplication : Started ApiApplication in 0.3 seconds (process running for 0.304)
```

Observe que la aplicación tardó menos de medio segundo en iniciarse, algo realmente impresionante, ya que cuando se ejecuta en la JVM, a través del archivo .jar, este tiempo aumenta a alrededor de 5 segundos.

Para obtener más detalles sobre la generación de una imagen nativa con Spring Boot 3, consulte la documentación en el sitio: [Soporte de imagen nativa GraalVM](https://www.graalvm.org/22.0/reference-manual/native-image/ "Soporte de imagen nativa GraalVM").

### Haga lo que hicimos en aula

Ya hemos implementado pruebas automatizadas y consideramos que hemos terminado el proyecto. Cuando terminamos un proyecto completo o una parte de él, queremos ponerlo en funcionamiento. Surge la siguiente pregunta: ¿cómo se puede disponibilizar un proyecto?

Build del proyecto

Hasta ahora, solo ejecutamos el proyecto localmente, en nuestra propia computadora donde corremos la aplicación, la base de datos y las pruebas. Sin embargo, para poner a disposición este proyecto, debemos alojarlo en algún servidor, ya sea interno de la empresa o algún servicio en la nube como Amazon, Google Cloud y Azure.

La idea es generar la compilación de nuestro proyecto y disponibilizar para su ejecución en algún servidor. Además, tendremos que hacer algunos ajustes en nuestra aplicación antes de alojarla.

El ajuste principal que se debe hacer está relacionado con la base de datos. Las configuraciones de la base de datos de nuestra aplicación se encuentran en el archivo application.yml; vamos a abrirlo en "src > main > resources".

```yml
spring:
 profile.active: dev, test, prod
 datasource:
   url: jdbc:mysql://localhost/vollmed_api
   username: root
   password: 2812
 jpa:
   show-sql: true
   properties:
     hibernate:
       format_sql: true

server:
 error:
   include-stacktrace: never
api:
 security:
   secret: ${JWT_SECRET:123456}
```

En esta carpeta, además de application.properties, tenemos application-test.yml, configurado solo para sobrescribir la base de datos de la aplicación durante la ejecución de las pruebas.

```yml
spring:
 datasource:
   url: jdbc:mysql://localhost/vollmed_api_test?createDatabaseIfNotExist=true&serverTimezone=UTC
   username: root
   password: 2812
```

En application.yml, tenemos las configuraciones de la base de datos que apuntan a "localhost". Con esto, le decimos a Spring que la base de datos se está ejecutando en la propia máquina y la base de datos se llama "vollmed_api"; el usuario de la base de datos es "root"; y la contraseña de la base de datos es "root".

Estas son las configuraciones fijas de la base de datos, pero solo funcionan localmente. Es posible que en el servidor donde vamos a disponibilizar el proyecto, la base de datos se encuentre en otra computadora, es decir, puede haber dos servidores: uno donde se alojará la aplicación; y otro donde se alojará la base de datos.

Por lo tanto, la dirección no será "localhost". Queremos flexibilizar estas configuraciones, tanto de la dirección de la base de datos como del usuario y la contraseña, para que podamos sobrescribir los parámetros en el entorno de producción.

Una forma de hacerlo es aplicando el concepto de ambientes, de perfiles, tal como lo hicimos para las pruebas. La prueba está en otro archivo (application-test.yml). Para el entorno de producción, podemos crear un tercer archivo separado.

Archivo application-prod.yml

Para crear este archivo, seleccionamos application.yml en el panel "Project" a la izquierda y presionamos "Ctrl + C" para copiar. Se abrirá una ventana para que renombramos el archivo; lo llamaremos application-prod.yml. Después de eso, presionamos "Enter" para finalizar la acción. "Prod" es una abreviatura de "production" (entorno de producción).

La carga de estos archivos funciona de la siguiente manera: Spring siempre carga todas las propiedades de application.yml, y si le indicamos que estamos en el entorno de producción, también carga las propiedades de application-prod.yml.

En este archivo, solo sobrescribiremos las propiedades deseadas. Las que queramos reutilizar no necesitan duplicarse: las dejamos en el propio archivo application.yml.

En este caso, queremos sobrescribir la URL de la base de datos, el usuario y la contraseña, además de las dos siguientes propiedades: show-sql y format_sql. En un entorno de producción, no queremos generar registro de SQL, por lo que definiremos esas propiedades como "false".

La propiedad stacktrace permanecerá como "never" y el secreto también no sufrirá alteraciones, por lo que podemos eliminar esas dos líneas de código; se leerán de application.yml.

```yml
spring:
 profile.active: prod
 datasource:
   url: jdbc:mysql://localhost/vollmed_api
   username: root
   password: root
 jpa:
   show-sql: false
   properties:
     hibernate:
       format_sql: false

server:
 error:
   include-stacktrace: never
api:
 security:
   secret: ${JWT_SECRET:123456}
```

Una vez hecho esto, ¿cómo pasar el nombre de usuario, la contraseña y la URL de la base de datos? Todavía no sabemos cuáles serán las propiedades y no es interesante dejarlas fijas en el código.

De hecho, esta es una buena práctica de seguridad: no debemos exponer contraseñas y otros aspectos de seguridad en el código fuente de la aplicación.

Para resolver este problema, solemos usar variables de entorno. En este caso, la idea es la siguiente: en la propiedad spring.datasource.url, escribimos $, abrimos llaves y escribimos el nombre de la variable de entorno (DATASOURCE_URL), similar a lo que hicimos con el secreto del token JSON Web. Haremos lo mismo en las otras dos propiedades, pero en la primera tenemos la variable DATASOURCE_USERNAME y en la segunda, DATASOURCE_PASSWORD.

```yml
spring:
 profile.active: prod
 datasource:
   url: ${DATASOURCE_URL}
   username: ${DATASOURCE_USERNAME}
   password: ${DATASOURCE_PASSWORD}
 jpa:
   show-sql: false
   properties:
     hibernate:
       format_sql: false

server:
 error:
   include-stacktrace: never
api:
 security:
   secret: ${JWT_SECRET:123456}
```

Con eso, Spring sabe que estas tres propiedades hacen referencia a variables de entorno. La idea es que busque esas variables en un sistema operativo o en un parámetro cuando ejecutemos la aplicación (aprenderemos cómo hacerlo más adelante) y reemplace en esas propiedades el valor asignado a ellas.

De esta manera, podemos tener otro archivo properties exclusivo para el ambiente de producción y sobrescribir solo lo que deseamos. El resto seguirá siendo extraído de application.yml.

De esta forma, configuramos nuestra base de datos en el ambiente de producción a través de variables de entorno. ¡Esta es una buena práctica!

Ahora, surge la siguiente pregunta:

Una vez configurado y finalizado el proyecto, ¿cómo generamos el ejecutable de esta aplicación y lo enviamos para que se ejecute en el servidor, ya sea en un ambiente en la nube o interno de la empresa?

---

Cómo ejecutar el proyecto?

Para hacerlo, basta con utilizar el comando java. Lo único que necesitamos tener instalado en el servidor donde vamos a ejecutar la aplicación es Java en la versión 17, utilizada en este curso.

Con Java instalado, digitamos java -version para mostrar esta información en la terminal:

```cmd
PS C:\Users\public\alura\springboot> java -version
java version "17.0.6" 2023-01-17 LTS
Java(TM) SE Runtime Environment (build 17.0.6+9-LTS-190)                       
Java HotSpot(TM) 64-Bit Server VM (build 17.0.6+9-LTS-190, mixed mode, sharing)
PS C:\Users\public\alura\springboot> 
```

El comando para ejecutar el proyecto esta compuesto por el comando java, por el parametro -jar, y por el camino al archivo:

```cmd
java -jar target/api-0.0.1-SNAPSHOT.jar
```

```cmd
PS C:\Users\public\alura\springboot> java -version
java version "17.0.6" 2023-01-17 LTS
Java(TM) SE Runtime Environment (build 17.0.6+9-LTS-190)                       
Java HotSpot(TM) 64-Bit Server VM (build 17.0.6+9-LTS-190, mixed mode, sharing)
PS C:\Users\public\alura\springboot> java -jar .\target\api-0.0.1-SNAPSHOT.jar 
```

A continuación, presionamos "Enter" para ejecutar. En principio, la aplicación se inicia, pero vamos a observar el comienzo del registro, debajo de la pantalla de inicio de Spring. En las primeras líneas, aparece el mensaje de que la aplicación se está iniciando ("Starting ApiApplication"), y en la segunda línea, se muestran información sobre el perfil.

No hay ningún perfil activo, por lo que se utilizará el perfil "default", es decir, Spring solo leerá el archivo application.yml.

Sin embargo, queremos que se lea el archivo application-prod.yml. ¿Cómo podemos hacer para que, al ejecutar el proyecto desde la línea de comandos, se considere "prod" como perfil activo?

Detenemos la aplicación y presionamos la flecha hacia arriba para listar el último comando. Ahora, antes del parámetro -jar, agregamos otro parámetro: --spring.profiles.active=prod.

```cmd
PS C:\Users\public\alura\springboot> java -jar .\target\api-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod
```

Así, pasamos el perfil activo al momento de ejecutar el proyecto: solo necesitamos pasar el parámetro -- y el nombre del parámetro que Spring espera (spring.profiles.active=prod). Después del nombre del perfil, que en mi caso es "prod", viene el parámetro -jar seguido de la ruta del archivo .jar.

Luego, presionamos "Enter" y ejecutamos de nuevo. Sin embargo, se producirá un error. Si vamos al inicio del registro, veremos los mensajes de que la aplicación se está iniciando y que el perfil "prod" está activo (The following 1 profile is active: "prod").

Sin embargo, recuerde que en el archivo application-prod.yml dijimos que la URL, el nombre de usuario y la contraseña de la base de datos deben recuperarse de las variables de entorno. No se encontraron en el sistema operativo, por lo que se produjo una Exception.

---

En el log, aparecerá una mensaje indicando que no se pudo cargar la parte del repositorio y persistencia, porque la URL no se identificó y debería iniciarse con "jdbc", pero por ahora se está utilizando ${}.

```cmd
Caused by: java.lang.IllegalArgumentException: URL must start with 'jdbc'
```

ntonces, ¿cómo pasar las variables de entorno? ¡De la misma manera que pasamos el parámetro de perfil! Con el proyecto detenido, presionamos la flecha hacia arriba y escribimos el siguiente comando:

```cmd
java -DDATASOURCE_URL=jdbc:mysql://localhost/vollmed_api -DDATASOURCE_USERNAME=root -DDATASOURCE_PASSWORD=root -jar target/api-0.0.1-SNAPSHOT.jar  - -spring.profiles.active=prod 
```

Antes del parámetro -jar, debemos insertar nuevamente el parámetro -D, reemplazando las tres variables de entorno. En cada parámetro, se reemplaza una variable, así que primero tenemos -DDATASOURCE_URL=, seguido de la dirección del servidor de la base de datos, que puede ser la propia máquina o la IP de otra máquina.

### Proyecto final

[Aquí puedes descargar los archivos del proyecto completo](https://github.com/alura-es-cursos/Spring-Boot-3/tree/stage-5 "Aquí puedes descargar los archivos del proyecto completo").

### Lo que aprendimos

Lo que aprendimos en esta aula:

- Realizar el build de una aplicación con Spring Boot;
- Utilizar archivos de propiedades específicos para cada perfil, cambiando las propiedades que deben modificarse en cada archivo;
- Configurar información confidencial de la aplicación, como datos de acceso a la base de datos, a través de variables de entorno;
- Crear el proyecto a través de Maven;
- Ejecutar la aplicación a través de la terminal, con el comando java -jar, pasando las variables de entorno como parámetros.
