# Requêtage SQL

## Commandes nécessaires pour avoir sa BDD d'entrainement à jour
```
CREATE DATABASE boutique;
\c boutique

CREATE TABLE utilisateur (id SERIAL PRIMARY KEY, nom varchar(100) NOT NULL, prenom varchar(100) NOT NULL, email varchar(255), inscription date DEFAULT current_date);

INSERT INTO utilisateur (nom, prenom, email, inscription) VALUES ('Torvalds', 'Linus', 'linus.torvalds@linux.com', '1991/10/05'), ('Bezos', 'Jeff', 'jeff.bezos@amazon.com', '1994/07/05'), ('Musk', 'Elon', 'elon.musk@gmail.com', '1995/01/01'), ('Page', 'Larry', 'larry.page@gmail.com', '1998/09/04'), ('Musk', 'Elon', 'elon.musk@tesla.com', '2003/07/01'), ('Doe', 'John', 'john.doe@anonymous.com', current_date), ('Doe', 'Jane', 'jane.doe@anonymous.com', current_date);
```

## Corrections exercices

### Exercices page 21
__1/__ `SELECT * FROM utilisateur;`

__2/__ `SELECT nom, prenom FROM utilisateur;`

__3/__ `SELECT * FROM utilisateur WHERE nom = 'Doe';`

__4/__ `SELECT inscription, email FROM utilisateur WHERE inscription = current_date;`

__5/__ `SELECT * FROM utilisateur WHERE inscription BETWEEN '1990/01/01' AND '1999/12/31';`
***

### Exercices page 26
__1/__ `SELECT nom, prenom, email, inscription FROM utilisateur ORDER BY nom;`

__2/__ `SELECT COUNT(nom) FROM utilisateur WHERE nom = 'Doe';`

__3/__ `SELECT nom, prenom, COUNT(nom) AS homonyme FROM utilisateur GROUP BY nom, prenom;`

__4/__ `SELECT DISTINCT nom FROM utilisateur;`

__5/__ `SELECT DISTINCT ON (nom, prenom) nom, prenom FROM utilisateur;`

__6/__ `SELECT * FROM utilisateur ORDER BY inscription DESC;`

__7/__ `SELECT COUNT(inscription) FROM utilisateur WHERE inscription = current_date;`

__8/__ `SELECT COUNT(nom) AS inscriptions FROM utilisateur WHERE inscription between '1990-01-01' and '1999-12-31';`

__9/__ `SELECT inscription, COUNT(inscription) AS nb FROM utilisateur GROUP BY inscription;`
***

### Exercices page 29
__1/__ `SELECT * FROM utilisateur WHERE email LIKE '%@gmail%';`

__2/__ `SELECT COUNT(email) AS nb FROM utilisateur WHERE email LIKE '%@gmail%';`

__3/__ `SELECT * FROM utilisateur WHERE email NOT LIKE '%_@_%._%';`
***