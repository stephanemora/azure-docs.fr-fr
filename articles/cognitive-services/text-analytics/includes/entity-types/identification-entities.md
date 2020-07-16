---
title: Entités d’identification
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140919"
---
Cette catégorie d’entité comprend les informations financières et les formes officielles d’identification. Disponible à partir de la version `2019-10-01` du modèle. Les sous-types sont listés ci-dessous. 

### <a name="financial-account-identification"></a>Identification de compte financier

| Nom de sous-type               | Description                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Numéro de routage ABA        | Numéros de routage de transit ABA (American Banker Association).                  |
| Code SWIFT                 | Codes SWIFT pour les informations sur les instructions de paiement.                           |
| Carte de crédit                | Numéros de carte de crédit.                                                       |
| Numéro de compte bancaire international (IBAN)                  | Codes IBAN pour les informations sur les instructions de paiement.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identification propres aux gouvernements et pays/régions

> [!NOTE]
> Les entités financières et propres au pays suivantes ne sont pas retournées avec le paramètre `domain=phi` :
> * Numéros de passeport
> * Numéros d’identification fiscale

Les entités ci-dessous sont regroupées et listées par pays :

Argentine
* Numéro d’identité nationale (DNI) en Argentine

Australie
* Numéro de passeport en Australie
* Numéro d’identification fiscale en Australie
* Numéro de permis de conduire en Australie
* Numéro de compte médical en Australie
* Numéro de compte bancaire en Australie

Belgique
* Numéro national en Belgique

Brésil 
* Numéro d’entité légale (CNPJ) au Brésil
* Numéro CPF au Brésil
* Carte d’identité nationale (RG) au Brésil

Canada
* Numéro d’assurance sociale au Canada
* Numéro de permis de conduire au Canada
* Numéro de compte bancaire au Canada
* Numéro de passeport au Canada
* Numéro d’identification médicale personnelle (PHIN) au Canada
* Numéro de service médical au Canada

Chili
* Numéro de carte d’identité 

Chine
* Numéro de carte d’identité de résident (RPC) en Chine

Croatie
* Numéro de carte d’identité en Croatie
* Numéro d’identification personnelle (OIB) en Croatie

République tchèque
* Numéro d’identité personnelle en Tchéquie

Danemark
* Numéro d’identification personnelle au Danemark

Union européenne (UE)
* Numéro d’identification nationale dans l’Union européenne
* Numéro de passeport dans l’Union européenne
* Numéro de permis de conduire dans l’Union européenne
* Numéro de sécurité sociale (SSN) dans l’Union européenne (ou équivalent)
* Numéros d’identification fiscale UE (TIN)
* Numéro de carte de débit UE

Finlande
* Numéro d’identité nationale en Finlande
* Numéro de passeport en Finlande

France
* Carte nationale d’identité (CNI) en France
* Numéro de sécurité sociale (INSEE) en France
* Numéro de passeport en France
* Numéro de permis de conduire en France

Allemagne
* Numéro de carte d’identité en Allemagne
* Numéro de passeport en Allemagne
* Numéro de permis de conduire en Allemagne

Grèce 
* Numéro de carte d’identité nationale en Grèce

Hong Kong (R.A.S.)
* Numéro de carte d’identité à Hong Kong (R.A.S.)

Inde
* Numéro de compte permanent (PAN) en Inde
* Numéro d’identification unique (Aadhaar) en Inde

Indonésie
* Numéro de carte d’identité (KTP) en Indonésie

Irlande
* Numéro de service public personnel (PPS) en Irlande

Israël
* Numéro d’identité nationale en Israël
* Numéro de compte bancaire en Israël

Italie
* Numéro de permis de conduire en Italie

Japon
* Numéro d’inscription de résident au Japon
* Numéro de carte de résidence japonaise
* Numéro de permis de conduire au Japon
* Numéro d’assurance sociale (SIN)
* Numéro de passeport au Japon
* Numéro de compte bancaire au Japon

Malaisie
* Numéro de carte d’identité en Malaisie

Pays-Bas
* Numéro de service citoyen (BSN) au Pays-Bas

Nouvelle-Zélande
* Numéro du ministère de la santé en Nouvelle-Zélande

Norvège
* Numéro d’identité en Norvège

Philippines
* Numéro d’identité polyvalent unifié aux Philippines

Pologne
* Carte d’identité en Pologne
* Numéro d’identité nationale (PESEL) en Pologne
* Passeport en Pologne

Portugal 
* Numéro de carte de citoyen au Portugal

Arabie Saoudite
* Numéro national en Arabie saoudite

Singapour
* Numéro de carte d’identité d’inscription nationale (NRIC) à Singapour

Afrique du Sud
* Numéro d’identification en Afrique du Sud

Corée du Sud
* Numéro d’inscription de résident en Corée du Sud

Espagne 
* Numéro de sécurité sociale (SSN) en Espagne

Suède
* Numéro d’identité nationale en Suède
* Numéro de passeport en Suède

Taïwan 
* Numéro d’identité nationale à Taïwan
* Certificat de résident (ARC/TARC) à Taïwan
* Numéro de passeport à Taïwan

Thaïlande
* Code d’identification de population thaï

Royaume-Uni
* Numéro de passeport
* au Royaume-Uni Numéro de permis de conduire
* au Royaume-Uni Numéro d’assurance nationale (NINO)
* au Royaume-Uni Numéro du service de santé national
* au Royaume-Uni Numéro d’électeur
* États-Unis/Royaume-Uni Numéro de passeport

États-Unis
* États-Unis Numéro de sécurité sociale (SSN)
* États-Unis Numéro de permis de conduire
* États-Unis/Royaume-Uni Numéro de passeport
* États-Unis Numéro d’identification de contribuable individuel (ITIN)
* Numéro DEA (Drug Enforcement Agency)
* Numéro de compte bancaire aux USA
