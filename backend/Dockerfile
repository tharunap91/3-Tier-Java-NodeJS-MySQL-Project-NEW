# syntax=docker/dockerfile:1

# ============================
# Stage 1 - Build
# ============================

FROM maven:3.9.11-eclipse-temurin-21 AS build

WORKDIR /workspace

# Copy dependency definition first
COPY pom.xml .

# Cache Maven dependencies
RUN --mount=type=cache,target=/root/.m2 \
    mvn -B dependency:go-offline

# Now copy application source
COPY src ./src

# Build application
RUN --mount=type=cache,target=/root/.m2 \
    mvn -B clean package


# ============================
# Stage 2 - Runtime
# ============================

FROM eclipse-temurin:21-jre-jammy AS runtime

# Create dedicated non-root user
RUN groupadd --gid 10001 appgroup \
    && useradd \
       --uid 10001 \
       --gid appgroup \
       --no-create-home \
       --shell /usr/sbin/nologin \
       appuser

WORKDIR /app

COPY --from=build \
     --chown=appuser:appgroup \
     /workspace/target/projectops-api.jar \
     /app/app.jar

USER 10001:10001

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]
