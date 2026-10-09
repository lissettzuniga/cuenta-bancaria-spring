# Cajero automático — Spring Core e inyección de dependencias

**Autor:** Lissett Zuñiga Reyes

## Cómo correrlo

```bash
./correr.sh App final-app
./probar.sh final
```

## Las piezas

| Bean | Clase | Cómo lo declara Spring (`@Component` o `@Bean`) | Singleton o prototype |
|---|---|---|---|
| cajeroAutomatico | CajeroAutomatico | `@Component` | Singleton |
| configuracionBanco | ConfiguracionBanco | `@Configuration` | Singleton |
| antifraudeEstricto | AntifraudeEstricto | `@Component` | Singleton |
| antifraudePorMonto | AntifraudePorMonto | `@Component` | Singleton |
| notificadorConsola | NotificadorConsola | `@Component` | Singleton |
| repositorioEnMemoria | RepositorioEnMemoria | `@Component` | Singleton |
| sesionCajero | SesionCajero | `@Component` | Prototype |
| reloj | Clock | `@Bean` (en `ConfiguracionBanco`) | Singleton |

## Boleto de salida

**1. ¿Qué es la inyección de dependencias? Explícalo con el cajero, en tus palabras.**
> La inyección de dependencias es un patrón donde una clase no instancia sus dependencias internamente usando `new`, sino que las recibe externamente desde una entidad administradora (el contenedor IoC de Spring). En el cajero automático, la clase `CajeroAutomatico` necesita un repositorio, un notificador, un servicio antifraude y un reloj para operar. En lugar de crear un `new RepositorioEnMemoria()`, Spring se encarga de crear e instanciar estas dependencias e inyectarlas a través del constructor de `CajeroAutomatico`.

**2. En la MP-1, ¿quién decidía qué antifraude usaba el cajero? ¿Y desde la MP-2?**
> En la MP-1, la clase creadora/llavín manual (`AppSinSpring`) decidía explícitamente instanciar un tipo de antifraude específico (`new AntifraudePorMonto()`) y pasárselo al constructor del cajero. A partir de la MP-2, es el contenedor de Spring quien escanea y decide qué beans inyectar basándose en las anotaciones (`@Component`, `@Primary`, `@Qualifier`) y la configuración (`@ComponentScan`).

**3. ¿Cuándo usarías `@Bean` en vez de `@Component`? Da el ejemplo de hoy.**
> Usaría `@Bean` cuando necesito registrar y configurar un objeto de una clase de una librería externa o del framework (cuyo código fuente no puedo modificar para agregarle la anotación `@Component`). El ejemplo de hoy fue la creación del bean de tipo `java.time.Clock` (`Clock.systemDefaultZone()`) dentro de la clase `@Configuration` `ConfiguracionBanco`.

**4. ¿Qué gana: `@Primary` o `@Qualifier`? ¿Por qué tiene sentido?**
> Gana `@Qualifier`. Tiene sentido porque `@Primary` establece cuál es la implementación preferida por defecto en caso de ambigüedad general, mientras que `@Qualifier` es una instrucción explícita y específica en el punto de inyección para indicar exactamente el nombre del bean deseado. Lo específico siempre sobreescribe lo predeterminado.

**5. En tu proyecto de Empleados de la Semana 3 nunca escribiste `@ComponentScan`. ¿Quién lo hace?**
> Lo hace la anotación `@SpringBootApplication`. Al inspeccionar sus metadatos, se observa que está meta-anotada con `@ComponentScan`, `@EnableAutoConfiguration` y `@SpringBootConfiguration`, por lo que escanea automáticamente el paquete donde se ubica la clase principal y todos sus subpaquetes.