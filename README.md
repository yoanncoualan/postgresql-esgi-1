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

ALTER TABLE produit ADD prixventeht real;

UPDATE produit SET prixventeht = 120 WHERE id = 1 OR id = 2;
UPDATE produit SET prixventeht = 15 WHERE id = 3;
UPDATE produit SET prixventeht = 30 WHERE id = 4;
UPDATE produit SET prixventeht = 70 WHERE id = 5;
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

__8/__ `SELECT COUNT(nom) AS inscriptions FROM utilisateur WHERE inscription BETWEEN '1990-01-01' AND '1999-12-31';`

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

### Exercices page 53
__1/__ 
```
SELECT nom, prenom, inscription, current_date - inscription AS jours 
FROM utilisateur;
```

__2/__ 
```
SELECT nom, prenom, inscription, current_date - inscription AS jours 
FROM utilisateur 
GROUP BY nom, prenom, inscription 
HAVING current_date - inscription > 365 
ORDER BY jours DESC;
```

__3/__ 
```
SELECT STRING_AGG(email, '; ') AS emails 
FROM utilisateur;
```

__4/__ 
```
SELECT c.id, c.date, STRING_AGG(p.nom, ', ') AS produits 
FROM commande c 
INNER JOIN contenucommande cc ON c.id = cc.id_commande 
INNER JOIN produit p ON p.id = cc.id_produit 
GROUP BY c.id;
```

__5/__ 
```
SELECT nom, prixht, prixht * 1.2 AS prixttc 
FROM produit;
```

__6/__ 
```
SELECT p.nom, SUM(cc.qte) 
FROM produit p 
INNER JOIN contenucommande cc ON cc.id_produit = p.id 
GROUP BY p.nom;
```

__7/__ 
```
SELECT u.nom, prenom, SUM(cc.qte * prixht) AS montantHT, SUM((cc.qte * prixht) * 1.2) AS montantTTC 
FROM utilisateur u
INNER JOIN commande c ON u.id = c.id_utilisateur
INNER JOIN contenucommande cc ON c.id = cc.id_commande
INNER JOIN produit p ON cc.id_produit = p.id
GROUP BY u.nom, u.prenom, c.id;
```

__8/__ 
```
SELECT nom, SUM(cc.qte) AS ventes 
FROM contenucommande cc 
INNER JOIN produit p ON p.id = cc.id_produit 
GROUP BY nom 
ORDER BY ventes DESC;
```

__9/__ 
```
SELECT u.nom, u.prenom, SUM(cc.qte) AS qte 
FROM utilisateur u 
LEFT JOIN commande c ON c.id_utilisateur = u.id 
LEFT JOIN contenucommande cc ON cc.id_commande = c.id 
GROUP BY u.nom, u.prenom, u.id;
```

__10/__ 
```
SELECT u.id, u.nom, u.prenom, SUM(p.prixht * cc.qte) AS depenses 
FROM utilisateur u 
LEFT JOIN commande c ON c.id_utilisateur = u.id 
LEFT JOIN contenucommande cc ON cc.id_commande = c.id 
LEFT JOIN produit p ON p.id = cc.id_produit 
GROUP BY u.id, u.nom, u.prenom;
```

__11/__ 
```
SELECT c.id AS commande, u.nom, u.prenom, SUM(cc.qte * p.prixht) AS montant 
FROM commande c 
LEFT JOIN contenucommande cc ON cc.id_commande = c.id 
LEFT JOIN produit p ON p.id = cc.id_produit 
LEFT JOIN utilisateur u ON u.id = c.id_utilisateur 
GROUP BY c.id, u.nom, u.prenom;
```

__12/__ 
```
SELECT AVG(p.prixht * cc.qte) AS moyenne 
FROM commande c 
INNER JOIN contenucommande cc ON cc.id_commande = c.id 
INNER JOIN produit p ON p.id = cc.id_produit;
```

__13/__ 
```
SELECT u.nom, u.prenom, SUM(p.prixht * cc.qte) / COUNT(DISTINCT(c.id)) AS moyenne 
FROM utilisateur u 
INNER JOIN commande c ON c.id_utilisateur = u.id 
INNER JOIN contenucommande cc ON cc.id_commande = c.id 
INNER JOIN produit p ON p.id = cc.id_produit 
GROUP BY u.nom, u.prenom;
```

__14/__ 
```
SELECT MIN(prixht), MAX(prixht), MAX(prixht) - MIN(prixht) AS ecart 
FROM produit;
```

__15/__ 
```
SELECT nom, prixht, qte, prixht * qte AS CA 
FROM produit;
```

__16/__ 
```
SELECT SUM(prixht * qte) AS CA 
FROM produit;
```

__17/__ 
```
SELECT SUM(cc.qte * p.prixht) AS ca 
FROM contenucommande cc 
INNER JOIN produit p ON cc.id_produit = p.id;
```

__18/__ 
```
SELECT EXTRACT(month FROM inscription) AS mois, COUNT(id) AS nb 
FROM utilisateur u 
GROUP BY EXTRACT(month FROM inscription) 
ORDER BY mois;
```
***

### Exercices page 57
__1/__ 
```
SELECT id, nom, prixventeht - prixht AS marge 
FROM produit 
ORDER BY prixventeht - prixht;
```

__2/__ 
```
SELECT p.id, p.nom, SUM(prixventeht - prixht) AS marge_unitaire, SUM(cc.qte) AS qte_vendu, SUM((p.prixventeht - p.prixht) * cc.qte) AS marge_totale 
FROM produit p 
LEFT JOIN contenucommande cc ON p.id = cc.id_produit 
GROUP BY p.id, p.nom 
ORDER BY SUM((p.prixventeht - p.prixht) * cc.qte);
```
***

## TRIGGERS

### Création de la fonction
```
CREATE FUNCTION maj_stock() RETURNS TRIGGER AS $stock$
BEGIN
	UPDATE produit SET qte = qte - NEW.qte WHERE id = NEW.id_produit;
	RETURN NEW;
END;
$stock$ LANGUAGE plpgsql;
```

### Création du trigger
```
CREATE TRIGGER maj_stock AFTER INSERT 
ON contenucommande 
FOR EACH ROW 
EXECUTE PROCEDURE maj_stock();
```