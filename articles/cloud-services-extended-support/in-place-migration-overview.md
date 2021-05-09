---
title: Migrer les services cloud Azure (classiques) vers les services cloud Azure (support étendu)
description: Vue d’ensemble de la migration des services cloud (classiques) vers les services cloud (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: f3b41bed481ff36ec1f2fef8e699e57f6138474c
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285931"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrer les services cloud Azure (classiques) vers les services cloud Azure (support étendu)

Cet article fournit une vue d’ensemble de l’outil de migration pris en charge par la plateforme et explique comment l’utiliser pour migrer les [services cloud Azure (classiques)](../cloud-services/cloud-services-choose-me.md) vers les [services cloud Azure (support étendu)](overview.md).

L’outil de migration utilise les mêmes API et a la même expérience que la [migration des machines virtuelles (classiques)](../virtual-machines/migration-classic-resource-manager-overview.md). 

> [!IMPORTANT]
> La migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu) à l’aide de l’outil de migration est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Reportez-vous aux ressources suivantes si vous avez besoin d’aide pour votre migration : 

- [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) : Support de Microsoft et de la communauté pour la migration.
- [Prise en charge de la migration Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D) : Équipe de support dédiée pour l’assistance technique pendant la migration. Les clients sans support technique peuvent utiliser la [fonction de support gratuit](https://aka.ms/cs-migration-errors) fournie spécifiquement pour cette migration.
- Si votre entreprise/organisation a conclu un partenariat avec Microsoft ou si vous collaborez avec des représentants Microsoft, par exemple un architecte de solution cloud ou un responsable technique de compte, contactez-les afin d’obtenir des ressources supplémentaires pour la migration.
- Répondez à [ce questionnaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) pour fournir des commentaires ou soumettre des problèmes à l’équipe produit Cloud Services (support étendu). 

## <a name="migration-benefits"></a>Avantages de la migration
La migration prise en charge par la plateforme offre les principaux avantages suivants :
- La migration est entièrement orchestrée par la plateforme : elle déplace l’ensemble du déploiement et les ressources associées vers Azure Resource Manager.
- Absence de temps d’arrêt au cours de la migration.
- Migration simple et rapide par rapport à d’autres approches de migration en réduisant au minimum les tâches manuelles. 
- Conserve l’adresse IP et l’étiquette DNS des services cloud dans le cadre de la migration. 

Pour découvrir les autres avantages et connaître les raisons de la migration, consultez les pages dédiées à [Cloud Services (support étendu)](overview.md) et [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Configurer l’accès pour la migration

Pour effectuer cette migration, vous devez être ajouté en tant que coadministrateur de l’abonnement et inscrire les fournisseurs nécessaires. 

1. Connectez-vous au portail Azure.
3. Dans le menu Hub, sélectionnez Abonnement. Si vous ne le voyez pas, sélectionnez Tous les services.
3. Recherchez l’entrée d’abonnement appropriée, puis examinez le champ MON RÔLE. Pour un coadministrateur, la valeur doit être Administrateur de compte. Si vous n'êtes pas en mesure d'ajouter un coadministrateur, contactez un administrateur de service ou un coadministrateur de l'abonnement afin qu'il vous ajoute.

4. Inscrivez votre abonnement pour l’espace de noms Microsoft.ClassicInfrastructureMigrate à l’aide du [portail](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), de [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou de l’[interface CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Inscrivez votre abonnement pour la fonctionnalité en préversion de la migration des services cloud à l’aide du [portail](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), de [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou de l’[interface CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Vérifiez l’état de votre inscription. L’inscription peut prendre quelques minutes. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>En quoi la migration des services cloud (classiques) est-elle différente de celle des machines virtuelles (classiques) ?
Azure Service Manager prend en charge deux produits de calcul différents : [Machines virtuelles Azure (classiques)](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) et [Azure Cloud Services (classique)](../cloud-services/cloud-services-choose-me.md) ou des rôles de travail/web. Les deux produits varient en fonction du type de déploiement qui se trouve dans le service cloud. L’offre Azure Cloud Services (classique) utilise le service cloud contenant des déploiements avec des rôles web/de travail. L’offre Machines virtuelles Azure (classique) utilise un service cloud contenant des déploiements avec des machines virtuelles IaaS.

La liste des scénarios pris en charge diffère entre les services cloud (classiques) et les machines virtuelles (classiques) en raison de différences dans les types de déploiement.

## <a name="migration-steps"></a>Étapes de la migration
 
Les clients peuvent migrer leurs déploiements de services cloud (classiques) à l’aide des quatre opérations utilisées pour migrer des machines virtuelles (classiques). 

1. **Valider la migration** : vérifie que la migration n’est pas empêchée par des scénarios courants non pris en charge.
2. **Préparer la migration** : duplique les métadonnées de la ressource dans Azure Resource Manager. Toutes les ressources sont verrouillées pour les opérations de création/mise à jour/suppression afin de garantir la synchronisation des métadonnées des ressources sur Azure Server Manager et Azure Resource Manager. Toutes les opérations de lecture fonctionnent à l’aide des API Cloud Services (classique) et Cloud Services (support étendu).
3. **Abandonner la migration** : supprime les métadonnées de ressources d’Azure Resource Manager. Déverrouille toutes les ressources pour les opérations de création/mise à jour/suppression.
4. **Valider la migration** : supprime les métadonnées de ressources d’Azure Service Manager. Déverrouille les ressources pour les opérations de création/mise à jour/suppression. L’abandon n’est plus autorisé après la tentative de validation.

>[!NOTE]
> Les opérations de préparation, d’abandon et de validation sont idempotentes et, par conséquent, en cas d’échec, une nouvelle tentative doit résoudre le problème.

:::image type="content" source="media/in-place-migration-1.png" alt-text="L’image présente le schéma des étapes associées à la migration.":::

Pour plus d’informations sur la migration, consultez [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Ressources et fonctionnalités prises en charge disponibles pour la migration associée à Cloud Services (classique)
-   Comptes de stockage
-   Virtual Network
-   Network Security Group
-   Adresses IP publiques réservées
-   Listes de contrôle d’accès par point de terminaison
-   Itinéraires définis par l’utilisateur
-   Équilibreur de charge interne
-   Migration de certificat vers un coffre de clés
-   Plug-ins et extension (basés sur XML et JSON)
-   Tâches Au démarrage/À l’arrêt
-   Déploiements avec performances réseau accélérées
-   Déploiements utilisant un ou plusieurs rôles
-   Équilibreur de charge de base
-   Entrée, entrée d’instance, points de terminaison internes
-   Adresses IP publiques dynamiques
-   Nom DNS 
-   Règles de trafic réseau
-   Réseau virtuel hypernet

## <a name="supported-configurations--migration-scenarios"></a>Configurations/scénarios de migration pris en charge
Il s’agit de scénarios principaux impliquant des combinaisons de ressources, de fonctionnalités et de services cloud. Cette liste n’est pas exhaustive.

| Service | Configuration | Commentaires | 
|---|---|---|
| [Services de domaine Azure AD](../active-directory-domain-services/migrate-from-classic-vnet.md) | Réseaux virtuels contenant Azure Active Directory Domain Services. | Un réseau virtuel contenant à la fois un déploiement de service cloud et les services de domaine Azure AD est pris en charge. Le client doit d’abord migrer séparément les services de domaine Azure AD, puis migrer le réseau virtuel vers la gauche uniquement avec le déploiement du service cloud |
| Service cloud | Service cloud avec un déploiement dans un seul emplacement uniquement. | Les services cloud contenant un déploiement d’emplacement de production ou de préproduction peuvent être migrés |
| Service cloud | Déploiement qui ne s’effectue pas dans un réseau virtuel visible publiquement (déploiement de réseau virtuel par défaut) | Un service cloud peut être dans un réseau virtuel visible publiquement, dans un réseau virtuel masqué ou peut ne pas être situé dans un réseau virtuel.  Les services cloud dans un réseau virtuel masqué et les réseaux virtuels visibles publiquement sont pris en charge pour la migration. Le client peut utiliser l’API Validate pour déterminer si un déploiement est à l’intérieur d’un réseau virtuel par défaut ou non et, par conséquent, déterminer s’il peut être migré. |
|Service cloud | Extensions XML (BGInfo, débogueur Visual Studio, Web Deploy et débogage à distance). | Toutes les extensions XML sont prises en charge pour la migration 
| Réseau virtuel | Réseau virtuel contenant plusieurs services cloud. | Un réseau virtuel contenant plusieurs services cloud est pris en charge pour la migration. Le réseau virtuel et tous les services cloud qu’il contient sont migrés ensemble vers Azure Resource Manager. |
| Réseau virtuel | Migration de réseaux virtuels créés via le portail (nécessite l’utilisation de « Group Resource-group-name VNet-Name » dans le fichier .cscfg)  | Dans le cadre de la migration, le nom du réseau virtuel dans cscfg est modifié de façon à ce qu’il utilise l’ID Azure Resource Manager du réseau virtuel. (subscription/subscription-id/resource-group/resource-group-name/resource/vnet-name) <br><br>Pour gérer le déploiement après la migration, mettez à jour la copie locale du fichier .cscfg pour qu’il commence à utiliser l’ID Azure Resource Manager au lieu du nom de réseau virtuel. <br><br>Un fichier .cscfg qui utilise l’ancien modèle d’affectation de noms ne réussira pas la validation. 
| Réseau virtuel | Migration de déploiement avec des rôles dans un sous-réseau différent. | Un service avec différents rôles dans différents sous-réseaux est pris en charge pour la migration. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Ressources et fonctionnalités non disponibles pour la migration
Il s’agit de scénarios principaux impliquant des combinaisons de ressources, de fonctionnalités et de services cloud. Cette liste n’est pas exhaustive. 

| Ressource | Étapes suivantes/solution de contournement | 
|---|---|
| Règles de mise à l’échelle automatique | La migration s’effectue mais les règles sont supprimées. [Recréez les règles ](./configure-scaling.md) après la migration sur les services cloud (support étendu). | 
| Alertes | La migration s’effectue mais les alertes sont supprimées. [Recréez les règles](./enable-alerts.md) après la migration sur les services cloud (support étendu). | 
| Passerelle VPN | Supprimez la passerelle VPN avant de commencer la migration, puis recréez la passerelle VPN une fois la migration terminée. | 
| Passerelles ExpressRoute (dans le même abonnement que le réseau virtuel uniquement) | Supprimez la passerelle ExpressRoute avant de commencer la migration, puis recréez la passerelle une fois la migration terminée. | 
| Quota  | Le quota n’est pas migré. [Demandez un nouveau quota](../azure-resource-manager/templates/error-resource-quota.md#solution) sur Azure Resource Manager avant la migration pour que la validation réussisse. | 
| Groupe d'affinités | Non pris en charge. Supprimez les groupes d’affinités avant la migration.  | 
| Réseaux virtuels utilisant l’[appairage de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md)| Avant de migrer un réseau virtuel appairé à un autre réseau virtuel, supprimez l’appairage, migrez le réseau virtuel vers Resource Manager et recréez l’appairage. Cela peut entraîner un temps d’arrêt en fonction de l’architecture. | 
| Réseaux virtuels contenant des environnements App Service | Non pris en charge | 
| Réseaux virtuels contenant des services HDInsight | Non pris en charge. 
| Réseaux virtuels contenant des déploiements Gestion des API Azure | Non pris en charge. <br><br> Pour migrer le réseau virtuel, modifiez le réseau virtuel du déploiement Gestion des API. Il s’agit d’une opération sans temps d’arrêt. | 
| Circuits Express Route classiques | Non pris en charge. <br><br>Ces circuits doivent migrer vers Azure Resource Manager avant le début de la migration PaaS. Pour en savoir plus, consultez [Migration de circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](../expressroute/expressroute-howto-move-arm.md). |  
| Contrôle d’accès en fonction du rôle | Après la migration, l’URI des modifications de ressource de `Microsoft.ClassicCompute` vers les stratégies RBAC `Microsoft.Compute` doit être mis à jour après la migration. | 
| Application Gateway | Non pris en charge. <br><br> Supprimez la passerelle applicative Application Gateway avant de commencer la migration, puis recréez-la une fois la migration vers Azure Resource Manager terminée. | 

## <a name="unsupported-configurations--migration-scenarios"></a>Configurations/scénarios de migration non pris en charge

| Configuration/scénario  | Étapes suivantes/solution de contournement | 
|---|---|
| Migration de certains déploiements plus anciens qui ne sont pas dans un réseau virtuel | Certains déploiements de service cloud qui ne se trouvent pas dans un réseau virtuel ne sont pas pris en charge pour la migration. <br><br> 1. Utilisez l’API Validate pour vérifier si le déploiement est éligible à la migration. <br> 2. S’il est éligible, les déploiements sont déplacés vers Azure Resource Manager sous un réseau virtuel avec le préfixe « DefaultRdfeVnet ». | 
| Migration de déploiements contenant à la fois le déploiement d’emplacements de production et de préproduction à l’aide d’adresses IP dynamiques | La migration d’un service cloud à deux emplacements nécessite la suppression de l’emplacement de préproduction. Une fois l’emplacement de préproduction supprimé, migrez l’emplacement de production en tant que service cloud indépendant (support étendu) dans Azure Resource Manager. Ensuite, redéployez l’environnement intermédiaire en tant que nouveau service cloud (support étendu) et rendez-le remplaçable par le premier. | 
| Migration de déploiements contenant à la fois le déploiement d’emplacements de production et de préproduction à l’aide d’adresses IP réservées | Non pris en charge. | 
| Migration du déploiement de production et de préproduction dans un réseau virtuel différent|La migration d’un service cloud à deux emplacements nécessite la suppression de l’emplacement de préproduction. Une fois l’emplacement de préproduction supprimé, migrez l’emplacement de production en tant que service cloud indépendant (support étendu) dans Azure Resource Manager. Un nouveau déploiement de services cloud (support étendu) peut ensuite être lié au déploiement migré avec la propriété remplaçable activée. Les fichiers de déploiement de l’ancien déploiement d’emplacement de préproduction peuvent être réutilisés pour créer ce nouveau déploiement remplaçable. | 
| Migration d’un service cloud vide (service cloud sans déploiement) | Non pris en charge. | 
| Migration d’un déploiement contenant le plug-in Bureau à distance et les extensions Bureau à distance | Option 1 : supprimez le plug-in Bureau à distance avant la migration. Cela nécessite de modifier les fichiers de déploiement. La migration va ensuite se poursuivre. <br><br> Option 2 : supprimez l’extension Bureau à distance et migrez le déploiement. Après la migration, supprimez le plug-in et installez l’extension. Cela nécessite de modifier les fichiers de déploiement. <br><br> Supprimez le plug-in et l’extension avant la migration. Les [plug-ins ne sont pas recommandés](./deploy-prerequisite.md#required-service-definition-file-csdef-updates) pour une utilisation sur les services cloud (support étendu).| 
| Réseaux virtuels avec un déploiement PaaS et IaaS |Non pris en charge <br><br> Déplacez les déploiements PaaS ou IaaS vers un autre réseau virtuel. Cela va entraîner un temps d’arrêt. | 
Déploiements de service cloud utilisant des tailles de rôle héritées (telles que Small ou ExtraLarge). | La migration s’effectue, mais les tailles de rôle sont mises à jour pour utiliser les tailles de rôle modernes. Il n’y a aucune modification des propriétés de coût ou de référence et la machine virtuelle n’est pas redémarrée pour cette modification. Mettez à jour tous les artefacts de déploiement pour qu’ils fassent référence à ces nouvelles tailles de rôle modernes. Pour plus d’informations, consultez [Tailles de machines virtuelles disponibles](available-sizes.md).|
| Migration du service cloud vers un autre réseau virtuel | Non pris en charge <br><br> 1. Déplacez le déploiement vers un autre réseau virtuel classique avant la migration. Cela va entraîner un temps d’arrêt. <br> 2. Migrez le nouveau réseau virtuel vers Azure Resource Manager. <br><br> ou <br><br> 1. Migrez le réseau virtuel vers Azure Resource Manager. <br>2. Déplacez le service cloud vers un nouveau réseau virtuel. Cela va entraîner un temps d’arrêt. | 
| Service cloud dans un réseau virtuel, mais sans sous-réseau explicite attribué | Non pris en charge. L’atténuation implique le déplacement du rôle dans un sous-réseau, ce qui nécessite un redémarrage du rôle (et donc un temps d’arrêt). | 


## <a name="post-migration-changes"></a>Modifications après la migration
Le déploiement de services cloud (classiques) est converti en déploiement de services cloud (support étendu). Pour plus d’informations, consultez la [documentation sur l’offre Cloud Services (support étendu)](deploy-prerequisite.md) .  

### <a name="changes-to-deployment-files"></a>Modifications apportées aux fichiers de déploiement 

Des modifications mineures sont apportées au fichier .csdef et .cscfg du client pour que les fichiers de déploiement soient conformes aux exigences d’Azure Resource Manager et des services cloud (support étendu). Après la migration, vos nouveaux fichiers de déploiement sont récupérés ou les fichiers existants sont mis à jour. Cela est nécessaire pour les opérations de mise à jour/suppression.  

- Le réseau virtuel utilise l’ID de ressource complet Azure Resource Manager au lieu du nom de ressource dans la section NetworkConfiguration du fichier .cscfg. Par exemple : `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. Pour les réseaux virtuels appartenant au même groupe de ressources que le service cloud, vous pouvez choisir de mettre à jour le fichier .cscfg à l’aide du nom de réseau virtuel uniquement.  

- Les tailles classiques comme Small, Large, ExtraLarge sont remplacées par leurs nouveaux noms de taille, Standard_A*. Les noms de taille doivent être remplacés par leurs nouveaux noms dans le fichier .csdef. Pour plus d’informations, consultez [Prérequis pour le déploiement de Cloud Services (support étendu)](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Utilisez l’API Get pour récupérer la copie la plus récente des fichiers de déploiement. 
    - Récupérez le modèle à l’aide du [portail](../azure-resource-manager/templates/export-template-portal.md), de [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), de l’[interface CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) et de l’[API REST](/rest/api/resources/resourcegroups/exporttemplate) 
    - Récupérez le fichier .csdef à l’aide de [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) ou de l’[API REST](/rest/api/compute/cloudservices/rest-get-package). 
    - Récupérez le fichier .cscfg à l’aide de [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) ou de l’[API REST](/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Modifications apportées à l’automatisation, au pipeline CI/CD, aux scripts personnalisés, aux tableaux de bord personnalisés, aux outils personnalisés, etc. du client.  

Les clients doivent mettre à jour leurs outils et leur automatisation pour commencer à utiliser les nouvelles API/commandes afin de gérer leur déploiement. Le client peut facilement adopter de nouvelles fonctionnalités et les fonctionnalités d’Azure Resource Manager/Cloud Services (support étendu) dans le cadre de cette modification. 

- Modifications apportées aux noms de ressources et de groupes de ressources après la migration
    - Dans le cadre de la migration, les noms de quelques ressources comme le service cloud, les adresses IP publiques, etc. changent. Ces modifications devront peut-être être reflétées dans les fichiers de déploiement avant la mise à jour du service cloud. [En savoir plus sur les noms des ressources qui changent](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Recréer les règles et les stratégies nécessaires pour gérer et mettre à l’échelle les services cloud 
    - Les [règles de mise à l’échelle automatique](configure-scaling.md) ne sont pas migrées. Après la migration, recréez les règles de mise à l’échelle automatique.  
    - Les [alertes](enable-alerts.md) ne sont pas migrées. Après la migration, recréez les alertes.
    - Le coffre de clés est créé sans stratégie d’accès. [Créez les stratégies appropriées](../key-vault/general/assign-access-policy-portal.md) sur le coffre de clés pour afficher ou gérer vos certificats. Les certificats sont visibles sous les paramètres sous l’onglet Secrets.

## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrer vers Cloud Services (support étendu) à l’aide du [portail Azure](in-place-migration-portal.md)
- Migrer vers Cloud Services (support étendu) à l’aide de [PowerShell](in-place-migration-powershell.md)