# ---------- Stage 1: Build the jar ----------
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /app

# Copy only wrapper + pom first so Docker caches the dependency layer
# (this means re-builds are fast unless pom.xml actually changes)
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN chmod +x mvnw && ./mvnw dependency:go-offline -B

# Now copy the actual source and build
COPY src ./src
RUN ./mvnw clean package -DskipTests -B

# ---------- Stage 2: Run the jar (much smaller final image) ----------
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# Optional but good practice: don't run as root inside the container
RUN addgroup -S spring && adduser -S spring -G spring
USER spring

COPY --from=build /app/target/*.jar app.jar

EXPOSE 5054
ENTRYPOINT ["java", "-jar", "app.jar"]