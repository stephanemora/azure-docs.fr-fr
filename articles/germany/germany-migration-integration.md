---
title: Migrer des ressources d’intégration Azure, Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources d’intégration Azure d’Azure Germany vers Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 0a4ea6393741e15d8dce94869213c62c08537c7e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122523975"
---
# <a name="migrate-integration-resources-to-global-azure"></a>Migrer des ressources d’intégration vers Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Cet article contient des informations qui peuvent vous aider à migrer des ressources d’intégration Azure d’Azure Germany vers Azure global.

## <a name="service-bus"></a>Service Bus

Les services Azure Service Bus ne disposent d’aucune fonctionnalité d’exportation ou d’importation des données. Pour migrer des ressources Service Bus d'Azure Germany vers Azure global, vous pouvez exporter les ressources [en tant que modèle Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md). Ensuite, adaptez le modèle exporté à Azure global et recréez les ressources.

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

### <a name="keys"></a>Keys

Les étapes d’exportation et de recréation précédentes ne copient pas les clés de signature d'accès partagé associées aux règles d'autorisation. Si vous devez conserver les clés de signature d'accès partagé, utilisez l’applet de commande `New-AzServiceBuskey` avec le paramètre optionnel `-Keyvalue` pour accepter la clé comme une chaîne. L’applet de commande mise à jour est disponible dans le [module Az Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="usage-example"></a>Exemple d’utilisation

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
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

**Azure global**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

Pour plus d’informations :

- Actualisez vos connaissances en effectuant les [tutoriels Service Bus](../service-bus-messaging/index.yml).
- Découvrez comment [exporter des modèles Resource Manager](../azure-resource-manager/templates/export-template-portal.md) ou lisez la présentation [d’Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Examinez la [vue d’ensemble de Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="logic-apps"></a>Logic Apps

Azure Logic Apps n’est pas disponible dans Azure Allemagne, mais vous pouvez créer des tâches de planification en utilisant Logic Apps dans Azure global. Bien que précédemment disponible dans Azure Allemagne, Azure Scheduler est mis hors service.

Pour plus d’informations :

- Apprenez-en davantage en suivant les [tutoriels Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md).
- Examinez la [vue d’ensemble d’Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils d'administration](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
