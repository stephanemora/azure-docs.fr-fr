---
title: Entités d’identification
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: a74c0cad971389168d643c9504f5bb809438a1ea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097774"
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

        Numéros de routage de transit ABA (American Banker Association). Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `ABARoutingNumber` au `pii-categories` paramètre. `ABARoutingNumber` est également retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`
      
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

        Codes SWIFT pour les informations sur les instructions de paiement. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `SWIFTCode` au `pii-categories` paramètre. `SWIFTCode` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Numéros de carte de crédit. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `CreditCardNumber` au `pii-categories` paramètre. `CreditCardNumber` est retourné dans la réponse de l’API si elle est détectée.

    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        Codes IBAN pour les informations sur les instructions de paiement. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `InternationlBankingAccountNumber` au `pii-categories` paramètre. `InternationlBankingAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Détails** Également retourné avec `domain=phi`.
        
        Pour accéder à cette catégorie d’entité, ajoutez `ARNationalIdentityNumber` au `pii-categories` paramètre. `ARNationalIdentityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Autriche

:::row:::
    :::column span="":::
        **Entité**

        Carte d’identité en Autriche

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `ATIdentityCard` au `pii-categories` paramètre. `ATIdentityCard` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale autrichien

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ATTaxIdentificationNumber` au `pii-categories` paramètre. `ATTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Autriche

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ATValueAddedTaxNumber` au `pii-categories` paramètre. `ATValueAddedTaxNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australie

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire en Australie

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `AUDriversLicenseNumber` au `pii-categories` paramètre. `AUDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’entreprise en Australie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `AUBusinessNumber` au `pii-categories` paramètre. `AUBusinessNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de société en Australie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `AUCompanyNumber` au `pii-categories` paramètre. `AUCompanyNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire en Australie  

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `AUDriversLicense` au `pii-categories` paramètre. `AUDriversLicense` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de compte médical en Australie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `AUMedicalAccountNumber` au `pii-categories` paramètre. `AUMedicalAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en Australie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ATPassportNumber` au `pii-categories` paramètre. `ATPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale en Australie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ATTaxIdentificationNumber` au `pii-categories` paramètre. `ATTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belgique

:::row:::
    :::column span="":::
        **Entité**

        Numéro national en Belgique

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `BENationalNumber` au `pii-categories` paramètre. `BENationalNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Belgique

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `BEValueAddedTaxNumber` au `pii-categories` paramètre. `BEValueAddedTaxNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brésil 

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’entité légale (CNPJ) au Brésil

        

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `BRLegalEntityNumber` au `pii-categories` paramètre. `BRLegalEntityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro CPF au Brésil

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `BRCPFNumber` au `pii-categories` paramètre. `BRCPFNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Carte d’identité nationale (RG) au Brésil

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `BRNationalIDRG` au `pii-categories` paramètre. `BRNationalIDRG` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire au Canada

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `CABankAccountNumber` au `pii-categories` paramètre. `CABankAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire au Canada

    :::column-end:::

    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `CADriversLicenseNumber` au `pii-categories` paramètre. `CADriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de service médical au Canada

        
    :::column-end:::

    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `CAHealthServiceNumber` au `pii-categories` paramètre. `CAHealthServiceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport au Canada

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `CAPassportNumber` au `pii-categories` paramètre. `CAPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification médicale personnelle (PHIN) au Canada

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `CAPersonalHealthIdentification` au `pii-categories` paramètre. `CAPersonalHealthIdentification` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’assurance sociale au Canada

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `CASocialInsuranceNumber` au `pii-categories` paramètre. `CASocialInsuranceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chili 

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité au Chili

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `CLIdentityCardNumber` au `pii-categories` paramètre. `CLIdentityCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Chine

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité de résident (RPC) en Chine

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `CNResidentIdentityCardNumber` au `pii-categories` paramètre. `CNResidentIdentityCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Union européenne (UE)

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte de débit UE

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `EUDebitCardNumber` au `pii-categories` paramètre. `EUDebitCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire dans l’Union européenne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `EUDriversLicenseNumber` au `pii-categories` paramètre. `EUDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordonnées GPU de l’Union européenne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `EUGPSCoordinates` au `pii-categories` paramètre. `EUGPSCoordinates` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification nationale dans l’Union européenne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `EUNationalIdentificationNumber` au `pii-categories` paramètre. `EUNationalIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport dans l’Union européenne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `EUPassportNumber` au `pii-categories` paramètre. `EUPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de sécurité sociale (NIR) dans l’Union européenne (ou équivalent)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `EUSocialSecurityNumber` au `pii-categories` paramètre. `EUSocialSecurityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale UE (TIN)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `EUTaxIdentificationNumber` au `pii-categories` paramètre. `EUTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>France

:::row:::
    :::column span="":::
        **Entité**

        Numéro de permis de conduire en France

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `FRDriversLicenseNumber` au `pii-categories` paramètre. `FRDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’assurance maladie en France

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `FRHealthInsuranceNumber` au `pii-categories` paramètre. `FRHealthInsuranceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Carte nationale d’identité (CNI) en France

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `FRNationalID` au `pii-categories` paramètre. `FRNationalID` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en France

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `FRPassportNumber` au `pii-categories` paramètre. `FRPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de sécurité sociale (INSEE) en France

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `FRSocialSecurityNumber` au `pii-categories` paramètre. `FRSocialSecurityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale en France (numéro SPI)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `FRTaxIdentificationNumber` au `pii-categories` paramètre. `FRTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en France

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `FRValueAddedTaxNumber` au `pii-categories` paramètre. `FRValueAddedTaxNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Allemagne

:::row:::
    :::column span="":::
        **Entité**

        Numéro de permis de conduire en Allemagne

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `DEDriversLicenseNumber` au `pii-categories` paramètre. `DEDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de carte d’identité en Allemagne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `DEIdentityCardNumber` au `pii-categories` paramètre. `DEIdentityCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport en Allemagne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `DEPassportNumber` au `pii-categories` paramètre. `DEPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale en Allemagne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `DETaxIdentificationNumber` au `pii-categories` paramètre. `DETaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Allemagne

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `DEValueAddedNumber` au `pii-categories` paramètre. `DEValueAddedNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>Hong Kong (R.A.S.)

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité à Hong Kong (R.A.S.)

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `HKIdentityCardNumber` au `pii-categories` paramètre. `HKIdentityCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hongrie

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification personnelle en Hongrie

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `HUPersonalIdentificationNumber` au `pii-categories` paramètre. `HUPersonalIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification fiscale en Hongrie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `HUTaxIdentificationNumber` au `pii-categories` paramètre. `HUTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Hongrie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `HUValueAddedNumber` au `pii-categories` paramètre. `HUValueAddedNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Inde

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte permanent (PAN) en Inde

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `INPermanentAccount` au `pii-categories` paramètre. `INPermanentAccount` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification unique (Aadhaar) en Inde

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `INUniqueIdentificationNumber` au `pii-categories` paramètre. `INUniqueIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésie

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité (KTP) en Indonésie

    :::column-end:::
    :::column span="2":::

        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `IDIdentityCardNumber` au `pii-categories` paramètre. `IDIdentityCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlande

:::row:::
    :::column span="":::
        **Entité**

        Numéro de service public personnel (PPS) en Irlande

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `IEPersonalPublicServiceNumber` au `pii-categories` paramètre. `IEPersonalPublicServiceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Numéro de service public personnel (PPS) en Irlande v2

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `IEPersonalPublicServiceNumberV2` au `pii-categories` paramètre. `IEPersonalPublicServiceNumberV2` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israël

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité nationale en Israël

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `ILNationalID` au `pii-categories` paramètre. `ILNationalID` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de compte bancaire en Israël

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ILBankAccountNumber` au `pii-categories` paramètre. `ILBankAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italie

:::row:::
    :::column span="":::
        **Entité**

        Numéro de permis de conduire en Italie

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `ITDriversLicenseNumber` au `pii-categories` paramètre. `ITDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Code fiscal en Italie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ITFiscalCode` au `pii-categories` paramètre. `ITFiscalCode` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de TVA en Italie

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ITValueAddedTaxNumber` au `pii-categories` paramètre. `ITValueAddedTaxNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japon

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire au Japon

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `JPBankAccountNumber` au `pii-categories` paramètre. `JPBankAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire au Japon

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `JPDriversLicenseNumber` au `pii-categories` paramètre. `JPDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japon « Mon numéro » (personnel)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `JPMyNumberPersonal` au `pii-categories` paramètre. `JPMyNumberPersonal` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japon « Mon numéro » (entreprise)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `JPMyNumberCorporate` au `pii-categories` paramètre. `JPMyNumberCorporate` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’inscription de résident au Japon

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ITValueAddedTaxNumber` au `pii-categories` paramètre. `ITValueAddedTaxNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de carte de résidence au Japon

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `JPResidenceCardNumber` au `pii-categories` paramètre. `JPResidenceCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’assurance sociale (SIN) au Japon

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `JPSocialInsuranceNumber` au `pii-categories` paramètre. `JPSocialInsuranceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport au Japon

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `JPPassportNumber` au `pii-categories` paramètre. `JPPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxembourg

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification nationale au Luxembourg (personnes physiques)

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `LUNationalIdentificationNumberNatural` au `pii-categories` paramètre. `LUNationalIdentificationNumberNatural` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro d’identification nationale au Luxembourg (personnes morales)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `LUNationalIdentificationNumberNonNatural` au `pii-categories` paramètre. `LUNationalIdentificationNumberNonNatural` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malte

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité à Malte

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `MTIdentityCardNumber` au `pii-categories` paramètre. `MTIdentityCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale à Malte

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `MTTaxIDNumber` au `pii-categories` paramètre. `MTTaxIDNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nouvelle-Zélande

:::row:::
    :::column span="":::
        **Entité**

        Numéro de compte bancaire en Nouvelle-Zélande

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `NZBankAccountNumber` au `pii-categories` paramètre. `NZBankAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de permis de conduire en Nouvelle-Zélande

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `NZDriversLicenseNumber` au `pii-categories` paramètre. `NZDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro Inland Revenue Department (IRD) en Nouvelle-Zélande

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `NZInlandRevenueNumber` au `pii-categories` paramètre. `NZInlandRevenueNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro du ministère de la santé en Nouvelle-Zélande

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `NZMinistryOfHealthNumber` au `pii-categories` paramètre. `NZMinistryOfHealthNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numéro de protection sociale (Social Welfare) en Nouvelle-Zélande

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `NZSocialWelfareNumber` au `pii-categories` paramètre. `NZSocialWelfareNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Philippines

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité polyvalent unifié aux Philippines

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `PHUnifiedMultiPurposeIDNumber` au `pii-categories` paramètre. `PHUnifiedMultiPurposeIDNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte de citoyen au Portugal

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `PTCitizenCardNumber` au `pii-categories` paramètre. `PTCitizenCardNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numéros d’identification fiscale au Portugal

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `PTTaxIdentificationNumber` au `pii-categories` paramètre. `PTTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapour

:::row:::
    :::column span="":::
        **Entité**

        Numéro de carte d’identité d’inscription nationale (NRIC) à Singapour

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `PTTaxIdentificationNumber` au `pii-categories` paramètre. `PTTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Afrique du Sud

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification en Afrique du Sud

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `ZAIdentificationNumber` au `pii-categories` paramètre. `ZAIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Corée du Sud

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’inscription de résident en Corée du Sud

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `KRResidentRegistrationNumber` au `pii-categories` paramètre. `KRResidentRegistrationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Espagne

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identification nationale en Espagne

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `ESDNI` au `pii-categories` paramètre. `ESDNI` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de sécurité sociale (SSN) en Espagne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ESSocialSecurityNumber` au `pii-categories` paramètre. `ESSocialSecurityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéros d’identification fiscale en Espagne

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `ESTaxIdentificationNumber` au `pii-categories` paramètre. `ESTaxIdentificationNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suisse

:::row:::
    :::column span="":::
        **Entité**

        Numéro d'assurance sociale (AVS) en Suisse

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `CHSocialSecurityNumber` au `pii-categories` paramètre. `CHSocialSecurityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taïwan 

:::row:::
    :::column span="":::
        **Entité**

        Numéro d’identité nationale à Taïwan

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `TWNationalID` au `pii-categories` paramètre. `TWNationalID` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificat de résident (ARC/TARC) à Taïwan

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `TWResidentCertificate` au `pii-categories` paramètre. `TWResidentCertificate` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numéro de passeport à Taïwan

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `TWPassportNumber` au `pii-categories` paramètre. `TWPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Royaume-Uni

:::row:::
    :::column span="":::
        **Entité**

        au Royaume-Uni Numéro de permis de conduire

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `UKDriversLicenseNumber` au `pii-categories` paramètre. `UKDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro d’électeur

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `UKNationalInsuranceNumber` au `pii-categories` paramètre. `UKNationalInsuranceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro du service de santé national (NHS)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `UKNationalHealthNumber` au `pii-categories` paramètre. `UKNationalHealthNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro d’assurance nationale (NINO)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `UKNationalInsuranceNumber` au `pii-categories` paramètre. `UKNationalInsuranceNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni ou aux États-Unis Numéro de passeport

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `USUKPassportNumber` au `pii-categories` paramètre. `USUKPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       au Royaume-Uni Numéro de référence fiscal unique (UTR)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `UKUniqueTaxpayerNumber` au `pii-categories` paramètre. `UKUniqueTaxpayerNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>États-Unis

:::row:::
    :::column span="":::
        **Entité**

        États-Unis Numéro de sécurité sociale (SSN)

    :::column-end:::
    :::column span="2":::
        **Détails**

        Pour accéder à cette catégorie d’entité, ajoutez `USSocialSecurityNumber` au `pii-categories` paramètre. `USSocialSecurityNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro de permis de conduire

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `USDriversLicenseNumber` au `pii-categories` paramètre. `USDriversLicenseNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       aux États-Unis ou au Royaume-Uni Numéro de passeport

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `USUKPassportNumber` au `pii-categories` paramètre. `USUKPassportNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro d’identification de contribuable individuel (ITIN)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `USIndividualTaxpayerIdentification` au `pii-categories` paramètre. `USIndividualTaxpayerIdentification` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro DEA (Drug Enforcement Agency)

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `DrugEnforcementAgencyNumber` au `pii-categories` paramètre. `DrugEnforcementAgencyNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       États-Unis Numéro de compte bancaire

    :::column-end:::
    :::column span="2":::

        Pour accéder à cette catégorie d’entité, ajoutez `USBankAccountNumber` au `pii-categories` paramètre. `USBankAccountNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
