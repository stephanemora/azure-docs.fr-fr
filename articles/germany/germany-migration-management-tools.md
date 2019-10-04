---
title: Migrer des outils de gestion Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos outils de gestion Azure depuis Azure Allemagne vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: bc237d0f78c77a6fbc89db4061ca38403c3d777b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033659"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>Migrer les ressources de l’outil de gestion vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des outils de gestion Azure depuis Azure Allemagne vers Azure global.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager peut vous aider à effectuer une migration en couceur. Toutefois, vous ne pouvez pas migrer les profils Traffic Manager que vous créez dans Azure Allemagne vers Azure global. (Lors d’une migration, vous migrez les points de terminaison Traffic Manager vers l’environnement cible. Vous devez donc quand même mettre à jour le profil Traffic Manager.)

Vous pouvez définir des points de terminaison supplémentaires dans l’environnement cible à l’aide de Traffic Manager pendant qu’il est en cours d’exécution dans l’environnement source. Lorsque Traffic Manager est en cours d’exécution dans le nouvel environnement, vous pouvez toujours définir des points de terminaison que vous n’avez pas encore migrés dans l’environnement source. Ce scénario est appelé le [scénario bleu-vert](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/). Ce scénario implique les étapes suivantes :

1. Créez un profil Traffic Manager dans Azure global.
1. Définissez les points de terminaison dans Azure Allemagne.
1. Modifiez votre enregistrement DNS CNAME dans le nouveau profil Traffic Manager.
1. Désactivez l’ancien profil Traffic Manager.
1. Migrez et configurez les points de terminaison. Pour chaque point de terminaison dans Azure Allemagne :
   1. Migrez le point de terminaison vers Azure global.
   1. Modifiez le profil Traffic Manager pour utiliser le nouveau point de terminaison.

Pour plus d'informations :

- Actualisez vos connaissances en complétant les [tutoriels sur Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- En savoir plus sur la [création d’un profil Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- En savoir plus sur le [scénario de bleu-vert](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/).

## <a name="backup"></a>Sauvegarde

Vous ne pouvez pas migrer des travaux de sauvegarde Azure et des captures instantanées depuis Azure Germany vers Azure global.

Pour plus d'informations : 

- Actualisez vos connaissances en complétant les [tutoriels sur Sauvegarde Azure](https://docs.microsoft.com/azure/backup/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Sauvegarde Azure](../backup/backup-introduction-to-azure-backup.md).

## <a name="scheduler"></a>Scheduler

Azure Scheduler est déconseillé. Utilisez Azure Logic Apps pour créer des tâches de planification.

Pour plus d'informations :

- Familiarisez-vous avec les fonctionnalités d’Azure Logic Apps en complétant les [tutoriels sur Logic Apps](https://docs.microsoft.com/azure/logic-apps/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="network-watcher"></a>Network Watcher

Actuellement, il n’est pas possible de migrer une instance Azure Network Watcher depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle instance Network Watcher dans Azure global. Ensuite, comparez les résultats entre l’ancien et le nouvel environnement. 

Pour plus d'informations :

- Actualisez vos connaissances en complétant les [tutoriels sur Network Watcher](https://docs.microsoft.com/azure/network-watcher/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).
- En savoir plus sur les [journaux d’activité des flux de groupe de sécurité réseau](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- En savoir plus sur le [moniteur de connexion](../network-watcher/connection-monitor.md).

## <a name="site-recovery"></a>Site Recovery

Vous ne pouvez pas migrer votre configuration Azure Site Recovery actuelle vers Azure global. Vous devez configurer une nouvelle solution Site Recovery dans Azure global.

Pour plus d’informations sur Site Recovery et pour savoir comment migrer des machines virtuelles depuis Azure Allemagne vers Azure global, consultez [Comment utiliser Site Recovery](./germany-migration-compute.md#compute-iaas).

Actualisez vos connaissances en complétant ces tutoriels pas à pas :

- [Récupération d’urgence d’Azure vers Azure](https://docs.microsoft.com/azure/site-recovery/#azure-to-azure)
- [Récupération d’urgence de VMware vers Azure](https://docs.microsoft.com/azure/site-recovery/#vmware)
- [Récupération d’urgence de Hyper-V vers Azure](https://docs.microsoft.com/azure/site-recovery/#hyper-v)

## <a name="azure-policies"></a>Stratégies Azure

Vous ne pouvez pas migrer directement des stratégies depuis Azure Allemagne vers Azure global. En règle générale, l’étendue des stratégies assignées change durant la migration. Cela est particulièrement vrai lorsque l’abonnement est différent dans l’environnement cible, comme dans ce scénario. Toutefois, vous pouvez conserver les définitions de stratégie et les réutiliser dans Azure global.

Dans Azure CLI, exécutez la commande suivante pour répertorier toutes les stratégies de votre environnement actuel.

> [!NOTE]
> Veillez à basculer vers l’environnement AzureGermanCloud dans Azure CLI avant d’exécuter les commandes suivantes.


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

Exportez uniquement les stratégies qui ont la valeur **PolicyType** **personnalisée**. Exportez **policyRule** dans un fichier. L’exemple suivant exporte la stratégie personnalisée « Allow Germany Central Only » (version courte : `allowgconly`) vers un fichier dans le dossier actuel : 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

Le fichier d’exportation ressemblera à l’exemple suivant :

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Ensuite, basculez vers l’environnement Azure global. Modifiez la règle de stratégie en modifiant le fichier. Par exemple, remplacez `germanycentral` par `westeurope`.

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Créez la nouvelle stratégie :

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

Vous disposez maintenant d’une nouvelle stratégie nommée `allowweonly`. La stratégie n’accepte que la région Europe Ouest.

Attribuez la stratégie aux étendues de votre nouvel environnement selon vos besoins. Vous pouvez renseigner les anciennes affectations dans Azure Allemagne en exécutant la commande suivante :

```azurecli
az policy assignment list
```

Pour plus d'informations :

- Actualisez vos connaissances en complétant les [tutoriels sur les stratégies Azure](../governance/policy/tutorials/create-and-manage.md).
- Découvrez comment [afficher les stratégies à l’aide de l’interface Azure CLI](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli) ou [afficher des stratégies à l’aide de PowerShell](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell).
- Découvrez comment [créer une définition de stratégie à l’aide de l’interface Azure CLI](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli) ou [créer une définition de stratégie à l’aide de PowerShell](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Médias](./germany-migration-media.md)
