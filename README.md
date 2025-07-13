# SQL Murder Mystery

## Tools & Skills Used

![SQL](https://img.shields.io/badge/SQL-Query%20Logic-%233298DA)
![SQL](https://img.shields.io/badge/SQL-JOINs-%233298DA)
![SQL](https://img.shields.io/badge/SQL-Data%20Investigation-%233298DA)
![SQL](https://img.shields.io/badge/SQL-Relational%20Database-%233298DA)

## Project Overview

This repository contains my personal solution to the crime-themed SQL game by Knight Lab [SQL Murder Mystery](https://mystery.knightlab.com/). Follow along as I unravel clues, chase down suspects, and sleuth my way through this quirky query-driven whodunnit.

![Knighlab SQL Murder Mystery Illustration](/knightlab-illustration.png)

## Where the Mystery Begins

The SQL game starts with this clue:

> A crime has taken place and the detective needs your help. The detective gave you the crime scene report, but you somehow lost it. You vaguely remember that the crime was a ​murder​ that occurred sometime on ​Jan.15, 2018​ and that it took place in ​SQL City​. Start by retrieving the corresponding crime scene report from the police department’s database.

A database schema diagram is also provided to assist in the investigation:

![SQL Murder Mystery Database Schema](/knightlab-database-schema.png)

## How I Approached the Mystery

### Step 1 | Get the Crime Scene Report

#### Query

```sql
SELECT *
FROM
    crime_scene_report
WHERE
	date = '20180115'
	AND type = 'murder'
	AND city = 'SQL City'
;
```

#### Output

| date | type | description | city |
| --- | --- | --- | --- |
| 20180115 | murder | Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave". | SQL City |

### Step 2 | Find the Witnesses 

#### Query

```sql
SELECT *
FROM
    person
WHERE
	(address_street_name LIKE '%Northwestern%'
  	AND address_number = (
        SELECT MAX(address_number)
    	FROM
		  	person
    	WHERE
			address_street_name LIKE '%Northwestern%'
	  	)
  	)
    OR
	(name LIKE '%Annabel%'
	AND address_street_name LIKE '%Franklin%'
    )
;
```

#### Output

| id | name | license_id | address_number | address_street_name | ssn |
| --- | --- | --- | --- | --- | --- |
| 14887 | Morty Schapiro | 118009 | 4919 | Northwestern Dr | 111564949 |
| 16371 | Annabel Miller | 490173 | 103	| Franklin Ave | 318771143 |

### Step 3 | Read the Witness Interviews

#### Query

```sql
SELECT 
    CASE 
        WHEN person_id = 14887 THEN 'Witness 1'
        WHEN person_id = 16371 THEN 'Witness 2'
        ELSE 'Unknown'
    END AS witness_label
    , transcript
FROM
    interview
WHERE
    person_id IN (14887, 16371)
;
```

#### Output

| witness_label	| transcript |
| --- | --- |
| Witness 1 | I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W". |
| Witness 2 | I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th. |

### Step 4 | Find the Killer
