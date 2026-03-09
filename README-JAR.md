# Ejecutar como JAR

## Compilar el proyecto
```bash
mvn clean package
```

## Ejecutar el JAR
```bash
java -jar target/mis-0.0.1-SNAPSHOT.jar
```

## Comandos alternativos
```bash
# Ejecutar directamente con Maven
mvn spring-boot:run

# Compilar sin ejecutar tests
mvn clean package -DskipTests
```