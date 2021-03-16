---
title: Entités d’identification
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750622"
---
### <a name="financial-account-identification"></a>Identification de compte financier

Cette catégorie d’entité comprend les informations financières et les formes officielles d’identification.

#### <a name="category-aba-routing-number"></a>Catégorie : Numéro de routage ABA

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Numéro de routage ABA

    :::column-end:::
    :::column span="2":::
        **Détails**

        Numéros de routage de transit ABA (American Banker Association).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Catégorie : Code SWIFT

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Code Swift

    :::column-end:::
    :::column span="2":::
        **Détails**

        Codes SWIFT pour les informations sur les instructions de paiement.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Catégorie : Carte de crédit

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Carte de crédit

    :::column-end:::
    :::column span="2":::
        **Détails**

        Numéros de carte de crédit. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Catégorie : Numéro de compte bancaire international (IBAN) 

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Carte de crédit

    :::column-end:::
    :::column span="2":::
        **Détails**

        Codes IBAN pour les informations sur les instructions de paiement.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identification propre aux gouvernements et pays/régions

> [!NOTE]
> Les entités financières et propres au pays suivantes ne sont pas retournées avec le paramètre `domain=phi` :
> * Numéros de passeport
> * Numéros d’identification fiscale

Les entités suivantes sont regroupées et listées par pays :

#### <a name="argentina"></a>Argentine

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité nationale (DNI) en Argentine

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Autriche

:::row:::
    :::column span="":::
        **Entité**

        Carte d’identité en Autriche

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale autrichien

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Autriche

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australie

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire en Australie

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’entreprise en Australie

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro de société en Australie

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire en Australie  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de compte médical en Australie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en Australie

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en Australie

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale en Australie

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Belgique

:::row:::
    :::column span="":::
        **Entité**

        Numéro national en Belgique

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Belgique

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brésil 

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’entité légale (CNPJ) au Brésil

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro CPF au Brésil

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Carte d’identité nationale (RG) au Brésil

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire au Canada

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire au Canada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de service médical au Canada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport au Canada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification médicale personnelle (PHIN) au Canada

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’assurance sociale au Canada

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chili 

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité au Chili

    :::column-end:::
:::row-end:::

#### <a name="china"></a>Chine

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité de résident (RPC) en Chine

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Union européenne (UE)

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte de débit UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire dans l’Union européenne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification nationale dans l’Union européenne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport dans l’Union européenne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de sécurité sociale (NIR) dans l’Union européenne (ou équivalent)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale UE (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordonnées GPS UE

    :::column-end:::
:::row-end:::

#### <a name="france"></a>France

:::row:::
    :::column span="":::
        **Entité**

        Numéro de permis de conduire en France

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’assurance maladie en France

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Carte nationale d’identité (CNI) en France

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en France

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de sécurité sociale (INSEE) en France

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale en France (numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en France

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Allemagne

:::row:::
    :::column span="":::
        **Entité**

        Numéro de permis de conduire en Allemagne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de carte d’identité en Allemagne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en Allemagne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale en Allemagne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Allemagne

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hong Kong (R.A.S.)

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité à Hong Kong (R.A.S.)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hongrie

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification personnelle en Hongrie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale en Hongrie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Hongrie

    :::column-end:::
:::row-end:::

#### <a name="india"></a>Inde

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte permanent (PAN) en Inde

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification unique (Aadhaar) en Inde

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésie

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité (KTP) en Indonésie

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlande

:::row:::
    :::column span="":::
        **Entité**

        Numéro de service public personnel (PPS) en Irlande

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israël

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité nationale en Israël

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de compte bancaire en Israël

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italie

:::row:::
    :::column span="":::
        **Entité**

        Numéro de permis de conduire en Italie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Code fiscal en Italie

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Italie

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japon

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire au Japon

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire au Japon

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japon « Mon numéro » (personnel)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japon « Mon numéro » (entreprise)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’inscription de résident au Japon

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de carte de résidence au Japon

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’assurance sociale (SIN) au Japon

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport au Japon

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxembourg

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification nationale au Luxembourg (personnes physiques)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification nationale au Luxembourg (personnes morales)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malte

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité à Malte

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale à Malte

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nouvelle-Zélande

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire en Nouvelle-Zélande

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire en Nouvelle-Zélande

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro Inland Revenue Department (IRD) en Nouvelle-Zélande

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro du ministère de la santé en Nouvelle-Zélande

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numéro de protection sociale (Social Welfare) en Nouvelle-Zélande

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Philippines

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité polyvalent unifié aux Philippines

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte de citoyen au Portugal

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numéros d’identification fiscale au Portugal

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapour

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité d’inscription nationale (NRIC) à Singapour

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Afrique du Sud

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification en Afrique du Sud

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Corée du Sud

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’inscription de résident en Corée du Sud

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Espagne

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification nationale en Espagne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de sécurité sociale (SSN) en Espagne

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale en Espagne

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suisse

:::row:::
    :::column span="":::
        **Entité**

        Numéro d'assurance sociale (AVS) en Suisse

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taïwan 

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité nationale à Taïwan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificat de résident (ARC/TARC) à Taïwan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport à Taïwan

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Royaume-Uni

:::row:::
    :::column span="":::
        **Entité**

        au Royaume-Uni Numéro de permis de conduire

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro d’électeur

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro du service de santé national (NHS)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro d’assurance nationale (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni ou aux États-Unis Numéro de passeport

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro de référence fiscal unique (UTR)

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>États-Unis

:::row:::
    :::column span="":::
        **Entité**

        États-Unis Numéro de sécurité sociale (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro de permis de conduire

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       aux États-Unis ou au Royaume-Uni Numéro de passeport

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro d’identification de contribuable individuel (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro DEA (Drug Enforcement Agency)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro de compte bancaire

    :::column-end:::
:::row-end:::
