## ADR 0002-saker-konfigurationshantering

## Metadata

- Datum: 2026-05-14
- Status: Accepterat
- Beslutsfattare: Vivienne Bengtsson

## Kontext

Jag behöver ett säkert sätt att hantera API-nycklar och hemligheter utan att hårdkoda dem.

Jag måste följa principen om "Least Privilege" för åtkomst mellan tjänster.

## Beslut

Jag använder Azure Key Vault för lagring av hemligheter och Managed Identity (User-Assigned) för att ge applikationen åtkomst till valvet utan lösenord.

## Alternativ som utvärderades

- Miljövariabler i Azure Container Apps

	- Fördelar: Enkelt att sätta upp.

	- Nackdelar: Hemligheter syns i klartext i Azure-portalen.

- Azure Key Vault med Managed Identity

	- Fördelar: Inga lösenord i koden, centraliserad loggning av vem som läser hemligheter.

	- Nackdelar: Kräver mer kod (SDK) och konfiguration i Azure.

	- Varför valdes det? Det är den säkraste metoden och eliminerar risken för läckta inloggningsuppgifter.

## Konsekvenser

- Tekniska konsekvenser: Koden måste använda Azure Identity SDK.

- Säkerhet: Mycket hög, då ingen i teamet behöver känna till de faktiska lösenorden i produktion.

## Uppföljning

- Vi kontrollerar att inga hemligheter finns i källkoden och att Managed Identity-loggarna visar korrekta anrop.