# Requêtage SQL

## Commandes nécessaires pour avoir sa BDD d'entrainement à jour
```
CREATE DATABASE boutique;
\c boutique

CREATE TABLE utilisateur (
	id SERIAL PRIMARY KEY, 
	nom varchar(100) NOT NULL, 
	prenom varchar(100) NOT NULL, 
	email varchar(255), 
	inscription date DEFAULT current_date
);

INSERT INTO utilisateur (nom, prenom, email, inscription) 
VALUES ('Torvalds', 'Linus', 'linus.torvalds@linux.com', '1991/10/05'), 
('Bezos', 'Jeff', 'jeff.bezos@amazon.com', '1994/07/05'), 
('Musk', 'Elon', 'elon.musk@gmail.com', '1995/01/01'), 
('Page', 'Larry', 'larry.page@gmail.com', '1998/09/04'), 
('Musk', 'Elon', 'elon.musk@tesla.com', '2003/07/01'), 
('Doe', 'John', 'john.doe@anonymous.com', current_date), 
('Doe', 'Jane', 'jane.doe@anonymous.com', current_date);

CREATE TABLE produit (
	id SERIAL PRIMARY KEY, 
	nom varchar(50) NOT NULL, 
	prixht real NOT NULL, 
	qte int DEFAULT 0
);

INSERT INTO produit (nom, prixht, qte) 
VALUES ('Objet 1', 100, 500), 
('Objet 2', 80, 10), 
('Livre 1', 10, 120), 
('Livre 2', 25, 8), 
('Jeux 1', 60, 200);

CREATE TABLE commande(
	id SERIAL PRIMARY KEY, 
	id_utilisateur int REFERENCES utilisateur(id), 
	date date DEFAULT current_date
);

INSERT INTO commande (id_utilisateur, date) 
VALUES (1, '1991/03/10'), 
(1, '1999/11/11'), 
(2, current_date), 
(6, current_date), 
(null, current_date);

CREATE TABLE contenucommande(
	id SERIAL PRIMARY KEY, 
	id_commande int REFERENCES commande(id), 
	id_produit int REFERENCES produit(id), 
	qte int DEFAULT 0
);

INSERT INTO contenucommande (id_commande, id_produit, qte) 
VALUES (1, 5, 1), 
(1, 2, 1), 
(2, 1, 4), 
(3, 2, 2), 
(3, 3, 1);
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

### Exercices page 33
__1/__ `SELECT * FROM utilisateur WHERE inscription::text LIKE '2019%';`

__2/__ `SELECT nom FROM utilisateur GROUP BY nom HAVING COUNT(nom) > 1;`

__3/__ `SELECT COUNT(nom), nom, prenom FROM utilisateur GROUP BY nom, prenom HAVING COUNT(nom) > 1;`

__4/__ `SELECT COUNT(DISTINCT email) AS emails FROM utilisateur;`
***

### Exercices page 36
__1/__ 
```
SELECT nom, prenom, email, inscription 
FROM  utilisateur 
WHERE inscription > (
	SELECT inscription 
	FROM utilisateur 
	WHERE prenom = 'Jeff'
);
```

__2/__ 
```
SELECT * 
FROM (
	SELECT DISTINCT ON (nom, prenom) nom, prenom, email, inscription 
	FROM utilisateur
) u 
ORDER BY inscription DESC;
```
***

### Exercices page 49
__1/__ 
```
SELECT * 
FROM utilisateur u 
INNER JOIN commande c ON u.id = c.id_utilisateur;
```

__2/__ 
```
SELECT DISTINCT ON (nom, prenom) nom, prenom, email, inscription 
FROM utilisateur u 
INNER JOIN commande c ON u.id = c.id_utilisateur;
```

__3/__ 
```
SELECT COUNT(DISTINCT u.id) 
FROM utilisateur u 
INNER JOIN commande c ON u.id = c.id_utilisateur;
```

__4/__ 
```
SELECT nom, prenom, COUNT(nom) AS nb 
FROM utilisateur u 
INNER JOIN commande c ON u.id = c.id_utilisateur 
GROUP BY nom, prenom;
```

__5/__ 
```
SELECT nom, prenom 
FROM utilisateur u 
LEFT JOIN commande c ON u.id = c.id_utilisateur 
WHERE c.id IS NULL;
```

__6/__ 
```
SELECT * 
FROM utilisateur u 
INNER JOIN commande c ON u.id = c.id_utilisateur 
WHERE c.date = current_date;
```

__7/__ 
```
SELECT p.nom 
FROM produit p 
LEFT JOIN contenucommande cc ON cc.id_produit = p.id 
WHERE cc.id IS NULL;
```

__8/__ 
```
SELECT u.nom, u.prenom, p.nom, cc.qte 
FROM produit p 
INNER JOIN contenucommande cc ON p.id = cc.id_produit 
INNER JOIN commande c ON cc.id_commande = c.id 
INNER JOIN utilisateur u ON u.id = c.id_utilisateur 
WHERE u.nom = 'Bezos';
```

__9/__ 
```
SELECT u.nom, u.prenom, p.nom, cc.qte 
FROM produit p 
INNER JOIN contenucommande cc ON p.id = cc.id_produit 
INNER JOIN commande c ON cc.id_commande = c.id 
INNER JOIN utilisateur u ON u.id = c.id_utilisateur 
WHERE c.id = (
	SELECT c.id 
	FROM commande c 
	INNER JOIN utilisateur u ON u.id = c.id_utilisateur 
	WHERE u.nom = 'Torvalds' 
	ORDER BY c.date ASC 
	LIMIT 1
);
```
***