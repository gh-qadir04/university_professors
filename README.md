# university_professors

CREATE TABLE university_professors (
    firstname VARCHAR(128) NOT NULL,
    lastname VARCHAR(128) NOT NULL,
    university VARCHAR(255) NOT NULL,
    university_shortname VARCHAR(50) NOT NULL,
    university_city VARCHAR(255) NOT NULL,
    function VARCHAR(255) NOT NULL,
    organization VARCHAR(255) NOT NULL,
    organization_sector VARCHAR(100) NOT NULL
);


SELECT * FROM university_professors;



--Universities Table
CREATE TABLE universities (
    id SERIAL PRIMARY KEY,
    university VARCHAR(255) NOT NULL,
    university_shortname VARCHAR(50) NOT NULL,
    university_city VARCHAR(100) NOT NULL,
    UNIQUE (university, university_shortname, university_city)
);


SELECT * FROM universities;


-- Organizations Table
CREATE TABLE organizations (
    id SERIAL PRIMARY KEY,
    organization VARCHAR(255) NOT NULL,
    organization_sector VARCHAR(100) NOT NULL,
    UNIQUE (organization, organization_sector)
);

SELECT * FROM organizations;



-- Professors Table
CREATE TABLE professors (
    id SERIAL PRIMARY KEY,
    firstname VARCHAR(128) NOT NULL,
    lastname VARCHAR(128) NOT NULL,
    university_id INT NOT NULL REFERENCES universities(id) ON DELETE CASCADE,
    UNIQUE (firstname, lastname, university_id)
);

SELECT * FROM professors;

-- Affiliations Table
CREATE TABLE affiliations (
    id SERIAL PRIMARY KEY,
    professor_id INT NOT NULL REFERENCES professors(id) ON DELETE CASCADE,
    function VARCHAR(255) NOT NULL,
    organization_id INT NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    UNIQUE (professor_id, function, organization_id)
);

SELECT * FROM affiliations;



INSERT INTO universities (university, university_shortname, university_city)
SELECT DISTINCT university, university_shortname, university_city
FROM university_professors;

select *
from universities


INSERT INTO organizations (organization, organization_sector)
SELECT DISTINCT organization, organization_sector
FROM university_professors;

select *
from organizations

INSERT INTO professors (firstname, lastname, university_id)
SELECT DISTINCT u.firstname, u.lastname, un.id
FROM university_professors u
JOIN universities un ON u.university = un.university;

select *
from professors

INSERT INTO affiliations (professor_id, function, organization_id)
SELECT DISTINCT p.id, u.function, o.id
FROM university_professors u
JOIN professors p ON u.firstname = p.firstname AND u.lastname = p.lastname
JOIN organizations o ON u.organization = o.organization;

select *
from affiliations

--TRYING DLELTE DATA
DELETE FROM professors
WHERE id = 10;


-- Trying to insert duplicate data
INSERT INTO universities (university, university_shortname, university_city)
VALUES ('Example University', 'EXU', 'Example City')

-- Update university name safely
UPDATE universities
SET university = 'Updated University Name'
WHERE id = 1;

SELECT p.firstname, p.lastname, un.university, a.function, o.organization
FROM professors p
JOIN universities un ON p.university_id = un.id
JOIN affiliations a ON p.id = a.professor_id
JOIN organizations o ON a.organization_id = o.id;

SELECT p.firstname, p.lastname, o.organization
FROM professors p
JOIN affiliations a ON p.id = a.professor_id
JOIN organizations o ON a.organization_id = o.id
WHERE o.organization = 'FNS';
