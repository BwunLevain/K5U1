## ADR 0003-automatisering-av-deploy-cicd

## Metadata

- Datum: 2026-05-14
- Status: Accepterat
- Beslutsfattare: Vivienne Bengtsson

## Kontext

- Vi behöver minimera mänskliga fel vid driftsättning.
- Varje ändring i master-branschen måste testas och valideras innan den når Azure.

## Beslut

Vi implementerar en CI/CD-pipeline med GitHub Actions som separerar testning (CI) från driftsättning (CD).

## Alternativ som utvärderades

- Manuell deploy via CLI (az containerapp up)
	- Fördelar: Snabbt för enstaka ändringar
	- Nackdelar: Ingen historik, risk för felaktiga versioner i molnet.

- GitHub Actions
	- Fördelar: Automatiserade tester vid varje push, full spårbarhet via Commit-SHA.
	- Nackdelar: Kräver hantering av GitHub Secrets för Azure-åtkomst.
	- Varför valdes det? Det skapar en stabil och repeterbar process som möjliggör snabba releaser.

## Konsekvenser

- Teamkonsekvenser: Utvecklare får snabb feedback om testerna misslyckas.
- Driftsäkerhet: Endast kod som passerat tester kan nå produktion.

## Uppföljning

Vi mäter "Deployment Success Rate" och ser till att pipelinen tar under 2 minuter att köra.