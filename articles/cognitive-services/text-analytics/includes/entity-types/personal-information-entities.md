---
title: Entités nommées pour les informations personnelles
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086919"
---
## <a name="personal-information-entity-types"></a>Types d’entités d’informations personnelles :

### <a name="person"></a>Person
Reconnaître les noms de personnes dans le texte.

Langages :
* Préversion publique : `English`

| Nom de sous-type | Description                                               | Disponible à partir de la version du modèle |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/A          | Noms reconnus, par exemple `Bill Gates`, `Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organization  

Reconnaître les organisations, sociétés, agences, entreprises, clubs et autres groupes de personnes.

Langages : 

* Préversion publique : `English`

| Nom de sous-type | Description                                                                                       | Disponible à partir de la version du modèle|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/A          | les organisations, par exemple `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Numéro de téléphone

Numéros de téléphone (Numéros de téléphone américains uniquement). 

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                                    | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | Numéros de téléphone américains, par exemple `(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

Adresse de messagerie. 

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                                      | Disponible à partir de la version du modèle |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | Adresse e-mail, par exemple `support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

URL Internet

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                                          | Disponible à partir de la version du modèle |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | URL de sites web, par exemple `https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Adresse IP

Adresse de protocole Internet

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                              | Disponible à partir de la version du modèle |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | Adresse réseau, par exemple `10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Quantité 

Quantités numériques

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                   | Disponible à partir de la version du modèle |
|--------------|-------------------------------|----------------------------------------|
| Age          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Entités de date et d'heure

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                   | Disponible à partir de la version du modèle |
|--------------|-------------------------------|----------------------------------------|
| Date         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Coordonnées GPS UE

 Coordonnées GPS de lieux situés au sein de l’Union européenne. 

Langages :

* Préversion publique : `English`

| Nom de sous-type | Description                               | Disponible à partir de la version du modèle |
|--------------|-------------------------------------------|----------------------------------------|
| N/A          | Coordonnées GPS au sein de l’Union européenne | `2019-10-01`                           |

### <a name="azure-information"></a>Informations Azure

Informations Azure identifiables, comme des informations d’authentification et des chaînes de connexion. 

* Disponible à partir de la version `2019-10-01` du modèle.

Langages :

* Préversion publique : `English`

| Nom de sous-type                          | Description                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Clé d’autorisation Azure DocumentDB             | Clé d’autorisation d’un serveur Azure DocumentDB.                           |
| Chaîne de connexion de base de données Azure IAAS | Chaîne de connexion d’une base de données IaaS (infrastructure as a service) Azure. |
| Chaîne de connexion Azure SQL           | Chaîne de connexion d’une base de données Azure SQL.                                |
| Chaîne de connexion Azure IoT           | Chaîne de connexion pour Azure Internet des objets (IoT).                        |
| Mot de passe de paramètre de publication Azure        | Mot de passe pour les paramètres de publication Azure.                                        |
| Chaîne de connexion Azure Cache pour Redis   | Chaîne de connexion pour Azure Cache pour Redis.                             |
| Azure SAAS                             | Chaîne de connexion pour SaaS (software as a service) Azure.                     |
| Chaîne de connexion Azure Service Bus   | Chaîne de connexion pour Azure Service Bus.                                |
| Clé du compte de Stockage Azure             | Clé d’un compte de stockage Azure.                                   |
| Clé du compte de Stockage Azure (générique)   | Clé générique d’un compte de stockage Azure.                           |
| Chaîne de connexion SQL Server          | Chaîne de connexion d’un serveur SQL Server.                                         |

### <a name="identification"></a>Identification

* Disponible à partir de la version `2019-10-01` du modèle.

Langages :

* Préversion publique : `English`

#### <a name="financial-account-identification"></a>Identification de compte financier

| Nom de sous-type               | Description                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Numéros de routage ABA        | Numéros de routage de transit ABA (American Banker Association).                  |
| Code SWIFT                 | Codes SWIFT pour les informations sur les instructions de paiement.                           |
| Carte de crédit                | Numéros de carte de crédit.                                                       |
| Code IBAN                  | Codes IBAN pour les informations sur les instructions de paiement.                            |

#### <a name="government-and-country-specific-identification"></a>Identification propres aux gouvernements et pays

Les entités ci-dessous sont regroupées et listées par pays :

Argentine
* Numéro d’identité nationale (DNI)

Australie
* Numéro d’identification fiscale 
* Numéro de permis de conduire
* Numéro de passeport
* Numéro de compte médical
* Numéros de comptes bancaires (par exemple, comptes chèques, comptes d’épargne et comptes courants)

Belgique
* Numéro national

Brésil
* Numéro d’entité légale (CNPJ)
* Numéro CPF
* Carte d’identité nationale (RG)

Canada
* Numéro de passeport
* Numéro de permis de conduire
* Numéro d’assurance maladie
* Numéro d’identification d’intégrité personnelle (PHIN)
* Numéro de sécurité sociale
* Numéros de comptes bancaires (par exemple, comptes chèques, comptes d’épargne et comptes courants)

Chili
* Numéro de carte d’identité 

Chine
* Numéro de carte d’identité
* Numéro de carte d’identité de résident (PRC)

Croatie
* Numéro de carte d’identité
* Numéro d’identification personnelle (OIB)

République tchèque
* Numéro de carte d’identité nationale

Danemark
* Numéro d’identité personnelle

Union européenne (UE)
* Numéro d’identité nationale
* Numéro de passeport
* Numéro de permis de conduire
* Numéro de sécurité sociale (ou équivalent)
* Numéros d’identification fiscale UE (TIN)
* Numéro de carte de débit UE

Finlande
* Numéro d’identité nationale
* Numéro de passeport

France
* Carte nationale d’identité (CNI)
* Numéro de sécurité sociale (INSEE)
* Numéro de passeport
* Numéro de permis de conduire

Allemagne
* Numéro de carte d’identité
* Numéro de passeport
* Numéro de permis de conduire

Grèce 
* Numéro de carte d’identité nationale

Hong Kong (R.A.S.)
* Numéro de carte d’identité (HKID)

Inde
* Numéro de compte permanent (PAN)
* Numéro d’identité unique (Aadhaar)

Indonésie
* Numéro de carte d’identité (KTP)

Irlande
* Numéro PPS (Personal Public Service)

Israël
* Identité nationale
* Numéros de comptes bancaires (par exemple, comptes chèques, comptes d’épargne et comptes courants)

Italie
* Numéro de permis de conduire

Japon
* Numéro d’enregistrement de résident
* Numéro de carte de résidence
* Numéro de permis de conduire
* Numéro d’assurance sociale (SIN)
* Numéro de passeport
* Numéros de comptes bancaires (par exemple, comptes chèques, comptes d’épargne et comptes courants)

Malaisie
* Numéro de carte d’identité

Pays-bas
* Numéro de service citoyen (BSN)

Nouvelle-Zélande
* Numéro du Ministère de la santé

Norvège
* Numéro de carte d’identité

Philippines
* Numéro d’identité polyvalent unifié

Pologne
* Numéro de carte d’identité
* Identité nationale (PESEL)
* Numéro de passeport

Portugal 
* Numéro de carte de citoyen

Arabie Saoudite
* Identité nationale

Singapour
* Numéro de carte d’identité d’enregistrement national (NRIC)

Afrique du Sud
* Numéro d’identité
* Numéro d’enregistrement de résident

Corée du Sud
* Numéro d’enregistrement de résident

Espagne 
* Numéro de sécurité sociale (SSN)

Suède
* Identité nationale
* Numéro de passeport

Taïwan 
* Identité nationale
* Numéro de certificat de résident (ARC/TARC)
* Numéro de passeport

Thaïlande
* Code d’identification de population

United Kingdom
* Numéro de passeport
* Numéro de permis de conduire
* Numéro d’assurance nationale (NINO)
* Numéro du service de santé national (NHS)

États-Unis
* Numéro de sécurité sociale (SSN)
* Numéro de permis de conduire
* Numéro de passeport
* Numéro d’électeur
* Numéro d’identification fiscale individuel (ITIN)
* Numéro DEA (Drug Enforcement Agency)
* Numéros de comptes bancaires (par exemple, comptes chèques, comptes d’épargne et comptes courants)
