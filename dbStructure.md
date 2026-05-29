# Struttura tabelle

## Schema Tabella `cars`

| Column Name | Type | Attributes | Index |
|---|---|---|---|
| id | UNSIGNED INT | NOT NULL AUTO_INCREMENT UNIQUE | PRIMARY KEY |
| maker | VARCHAR(50) | NOT NULL |  |
| model | VARCHAR(50) | NOT NULL |  |
| mileage | UNSIGNED INT | NOT NULL |  | 
| colour | VARCHAR(30) | NOT NULL |  |
| img | VARCHAR(255) | NULL DEFAULT(placeholder.jpg) | |
| price | UNSIGNED INT | NOT NULL |  |
| condition | ENUM('Excellent', 'Good', 'Decent', 'Bad', 'For Parts') | NOT NULL |  |
| year | YEAR | NOT NULL |  |
| power_kw | UNSIGNED MEDIUMINT | NOT NULL |  |
| power_hp | UNSIGNED MEDIUMINT | NOT NULL |  |
| fuel_type | ENUM ('Gasoline', 'Diesel', 'Methane', 'Lpg', 'Electric')| NOT NULL |  |
| emission_class | CHAR(10) | NOT NULL |  |
| gearbox | ENUM ('Manual', 'Automatic', 'Sequential', 'Other') | NOT NULL |  |
| seats | UNSIGNED TINYINT | NOT NULL |  |
| doors | UNSIGNED TINYINT | NOT NULL |  |
| frame_type | VARCHAR(30) | NOT NULL |  |
| length_m | FLOAT (4,2) | NOT NULL |  |
| height_m | FLOAT (4,2) | NOT NULL |  |
| width_m | FLOAT (4,2) | NOT NULL |  |
| readily_available | TINYINT | NOT NULL |  | 
| number_of_owners | UNSIGNED TINYINT | NULL DEFAULT(1)| |
| optionals | TEXT | NULL | |
| description | TEXT | NULL | |
| for_sale_since | DATE | NOT NULL |  |
| vin | CHAR(17) | NOT NULL UNIQUE | |
| license_plate | VARCHAR(14) | NOT NULL UNIQUE | INDEX |

### Problemi trovati:
- readily_available dovrebbe essere un booleano, mysql non  offre un vero e proprio booleano, sotto la scocca è un TINYINT, quindi potrebbe essere un valore qualsiasi e solo 0 verrebbe letto come false lato backend js, a meno che non facciamo manipolazioni dei dati recuperati dal db.
L'alternativa è usare BIT(1) che forza la colonna ad avere valore 0 o 1. Il problema è che quando la andiamo a recuperare dal db, lui ci restituisce un buffer, che per js è un valore truthy sempre, quindi non possiamo fare conversione immediata a boolean. La soluzione più semplice è fare lo storage come TINYINT(1) che è più portable attraverso vari dbms e poi fare la conversione a valore booleano e i controlli al lato backend in modo da non avere mai valori diversi da 0 e 1 nel db.
- optionals come text non consente di usarlo per l'indicizzazione, quello di cui avrei veramente bisogno è una tabella optionals, con assegnati degli id, e poi collegare le due tabelle. Il problema è che sarebbe un collegamento many to many che ancora non abbiamo visto come si fa
- number_of_owners sarebbe utile utilizzarlo per l'indicizzazione visto che mi aspetto che lato front-end qualcuno possa voler fare il sorting in base al numero di owners, il problema è che non sono sicuro che si possa sempre sapere quanti owners ci sono stati (ad esempio se sono macchine molto vecchie) quindi dovendo mettere NULL come attributo, al massimo con DEFAULT(1) perché almeno un owner c'è già stato essendo macchine usate, rischierei di dare informazioni non vere (ad esempio dire che c'è stato 1 solo owner) quando in realtà ce ne sono stati svariati ma semplicemente non abbiamo quel dato al momento della creazione. Per cui consentire l'indicizzazione per quel dato non mi sembra corretto come pensiero (ovviamente lato front-end mi aspetterei un disclaimer sul fatto che il numero di owners non è sempre accertato).
- vin è stata impostata come CHAR(17) perché è un codice di 17 caratteri, questo però potrebbe essere uno spreco di spazion con auto pre-1981 come data di creazione visto che all'epoca il vin era di soli 11 caratteri, essendo che mi aspetto che la maggior parte delle auto in vendita siano post-1981, dovrebbe comunque essere meglio usare un CHAR(17) rispetto a un VARCHAR(17)