# Käyttäjätarinoita

Tälle sivulle on koottu relevantteja käyttäjätarinoita projektille. Käyttäjätarinoiden alle on kirjattu ehtoja (conditions of satisfaction), joiden kautta käyttäjätarina voidaa katsoa toteutuneeksi. Jokaisen kohdan alle on kirjattu tapauksiin liittyviä SQL kyselyitä pseudokoodilla täydennettyinä. Tämän dokumentin loppuun on myös kirjattuna CREATE TABLE -lausekkeet tauluille, joihin kyselyissä referoidaan.

#### 1. Tiedon selaaminen
Käyttäjänä voin tarkastella tietokantaan tallennettuja tietoja listaus- tai indeksisivustolta.
+ Ylläpitäjä voi tarkastella kaikkia tietokannassa olevia suoritettuja töitä.
+ Käyttäjä voi tarkastella vain itse kirjaamiaan töitä.
+ Käyttäjä näkee lukumäärän kirjaamistaan työtehtävistä ja työtunneistaan
+ Käyttäjä näkee lukumäärän kaikkista järjestelmään kirjatuista työtehtävistä ja työtunneista.

```sql
SELECT * FROM work_done;

SELECT * FROM work_done WHERE (account_id = <parametrina annettu kirjautuneen käyttäjän id>);

SELECT COUNT(work_done.id) FROM work_done LEFT JOIN account ON account.id = work_done.account_id;

SELECT SUM(worked_hours) FROM work_done;

SELECT COUNT(work_done.id) FROM work_done LEFT JOIN account ON account.id = work_done.account_id WHERE (account_id = <parametrina annettu id>);

SELECT SUM(worked_hours) FROM work_done LEFT JOIN account ON account.id = work_done.account_id WHERE (account_id = <parametrina annettu id>);
```

#### 2. Tiedon lisääminen
Käyttäjänä voin navigoida linkin avulla uusien suoritettujen työtehtävien kirjaussivustolle ja lisätä siellä kenttiä täyttämällä uuden työtehtävän tietokantaan.
+ Käyttäjä voi lisätä tietoa järjestelmään.
+ Käyttäjän lisäämä tieto on validoitua.

```sql
INSERT INTO work_done (id, date_created, date_modified, account_id, task, task_id, worked_hours) VALUES (<juokseva id>, <DateTime funktio>, <DateTime funktio>, <luonut käyttäjä>, <työtehtävä>, <työtehtävän tunniste optional>, <työtunnit>);
```

#### 3. Tiedon muokkaaminen
Käyttäjänä voin navigoida linkin tai napin avulla yksittäisen jo kirjatun tehtävän muokkaussivustolle, jonka avulla voin muokata tietokantaan aiemmin lisättyä työtehtävää.
+ Käyttäjä voi muokata itse kirjaamiaan tehtäviä.
+ Muokattava tieto on validoitua.

```sql
UPDATE work_done SET task = <muutos>, task_id = <muutos>, worked_hours = <muutos> WHERE (id = <parametrina annettu id>);
```

#### 4. Tiedon poistaminen
Käyttäjänä voin poistaa linkin tai napin avulla yksittäisen jo kirjatun tehtävän tietokannasta.
+ Käyttäjä voi poistaa lisäämäänsä tietoa tietokannasta.

```sql
DELETE FROM work_done WHERE (id = <parametrina annettu id);
```

#### 5. Kirjautuminen
Käyttäjänä voin kirjautua sivustolle.
+ Käyttäjä voi kirjautua sivustolle kirjaudu näkymän kautta.
+ Sivuston tiedot näkyvät vain kirjautuneille käyttäjille.
+ Salasanat on kryptattu.

```sql
SELECT * FROM user WHERE username = <parametrina annettu username>;
```

#### 6. Tapahtumat
Käyttäjänä näen minulle suunnitellut tulevat työtehtävät. Ylläpitäjänä voin lisätä, poistaa ja muokata tulevia työtehtäviä.
+ Käyttäjälle näytetään tälle osoitetut uudet tapahtumat.
+ Ylläpitäjä voi selata kaikkia tapahtumia.
+ Ylläpitäjä voi muokata ja poistaa tulevia tapahtumia.
+ Uusien tapahtumien luonti on rajoitettu ylläpitäjälle.

```sql
SELECT * FROM upcoming_work WHERE (account_id = <parametrina annettu id>);

SELECT * FROM upcoming_work;

INSERT INTO upcoming_work (id, account_id, name, date, hours) VALUES (<juokseva id>, <käyttäjä>, <tehtävä>, <päivämäärä>, <työtunnit>);

UPDATE upcoming_work SET name = <muutos>, date = <muutos>, hours = <muutos> WHERE (id = <parametrina annettu id>);

DELETE FROM upcoming_work WHERE (id = <parametrina annettu id>);
```

#### CREATE TABLE -lauseet

```sql
CREATE TABLE work_done (
    id INT NOT NULL,
    date_created DATETIME,
    date_modified DATETIME,
    account_id VARCHAR(144) NOT NULL,
    task VARCHAR(144) NOT NULL,
    task_id INT,
    worked_hours INT NOT NULL,
    PRIMARY KEY (id),
    FOREIGN KEY (account_id) REFERENCES account(account_id)
);

CREATE TABLE upcoming_work (
    id INT NOT NULL,
    account_id VARCHAR(144) NOT NULL,
    name VARCHAR(144) NOT NULL,
    date DATE NOT NULL,
    hours INT NOT NULL,
    PRIMARY KEY (id),
    FOREIGN KEY (account_id) REFERENCES account(account_id)
);

CREATE TABLE account (
    id INT NOT NULL,
    username VARCHAR(144) NOT NULL UNIQUE,
    _password VARCHAR(144) NOT NULL,
    role VARCHAR(60) NOT NULL,
    name VARCHAR(144) NOT NULL,
    certificates VARCHAR(144) NOT NULL,
    work INT NOT NULL,
    PRIMARY KEY (id),
    FOREIGN KEY (work) REFERENCES work_done(account_id)
);

CREATE TABLE planned_work (
    account_id INT NOT NULL,
    course_id INT NOT NULL,
    PRIMARY KEY (account_id),
    PRIMARY KEY (course_id),
    FOREIGN KEY (account_id) REFERENCES account(id),
    FOREIGN KEY (course_id) REFERENCES upcoming_work(id)
```
