# Используем базовый образ OpenJDK для сборки и запуска приложения
FROM openjdk:8-jdk-alpine as build

# Устанавливаем рабочую директорию
WORKDIR /app

# Копируем файлы сборки и исходный код
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
COPY src/ src/

# Устанавливаем Maven, собираем зависимости и приложение
RUN apk add --no-cache maven && \
    mvn -B dependency:go-offline && \
    mvn -B package -DskipTests

# Начинаем новый этап сборки для запуска приложения
FROM openjdk:8-jre-alpine

# Копируем собранный JAR-файл в корень рабочей директории
COPY --from=build /app/target/*.jar /app.jar

# Копируем скрипт ожидания
COPY wait-for-it.sh .
RUN apk add --no-cache bash
RUN chmod +x /wait-for-it.sh

# Устанавливаем переменные окружения
ENV POSTGRES_HOST=postgres
ENV POSTGRES_PORT=5432
ENV POSTGRES_USER=postgres
ENV POSTGRES_PASSWORD=postgres
ENV POSTGRES_DB=statistics_db
ENV RABBIT_MQ_HOST=rabbitmq
ENV RABBIT_MQ_PORT=5672
ENV RABBIT_MQ_USER=guest
ENV RABBIT_MQ_PASSWORD=guest
ENV RABBIT_MQ_QUEUE_NAME=messagequeue
ENV RABBIT_MQ_EXCHANGE=messagequeue-exchange

# Если все зависимые сервисы доступны, запускаем приложение
CMD ["./wait-for-it.sh", "postgres:5432", "--", \
     "./wait-for-it.sh", "rabbitmq:5672", "--", \
     "java", "-jar", "/app.jar"]
