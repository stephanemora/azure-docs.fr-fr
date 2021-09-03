---
title: Détails techniques et configuration requise pour la migration vers Azure Cloud Services (support étendu)
description: Décrit les détails techniques et la configuration requise pour la migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu)
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.openlocfilehash: 55ce5305962562876a97dfd7677e6af5e1eb9e3a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532637"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Détails techniques de la migration vers Azure Cloud Services (support étendu)   

Cet article présente les détails techniques relatifs à l’outil de migration, en ce qui concerne Azure Cloud Services (classique). 

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Détails sur les fonctionnalités/scénarios pris en charge pour la migration 


### <a name="extensions-and-plugin-migration"></a>Migration des extensions et des plug-ins 
- Toutes les extensions activées et prises en charge seront migrées. 
- Les extensions désactivées ne seront pas migrées. 
- Les plug-ins constituent un concept hérité. Il faut les supprimer avant la migration. Ils sont pris en charge pour la migration mais, à l’issue de celle-ci, si l’extension doit être activée, il faut supprimer le plug-in avant d’installer l’extension. Les extensions et plug-ins de Bureau à distance sont les plus affectés par cela.   
 
### <a name="certificate-migration"></a>Migration de certificat
- Dans Azure Cloud Services (support étendu), les certificats sont stockés dans un Key Vault. Dans le cadre de la migration, nous créons un Key Vault pour les clients ayant le nom du service cloud, et nous transférons tous les certificats d’Azure Service Manager vers le Key Vault. 
- La référence à ce Key Vault est spécifiée dans le modèle ou transmise via PowerShell ou Azure CLI. 

### <a name="service-configuration-and-service-definition-files"></a>Fichiers de configuration et de définition du service
- Les fichiers. cscfg et. csdef doivent être mis à jour pour Azure Cloud Services (support étendu) avec des modifications mineures. 
- Les noms de ressources telles que le réseau virtuel et la référence (SKU) de machine virtuelle sont différents. Consultez [Traduction des ressources et convention d’affectation de noms post-migration](#translation-of-resources-and-naming-convention-post-migration)
- Les clients peuvent récupérer leurs nouveaux déploiements via [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) et une [API REST](/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Services cloud et déploiements
- Chaque déploiement d’Azure Cloud Services (support étendu) est un service cloud indépendant. Les déploiements ne sont plus regroupés dans un service cloud à l’aide d’emplacements.
- Si vous avez deux emplacements dans votre service cloud (classique), vous devez en supprimer un (intermédiaire) et utiliser l’outil de migration pour déplacer l’autre (production) vers Azure Resource Manager. 
- L’adresse IP publique du déploiement du service cloud reste la même après la migration vers Azure Resource Manager, et est exposée en tant que ressource (dynamique ou statique) d’adresse IP de référence (SKU) de base. 
- Le nom DNS et le domaine (cloudapp.azure.net) pour le service cloud migré restent les mêmes. 

### <a name="virtual-network-migration"></a>Migration de réseau virtuel
- Si un déploiement d’Azure Cloud Services se trouve dans un réseau virtuel, pendant la migration, l’ensemble des services cloud et des ressources de réseau virtuel associées sont migrés simultanément. 
- Après la migration, le réseau virtuel est placé dans un groupe de ressources différent de celui du service cloud. 
- Pour les réseaux virtuels avec plusieurs services cloud, ceux-ci sont migrés l’un après l’autre. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migration de déploiements qui ne sont pas dans un réseau virtuel
- En 2017, Azure a commencé automatiquement à créer de nouveaux déploiements (sans réseau virtuel spécifié par le client) dans un réseau virtuel « par défaut » créé par la plateforme. Ces réseaux virtuels par défaut sont masqués aux clients.   
- Dans le cadre de la migration, ce réseau virtuel par défaut est exposé aux clients une fois dans Azure Resource Manager. Pour gérer ou mettre à jour le déploiement dans Azure Resource Manager, les clients doivent ajouter ces informations de réseau virtuel dans la section NetworkConfiguration du fichier. cscfg.    
- Le réseau virtuel par défaut, lors de la migration vers Azure Resource Manager, est placé dans le même groupe de ressources que le service cloud.
- Les services cloud créés avant ce moment ne se trouvent dans aucun réseau virtuel et ne peuvent pas être migrés à l’aide de l’outil. Envisagez de redéployer ces services cloud directement dans Azure Resource Manager. 
- Pour vérifier si un déploiement est éligible pour la migration, exécutez dessus l’API Validate. Le résultat de l’API Valide contient un message d’erreur mentionnant explicitement si ce déploiement est éligible pour la migration.     

### <a name="load-balancer"></a>Load Balancer   
- Pour un service cloud utilisant un point de terminaison public, une plateforme créée par un équilibreur de charge associé au service cloud est exposée dans l’abonnement du client dans Azure Resource Manager. L’équilibreur de charge est une ressource en lecture seule, et les mises à jour sont limitées uniquement par le biais des fichiers de configuration de service (.cscfg) et de définition de service (.csdef). 

### <a name="key-vault"></a>Key Vault
- Dans le cadre de la migration, Azure crée automatiquement un nouveau Key Vault, et migre tous les certificats vers celui-ci. L’outil ne vous permet pas d’utiliser un Key Vault existant. 
- Azure Cloud Services (support étendu) requiert un Key Vault situé dans les mêmes région et abonnement. Ce Key Vault est créé automatiquement dans le cadre de la migration. 

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

## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Traduction des ressources et convention d’affectation de noms post-migration
Dans le cadre de la migration, les noms de ressources sont modifiés, et peu de fonctionnalités des services cloud sont exposées en tant que ressources Azure Resource Manager. Le tableau récapitule les modifications spécifiques à la migration des services cloud.

| Services cloud (classiques) <br><br> Nom de la ressource | Services cloud (classiques) <br><br> Syntaxe| Cloud Services (support étendu) <br><br> Nom de la ressource| Cloud Services (support étendu) <br><br> Syntaxe | 
|---|---|---|---|
| Service cloud | `cloudservicename` | Non associé| Non associé |
| Déploiement (créé par le portail) <br><br> Déploiement (non créé par le portail)  | `deploymentname` | Cloud Services (support étendu) | `cloudservicename` |  
| Réseau virtuel | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Non associé |  Réseau virtuel (non créé par le portail) <br><br> Réseau virtuel (créé par le portail) <br><br> Réseaux virtuels (par défaut) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `VNet-cloudservicename`|
| Non associé | Non associé | Key Vault | `KV-cloudservicename` | 
| Non associé | Non associé | Groupe de ressources pour déploiements de service cloud | `cloudservicename-migrated` | 
| Non associé | Non associé | Groupe de ressources pour réseau virtuel | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Non associé | Non associé | Adresse IP publique (dynamique) | `cloudservicenameContractContract` | 
| Nom de l’adresse IP réservée | `reservedipname` | Adresse IP réservée (non créée par le portail) <br><br> Adresse IP réservée (créée par le portail) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Non associé| Non associé | Load Balancer | `LB-cloudservicename`|



## <a name="migration-issues-and-how-to-handle-them"></a>Problèmes de migration et comment les gérer. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Migration longuement bloquée dans une opération. 
- La validation, la préparation et l’abandon peuvent prendre beaucoup de temps en fonction du nombre de déploiements. Les opérations expirent au bout de 24 heures.   
- Les opérations de validation, de préparation et d’abandon sont idempotentes. La plupart des problèmes peuvent être résolus par une nouvelle tentative. Des erreurs temporaires peuvent se produire, qui disparaissent après quelques minutes. Nous vous recommandons d’effectuer une nouvelle tentative après un certain temps. Si vous opérez la migration à l’aide du Portail Azure et que l’opération est bloquée dans un état « en cours », utilisez PowerShell pour retenter l’opération. 
- Contactez le support technique pour vous aider à migrer ou à restaurer le déploiement à partir du serveur principal. 

### <a name="migration-failed-in-an-operation"></a>La migration a échoué au cours d’une opération. 
- Si la validation a échoué, cela est dû au fait que le déploiement ou le réseau virtuel contient un scénario, une fonctionnalité ou une ressource non pris en charge. Utilisez la liste des scénarios non pris en charge pour rechercher la solution de contournement dans les documents.  
- L’opération de préparation effectue d’abord une validation, qui inclut certaines vérifications coûteuses (non couvertes par la validation). Une échec de préparation peut être du à un scénario non pris en charge. Recherchez le scénario et la solution de contournement dans les documents publics. Vous devez abandonner l’opération pour revenir à l’état d’origine et déverrouiller le déploiement pour les opérations de suppression et de mise à jour.
- En cas d’échec de l’abandon, retentez l’opération. Si les nouvelles tentatives échouent, contactez le support technique.
- En cas d’échec de la validation, retentez l’opération. Si la nouvelle tentative échoue, contactez le support technique. Même en cas d’échec de validation, il ne devrait pas y avoir de problème de plan de données pour votre déploiement. Votre déploiement doit être en mesure de gérer le trafic client sans problème. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Portail actualisé après la préparation. L’expérience a redémarré les boutons Valider ou Abandonner ne sont plus visibles. 
- Le portail stocke les informations de migration localement. Par conséquent, après actualisation, il démarre à partir d’une phase de validation, même si le service cloud est en phase de préparation.  
- Vous pouvez utiliser le portail pour parcourir les étapes de validation et de préparation afin d’exposer les bouton Abandonner et Valider. Cela ne provoque aucun échec.
- Les clients peuvent utiliser PowerShell ou l’API REST pour abandonner ou valider. 

### <a name="how-much-time-can-the-operations-takebr"></a>Combien de temps les opérations peuvent-elles prendre ?<br>
La validation est conçue pour être rapide. La préparation est plus longue et prend un certain temps en fonction du nombre total d’instances de rôle migrées. L’abandon et la validation peuvent également prendre du temps, mais moins que la préparation. Toutes les opérations expirent au bout de 24 heures.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir de l’aide sur la migration de votre déploiement de services cloud (classique) vers les services cloud (prise en charge étendue), consultez notre page d’accueil [Support et résolution des problèmes](support-help.md).