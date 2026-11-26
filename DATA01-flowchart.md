# Test av databasmodellen – punkt 2

Vi utgår från två fiktiva kundföretag, Vigil AB och Axiom AB, för att se hur modellen fungerar när två olika företag rapporterar incidenter.

## 1. Skapa organisationer

**Organization**

| id | name | organization_type |
|---|---|---|
| 1 | Vigil AB | CUSTOMER |
| 2 | Axiom AB | CUSTOMER |
| 3 | Sentinel Security AB | SECURITY_PROVIDER |

## 2. Skapa roller och användare

**Role**

| id | name |
|---|---|
| 1 | CUSTOMER |
| 2 | HANDLER |

**User**

| id | first_name | organization_id | role_id |
|---|---|---|---|
| 1 | Fredrik | 1 | 1 |
| 2 | Yunus | 2 | 1 |
| 3 | Erik | 3 | 2 |

Fredrik och Yunus tillhör varsitt kundföretag och Erik är handläggare på Sentinel Security AB.

## 3. Skapa incidenter utan handläggare

**Incident**

| Fält | Incident 1 | Incident 2 |
|---|---|---|
| id | 1 | 2 |
| case_number | INC-001 | INC-002 |
| organization_id | 1 | 2 |
| created_by_user_id | 1 | 2 |
| assigned_handler_id | NULL | NULL |
| title | Nätfiske | Kontoövertagande |
| description | Misstänkt e-post | Okänd inloggning |
| status | NEW | NEW |
| priority | NULL | NULL |
| resolution | NULL | NULL |
| outcome | NULL | NULL |
| closed_at | NULL | NULL |

Båda incidenterna tillhör respektive kundföretag och kan skapas utan handläggare eftersom `assigned_handler_id` tillåter NULL.

## 4. Tilldela handläggare

Erik tar över incident 1.

| Fält | Före | Efter |
|---|---|---|
| assigned_handler_id | NULL | 3 |
| status | NEW | IN_PROGRESS |
| priority | NULL | HIGH |

## 5. Stäng incidenten

Erik har hanterat incidenten och stänger ärendet.

| Fält | Nytt värde |
|---|---|
| status | CLOSED |
| resolution | Blockerade avsändaren och tog bort mejlet. |
| outcome | Inga konton blev komprometterade. |
| closed_at | 2026-11-26 10:30:00 |

## 6. Flödesschema

```text
     Vigil AB - Fredrik          Axiom AB - Yunus
              |                         |
              v                         v
     INC-001: Natfiske        INC-002: Kontovertagande
              |                         |
              v                         v
             NEW                       NEW
       Handlaggare: NULL         Handlaggare: NULL
              |                         |
              v                         v
      Erik tilldelas arendet     Vantar pa handlaggare
              |
              v
         IN_PROGRESS
              |
              v
       Atgard genomfors
              |
              v
  Atgard och resultat dokumenteras
              |
              v
            CLOSED
```

Båda företagen rapporterar varsitt ärende utan ansvarig handläggare. Erik tilldelas ärendet från Vigil AB, hanterar det och stänger det. Ärendet från Axiom AB ligger kvar som NEW utan handläggare.

## 7. Slutsats

Utifrån exemplet kan vi se att modellen täcker följande krav:

- Flera kundföretag kan finnas i samma system.
- Varje incident tillhör ett kundföretag.
- En incident kan skapas utan handläggare.
- En handläggare kan tilldelas i efterhand.
- En incident kan stängas med en beskrivning av åtgärder och resultat.

Modellen verkar fungera för det här scenariot. Företagsnamnen och uppgifterna är exempeldata. Detta är en genomgång av modellen med testdata, inte ett genomfört databastest. Vi behöver senare verifiera modellen i den faktiska databasen och testa behörigheter. Tabellerna visar de fält som är relevanta för scenariot, inte samtliga obligatoriska fält.
