# Databasmodell

## Tabeller:
* Organization = företag/organisation.
* User = användare som tillhör en organisation och har en roll.
* Role = användarroll, t.ex. kund eller handläggare.
* Incident = själva ärendet.
* IncidentMessage = meddelanden i ärendet.
* IncidentHistory = logg över ändringar i ärendet.
* IncidentEvent = händelse som kan trigga avisering.
* Notification = avisering till en viss användare.


## Organization
(Alla är obligatoriska)
### Attribut		### Datatyp
id			Long (PK)
name 		String
type			Enum
createdAt	LocalDateTime

## User
En användare tillhör exakt en organisation och har exakt en roll i systemet.
(Alla fält är obligatoriska)
### Attribut			### Datatyp			
id				Long (PK)		
firstName		String			
lastName		String
email			String
passwordHash	String
orgnanizationId	Long (FK)
roleId			Long (FK)
active			Boolean
createAt			LocalDateTime

## Role
En roll bestämmer vilka behörigheter en användare har i systemet.
### Attribut		### Datatyp		### Obligatoriskt
id			Long (PK)	Ja
name		String		Ja
descritpion	String		Nej

