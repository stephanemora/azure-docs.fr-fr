---
title: Entités nommées pour les informations personnelles
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749991"
---
> [!NOTE]
> Pour détecter des informations médicales protégées, utilisez le paramètre `domain=phi` et la version du modèle `2020-04-01` (ou une version ultérieure).
>
> Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Les catégories d’entité suivantes sont retournées lorsque vous envoyez des requêtes au point de terminaison `/v3.1-preview.3/entities/recognition/pii`.


| Category   |  Description                          |
|------------|-------------|
| [Person](#category-person)      |  Noms des personnes.  |
| [PersonType](#category-persontype) | Type de tâche ou rôle d’une personne. |
| [Numéro de téléphone](#category-phonenumber) |Numéros de téléphone (Numéros de téléphone américains et européens uniquement). |
| [Organisation](#category-organization) |  Les sociétés, les groupes, les organismes gouvernementaux et d’autres organisations.  |
| [Adresse](#category-address) | Adresses postales complètes.  |
| [E-mail](#category-email) | Adresses e-mail.   |
| [URL](#category-url) | URL vers des sites web.  |
| [IP](#category-ip) | Adresses IP du réseau.  |
| [DateTime](#category-datetime) | Dates et heures du jour. | 
| [Quantité](#category-quantity) | Nombres et quantités numériques.  |
| [Informations Azure](#azure-information) | Informations Azure identifiables, telles que les informations d’authentification.  |
| [Identification](#identification) | Identification propre à l’état financier et au pays.  |

### <a name="category-person"></a>Catégorie : Personne

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Personne

    :::column-end:::
    :::column span="2":::
        **Détails**

        Noms des personnes. Également retourné avec `domain=phi`.
      
    :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Catégorie : PersonType

Cette catégorie contient l’entité suivante :


:::row:::
    :::column span="":::
        **Entité**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Détails**

        Type de tâche ou rôle d’une personne.
      
    :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Catégorie : PhoneNumber

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Détails**

        Numéros de téléphone (Numéros de téléphone américains et européens uniquement). Également retourné avec `domain=phi`.
      
    :::column-end:::
:::row-end:::


### <a name="category-organization"></a>Catégorie : Organisation

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Organisation

    :::column-end:::
    :::column span="2":::
        **Détails**

        Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Médecine

    :::column-end:::
    :::column span="2":::
        **Détails**

        Sociétés et groupes médicaux.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Bourse

    :::column-end:::
    :::column span="2":::

        Groupes de bourse. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sports

    :::column-end:::
    :::column span="2":::

        Les organisations liées au sport.
      
    :::column-end:::

:::row-end:::


### <a name="category-address"></a>Catégorie : Adresse

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        Adresse

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresse postale complète.
      
    :::column-end:::
:::row-end:::

### <a name="category-email"></a>Catégorie : E-mail

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresses e-mail.
      
    :::column-end:::
:::row-end:::

### <a name="category-url"></a>Catégorie : URL

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        URL

    :::column-end:::
    :::column span="2":::
        **Détails**

        URL vers des sites web. 
      
    :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Catégorie : IP

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        IP

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresses IP du réseau. 
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Catégorie : DateTime

Cette catégorie contient les entités suivantes :

:::row:::
    :::column span="":::
        **Entité**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Détails**

        Dates et heures du jour. 
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Date

    :::column-end:::
    :::column span="2":::
        **Détails**

        Dates du calendrier.
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Catégorie : Quantité

Cette catégorie contient les entités suivantes :

:::row:::
    :::column span="":::
        **Entité**

        Quantité

    :::column-end:::
    :::column span="2":::
        **Détails**

        Nombres et quantités numériques.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sous-catégories

L’entité de cette catégorie peut contenir les sous-catégories suivantes.

:::row:::
    :::column span="":::
        **Sous-catégorie d’entité**

        Age

    :::column-end:::
    :::column span="2":::
        **Détails**

        Ages.
      
    :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informations Azure

Ces catégories d’entités englobent les informations Azure identifiables, notamment les informations d’authentification et les chaînes de connexion. Non retourné avec le paramètre `domain=phi`.

:::row:::
    :::column span="":::
        **Entité**

        Clé d’autorisation Azure DocumentDB 

    :::column-end:::
    :::column span="2":::
        **Détails**

        Clé d’autorisation d’un serveur Azure CosmosDB.   
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion à la base de données Azure IAAS et chaîne de connexion Azure SQL

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour une base de données IaaS (infrastructure as a service) Azure et chaîne de connexion SQL.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure SQL

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour une base de données dans Azure SQL Database.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure IoT  

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour Azure IoT. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mot de passe de paramètre de publication Azure  

    :::column-end:::
    :::column span="2":::

        Mot de passe pour les paramètres de publication Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure Cache pour Redis 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour un cache Redis
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAAS 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour SaaS (software as a service) Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour Azure Service Bus.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Clé du compte de Stockage Azure 

    :::column-end:::
    :::column span="2":::

       Clé d’un compte de stockage Azure. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Clé du compte de Stockage Azure (générique)

    :::column-end:::
    :::column span="2":::

       Clé générique d’un compte de stockage Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion SQL Server 

    :::column-end:::
    :::column span="2":::

       Chaîne de connexion pour un ordinateur exécutant SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
