---
title: Isolement réseau
description: Les utilisateurs peuvent restreindre l’accès public aux ressources QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 87726db1f0747c3f9383168321bc3054c685c8a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038718"
---
# <a name="recommended-settings-for-network-isolation"></a>Paramétrages recommandés pour l’isolement réseau

Effectuez les étapes suivantes pour restreindre l’accès public aux ressources QnA Maker. Protégez une ressource Cognitive Services contre l’accès public en [configurant le réseau virtuel](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Restreindre l’accès à App Service (runtime QnA)

Vous pouvez utiliser le ServiceTag `CognitiveServicesMangement` pour restreindre l’accès entrant à App Service ou des règles de trafic entrant de groupe de sécurité réseau ASE (App Service Environment).Pour plus d’informations sur les étiquettes de service, consultez l’ [article sur les étiquettes de service de réseau virtuel](../../../virtual-network/service-tags-overview.md). 

### <a name="regular-app-service"></a>App Service standard

1. Ouvrez Cloud Shell (PowerShell) à partir du portail Azure.
2. Exécutez la commande suivante dans la fenêtre PowerShell au bas de la page :

```ps
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "<resource group name>" -WebAppName "<app service name>" -Name "cognitive services Tag" -Priority 100 -Action Allow -ServiceTag "CognitiveServicesManagement" 
```
3.  Vérifiez que la règle d’accès ajoutée est présente dans la section **Restrictions d’accès** de l’onglet **Réseau** :  

    > [!div class="mx-imgBorder"]
    > [ ![Capture d’écran montrant la règle de restriction d’accès]( ../media/network-isolation/access-restrictions.png) ](  ../media/network-isolation/access-restrictions.png#lightbox)

4. Pour accéder au **volet de test** sur le portail https://qnamaker.ai, ajoutez l’**adresse IP publique de la machine** à partir de laquelle vous souhaitez accéder au portail. Dans la page **Restrictions d’accès**, sélectionnez **Ajouter une règle**, puis autorisez l’accès à votre IP cliente. 

    > [!div class="mx-imgBorder"]
    > [ ![Capture d’écran montrant la règle de restriction d’accès avec l’ajout de l’adresse IP publique]( ../media/network-isolation/public-address.png) ](  ../media/network-isolation/public-address.png#lightbox)

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configurer App Service Environment pour héberger le service d’application QnA Maker

L’environnement ASE (App Service Environment) peut être utilisé pour héberger l’instance App Service QnA Maker. Effectuez les étapes ci-dessous :

1. Créez une [ressource Recherche cognitive Azure](https://ms.portal.azure.com/#create/Microsoft.Search).
2. Créez un environnement ASE externe avec App Service.
    - Suivez ce [guide de démarrage rapide App Service](../../../app-service/environment/create-external-ase.md#create-an-ase-and-an-app-service-plan-together) pour obtenir des instructions. Ce processus peut prendre jusqu’à 1 ou 2 heures.
    - Vous disposerez finalement d’un point de terminaison App Service qui se présentera comme suit : `https://<app service name>.<ASE name>.p.azurewebsite.net`. 
    - Exemple : `https:// mywebsite.myase.p.azurewebsite.net`  
3. Ajoutez les configurations App Service suivantes :
    
    | Nom                       | Valeur                                                     |
    |:---------------------------|:----------------------------------------------------------| 
    | PrimaryEndpointKey         | `<app service name>-PrimaryEndpointKey`                   | 
    | AzureSearchName            | `<Azure Cognitive Search Resource Name from step #1>`     | 
    | AzureSearchAdminKey        | `<Azure Cognitive Search Resource admin Key from step #1>`| 
    | QNAMAKER_EXTENSION_VERSION | `latest`                                                  |
    | DefaultAnswer              | `no answer found`                                         |

4. Ajoutez une origine CORS « * » au service d’application pour autoriser l’accès au volet de test du portail https://qnamaker.ai. L’interface **CORS** est disponible sous l’en-tête d’API du volet App Service.

    > [!div class="mx-imgBorder"]
    > [ ![Capture d’écran de l’interface CORS dans l’IU App Service]( ../media/network-isolation/cross-orgin-resource-sharing.png) ](  ../media/network-isolation/cross-orgin-resource-sharing.png#lightbox)

5. Créez une instance Cognitive Services QnA Maker (Microsoft.CognitiveServices/accounts) à l’aide d’Azure Resource Manager. Le point de terminaison QnA Maker doit être défini sur le point de terminaison App Service créé plus haut (`https:// mywebsite.myase.p.azurewebsite.net`). Vous pouvez utiliser cet [exemple de modèle Azure Resource Manager](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/QnAMakerASEArmTemplate) pour référence.

### <a name="related-questions"></a>Questions associées

#### <a name="can-qna-maker-be-deployed-to-an-internal-ase"></a>QnA Maker peut-il être déployé sur un environnement ASE interne ?

Un environnement ASE externe est principalement utilisé pour que le back-end du service QnA Maker (API de création) puisse atteindre le service d’application par Internet. Toutefois, vous pouvez toujours le protéger en ajoutant une restriction d’accès entrant pour autoriser uniquement les connexions à partir des adresses associées à l’étiquette de service `CognitiveServicesManagement`.

Si vous souhaitez toujours utiliser un environnement ASE interne, vous devez exposer cette application QnA Maker spécifique dans l’environnement ASE sur un domaine public via le certificat TLS/SSL DNS de la passerelle d’application. Pour plus d’informations, consultez cet [article sur le déploiement en entreprise de services d’application](/azure/architecture/reference-architectures/enterprise-integration/ase-standard-deployment).

## <a name="restrict-access-to-cognitive-search-resource"></a>Restreindre l’accès à une ressource Recherche cognitive

L’instance Recherche cognitive peut être isolée par le biais d’un point de terminaison privé après la création de ressources QnA Maker. Pour verrouiller l’accès, effectuez les étapes suivantes :

1. Créez un [réseau virtuel](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) ou utilisez un réseau virtuel existant d’un environnement ASE (App Service Environment).
2. Ouvrez la ressource de réseau virtuel, puis, sous l’onglet **Sous-réseaux**, créez deux sous-réseaux : un pour le service d’application **(appservicesubnet)** et un autre pour le sous-réseau **(searchservicesubnet)** pour la ressource Recherche cognitive sans délégation. 

    > [!div class="mx-imgBorder"]
    > [ ![Capture d’écran de l’interface utilisateur - sous-réseaux de réseaux virtuels]( ../media/network-isolation/subnets.png) ](  ../media/network-isolation/subnets.png#lightbox)

3. Sous l’onglet **Réseau** de l’instance du service Recherche cognitive, basculez les données de connectivité du point de terminaison de Public vers Privé. Cette opération est un long processus qui peut **prendre jusqu’à 30 minutes**.

    > [!div class="mx-imgBorder"]
    > [ ![Capture d’écran de l’interface utilisateur - Réseau avec le bouton bascule Public/Privé]( ../media/network-isolation/private.png) ](  ../media/network-isolation/private.png#lightbox)

4. Après le basculement de la ressource de recherche sur Privé, sélectionnez **Point de terminaison privé** en regard du signe +.
    - **Onglet Informations de base** : veillez à créer votre point de terminaison dans la même région que la ressource de recherche.
    - **Onglet Ressource** : sélectionnez la ressource de recherche nécessaire de type `Microsoft.Search/searchServices`.

    > [!div class="mx-imgBorder"]
    > [ ![Capture d’écran de la fenêtre d’IU Créer un point de terminaison privé]( ../media/network-isolation/private-endpoint.png) ](  ../media/network-isolation/private-endpoint.png#lightbox)

    - **Onglet Configuration** : utilisez le sous-réseau de réseau virtuel (searchservicesubnet) créé à l’étape 2. Après cela, dans la section **Intégration à DNS privé**, sélectionnez l’abonnement correspondant et créez une zone DNS privée nommée **privatelink.search.windows.net**.

     > [!div class="mx-imgBorder"]
     > [ ![Capture d’écran de la fenêtre d’IU Créer un point de terminaison privé avec le champ Sous-réseau renseigné]( ../media/network-isolation/subnet.png) ](  ../media/network-isolation/subnet.png#lightbox)

5. Activez l’intégration au réseau virtuel pour le service d’application standard. Vous pouvez ignorer cette étape pour ASE, qui a déjà accès au réseau virtuel.
    - Accédez à la section **Réseau** d’App Service, puis ouvrez **Intégration de réseau virtuel**.
    - Effectuez la liaison avec le sous-réseau de réseau virtuel App Service dédié (appservicevnet) créé à l’étape 2.
    
     > [!div class="mx-imgBorder"]
     > [ ![Capture d’écran de l’IU - Intégration de réseau virtuel]( ../media/network-isolation/integration.png) ](  ../media/network-isolation/integration.png#lightbox)

[Créez des points de terminaison privés](../reference-private-endpoint.md) sur la ressource Recherche Azure.

Effectuez les étapes suivantes pour restreindre l’accès public aux ressources QnA Maker. Protégez une ressource Cognitive Services contre l’accès public en [configurant le réseau virtuel](../../cognitive-services-virtual-networks.md?tabs=portal).

Après avoir restreint l’accès à la ressource Cognitive Services en fonction du sous-réseau, effectuez les étapes suivantes pour parcourir les bases de connaissances sur le portail https://qnamaker.ai à partir de votre réseau local ou de votre navigateur local.
- Accordez l’accès au [réseau local](../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks).
- Accordez l’accès à votre [machine/navigateur local](../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules).
- Ajoutez l’**adresse IP publique de la machine sous la section Pare-feu** de l’onglet **Réseau**. Par défaut, `portal.azure.com` affiche l’IP publique de la machine de navigation actuelle. Sélectionnez cette entrée, puis sélectionnez **Enregistrer**.

     > [!div class="mx-imgBorder"]
     > [ ![Capture d’écran de l’IU de configuration - Pare-feux et réseaux virtuels]( ../media/network-isolation/firewall.png) ](  ../media/network-isolation/firewall.png#lightbox)
