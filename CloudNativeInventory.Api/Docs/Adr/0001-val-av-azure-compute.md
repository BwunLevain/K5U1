## ADR 0001-val-av-azure-compute

Vivienne fick som uppdrag att flytta ett äldre system till molnet.

## Metadata

- Datum: 2026-05-04
- Status: Accepterat
- Beslutsfattare: Vivienne Bengtsson
- Relaterat (länk till ticket/issue/spike):

## Kontext (problemet som ska lösas)

- Flytta ett äldre system till molnet.
- Systemet måste kunna klara av varierande trafikmönster.

## Beslut

- Jag väljer att använda Azure Container Apps för att köra våra mikrotjänster. Detta ger oss möjlighet att enkelt skala upp och ner baserat på trafik, vilket är viktigt för att hantera de tunga trafiktopparna runt löning och den låga trafiken på helgerna.

## Alternativ som utvärderades

1. Azure App Service
2. Azure Container Apps

## Alternativ som utvärderades

1. Azure App Service
	- Fördelar 
		- Enkel att använda och integrera med andra Azure-tjänster.
		- Stöd för flera programmeringsspråk och ramverk.
	- Nackdelar
		- Begränsad skalbarhet jämfört med Container Apps.
		- Mindre flexibilitet i konfiguration och anpassning.
	- Varför valdes det bort?
		- Valet föll på Azure Container Apps på grund av dess bättre skalbarhet och flexibilitet, vilket är viktigt för att hantera de varierande trafikmönstren.

2. Azure Container Apps
	- Fördelar
		- Hög skalbarhet och flexibilitet.
		- Stöd för mikrotjänster och containerbaserade applikationer.
	- Nackdelar
		- Kan vara mer komplex att konfigurera och underhålla.
	- Varför valdes det?
		- Valet gjordes på grund av dess förmåga att hantera både höga trafiktoppar och låg trafikperioder effektivt.

## Konsekvenser

- Tekniska konsekvenser: Kräver kunskap om Docker, vi sparar pengar när det skalas till noll
- Drift & support: Kräver att teamet hanterar och underhåller containerbaserade applikationer, inklusive övervakning och felsökning av containeriserade tjänster.
- Säkerhet & compliance: Kräver att teamet hanterar säkerheten för containerbaserade applikationer, inklusive uppdateringar och patchning av containerbilder.
- Kostnad: Dynamisk kostnad baserat på trafik, potentiellt lägre kostnader under lågtrafikperioder.
- Teamkonsekvenser: Kräver att teamet lär sig och anpassar sig till en ny plattform, vilket kan innebära en inlärningskurva.

## Uppföljning

- Ej testad än