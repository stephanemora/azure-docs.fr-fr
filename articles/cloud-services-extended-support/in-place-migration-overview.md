---
title: Migrer les services cloud Azure (classiques) vers les services cloud Azure (support étendu)
description: Vue d’ensemble de la migration des services cloud (classiques) vers les services cloud (support étendu)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: ef2d8b68d6fb56f5d9b3508d550303aa9d9526d1
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532917"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrer les services cloud Azure (classiques) vers les services cloud Azure (support étendu)

Ce document fournit une vue d’ensemble de la migration de Cloud Services (classique) vers Cloud Services (support étendu).     

[Azure Cloud Services (support étendu)](overview.md) a pour principal avantage de fournir une résilience régionale ainsi qu’une parité des fonctionnalités avec Azure Cloud Services déployé à l’aide d’Azure Service Manager. Il offre également certaines fonctionnalités Azure Resource Manager telles que le contrôle d’accès en fonction du rôle (RBAC), les balises, la stratégie et prend en charge les modèles de déploiement et les liaisons privées. Les deux modèles de déploiement (support étendu et classique) sont disponibles avec des [structures de tarification similaires](https://azure.microsoft.com/pricing/details/cloud-services/).

Azure Cloud Services (support étendu) offre deux possibilités aux clients pour migrer d’Azure Service Manager vers Azure Resource Manager : redéploiement et migration sur place. 

Le tableau ci-dessous met en évidence les comparaisons entre ces deux options.  


| Redeploy | Migration sur place | 
|---|---|
| Les clients peuvent déployer un nouveau service cloud directement dans Azure Resource Manager, puis supprimer l’ancien service cloud dans Azure Service Manager via la validation. | L’outil de migration sur place permet une migration transparente orchestrée via la plateforme des déploiements Cloud Services (classique) existants vers Cloud Services (support étendu). | 
| Le redéploiement permet aux clients d’effectuer les opérations suivantes : <br><br> - Définir des noms de ressources. <br><br> - Organiser ou réutiliser les ressources en fonction des besoins. <br><br> - Réutiliser la configuration du service et les fichiers de définition avec des modifications minimes. | Pour la migration sur place, la plateforme : <br><br> - Définit les noms des ressources. <br><br> - Organise chaque déploiement et les ressources associées dans des groupes de ressources individuels. <br><br> - Modifie le fichier de définition et de configuration existant pour Azure Resource Manager. | 
| Les clients doivent orchestrer le trafic vers le nouveau déploiement. | La migration conserve l’adresse IP et le chemin d’accès aux données reste inchangé. | 
| Les clients doivent supprimer les anciens services cloud dans Azure Resource Manager. | La plateforme supprime les ressources Cloud Services (classique) après la migration. | 
| Il s’agit d’une migration de type lift-and-shift qui offre plus de flexibilité mais qui nécessite plus de temps. | Il s’agit d’une migration automatisée qui offre une migration rapide, mais moins de flexibilité. | 

Lors de l’évaluation des plans de migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu), vous pouvez examiner d’autres services Azure, tels que : [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [Azure Kubernetes Service](../aks/intro-kubernetes.md) et [Azure Service Fabric](../service-fabric/overview-managed-cluster.md). Ces services continueront à offrir des capacités supplémentaires, tandis qu’Azure Cloud Services (support étendu) conservera principalement la parité des fonctionnalités avec Azure Cloud Services (classique).

En fonction de l’application, Azure Cloud Services (support étendu) peut nécessiter beaucoup moins d’efforts pour passer à Azure Resource Manager par rapport à d’autres options. Si votre application n’évolue pas, Azure Cloud Services (support étendu) est une option viable à prendre en compte, car elle fournit un chemin de migration rapide. À l’inverse, si votre application évolue en permanence et a besoin d’un ensemble de fonctionnalités plus moderne, explorez d’autres services Azure pour mieux répondre à vos besoins actuels et futurs.

## <a name="redeploy-overview"></a>Présentation du redéploiement

Le redéploiement de vos services avec [Cloud Services (support étendu)](overview.md) offre les avantages suivants : 

- Prend en charge les rôles web et de travail, comme [Cloud Services (classique).
- Il n’y a pas de changement dans la conception, l’architecture ou les composants des rôles de travail et web. 
- Aucune modification n’est requise pour le code du runtime, car le plan de données est identique aux services cloud. 
- Les versions du système d’exploitation invité Azure et les mises à jour associées sont alignées sur Cloud Services (classique). 
- Le processus de mise à jour sous-jacent en ce qui concerne les domaines de mise à jour, la procédure de mise à niveau, la restauration et les changements de service autorisés pendant une mise à jour ne changent pas.

Un nouveau service cloud (support étendu) peut être déployé directement dans Azure Resource Manager à l’aide des outils clients suivants :

- [Déployer un service cloud - Portail](deploy-portal.md)
- [Déployer un service cloud - PowerShell](deploy-powershell.md)
- [Déployer un service cloud - Modèle](deploy-template.md)
- [Déployer un service cloud - SDK](deploy-sdk.md)
- [Déployer un service cloud - Visual Studio](/visualstudio/azure/cloud-services-extended-support?context=%2fazure%2fcloud-services-extended-support%2fcontext%2fcontex)


## <a name="migration-tool-overview"></a>Présentation de l’outil de migration

La migration prise en charge par la plateforme offre les principaux avantages suivants :

- Permet une migration fluide orchestrée par une plateforme sans temps d’arrêt pour la plupart des scénarios. Apprenez-en davantage sur les [scénarios pris en charge](in-place-migration-technical-details.md).  
- Migre les services cloud actuels en trois étapes simples : valider, préparer, valider (ou abandonner). Apprenez-en davantage sur la façon dont l’[outil de migration fonctionne]in-place-migration-overview.md#migration-steps).
- Offre la possibilité de tester les déploiements migrés après une préparation réussie. Valide et finalise la migration alors que la fonction d’abandon restaure la migration.

L’outil de migration utilise les mêmes API et a la même expérience que la [migration des machines virtuelles (classiques)](../virtual-machines/migration-classic-resource-manager-overview.md). 

## <a name="setup-access-for-migration"></a>Configurer l’accès pour la migration

Pour effectuer cette migration, vous devez être ajouté en tant que coadministrateur de l’abonnement et inscrire les fournisseurs nécessaires. 

1. Connectez-vous au portail Azure.
3. Dans le menu Hub, sélectionnez Abonnement. Si vous ne le voyez pas, sélectionnez Tous les services.
3. Recherchez l’entrée d’abonnement appropriée, puis examinez le champ MON RÔLE. Pour un coadministrateur, la valeur doit être Administrateur de compte. Si vous n'êtes pas en mesure d'ajouter un coadministrateur, contactez un administrateur de service ou un coadministrateur de l'abonnement afin qu'il vous ajoute.

4. Inscrivez votre abonnement pour l’espace de noms Microsoft.ClassicInfrastructureMigrate à l’aide du [portail](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), de [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) ou de l’[interface CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Vérifiez l’état de votre inscription. L’inscription peut prendre quelques minutes. 

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

## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrer vers Cloud Services (support étendu) à l’aide du [portail Azure](in-place-migration-portal.md)
- Migrer vers Cloud Services (support étendu) à l’aide de [PowerShell](in-place-migration-powershell.md)
