## Beveiliging

Waarschijnlijk wil een dataportal het gebruik van de API beveiligen, zeker als het gaat om het insturen van data (de POST-requests van API 3).
Om discussies over de voor- en nadelen van diverse authenticatie-protocollen te voorkomen, schrijft de datastandaard niet voor op welke manier deze beveiliging uitgevoerd dient te worden.  

Het is aan te raden om `authorityId` en `contractorId` te gebruiken als authenticatie. Deze id's kunnen dan dienen om de ingestuurde data te labellen en daar zoekopdrachten aan te koppelen. Als bijvoorbeelde contractor met id `de_fietstellers_bv` dynamische data instuurt, kan elke sectie uitgebreid worden met een veld `contractorId` met de waarde `de_fietstellers_bv`.

