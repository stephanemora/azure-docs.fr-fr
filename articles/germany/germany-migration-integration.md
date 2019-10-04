---
title: Migrer des ressources d’intégration Azure d’Azure Germany vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources d’intégration Azure d’Azure Germany vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: d85c2e7c1aa3738ce6a9d3130d2ddc400c333a9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033735"
---
# <a name="migrate-integration-resources-to-global-azure"></a>Migrer des ressources d’intégration vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources d’intégration Azure d’Azure Germany vers Azure global.

## <a name="service-bus"></a>Service Bus

Les services Azure Service Bus ne disposent d’aucune fonctionnalité d’exportation ou d’importation des données. Pour migrer des ressources Service Bus d'Azure Germany vers Azure global, vous pouvez exporter les ressources [en tant que modèle Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates). Ensuite, adaptez le modèle exporté à Azure global et recréez les ressources.

> [!NOTE]
> L’exportation d’un modèle Resource Manager ne copie pas les données (par exemple, les messages) ; elle recrée uniquement les métadonnées.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Azure Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="service-bus-metadata"></a>Métadonnées Service Bus

Les éléments de métadonnées Service Bus suivants sont recréés lorsque vous exportez un modèle Resource Manager :

- Espaces de noms
- Files d’attente
- Rubriques
- Abonnements
- Règles
- Règles d’autorisation

### <a name="keys"></a>Clés

Les étapes d’exportation et de recréation précédentes ne copient pas les clés de signature d'accès partagé associées aux règles d'autorisation. Si vous devez conserver les clés de signature d'accès partagé, utilisez l’applet de commande `New-AzServiceBuskey` avec le paramètre optionnel `-Keyvalue` pour accepter la clé comme une chaîne. L’applet de commande mise à jour est disponible dans le [module Az Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="usage-example"></a>Exemple d’utilisation

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> Vous devez mettre à jour vos applications pour utiliser une nouvelle chaîne de connexion même si vous conservez les clés. Les noms d'hôtes DNS sont différents dans Azure Germany et Azure global.

### <a name="sample-connection-strings"></a>Exemples de chaîne de connexion

**Azure Allemagne**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**Global Azure**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

Pour plus d'informations :

- Actualisez vos connaissances en effectuant les [tutoriels Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/#step-by-step-tutorials).
- Découvrez comment [exporter des modèles Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) ou lisez la présentation [d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Examinez la [vue d’ensemble de Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="logic-apps"></a>Logic Apps

Le service Azure Logic Apps n’est pas disponible dans Azure Germany. Cependant, Azure Scheduler, qui est disponible, est déconseillé. Utilisez Logic Apps pour créer des tâches de planification dans Azure global.

Pour plus d'informations :

- Familiarisez-vous avec les fonctionnalités d’Azure Logic Apps en complétant les [tutoriels sur Logic Apps](https://docs.microsoft.com/azure/logic-apps/#step-by-step-tutorials).
- Examinez la [vue d’ensemble d’Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Médias](./germany-migration-media.md)
