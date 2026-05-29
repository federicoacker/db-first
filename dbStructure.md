# Struttura tabelle

## Schema Tabella `cars`

| Column Name | Type | Attributes | Index |
|---|---|---|---|
| id | UNSIGNED INT | NOT NULL AUTO_INCREMENT UNIQUE | PRIMARY KEY |
| maker | VARCHAR(50) | NOT NULL | INDEX |
| model | VARCHAR(50) | NOT NULL | INDEX |
| mileage | UNSIGNED INT | NOT NULL | INDEX | 
| colour | VARCHAR(30) | NOT NULL | INDEX |
| img | VARCHAR(128) | NULL DEFAULT(placeholder.jpg) | |
| price | UNSIGNED INT | NOT NULL | INDEX |
| condition | ENUM('Excellent', 'Good', 'Decent', 'Bad', 'For Parts') | NOT NULL | INDEX |
| year | YEAR | NOT NULL | INDEX |
| power_kw | UNSIGNED MEDIUMINT | NOT NULL | INDEX |
| power_hp | UNSIGNED MEDIUMINT | NOT NULL | INDEX |
| fuel_type | ENUM ('Gasoline', 'Diesel', 'Methane', 'Lpg', 'Electric')| NOT NULL | INDEX |
| emission_class | ENUM ('Euro 6e', 'Euro 6d', 'Euro 6d-TEMP', 'Euro 6c', 'Euro 6b', 'Euro 6a', 'Euro 6', 'Euro 5', 'Euro 4', 'Euro 3', 'Euro 2', 'Euro 1', 'Pre-Euro') | NOT NULL | INDEX |
| gearbox | ENUM ('Manual', 'Automatic', 'Sequential', 'Other') | NOT NULL | INDEX |
| seats | UNSIGNED TINYINT | NOT NULL | INDEX |
| frame_type | ENUM ('City Car', 'Cabrio', 'Coupé', 'SUV/Offroad/Pick-up', 'Station Wagon', 'Sedan', 'Minivan', 'Truck', 'Other') | NOT NULL | INDEX |
| length_m | FLOAT (4,2) | NOT NULL | INDEX |
| height_m | FLOAT (4,2) | NOT NULL | INDEX |
| width_m | FLOAT (4,2) | NOT NULL | INDEX |
| readily_available | TINYINT | NOT NULL | INDEX | 
| number_of_owners | UNSIGNED TINYINT | NULL DEFAULT(1)| |
| optionals | TEXT | NULL | |
| for_sale_since | DATE | NOT NULL | INDEX |

### Problemi trovati:
- readily_available dovrebbe essere un booleano, mysql non  offre un vero e proprio booleano, sotto la scocca è un TINYINT, quindi potrebbe essere un valore qualsiasi e solo 0 verrebbe letto come false lato backend js, a meno che non facciamo manipolazioni dei dati recuperati dal db.
L'alternativa è usare BIT(1) che forza la colonna ad avere valore 0 o 1. Il problema è che quando la andiamo a recuperare dal db, lui ci restituisce un buffer, che per js è un valore truthy sempre, quindi non possiamo fare conversione immediata a boolean. La soluzione più semplice è fare lo storage come TINYINT(1) che è più portable attraverso vari dbms e poi fare la conversione a valore booleano e i controlli al lato backend in modo da non avere mai valori diversi da 0 e 1 nel db.
- optionals come text non consente di usarlo per l'indicizzazione, quello di cui avrei veramente bisogno è una tabella optionals, con assegnati degli id, e poi collegare le due tabelle. Il problema è che sarebbe un collegamento many to many che ancora non abbiamo visto come si fa
