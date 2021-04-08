---
title: Configurer le back-end Service Fabric dans Gestion des API Azure | Microsoft Docs
description: Comment créer un back-end de service Service Fabric dans Gestion des API Azure avec le portail Azure
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99499429"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Configurer un back-end Service Fabric dans Gestion des API avec le portail Azure

Cet article explique comment configurer un service [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) en tant que back-end d’API personnalisé à l’aide du portail Azure. Il montre comment configurer un service fiable ASP.NET Core simple sans état en tant que back-end Service Fabric.

Pour plus d’informations, consultez [Back-ends dans Gestion des API](backends.md).

## <a name="prerequisites"></a>Prérequis

Prérequis pour la configuration d’un exemple de service dans un cluster Service Fabric exécutant Windows en tant que back-end personnalisé :

* **Environnement de développement Windows** : installez les charges de travail [Visual Studio 2019](https://www.visualstudio.com) et le **développement Azure**, **ASP.NET et le développement web** ainsi que **Développement multiplateforme .NET Core**. Ensuite, configurez un [environnement de développement .NET](../service-fabric/service-fabric-get-started.md).

* **Cluster Service Fabric** : consultez [Tutoriel : Déployer un cluster Service Fabric exécutant Windows sur un réseau virtuel Azure](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Vous pouvez créer un cluster avec un certificat X.509 existant, ou à des fins de test, créer un certificat auto-signé. Le cluster est créé dans un réseau virtuel.

* **Exemple d’application Service Fabric** : créez une application API web et déployez-la sur le cluster Service Fabric, comme décrit dans [Intégrer Gestion des API à Service Fabric dans Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Ces étapes créent un service fiable ASP.NET Core simple sans état en utilisant le modèle de projet d’API web par défaut. Par la suite, vous exposez le point de terminaison HTTP pour ce service par le biais de Gestion des API Azure.

    Prenez note du nom de l’application, par exemple `fabric:/myApplication/myService`. 

* **Instance Gestion des API** : instance de Gestion des API existante ou nouvelle dans le niveau **Premium** ou **Développeur** et dans la même région que le cluster Service Fabric. Si nécessaire, [créez une instance de Gestion des API](get-started-create-service-instance.md).

* **Réseau virtuel** : ajoutez votre instance de Gestion des API au réseau virtuel que vous avez créé pour votre cluster Service Fabric. Gestion des API nécessite un sous-réseau dédié dans le réseau virtuel.

  Pour connaître les étapes permettant d’activer la connectivité de réseau virtuel pour l’instance de Gestion des API, consultez [Utilisation de la gestion des API Azure avec des réseaux virtuels](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Créer un back-end - Portail

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Ajouter le certificat du cluster Service Fabric à Gestion des API

Le certificat du cluster Service Fabric est stocké et géré dans un coffre de clés Azure associé au cluster. Ajoutez ce certificat à votre instance de Gestion des API en tant que certificat client.

Pour connaître les étapes permettant d’ajouter un certificat à votre instance de Gestion des API, consultez [Comment sécuriser les services back-end à l’aide d’une authentification par certificat client dans la Gestion des API Azure](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Nous vous recommandons d’ajouter le certificat à Gestion des API en référençant le certificat de coffre de clés. 

### <a name="add-service-fabric-backend"></a>Ajouter le back-end Service Fabric

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre instance APIM.
1. Sous **API**, sélectionnez **Backends** >  **+ Ajouter**.
1. Entrez un nom de back-end et une description facultative.
1. Dans **Type**, sélectionnez **Service Fabric**.
1. Dans **URL du runtime**, entrez le nom du service back-end Service Fabric auquel le service Gestion des API doit transférer les demandes. Exemple : `fabric:/myApplication/myService`. 
1. Dans **Nombre maximal de tentatives de résolution de partition**, entrez un nombre compris entre 0 et 10.
1. Entrez le point de terminaison de gestion du cluster Service Fabric. Ce point de terminaison est l’URL du cluster sur le port `19080`, par exemple, `https://mysfcluster.eastus.cloudapp.azure.com:19080`.
1. Dans **Certificat client**, sélectionnez le certificat de cluster Service Fabric que vous avez ajouté à votre instance de Gestion des API dans la section précédente.
1. Dans **Méthode d’autorisation du point de terminaison de gestion**, entrez une empreinte ou le nom de serveur x509 d’un certificat utilisé par le service de gestion de cluster Service Fabric pour la communication TLS.
1. Activez les paramètres **Valider la chaîne de certificats** et **Valider le nom du certificat**.
1. Dans **Informations d’identification d’autorisation**, fournissez des informations d’identification, si nécessaire, pour atteindre le service back-end configuré dans Service Fabric. Pour l’exemple d’application utilisé dans ce scénario, les informations d’identification d’autorisation ne sont pas nécessaires.
1. Sélectionnez **Créer**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Créer un back-end Service Fabric":::

## <a name="use-the-backend"></a>Utiliser le back-end

Pour utiliser un back-end personnalisé, référencez-le avec la stratégie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService). Cette stratégie convertit l’URL de base du service back-end par défaut d’une demande d’API entrante en un back-end spécifique, en l’occurrence, le back-end Service Fabric. 

La stratégie `set-backend-service` peut être utile avec une API existante pour transformer une demande entrante en un back-end autre que celui qui est spécifié dans les paramètres d’API. À des fins de démonstration dans cet article, créez une API de test et définissez la stratégie pour diriger les demandes d’API vers le back-end Service Fabric. 

### <a name="create-api"></a>Create API

Suivez les étapes décrites dans [Ajouter une API manuellement](add-api-manually.md) pour créer une API vide.

* Dans les paramètres de l’API, laissez **URL du service web** vide.
* Ajoutez un **Suffixe de l’URL de l’API**, tel que *fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Créer une API vide":::

### <a name="add-get-operation-to-the-api"></a>Ajouter une opération GET à l’API

Comme indiqué dans [Déploiement d’un service principal de Service Fabric](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), l’exemple de service ASP.NET Core déployé sur le cluster Service Fabric prend en charge une seule opération HTTP GET sur le chemin d’URL `/api/values`.

La réponse par défaut sur ce chemin est un tableau JSON de deux chaînes :

```json
["value1", "value2"]
```

Pour tester l’intégration de la Gestion des API au cluster, ajoutez l’opération GET correspondante à l’API sur le chemin `/api/values` :

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
1. Sélectionnez **+ Ajouter une opération**.
1. Dans la fenêtre **Front-end**, entrez les valeurs suivantes, puis sélectionnez **Enregistrer**.

     | Paramètre             | Value                             | 
    |---------------------|-----------------------------------|
    | **Nom complet**    | *Test backend*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Ajouter une opération GET à l’API":::

### <a name="configure-set-backend-policy"></a>Configurer la stratégie `set-backend`

Ajoutez la stratégie [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) à l’API de test.

1. Sous l’onglet **Conception**, dans la section **Traitement entrant**, sélectionnez l’icône Éditeur de code ( **</>** ). 
1. Placez le curseur à l’intérieur de l’élément **&lt;inbound&gt;** .
1. Ajoutez l’instruction de stratégie suivante. Dans `backend-id`, substituez le nom de votre back-end Service Fabric.

   `sf-resolve-condition` est une condition de nouvelle tentative si la partition de cluster n’est pas résolue. Le nombre de nouvelles tentatives a été défini lors de la configuration du back-end.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Configurer la stratégie set-backend-service":::

### <a name="test-backend-api"></a>Tester l’API back-end

1. Sous l’onglet **Test**, sélectionnez l’opération **GET** que vous avez créée dans la section précédente.
1. Sélectionnez **Envoyer**.

Quand elle est correctement configurée, la réponse HTTP indique un code de réussite HTTP et affiche le JSON retourné par le service Service Fabric back-end.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Tester le back-end Service Fabric":::

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [configurer des stratégies](api-management-advanced-policies.md) pour transférer des demandes à un back-end.
* Les back-ends peuvent également être configurés à l’aide de l’[API REST](/rest/api/apimanagement/2020-06-01-preview/backend) Gestion des API, d’[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) ou de [modèles Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

