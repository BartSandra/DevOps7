# Используем базовый образ OpenJDK для сборки и запуска приложения
FROM openjdk:8-jdk-alpine as build

# Устанавливаем рабочую директорию
WORKDIR /app

# Копируем файлы сборки и исходный код
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
COPY src/ src/

# Устанавливаем Maven и компилируем приложение
RUN apk update && apk add --no-cache maven && \
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
ENV SESSION_SERVICE_HOST=session-service \
    SESSION_SERVICE_PORT=8081 \
    HOTEL_SERVICE_HOST=hotel-service \
    HOTEL_SERVICE_PORT=8082 \
    BOOKING_SERVICE_HOST=booking-service \
    BOOKING_SERVICE_PORT=8083 \
    PAYMENT_SERVICE_HOST=payment-service \
    PAYMENT_SERVICE_PORT=8084 \
    LOYALTY_SERVICE_HOST=loyalty-service \
    LOYALTY_SERVICE_PORT=8085 \
    REPORT_SERVICE_HOST=report-service \
    REPORT_SERVICE_PORT=8086

# Если все зависимые сервисы доступны, запускаем приложение
CMD ["./wait-for-it.sh", "session-service:8081", "--", \
     "./wait-for-it.sh", "hotel-service:8082", "--", \
     "./wait-for-it.sh", "booking-service:8083", "--", \
     "./wait-for-it.sh", "payment-service:8084", "--", \
     "./wait-for-it.sh", "loyalty-service:8085", "--", \
     "./wait-for-it.sh", "report-service:8086", "--", \
     "java", "-jar", "app.jar"]
