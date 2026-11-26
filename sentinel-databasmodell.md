# Sentinel – Databasmodell

Detta är ett första utkast till databasmodellen för Sentinels incidenthanteringssystem. Modellen bygger på en relationsdatabas och kan komma att ändras under utvecklingens gång.

## 1. Tabellernas syfte

| Tabell | Beskrivning |
| --- | --- |
| Organization | Kundföretag eller säkerhetsföretag. |
| User | Användare som tillhör en organisation. |
| Role | Användarens roll och behörighet. |
| Incident | Ett rapporterat IT-ärende som tillhör ett kundföretag. |
| IncidentMessage | Kommunikation mellan kund och handläggare i ett ärende. |
| IncidentHistory | Historik över ändringar av status, prioritet och ansvarig handläggare. |
| IncidentEvent | Händelser i ett ärende som kan utlösa aviseringar. |
| Notification | En avisering till en specifik mottagare via en viss kanal. |

## 2. Tabeller och fält

- **PK (Primary Key):** Identifierar en rad i tabellen.
- **FK (Foreign Key):** Refererar till en annan tabell.
- **NULL:** Fältet får sakna värde.

### Organization

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Organisationens ID. |
| name | VARCHAR | | Nej | Organisationens namn. |
| organization_type | VARCHAR | | Nej | CUSTOMER eller SECURITY_PROVIDER. |
| created_at | TIMESTAMP | | Nej | När organisationen skapades. |

### User

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Användarens ID. |
| organization_id | BIGINT | FK → Organization.id | Nej | Organisationen användaren tillhör. |
| role_id | BIGINT | FK → Role.id | Nej | Användarens roll. |
| first_name | VARCHAR | | Nej | Förnamn. |
| last_name | VARCHAR | | Nej | Efternamn. |
| email | VARCHAR | | Nej | E-postadress. |
| password_hash | VARCHAR | | Nej | Hashat lösenord. |
| created_at | TIMESTAMP | | Nej | När användaren skapades. |

### Role

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Rollens ID. |
| name | VARCHAR | | Nej | Rollens namn, exempelvis CUSTOMER, HANDLER eller ADMIN. |

### Incident

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Incidentens ID. |
| case_number | VARCHAR | | Nej | Unikt ärendenummer. |
| organization_id | BIGINT | FK → Organization.id | Nej | Kundföretaget som ärendet gäller. |
| created_by_user_id | BIGINT | FK → User.id | Nej | Användaren som rapporterade ärendet. |
| assigned_handler_id | BIGINT | FK → User.id | Ja | Ansvarig handläggare. |
| title | VARCHAR | | Nej | Ärendets rubrik. |
| description | TEXT | | Nej | Beskrivning av incidenten. |
| status | VARCHAR | | Nej | Ärendets aktuella status, exempelvis NEW eller CLOSED. |
| priority | VARCHAR | | Ja | Ärendets prioritet. |
| resolution | TEXT | | Ja | Beskrivning av genomförda åtgärder. |
| outcome | TEXT | | Ja | Resultatet av åtgärderna. |
| created_at | TIMESTAMP | | Nej | När ärendet skapades. |
| updated_at | TIMESTAMP | | Nej | När ärendet senast uppdaterades. |
| closed_at | TIMESTAMP | | Ja | När ärendet stängdes. |

### IncidentMessage

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Meddelandets ID. |
| incident_id | BIGINT | FK → Incident.id | Nej | Ärendet som meddelandet tillhör. |
| user_id | BIGINT | FK → User.id | Nej | Användaren som skrev meddelandet. |
| message | TEXT | | Nej | Meddelandets innehåll. |
| created_at | TIMESTAMP | | Nej | När meddelandet skapades. |

### IncidentHistory

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Historikpostens ID. |
| incident_id | BIGINT | FK → Incident.id | Nej | Ärendet som ändrades. |
| changed_by | BIGINT | FK → User.id | Nej | Användaren som gjorde ändringen. |
| changed_field | VARCHAR | | Nej | Fältet som ändrades, exempelvis status. |
| old_value | VARCHAR | | Ja | Värdet före ändringen. |
| new_value | VARCHAR | | Ja | Värdet efter ändringen. |
| created_at | TIMESTAMP | | Nej | När ändringen gjordes. |

### IncidentEvent

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Händelsens ID. |
| incident_id | BIGINT | FK → Incident.id | Nej | Ärendet där händelsen inträffade. |
| event_type | VARCHAR | | Nej | Typ av händelse, exempelvis STATUS_CHANGED. |
| created_at | TIMESTAMP | | Nej | När händelsen inträffade. |

### Notification

| Fält | Datatyp | Nyckel | Tillåter NULL | Beskrivning |
| --- | --- | --- | --- | --- |
| id | BIGINT | PK | Nej | Aviseringens ID. |
| incident_event_id | BIGINT | FK → IncidentEvent.id | Nej | Händelsen som utlöste aviseringen. |
| user_id | BIGINT | FK → User.id | Nej | Mottagaren av aviseringen. |
| channel | VARCHAR | | Nej | Aviseringskanal, exempelvis EMAIL. |
| message | TEXT | | Nej | Aviseringens innehåll. |
| status | VARCHAR | | Nej | Aviseringens status, exempelvis PENDING eller SENT. |
| created_at | TIMESTAMP | | Nej | När aviseringen skapades. |
| sent_at | TIMESTAMP | | Ja | När aviseringen skickades. |

## 3. Kommentarer till modellen

- Ett ärende ska kunna skapas utan ansvarig handläggare. Därför får `assigned_handler_id` vara NULL.
- När ett ärende stängs ska `resolution`, `outcome` och `closed_at` vara ifyllda. Dessa får vara NULL medan ärendet är öppet.
- Varje användare har i nuläget en roll och tillhör en organisation.
- Kundanvändare ska endast kunna komma åt ärenden som tillhör den egna organisationen. Handläggare ska kunna hantera ärenden från flera kundföretag.
- Ärendenummer ska vara unika. Behörighetsregler och villkor för att stänga ett ärende behöver också säkerställas vid implementationen.
- Fält, datatyper och exempelvärden är preliminära. Modellen är ännu inte implementerad eller verifierad med testdata.
