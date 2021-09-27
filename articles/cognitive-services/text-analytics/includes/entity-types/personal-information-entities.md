---
title: Entités nommées pour les informations personnelles
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/09/2021
ms.author: aahi
ms.openlocfilehash: ce85895307af9e437fb9ceaaff3f1a385e77e17d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672563"
---
> [!NOTE]
> Pour détecter des informations médicales protégées, utilisez le paramètre `domain=phi` et la version du modèle `2020-04-01` (ou une version ultérieure).
>
> Par exemple : `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Les catégories d’entité suivantes sont retournées lorsque vous envoyez des requêtes au point de terminaison `/v3.1/entities/recognition/pii`.


| Category   |  Description                          |
|------------|-------------|
| [Person](#category-person)      |  Noms des personnes.  |
| [PersonType](#category-persontype) | Type de tâche ou rôle d’une personne. |
| [Numéro de téléphone](#category-phonenumber) |Numéros de téléphone (Numéros de téléphone américains et européens uniquement). |
| [Organisation](#category-organization) |  Les sociétés, les groupes, les organismes gouvernementaux et d’autres organisations.  |
| [Adresse](#category-address) | Adresses postales complètes.  |
| [E-mail](#category-email) | Adresses e-mail.   |
| [URL](#category-url) | URL vers des sites web.  |
| [IPAddress](#category-ipaddress) | Adresses IP du réseau.  |
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

        Pour accéder à cette catégorie d’entité, ajoutez `Person` au `piiCategories` paramètre. `Person`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `PersonType` au `piiCategories` paramètre. `PersonType` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `PhoneNumber` au `piiCategories` paramètre. `PhoneNumber` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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

        Sociétés, partis politiques, groupes de musique, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `Organization` au `piiCategories` paramètre. `Organization`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Adresse postale complète. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `Address` au `piiCategories` paramètre. `Address`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Adresses e-mail. Également retourné avec `domain=phi`.
      
        Pour accéder à cette catégorie d’entité, ajoutez `Email` au `piiCategories` paramètre. `Email`  est retourné dans la réponse de l’API si elle est détectée.

    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        URL vers des sites web. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `URL` au `piiCategories` paramètre. `URL`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ipaddress"></a>Catégorie : IPAddress

Cette catégorie contient l’entité suivante :

:::row:::
    :::column span="":::
        **Entité**

        IPAddress

    :::column-end:::
    :::column span="2":::
        **Détails**

        Adresses IP du réseau. Également retourné avec `domain=phi`.

        Pour accéder à cette catégorie d’entité, ajoutez `IPAddress` au `piiCategories` paramètre. `IPAddress`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::

    :::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `DateTime` au `piiCategories` paramètre. `DateTime`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
:::column span="":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `Quantity` au `piiCategories` paramètre. `Quantity`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
      **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `Age` au `piiCategories` paramètre. `Age`  est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="2":::
        **Langues de document prises en charge**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        Pour accéder à cette catégorie d’entité, ajoutez `AzureDocumentDBAuthKey` au `piiCategories` paramètre. `AzureDocumentDBAuthKey` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::
      **Langues de document prises en charge**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion à la base de données Azure IAAS et chaîne de connexion Azure SQL
        

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour une base de données IaaS (infrastructure as a service) Azure et chaîne de connexion SQL.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureIAASDatabaseConnectionAndSQLString` au `piiCategories` paramètre. `AzureIAASDatabaseConnectionAndSQLString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure IoT  

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour Azure IoT. 
      
        Pour accéder à cette catégorie d’entité, ajoutez `AzureIoTConnectionString` au `piiCategories` paramètre. `AzureIoTConnectionString` est retourné dans la réponse de l’API si elle est détectée.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Mot de passe de paramètre de publication Azure  

    :::column-end:::
    :::column span="2":::

        Mot de passe pour les paramètres de publication Azure.

        Pour accéder à cette catégorie d’entité, ajoutez `AzurePublishSettingPassword` au `piiCategories` paramètre. `AzurePublishSettingPassword` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure Cache pour Redis 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour un cache Redis

        Pour accéder à cette catégorie d’entité, ajoutez `AzureRedisCacheString` au `piiCategories` paramètre. `AzureRedisCacheString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAAS 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour SaaS (software as a service) Azure.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureSAS` au `piiCategories` paramètre. `AzureSAS` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion Azure Service Bus

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour Azure Service Bus.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureServiceBusString` au `piiCategories` paramètre. `AzureServiceBusString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Clé du compte de Stockage Azure 

    :::column-end:::
    :::column span="2":::

        Clé d’un compte de stockage Azure. 

        Pour accéder à cette catégorie d’entité, ajoutez `AzureStorageAccountKey` au `piiCategories` paramètre. `AzureStorageAccountKey` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Clé du compte de Stockage Azure (générique)

    :::column-end:::
    :::column span="2":::

        Clé générique d’un compte de stockage Azure.

        Pour accéder à cette catégorie d’entité, ajoutez `AzureStorageAccountGeneric` au `piiCategories` paramètre. `AzureStorageAccountGeneric` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chaîne de connexion SQL Server 

    :::column-end:::
    :::column span="2":::

        Chaîne de connexion pour un ordinateur exécutant SQL Server.

        Pour accéder à cette catégorie d’entité, ajoutez `SQLServerConnectionString` au `piiCategories` paramètre. `SQLServerConnectionString` est retourné dans la réponse de l’API si elle est détectée.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
