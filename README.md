# driver1
POST /customers
Тело запроса:

{
  ""name"": ""Иван Иванов"",
  ""phone"": ""+7 900 123 4567""
}

Тело ответа:

{
  ""customer_id"": 1,
  ""name"": ""Иван Иванов"",
  ""phone"": ""+7 900 123 4567""
}


GET /customers
Тело ответ:

[
  {
    ""customer_id"": 1,
    ""name"": ""Иван Иванов"",
    ""phone"": ""+7 900 123 4567""
  }
]



GET /customers/{id}
Тело ответа:

{
  ""customer_id"": 1,
  ""name"": ""Иван Иванов"",
  ""phone"": ""+7 900 123 4567""
}


DELETE /customers/{id}
Тело ответа:

{
  ""message"": ""200""
}

2.
POST /drivers
Тело запроса:

{
  ""name"": ""Петр Петров"",
  ""phone"": ""+7 911 123 4567""
}

Тело ответ:

{
  ""driver_id"": 1,
  ""name"": ""Петр Петров"",
  ""phone"": ""+7 911 123 4567""
}


GET /drivers
Тело ответ:

[
  {
    ""driver_id"": 1,
    ""name"": ""Петр Петров"",
    ""phone"": ""+7 911 123 4567""
  }
]

DELETE /drivers/{id}
Тело ответ:

{
  ""message"": ""200""
}


---

3.

POST /cars
Тело запроса:

{
  ""model"": ""Toyota Camry"",
  ""car_number"": ""А123ВС77""
}

Тело ответ:

{
  ""car_id"": 1,
  ""model"": ""Toyota Camry"",
  ""car_number"": ""А123ВС77""
}


GET /cars
Тело ответа:

[
  {
    ""car_id"": 1,
    ""model"": ""Toyota Camry"",
    ""car_number"": ""А123ВС77""
  }
]


DELETE /cars/{id}
Ответ:

{
  ""message"": ""200""
}


4.

POST /orders
Тело запроса:

{
  ""start_destination"": ""ул.Ленина, 3"",
  ""end_destination"": ""ул. Пушкина, 5"",
  ""order_time"": ""2025-02-26 12:00:00"",
  ""car_id"": 1,
  ""driver_id"": 1,
  ""customer_id"": 1
}

Тело ответа:

{
  ""order_id"": 1,
  ""start_destination"": ""ул. Ленина, 10"",
  ""end_destination"": ""ул. Пушкина, 5"",
  ""order_time"": ""2025-02-26 12:00:00"",
  ""car_id"": 1,
  ""driver_id"": 1,
  ""customer_id"": 1
}


GET /orders/{Id}
Тело ответа:

[
   {
    ""order_id"": 1,
    ""start_destination"": ""ул. Ленина, 10"",
    ""end_destination"": ""ул. Пушкина, 5"",
    ""order_time"": ""2025-02-26 12:00:00"",
    ""car_id"": 1,
    ""driver_id"": 1,
    ""customer_id"": 1
  }
]


PUT /orders/{id}
Тело запроса:

{
  ""status"": ""completed""
}

Ответ:

{
  ""message"": ""200""
}


DELETE /orders/{id}
Ответ:

{
  ""message"": ""200""
}
import psycopg2
from psycopg2 import sql

DB_NAME = 'your_database'
DB_USER = 'your_username'
DB_PASSWORD = 'your_password'
DB_HOST = 'localhost'
DB_PORT = '5432'

bazeeva_berdnikov = "CREATE SCHEMA IF NOT EXISTS taxi_service"

create_tables = [
    """
    CREATE TABLE IF NOT EXISTS taxi_service.customers (
        customer_id SERIAL PRIMARY KEY,
        name TEXT NOT NULL,
        phone TEXT UNIQUE NOT NULL
    );
    """,
    """
    CREATE TABLE IF NOT EXISTS taxi_service.drivers (
        driver_id SERIAL PRIMARY KEY,
        name TEXT NOT NULL,
        phone TEXT UNIQUE NOT NULL
    );
    """,
    """
    CREATE TABLE IF NOT EXISTS taxi_service.cars (
        car_id SERIAL PRIMARY KEY,
        model TEXT NOT NULL,
        car_number TEXT UNIQUE NOT NULL
    );
    """,
    """
    CREATE TABLE IF NOT EXISTS taxi_service.taxi (
        order_id SERIAL PRIMARY KEY,
        start_destination TEXT NOT NULL,
        end_destination TEXT NOT NULL,
        order_time TEXT NOT NULL,
        car_id INT,
        driver_id INT,
        customer_id INT NOT NULL,
        status TEXT NOT NULL,
        FOREIGN KEY (car_id) REFERENCES taxi_service.cars(car_id) ON DELETE SET NULL,
        FOREIGN KEY (driver_id) REFERENCES taxi_service.drivers(driver_id) ON DELETE SET NULL,
        FOREIGN KEY (customer_id) REFERENCES taxi_service.customers(customer_id) ON DELETE CASCADE
    );
    """
]


def create_database_schema():
    try:
        conn = psycopg2.connect(dbname=DB_NAME, user=DB_USER, password=DB_PASSWORD, host=DB_HOST, port=DB_PORT)
        conn.autocommit = True

        cursor = conn.cursor()

        cursor.execute(bazeeva_berdnikov)
        print("Схема 'taxi_service' успешно создана.")

        for table_sql in create_tables:
            cursor.execute(table_sql)
            print("Таблица успешно создана.")

    except Exception as e:
        print(f"Произошла ошибка: {e}")
    finally:
        if cursor:
            cursor.close()
        if conn:
            conn.close()


if __name__ == "__main__":
    create_database_schema()
