---
title: Créer une identité d’informations d’identification
titleSuffix: Azure Cognitive Services
description: Comment créer une entité d’informations d’identification pour gérer vos informations d’identification en toute sécurité.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: mbullwin
ms.openlocfilehash: 0e16b92ff4a2316042e2177f9525734ba1949db1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341255"
---
# <a name="how-to-create-a-credential-entity"></a>Comment : Créer une identité d’informations d’identification

Lorsque vous intégrez un flux de données, vous devez sélectionner un type d’authentification, certains types d’authentification tels que la *Chaîne de connexion Azure SQL* et le *Principal de Service* ont besoin d’une entité d’informations d’identification pour stocker les informations relatives aux informations d’identification, afin de gérer vos informations d’identification en toute sécurité. Cet article indique comment créer une entité d’informations d’identification pour différents types d’informations d’identification dans Metrics Advisor.
    

## <a name="basic-procedure-create-a-credential-entity"></a>Procédure de base : Créer une entité d’informations d’identification

Vous pouvez créer une **entité d’informations d’identification** pour stocker les informations relatives aux informations d’identification et les utiliser pour l’authentification auprès de vos sources de données. Vous pouvez partager l’entité d’informations d’identification avec d’autres personnes et les autoriser à se connecter à vos sources de données sans partager les informations d’identification réelles. Elle peut être créée dans l’onglet « Ajout d’un flux de données » ou l’onglet « Entité des informations d’identification ». Après avoir créé une entité d’informations d’identification pour un type d’authentification spécifique, vous pouvez simplement choisir une entité d’informations d’identification que vous avez créée lors de l’ajout d’un nouveau flux de données. Cette opération sera utile lors de la création de plusieurs flux de données. La procédure générale de création et d’utilisation d’une entité d’informations d’identification est indiquée ci-dessous :

1. Sélectionnez « + » pour créer une nouvelle entité d’informations d’identification dans l’onglet « Ajout d’un flux de données » (vous pouvez également en créer un dans l’onglet « Flux d’entités d’informations d’identification »).

   ![créer une identité d’informations d’identification](../media/create-credential-entity.png)
 
2. Définissez le nom de l’entité d’informations d'identification, sa description (le cas échéant), le type d’informations d’identification (l’équivalent du *type d’authentification*) et autres paramètres.

   ![créer une entité d’informations d’identification](../media/set-credential-entity.png)
 
3. Après avoir créé une entité d’informations d’identification, vous pouvez la sélectionner lors de la spécification du type d’authentification.

   ![choisir une identité d’informations d’identification](../media/choose-credential-entity.png)
 
Il existe **quatre types d’informations d’identification** dans Metrics Advisor : la Chaîne de connexion Azure SQL, l’Entité de clé partagée Azure Data Lake Storage Gen2, le Principal de service, le Principal de service à partir du Coffre de clés. Pour connaître les différents paramètres du type d’informations d’identification, consultez les instructions suivantes.

## <a name="azure-sql-connection-string"></a>Chaîne de connexion Azure SQL

Vous devez définir le **Nom** et la **Chaîne de connexion**, puis sélectionner « créer ».

![définir l’entité d’informations d’identification pour la chaîne de connexion SQL](../media/credential-entity/credential-entity-sql-connection-string.png)

## <a name="azure-data-lake-storage-gen2-shared-key-entity"></a>Entité de clé partagée Azure Data Lake Storage Gen2

Vous devez définir le **Nom** et la **Clé de compte**, puis sélectionner « créer ». La Clé de compte se trouve dans la ressource de Compte de Stockage Azure (Azure Data Lake Storage Gen2) dans le paramètre **Clés d’accès**.

<!-- 增加basic说明，tips是错的；增加一下怎么管理；加一个step1的link
-->
![définir l’entité d’informations d’identification pour Data Lake](../media/credential-entity/credential-entity-data-lake.png)

## <a name="service-principal"></a>Principal du service

Pour créer un principal de service pour votre source de données, vous pouvez suivre les instructions détaillées dans [Connecter différentes sources de données](../data-feeds-from-different-sources.md). Après avoir créé un principal de service, vous devez renseigner les configurations suivantes dans l’entité d’informations d’identification.

![entité d’informations d’identification sp](../media/credential-entity/credential-entity-service-principal.png)

* **Nom :** Définissez un nom pour votre entité d’informations d’identification de principal de service.
* **ID d’abonné & ID de client :** Après la création d’un principal de service dans le portail Azure, vous pouvez trouver `Tenant ID` et `Client ID` dans **Vue d’ensemble**.

    ![ID de client et ID de locataire sp](../media/credential-entity/sp-client-tenant-id.png)

* **Clé secrète client :** Après la création d’un principal de service dans le portail Azure, vous devez vous rendre dans **Certificats & Secrets** pour créer une nouvelle clé secrète client, et la **valeur** devra être utilisée comme `Client Secret` dans l’entité d’informations d’identification. (Remarque : la valeur n’apparaît qu’une seule fois. Il est donc préférable de la stocker quelque part.)


    ![valeur de la Clé secrète du client sp](../media/credential-entity/sp-secret-value.png)

## <a name="span-idsp-from-kvservice-principal-from-key-vaultspan"></a><span id="sp-from-kv">Principal de service du Coffre de clés</span>

Il existe plusieurs étapes pour créer un principal de service à partir du coffre de clés.

**Étape 1. Créez un Principal de service et accordez-lui l’accès à votre base de données.** Vous pouvez suivre les instructions détaillées dans [Connecter différentes sources de données](../data-feeds-from-different-sources.md), dans la section création d’un principal de service pour chaque source de données. 

Après la création d’un principal de service dans le portail Azure, vous pouvez trouver `Tenant ID` et `Client ID` dans **Vue d’ensemble**. L’**ID de Répertoire (abonné)** doit se trouver `Tenant ID` dans les configurations de l’entité d’informations d’identification.

![ID de client et ID d’abonné sp](../media/credential-entity/sp-client-tenant-id.png)

**Etape 2. Créez une nouvelle clé secrète client.** Vous devez vous rendre dans **Certificats & Secrets** pour créer une nouvelle clé secrète client, et la **valeur** sera utilisée lors des prochaines étapes. (Remarque : la valeur n’apparaît qu’une seule fois. Il est donc préférable de la stocker quelque part.)

![valeur de la Clé secrète du client sp](../media/credential-entity/sp-secret-value.png)

**Étape 3 : Créez un coffre de clés.** Dans le [portail Azure](https://ms.portal.azure.com/#home), sélectionnez **Coffres de clés** pour en créer un.

![créer un coffre de clés dans le portail Azure](../media/credential-entity/create-key-vault.png)

Après la création d’un coffre de clés, l’**URI de coffre** est le `Key Vault Endpoint` dans l’entité d’informations d'identification MA (Metrics Advisor).

![point de terminaison de coffre de clés](../media/credential-entity/key-vault-endpoint.png)

**Étape 4. Créez des secrets pour le Coffre de clés.** Dans le portail Azure pour coffre de clés, générez deux secrets dans **Paramètres->Secrets**.
Le premier est pour `Service Principal Client Id`, le second est pour `Service Principal Client Secret`, leurs deux noms seront utilisés dans les configurations de l’entité d’informations de connexion.

![générer des secrets](../media/credential-entity/generate-secrets.png)

* **ID client du Principal de service :** Définissez un `Name` pour ce secret, le nom sera utilisé dans la configuration de l’entité d’informations de connexion, et la valeur sera votre Principal de service `Client ID` à l’**Etape 1**.

    ![secret1 : ID client sp](../media/credential-entity/secret-1-sp-client-id.png)

* **ID client du Principal de service :** Définissez un `Name` pour ce secret, le nom sera utilisé dans la configuration de l’entité d’informations de connexion, et la valeur sera votre Principal de service `Client Secret Value` à l’**Etape 2**.

    ![secret2 : clé secrète du client sp](../media/credential-entity/secret-2-sp-secret-value.png)

Jusque là, l’*ID client* et la *clé secrète client* du principal de service sont au final stockés dans le Coffre de clés. Ensuite, vous devez créer un autre principal de service pour stocker le coffre de clés. Par conséquent, vous devez **créer deux principaux de service** : l’un pour enregistrer l’ID client et la clé secrète client, lesquels seront stockés dans un coffre de clés, l’autre pour stocker le coffre de clés.

**Étape 5. Créez un principal de service pour stocker le coffre de clés.** 

1. Accédez au [portail Azure AAD (Azure Active Directory)](https://portal.azure.com/?trace=diagnostics&feature.customportal=false#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) puis créez une nouvelle inscription.

    ![créer une nouvelle inscription](../media/credential-entity/create-registration.png)

    Après la création du principal de service, l’**ID d’application (client)** dans la Vue d’ensemble sera le `Key Vault Client ID` dans la configuration de l’entité d’informations d’identification.

2. Dans **Gérer->Certificats & Secrets**, créez une clé secrète client en sélectionnant « Nouvelle clé secrète client ». Ensuite, vous devrez **copier la valeur**, car celle-ci n’apparaît qu’une fois. La valeur est `Key Vault Client Secret` dans la configuration de l’entité d’informations d’identification.

    ![Ajouter une clé secrète client](../media/credential-entity/add-client-secret.png)

**Etape 6. Accordez au Principal de service l’accès au coffre de clés.**  Rendez-vous dans la ressource de coffre de clés que vous avez créée, dans **Paramètres->Stratégies d’accès**, sélectionnez « Ajouter une Stratégie d’accès » pour établir la connexion entre le coffre de clés et le second principal de service à l’**Etape 5**, et « Enregistrer ».

![accorder sp au coffre de clés](../media/credential-entity/grant-sp-to-kv.png)


## <a name="configurations-conclusion"></a>Conclusion des configurations
Pour conclure, les configurations de l’entité d’informations d’identification dans Metrics Advisor pour le *Principal de service à partir du Coffre de clés* et la manière de les obtenir sont indiquées dans le tableau ci-dessous :

| Configuration | Comment obtenir |
|-------------| ---------------------|
| Point de terminaison du coffre de clés | **Etape 3 :** URI de coffre du coffre de clés. |
| ID client | **Étape 1 :** ID de répertoire (abonné) de votre premier principal de service. |
| ID client du Coffre de clés | **Etape 5 :** L’ID d’application (client) de votre deuxième principal de service. |
| Clé secrète du client du Coffre de clés | **Etape 5 :** Valeur de la clé secrète client de votre deuxième principal de service. |
| Nom de l’ID de client du Principal de service | **Etape 4 :** Nom de la clé secrète que vous avez défini pour l’ID client. |
| Nom de la clé secrète client du Principal de service | **Etape 4 :** Nom de la clé secrète que vous avez défini pour la Valeur de Clé secrète client. |


## <a name="next-steps"></a>Étapes suivantes

- [Intégrer vos données](onboard-your-data.md)
- [Connecter différentes sources de données](../data-feeds-from-different-sources.md)
