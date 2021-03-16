---
title: Isolement réseau
description: Les utilisateurs peuvent restreindre l’accès public aux ressources QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8fe8c07866b23e5d990b71bfc9cd556c338634d3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203366"
---
# <a name="recommended-settings-for-network-isolation"></a>Paramétrages recommandés pour l’isolement réseau

Vous devez suivre les étapes ci-dessous pour restreindre l’accès public aux ressources QnA Maker. Protégez une ressource Cognitive Services contre l’accès public en [configurant le réseau virtuel](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>Restreindre l’accès à une ressource Recherche cognitive

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Configuration de la Recherche cognitive en tant que point de terminaison privé au sein d’un réseau virtuel. Lorsqu’une instance de recherche est créée lors de la création d’une ressource QnA Maker, vous pouvez forcer Recherche cognitive à prendre en charge une configuration de point de terminaison privée entièrement créée au sein du réseau virtuel d’un client.

Afin d’utiliser un point de terminaison privé, toutes les ressources doivent être créées dans la même région.

* Ressource QnA Maker
* nouvelle ressource Recherche cognitive
* nouvelle ressource Réseau virtuel

Procédez comme suit dans le [portail Azure](https://portal.azure.com) :

1. Créez une [ressource QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Créez une ressource Recherche cognitive avec une connectivité Point de terminaison (données) définie sur _Privée_. Créez la ressource dans la même région que la ressource QnA Maker créée à l’étape 1. Apprenez-en davantage sur [la création d’une ressource Recherche cognitive](../../../search/search-create-service-portal.md), puis utilisez ce lien pour accéder directement à la [page de création de la ressource](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Créez une [ressource Réseau virtuel](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Configurez le réseau virtuel sur la ressource App Service créée à l’étape 1 de cette procédure. Créez une entrée DNS dans le réseau virtuel pour la nouvelle ressource Recherche cognitive créée à l’étape 2 à l’adresse IP Recherche cognitive.
5. [Associez App Service à la nouvelle ressource Recherche cognitive](../how-to/set-up-qnamaker-service-azure.md) créée à l’étape 2. Vous pouvez ensuite supprimer la ressource Recherche cognitive d’origine créée à l’étape 1.
    
Sur le portail [QnA Maker](https://www.qnamaker.ai/), créez votre première base de connaissances.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/v2)

[Créez des points de terminaison privés](../reference-private-endpoint.md) sur la ressource Recherche Azure.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restreindre l’accès à App Service (Runtime QnA)

Vous pouvez ajouter des IP à la liste d’autorisation d’App Service pour restreindre l’accès, ou configurer App Service Environnement pour héberger QnA Maker App Service.

#### <a name="add-ips-to-app-service-allowlist"></a>Ajouter des adresses IP à la liste d’autorisation d’App Service

1. Autorisez le trafic uniquement à partir des adresses IP Cognitive Services. Celles-ci sont déjà incluses dans l’étiquette de service `CognitiveServicesManagement`. Les API de création (création/mise à jour de base de connaissances) peuvent ainsi appeler le service d’application et mettre à jour le service Recherche Azure en conséquence. Consultez d’[autres informations sur les étiquettes de service](../../../virtual-network/service-tags-overview.md).
2. Assurez-vous d’autoriser également d’autres points d’entrée, tels que Azure Bot Service, le portail QnA Maker, etc. pour accéder à l’API de prévision « GenerateAnswer ».
3. Pour ajouter les plages d’adresses IP à une liste verte, effectuez les étapes suivantes :

   1. Téléchargez les [plages d’adresses IP de toutes les étiquettes de service](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Sélectionnez les adresses IP de « CognitiveServicesManagement ».
   3. Accédez à la section réseau de votre ressource App Service, puis cliquez sur l’option « Configurer une restriction d’accès » pour ajouter les adresses IP à une liste verte.

    ![exceptions de port d’entrée](../media/inbound-ports.png)

Nous avons aussi un script automatisé pour faire la même chose pour votre App Service. Vous trouverez le [script PowerShell pour configurer une liste verte](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) sur GitHub. Vous devez entrer l’ID d’abonnement, le groupe de ressources et le nom App Service réel en tant que paramètres du script. L’exécution du script va automatiquement ajouter les adresses IP à la liste verte App Service.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configurer App Service Environment pour héberger le service d’application QnA Maker
    
Un App Service Environment (ASE) peut être utilisé pour héberger le service d’application QnA Maker. Procédez comme suit :

1. Créez un App Service Environment et marquez-le comme étant « externe ». Pour obtenir des instructions, suivez le [tutoriel](../../../app-service/environment/create-external-ase.md).
2.  Créez un App Service à l’intérieur du App Service Environment.
    1. Vérifiez la configuration de l’App Service et ajoutez « PrimaryEndpointKey » en tant que paramètre d’application. La valeur de « PrimaryEndpointKey » doit être définie sur «\<app-name\>-PrimaryEndpointKey ». Le nom de l’application est défini dans l’URL App Service. Par exemple, si l’URL App Service est « mywebsite.myase.p.azurewebsite.net », le nom de l’application est « mywebsite ». Dans ce cas, la valeur de « PrimaryEndpointKey » doit être définie sur « mywebsite-PrimaryEndpointKey ».
    2. Créez un service Recherche Azure.
    3. Vérifiez que les paramètres d’application et de recherche Azure sont configurés correctement. 
          Veuillez suivre ce [tutoriel](../reference-app-service.md?tabs=v1#app-service).
3.  Mettre à jour le groupe de sécurité réseau associé au App Service Environment
    1. Mettez à jour les règles de sécurité de trafic entrant précréées en fonction de vos besoins.
    2. Ajoutez une nouvelle règle de sécurité de trafic entrant avec la source « balise de service » et la balise de service source « CognitiveServicesManagement ».
4.  Créez une instance de service cognitif QnA Maker (Microsoft.CognitiveServices/Accounts) à l’aide d’Azure Resource Manager, où le point de terminaison QnA Maker doit être défini sur le point de terminaison App Service créé ci-dessus (https://mywebsite.myase.p.azurewebsite.net).
    
---
