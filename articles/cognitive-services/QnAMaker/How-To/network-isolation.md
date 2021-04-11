---
title: Isolement réseau
description: Les utilisateurs peuvent restreindre l’accès public aux ressources QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: c2fad19bd84418d41aca1b2e0770eaa3cde488b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043357"
---
# <a name="recommended-settings-for-network-isolation"></a>Paramétrages recommandés pour l’isolement réseau

Vous devez suivre les étapes ci-dessous pour restreindre l’accès public aux ressources QnA Maker. Protégez une ressource Cognitive Services contre l’accès public en [configurant le réseau virtuel](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restreindre l’accès à App Service (Runtime QnA)

Vous pouvez ajouter des IP à la liste verte d’App Service pour restreindre l’accès ou configurer App Service Environment pour héberger QnA Maker App Service.

#### <a name="add-ips-to-app-service-allow-list"></a>Ajouter des adresses IP à la liste verte d’App Service

1. Autorisez le trafic uniquement à partir des adresses IP Cognitive Services. Celles-ci sont déjà incluses dans l’étiquette de service `CognitiveServicesManagement`. Les API de création (création/mise à jour de base de connaissances) peuvent ainsi appeler le service d’application et mettre à jour le service Recherche Azure en conséquence. Consultez d’[autres informations sur les étiquettes de service](../../../virtual-network/service-tags-overview.md).
2. Assurez-vous d’autoriser également d’autres points d’entrée, tels que Azure Bot Service, le portail QnA Maker, etc. pour accéder à l’API de prévision « GenerateAnswer ».
3. Pour ajouter les plages d’adresses IP à une liste verte, effectuez les étapes suivantes :

   1. Téléchargez les [plages d’adresses IP de toutes les étiquettes de service](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Sélectionnez les adresses IP de « CognitiveServicesManagement ».
   3. Accédez à la section Mise en réseau de votre ressource App Service, puis cliquez sur l’option « Configurer une restriction d’accès » pour ajouter les adresses IP à une liste verte.

Nous avons aussi un script automatisé pour faire la même chose pour votre App Service. Vous trouverez le [script PowerShell pour configurer une liste verte](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) sur GitHub. Vous devez entrer l’ID d’abonnement, le groupe de ressources et le nom App Service réel en tant que paramètres du script. L’exécution du script ajoute automatiquement les adresses IP à la liste verte d’App Service.

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
       
    ![exceptions de port d’entrée](../media/inbound-ports.png)

4.  Créez une instance de service cognitif QnA Maker (Microsoft.CognitiveServices/Accounts) à l’aide d’Azure Resource Manager, où le point de terminaison QnA Maker doit être défini sur le point de terminaison App Service créé ci-dessus (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Restreindre l’accès à une ressource Recherche cognitive

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Une instance de Recherche cognitive peut être isolée par le biais d’un point de terminaison privé après la création de ressources QnA Maker. Les connexions de points de terminaison privés requièrent un réseau virtuel par lequel il est possible d’accéder à l’instance de service de recherche. 

Si QnA Maker App Service est restreint à l’aide d’App Service Environment, utilisez le même réseau virtuel pour créer une connexion de point de terminaison privé à l’instance de Recherche cognitive. Créez une entrée DNS dans le réseau virtuel pour mapper le point de terminaison Recherche cognitive à l’adresse IP du point de terminaison privé Recherche cognitive. 

Si App Service Environment n’est pas utilisé pour QnA Maker App Service, créez d’abord une ressource de réseau virtuel, puis créez la connexion de point de terminaison privé à l’instance de Recherche cognitive. Dans ce cas, QnA Maker App Service doit [être intégré au réseau virtuel](../../../app-service/web-sites-integrate-with-vnet.md) pour se connecter à l’instance de Recherche cognitive. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker managé (préversion)](#tab/v2)

[Créez des points de terminaison privés](../reference-private-endpoint.md) sur la ressource Recherche Azure.

---