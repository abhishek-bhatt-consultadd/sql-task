# Problem Statement

## Database Schema

```sql
CREATE TABLE city
(
	id INT PRIMARY KEY,
	name VARCHAR(50) NOT NULL
);

CREATE TABLE hotel
(
	id INT PRIMARY KEY,
	city_id INT NOT NULL REFERENCES cityl,
	name VARCHAR(50) NOT NULL,
	day_price NUMERIC(8, 2) NOT NULL,
	photos JSONB DEFAULT '[]'
);


CREATE TABLE booking
(
	id int PRIMARY KEY,
	hotel_id INT NOT NULL REFERENCES hotel,
	booking_date DATE NOT NULL,
	start_date DATE NOT NULL,
	end_date DATE NOT NULL
);
```

## DESCRIPTION

Your task is to prepare a list of cities with the date of last reservation made in the city and a main photo (photos[0]) of the most popular (by number of bookings) hotel in this city.

Sort results in ascending order by city. If many hotels have the same amount of bookings sort them by ID (ascending order).
Remember that the query will also be run of different datasets.

## Example Output

|   City    | last_booking_date | hotel_id | hotel_photo |
| :-------: | :---------------: | :------: | :---------: |
| Barcelona |    2024-06-15     |    3     |   3-1.jpg   |
|   Roma    |    2024-06-16     |    6     |   6-1.jpg   |

## Sample Data

### Table: city

| id  |   name    |
| :-: | :-------: |
|  1  | Barcelona |
|  2  |   Roma    |
|  3  |   Paris   |
|  4  | New York  |
|  5  |   Tokyo   |
|  6  |  Sydney   |

### Table: hotel

| id  | city_id |      name       | day_price |          photos          |
| :-: | :-----: | :-------------: | :-------: | :----------------------: |
|  1  |    1    |  The New View   |   67.00   |  ["1-1.jpg", "1-2.jpg"]  |
|  2  |    1    | The Upper House |   99.00   |  ["2-1.jpg", "2-2.jpg"]  |
|  3  |    2    |    Ace Hotel    |   90.00   |  ["3-1.jpg", "3-2.jpg"]  |
|  4  |    2    |   Hotel Diva    |  111.00   |  ["4-1.jpg", "4-2.jpg"]  |
|  5  |    3    |  Hotel Triton   |  105.00   |  ["5-1.jpg", "5-2.jpg"]  |
|  6  |    4    |  Grand Palace   |  150.00   |  ["6-1.jpg", "6-2.jpg"]  |
|  7  |    5    | Tokyo Tower Inn |  200.00   |  ["7-1.jpg", "7-2.jpg"]  |
|  8  |    6    |  Sydney Suites  |  180.00   |  ["8-1.jpg", "8-2.jpg"]  |
|  9  |    4    |  New York Inn   |  120.00   |  ["9-1.jpg", "9-2.jpg"]  |
| 10  |    5    |  Sakura Hotel   |  220.00   | ["10-1.jpg", "10-2.jpg"] |

### Table: booking

| id  | hotel_id | booking_date | start_date |  end_date  |
| :-: | :------: | :----------: | :--------: | :--------: |
|  1  |    1     |  2024-06-10  | 2024-07-01 | 2024-07-05 |
|  2  |    2     |  2024-06-11  | 2024-07-02 | 2024-07-06 |
|  3  |    3     |  2024-06-12  | 2024-07-03 | 2024-07-07 |
|  4  |    4     |  2024-06-13  | 2024-07-04 | 2024-07-08 |
|  5  |    5     |  2024-06-14  | 2024-07-05 | 2024-07-09 |
|  6  |    6     |  2024-06-15  | 2024-07-06 | 2024-07-10 |
|  7  |    7     |  2024-06-16  | 2024-07-07 | 2024-07-11 |
|  8  |    8     |  2024-06-17  | 2024-07-08 | 2024-07-12 |
|  9  |    9     |  2024-06-18  | 2024-07-09 | 2024-07-13 |
| 10  |    10    |  2024-06-19  | 2024-07-10 | 2024-07-14 |
| 11  |    1     |  2024-06-20  | 2024-07-11 | 2024-07-15 |
| 12  |    2     |  2024-06-21  | 2024-07-12 | 2024-07-16 |
| 13  |    3     |  2024-06-22  | 2024-07-13 | 2024-07-17 |
| 14  |    4     |  2024-06-23  | 2024-07-14 | 2024-07-18 |
| 15  |    5     |  2024-06-24  | 2024-07-15 | 2024-07-19 |
| 16  |    6     |  2024-06-25  | 2024-07-16 | 2024-07-20 |
| 17  |    7     |  2024-06-26  | 2024-07-17 | 2024-07-21 |
| 18  |    8     |  2024-06-27  | 2024-07-18 | 2024-07-22 |
| 19  |    9     |  2024-06-28  | 2024-07-19 | 2024-07-23 |
| 20  |    10    |  2024-06-29  | 2024-07-20 | 2024-07-24 |

### Expected ouput

| city_name | last_booking_date        | hotel_id | hotel_photo |
| --------- | ------------------------ | -------- | ----------- |
| Barcelona | 2024-06-21T00:00:00.000Z | 1        | 1-1.jpg     |
| Barcelona | 2024-06-21T00:00:00.000Z | 2        | 2-1.jpg     |
| New York  | 2024-06-28T00:00:00.000Z | 6        | 6-1.jpg     |
| New York  | 2024-06-28T00:00:00.000Z | 9        | 9-1.jpg     |
| Paris     | 2024-06-24T00:00:00.000Z | 5        | 5-1.jpg     |
| Roma      | 2024-06-23T00:00:00.000Z | 3        | 3-1.jpg     |
| Roma      | 2024-06-23T00:00:00.000Z | 4        | 4-1.jpg     |
| Sydney    | 2024-06-27T00:00:00.000Z | 8        | 8-1.jpg     |
| Tokyo     | 2024-06-29T00:00:00.000Z | 7        | 7-1.jpg     |
| Tokyo     | 2024-06-29T00:00:00.000Z | 10       | 10-1.jpg    |

# Solution

```sql
CREATE TABLE city
(
	id INT PRIMARY KEY,
	name VARCHAR(50) NOT NULL
);

CREATE TABLE hotel
(
	id INT PRIMARY KEY,
	city_id INT NOT NULL REFERENCES city,
	name VARCHAR(50) NOT NULL,
	day_price NUMERIC(8, 2) NOT NULL,
	photos JSONB DEFAULT '[]'
);


CREATE TABLE booking
(
	id int PRIMARY KEY,
	hotel_id INT NOT NULL REFERENCES hotel,
	booking_date DATE NOT NULL,
	start_date DATE NOT NULL,
	end_date DATE NOT NULL
);

INSERT INTO city (id, name) VALUES
(1, 'Barcelona'),
(2, 'Roma'),
(3, 'Paris'),
(4, 'New York'),
(5, 'Tokyo'),
(6, 'Sydney');

INSERT INTO hotel (id, city_id, name, day_price, photos) VALUES
(1, 1, 'The New View', 67.00, '["1-1.jpg", "1-2.jpg"]'),
(2, 1, 'The Upper House', 99.00, '["2-1.jpg", "2-2.jpg"]'),
(3, 2, 'Ace Hotel', 90.00, '["3-1.jpg", "3-2.jpg"]'),
(4, 2, 'Hotel Diva', 111.00, '["4-1.jpg", "4-2.jpg"]'),
(5, 3, 'Hotel Triton', 105.00, '["5-1.jpg", "5-2.jpg"]'),
(6, 4, 'Grand Palace', 150.00, '["6-1.jpg", "6-2.jpg"]'),
(7, 5, 'Tokyo Tower Inn', 200.00, '["7-1.jpg", "7-2.jpg"]'),
(8, 6, 'Sydney Suites', 180.00, '["8-1.jpg", "8-2.jpg"]'),
(9, 4, 'New York Inn', 120.00, '["9-1.jpg", "9-2.jpg"]'),
(10, 5, 'Sakura Hotel', 220.00, '["10-1.jpg", "10-2.jpg"]');

INSERT INTO booking (id, hotel_id, booking_date, start_date, end_date) VALUES
(1, 1, '2024-06-10', '2024-07-01', '2024-07-05'),
(2, 2, '2024-06-11', '2024-07-02', '2024-07-06'),
(3, 3, '2024-06-12', '2024-07-03', '2024-07-07'),
(4, 4, '2024-06-13', '2024-07-04', '2024-07-08'),
(5, 5, '2024-06-14', '2024-07-05', '2024-07-09'),
(6, 6, '2024-06-15', '2024-07-06', '2024-07-10'),
(7, 7, '2024-06-16', '2024-07-07', '2024-07-11'),
(8, 8, '2024-06-17', '2024-07-08', '2024-07-12'),
(9, 9, '2024-06-18', '2024-07-09', '2024-07-13'),
(10, 10, '2024-06-19', '2024-07-10', '2024-07-14'),
(11, 1, '2024-06-20', '2024-07-11', '2024-07-15'),
(12, 2, '2024-06-21', '2024-07-12', '2024-07-16'),
(13, 3, '2024-06-22', '2024-07-13', '2024-07-17'),
(14, 4, '2024-06-23', '2024-07-14', '2024-07-18'),
(15, 5, '2024-06-24', '2024-07-15', '2024-07-19'),
(16, 6, '2024-06-25', '2024-07-16', '2024-07-20'),
(17, 7, '2024-06-26', '2024-07-17', '2024-07-21'),
(18, 8, '2024-06-27', '2024-07-18', '2024-07-22'),
(19, 9, '2024-06-28', '2024-07-19', '2024-07-23'),
(20, 10, '2024-06-29', '2024-07-20', '2024-07-24');


-- Write your code here
```
