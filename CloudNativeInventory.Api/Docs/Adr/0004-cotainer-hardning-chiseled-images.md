## ADR 0004-container-hardning-chiseled-images

## Metadata

- Datum: 2026-05-14
- Status: Accepterat
- Beslutsfattare: Vivienne Bengtsson

## Kontext

- Standard-containrar innehåller ofta verktyg (skal, pakethanterare) som kan utnyttjas av hackare.
- Vi vill ha så små och snabba images som möjligt för att korta ner skalningstiden.

## Beslut

Vi använder .NET Chiseled Ubuntu Images som bas för våra produktionscontainers.

## Alternativ som utvärderades

- Standard ASP.NET Images
	- Fördelar: Innehåller alla verktyg för felsökning inuti containern.
	- Nackdelar: Stor storlek (ca 200MB+), större attackyta.

- Chiseled Images
	- Fördelar: Extremt liten storlek, saknar skal (bash/sh) vilket gör dem mycket svårare att attackera, körs som icke-root som standard.
	- Nackdelar: Svårare att felsöka inuti containern (man kan inte köra "exec").
	- Varför valdes det? Säkerheten i produktion prioriteras högre än enkel felsökning inuti containern.

## Konsekvenser

- Säkerhet: Drastiskt minskad attackyta.
- Drift: Kräver att vi litar på loggar och telemetri istället för att logga in i containern.

## Uppföljning
- Vi övervakar säkerhetsincidenter och ser till att inga försök att utnyttja containrarna lyckas.