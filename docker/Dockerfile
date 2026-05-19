# ============================================================
#  CLYVO VET — Dockerfile (API Java Spring Boot)
#  Multi-stage build: Maven (build) + JRE (execucao)
# ============================================================

# --- Estagio 1: Build com Maven ---
FROM maven:3.9.9-eclipse-temurin-21 AS builder
WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn clean package -DskipTests

# --- Estagio 2: Imagem enxuta para execucao ---
FROM eclipse-temurin:21-jre
RUN groupadd --system appuser && useradd --system --gid appuser appuser
WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar
RUN chown appuser:appuser app.jar

USER appuser
EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8080/v3/api-docs || exit 1

ENTRYPOINT ["java", "-jar", "app.jar"]
