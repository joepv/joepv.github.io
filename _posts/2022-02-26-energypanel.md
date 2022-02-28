---
layout: post
title:  "FIBARO Energy Panel explained"
description: "How to configure and use the Home Center 3 Energy Panel"
author: joep
categories: [ Home Center 3, Smart Meter, SolarEdge ]
tags: [ Lua, Quick App ]
image: assets/images/hc3-energypanel.png
beforetoc: "In this blog I'll show you what you need to configure to use the HC3 Energy Panel optimally."
toc: true
---

Intelligent energiebeheer is belangrijker dan ooit. Alleen al in Nederland is de verwachting dat de energietarieven voor een gemiddeld huishouden in 2022 met minimaal 700 tot 1000 euro stijgen! Ook heeft het kabinet besloten de salderingsregeling voor zonnepanelen in 2023 geleidelijk af te bouwen.

## Waarom intelligent energie beheer?

Energie management is niet alleen inzicht in het verbruik, maar ook het efficiënt benutten van energie.

Met de door mij ontwikkelde Quick App kun je de slimmer meter koppelen aan de FIBARO Home Center 3 om inzicht te krijgen in je energie verbruik/opbrengst. Ook kun je op fase niveau energie overschot uitlezen en hierop acteren door bijvoorbeeld je wasmachine te laten wassen op het moment dat je aan het terugleveren bent.

Hiermee zorg je ervoor dat je efficiënt energie benut in je woning en kun je het totale energieverbruik verminderen door de duurzame energie van je zonnepanelen optimaal te benutten.

Het monitoren van het energieverbruik draagt ​​bij aan:

- Vermindering van energieverbruik met 15%, soms zelfs 30%!
- Verlaging van energiekosten en/of energierekeningen.
- Factuur controle van de energieleveranciers met gevalideerde gegevens.
- CO2-reductie, wat een positief effect heeft op de opwarming van de aarde.
- Inzicht in verschillende soorten energieverbruik (actieve en reactieve energie).
- In kaart brengen van onjuist geconfigureerde apparaten.

## Hoe werkt dit in de Home Center 3?

De controller beheert de energie rapportages van alle verbonden apparaten die zowel zijn geconfigureerd als productie of consumptie. Dit maakt het mogelijk om zelf te monitoren en met automatisering controle te krijgen over je energie verbruik. Het systeem verzamelt real-time en historische verbruiksgegevens en geeft advies met een hoog betrouwbaarheidsniveau om een optimale balans te vinden in het energie gebruik.

## Mooi stuk theorie! En nu de praktijk!

Om inzicht te krijgen zorg je eerst dat je minimaal firmware 5.093 hebt geïnstalleerd op je Home Center 3 (Lite). Daarnaast heb je Z-Wave apparaten nodig met de klasse Power Meter en Energy Meter of een Quick App integratie voor niet Z-wave modules.

### Modules

Buiten de FIBARO modules zijn de Qubino Smart Meter Z-wave modules de meest geschikte oplossing om energie te meten. Deze apparaten kunnen actief energie verbruik meten van een enkele kamer of een complete groep. Ook zijn ze zeer geschikt om tussen de omvormer en het electriciteitsnet te plaatsen.

PLAATJE QUBINO

### Quick App P1 integratie

Kun je geen Qubino Smart Meter Z-wave modules plaatsen dan is het met mijn ontwikkelde Quick App nu mogelijk de gegevens uit je slimme meter te lezen. Door het gebruik van de HomeWizard P1 meter icm de Quick App kun je de energie consumptie en productie direct uitlezen op de slimme meter om het Energy Panel (monitoring) te voeden en de real-time gegevens gebruiken voor automatiseringen in Lua scenes/Quick Apps.

PLAATJE DEVICES

## Energie Paneel

Als je een Qubino Smart Meter Z-wave module hebt geplaatst tussen je thuisnetwerk en het electriciteitsnet, of als je de Wi-Fi P1 meter Quick App gebruikt dan zijn dit hoofd verbuiksmeters. Door deze toe te voegen als Main energy meters in de algemene opties van de Home Center 3 zal deze automatisch het resterende stroomverbruik, dat niet is gemeten door aangesloten Z-Wave apparaten, berekenen. Dit wordt weergeven in het Energie Paneel als rest. Hierdoor krijg je een compleet beeld van je energieverbruik in het Energie Paneel.

Als je een Qubino Smart Meter Z-wave module hebt geplaatst tussen je omvormer en het electriciteitsnet, of als je bijvoorbeeld de SolarEdge Monitoring Quick App gebruikt dan zijn deze energie meters geconfigureerd als apparaten die productie rapporteren. Dit wordt in het Energie Paneel weergegeven als productie en het paneel zal dan de energie balans en kosten uitrekenen!

PLAATJE ENERGIE PANEEL

### Uitleg voor ontwikkelaars

slide 29