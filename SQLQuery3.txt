CREATE DATABASE PoliziaMunicipale;

USE PoliziaMunicipale;

CREATE TABLE ANAGRAFICA (
    idanagrafica INT PRIMARY KEY IDENTITY(1,1),
    Cognome VARCHAR(255) NOT NULL,
    Nome VARCHAR(255) NOT NULL,
    Indirizzo VARCHAR(255),
    Città VARCHAR(255),
    CAP VARCHAR(10),
    Cod_Fisc VARCHAR(16) UNIQUE
);

CREATE TABLE TIPO_VIOLAZIONE (
    idviolazione INT PRIMARY KEY IDENTITY(1,1),
    descrizione VARCHAR(255) NOT NULL
);

CREATE TABLE VERBALE (
    idverbale INT PRIMARY KEY IDENTITY(1,1),
    idanagrafica INT FOREIGN KEY REFERENCES ANAGRAFICA(idanagrafica),
    idviolazione INT FOREIGN KEY REFERENCES TIPO_VIOLAZIONE(idviolazione),
    DataViolazione DATE NOT NULL,
    IndirizzoViolazione VARCHAR(255),
    Nominativo_Agente VARCHAR(255),
    DataTrascrizioneVerbale DATE,
    Importo DECIMAL(10, 2),
    DecurtamentoPunti INT
);

INSERT INTO ANAGRAFICA (Cognome, Nome, Indirizzo, Città, CAP, Cod_Fisc) VALUES
('Rossi', 'Mario', 'Via Roma 1', 'Palermo', '90100', 'RSSMRA80A01H902X'),
('Verdi', 'Luigi', 'Via Milano 2', 'Roma', '00100', 'VRDLGI75B02G801Y'),
('Bianchi', 'Anna', 'Via Napoli 3', 'Palermo', '90100', 'BNCANN90C03H703Z');

INSERT INTO TIPO_VIOLAZIONE (descrizione) VALUES
('Eccesso di velocità'),
('Sosta vietata'),
('Mancata precedenza');

INSERT INTO VERBALE (idanagrafica, idviolazione, DataViolazione, IndirizzoViolazione, Nominativo_Agente, DataTrascrizioneVerbale, Importo, DecurtamentoPunti) VALUES
(1, 1, '2009-02-10', 'Via Libertà 10', 'Agente Bianchi', '2009-02-15', 500.00, 6),
(2, 2, '2009-03-20', 'Piazza Duomo 5', 'Agente Verdi', '2009-03-25', 100.00, 2),
(1, 3, '2009-07-05', 'Via Ruggero Settimo 15', 'Agente Bianchi', '2009-07-10', 200.00, 3),
(3, 1, '2009-08-15', 'Viale della Regione 20', 'Agente Rossi', '2009-08-20', 600.00, 8),
(3, 2, '2009-09-25', 'Corso Calatafimi 25', 'Agente Verdi', '2009-09-30', 150.00, 2);

SELECT COUNT(*) AS TotaleVerbali FROM VERBALE;

SELECT a.Cognome, a.Nome, COUNT(v.idverbale) AS NumeroVerbali
FROM ANAGRAFICA a
LEFT JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
GROUP BY a.Cognome, a.Nome;

SELECT tv.descrizione, COUNT(v.idverbale) AS NumeroVerbali
FROM TIPO_VIOLAZIONE tv
LEFT JOIN VERBALE v ON tv.idviolazione = v.idviolazione
GROUP BY tv.descrizione;

SELECT a.Cognome, a.Nome, SUM(v.DecurtamentoPunti) AS TotalePuntiDecurtati
FROM ANAGRAFICA a
LEFT JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
GROUP BY a.Cognome, a.Nome;

SELECT a.Cognome, a.Nome, v.DataViolazione, v.IndirizzoViolazione, v.Importo, v.DecurtamentoPunti
FROM ANAGRAFICA a
JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
WHERE a.Città = 'Palermo';

SELECT a.Cognome, a.Nome, a.Indirizzo, v.DataViolazione, v.Importo, v.DecurtamentoPunti
FROM ANAGRAFICA a
JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
WHERE v.DataViolazione BETWEEN '2009-02-01' AND '2009-07-31';

SELECT a.Cognome, a.Nome, SUM(v.Importo) AS TotaleImporti
FROM ANAGRAFICA a
LEFT JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
GROUP BY a.Cognome, a.Nome;

SELECT * FROM ANAGRAFICA WHERE Città = 'Palermo';

SELECT DataViolazione, Importo, DecurtamentoPunti FROM VERBALE WHERE DataViolazione = '2009-02-10';

SELECT Nominativo_Agente, COUNT(*) AS NumeroViolazioni
FROM VERBALE
GROUP BY Nominativo_Agente;

SELECT a.Cognome, a.Nome, a.Indirizzo, v.DataViolazione, v.Importo, v.DecurtamentoPunti
FROM ANAGRAFICA a
JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
WHERE v.DecurtamentoPunti > 5;

SELECT a.Cognome, a.Nome, a.Indirizzo, v.DataViolazione, v.Importo, v.DecurtamentoPunti
FROM ANAGRAFICA a
JOIN VERBALE v ON a.idanagrafica = v.idanagrafica
WHERE v.Importo > 400.00;

SELECT Nominativo_Agente, COUNT(*) AS NumeroVerbali, SUM(Importo) AS TotaleImportoMulte
FROM VERBALE
GROUP BY Nominativo_Agente;

SELECT tv.descrizione AS TipoViolazione, AVG(v.Importo) AS MediaImportoMulte
FROM TIPO_VIOLAZIONE tv
LEFT JOIN VERBALE v ON tv.idviolazione = v.idviolazione
GROUP BY tv.descrizione;