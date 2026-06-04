# Stage 1: Build (Cambiamos a una imagen que incluye Maven y Java 17 nativo)
FROM maven:3.8.5-openjdk-17 AS builder

WORKDIR /app

# Copiamos el archivo de configuración de dependencias
COPY pom.xml .

# Descargamos las dependencias usando el Maven global de la imagen (evita fallos de mvnw)
RUN mvn dependency:go-offline -B

# Copiamos el código fuente
COPY src ./src

# Compilamos y empaquetamos el archivo .jar
RUN mvn clean package -DskipTests -B

# Stage 2: Runtime (Mantenemos tu configuración ligera con Alpine)
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

# Crear usuario seguro para ejecutar Spring Boot
RUN addgroup -S spring && adduser -S spring -G spring

# Copiamos el .jar generado en la etapa anterior
COPY --from=builder /app/target/*.jar app.jar

RUN chown spring:spring app.jar

USER spring

# El puerto 5000 que definiste
EXPOSE 5000

ENTRYPOINT ["java", "-jar", "app.jar"]
