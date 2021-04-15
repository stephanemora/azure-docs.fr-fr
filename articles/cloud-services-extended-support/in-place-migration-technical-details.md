---
title: Détails techniques et configuration requise pour la migration vers Azure Cloud Services (support étendu)
description: Décrit les détails techniques et la configuration requise pour la migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu)
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286685"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Détails techniques de la migration vers Azure Cloud Services (support étendu)   

Cet article présente les détails techniques relatifs à l’outil de migration, en ce qui concerne Azure Cloud Services (classique). 

> [!IMPORTANT]
> La migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu) à l’aide de l’outil de migration est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
- Les clients peuvent récupérer leurs nouveaux déploiements via [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) et une [API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/get). 

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


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Traduction des ressources et convention d’affectation de noms post-migration
Dans le cadre de la migration, les noms de ressources sont modifiés, et peu de fonctionnalités des services cloud sont exposées en tant que ressources Azure Resource Manager. Le tableau récapitule les modifications spécifiques à la migration des services cloud.

| Services cloud (classiques) <br><br> Nom de la ressource | Services cloud (classiques) <br><br> Syntaxe| Cloud Services (support étendu) <br><br> Nom de la ressource| Cloud Services (support étendu) <br><br> Syntaxe | 
|---|---|---|---|
| Service cloud | `cloudservicename` | Non associé| Non associé |
| Déploiement (créé par le portail) <br><br> Déploiement (non créé par le portail)  | `deploymentname` | Cloud Services (support étendu) | `deploymentname` |  
| Réseau virtuel | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Non associé |  Réseau virtuel (non créé par le portail) <br><br> Réseau virtuel (créé par le portail) <br><br> Réseaux virtuels (par défaut) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Non associé | Non associé | Key Vault | `cloudservicename` | 
| Non associé | Non associé | Groupe de ressources pour déploiements de service cloud | `cloudservicename-migrated` | 
| Non associé | Non associé | Groupe de ressources pour réseau virtuel | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Non associé | Non associé | Adresse IP publique (dynamique) | `cloudservicenameContractContract` | 
| Nom de l’adresse IP réservée | `reservedipname` | Adresse IP réservée (non créée par le portail) <br><br> Adresse IP réservée (créée par le portail) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Non associé| Non associé | Load Balancer | `deploymentname-lb`|



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
