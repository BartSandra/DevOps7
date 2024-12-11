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
ENV POSTGRES_HOST=postgres \
    POSTGRES_PORT=5432 \
    POSTGRES_USER=postgres \
    POSTGRES_PASSWORD=postgres \
    POSTGRES_DB=reservations_db \
    RABBIT_MQ_HOST=rabbitmq \
    RABBIT_MQ_PORT=5672 \
    RABBIT_MQ_USER=guest \
    RABBIT_MQ_PASSWORD=guest \
    RABBIT_MQ_QUEUE_NAME=messagequeue \
    RABBIT_MQ_EXCHANGE=messagequeue-exchange \
    HOTEL_SERVICE_HOST=hotel-service \
    HOTEL_SERVICE_PORT=8082 \
    PAYMENT_SERVICE_HOST=payment-service \
    PAYMENT_SERVICE_PORT=8084 \
    LOYALTY_SERVICE_HOST=loyalty-service \
    LOYALTY_SERVICE_PORT=8085

# Если все зависимые сервисы доступны, запускаем приложение
CMD ["./wait-for-it.sh", "postgres:5432", "--", \
     "./wait-for-it.sh", "rabbitmq:5672", "--", \
     "./wait-for-it.sh", "hotel-service:8082", "--", \
     "./wait-for-it.sh", "payment-service:8084", "--", \
     "./wait-for-it.sh", "loyalty-service:8085", "--", \
     "java", "-jar", "/app.jar"]
