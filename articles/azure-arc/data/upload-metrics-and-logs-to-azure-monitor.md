---
title: Charger les données d’utilisation, les métriques et les journaux sur Azure
description: Charger l’inventaire des ressources, les données d’utilisation, les métriques et les journaux sur Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 6f314b8918b415c0449722d1229c6de47af1cac5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532305"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure"></a>Charger les données d’utilisation, les métriques et les journaux sur Azure

Vous pouvez régulièrement exporter les informations d’utilisation à des fins de facturation et de surveillance des métriques et des journaux, puis les charger dans Azure. L’exportation et le chargement d’un de ces trois types de données a aussi pour effet de créer et mettre à jour le contrôleur de données, l’instance managée SQL et les ressources de groupe de serveurs PostgreSQL Hyperscale dans Azure.

Avant de pouvoir charger des données d’utilisation, des métriques ou des journaux, vous devez :

* Installer des outils 
* [Inscrire le fournisseur de ressources `Microsoft.AzureArcData`](#register-the-resource-provider) 
* [Créer le principal du service](#create-service-principal)

## <a name="install-tools"></a>Installer des outils

Les outils requis sont les suivants : 
* Azure CLI (az) 
* Extension `arcdata` 

Voir [Installer des outils](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

Avant de charger des métriques ou des données utilisateur dans Azure, vous devez vous assurer que le fournisseur de ressources `Microsoft.AzureArcData` est inscrit dans votre abonnement Azure.

Pour vérifier le fournisseur de ressources, exécutez la commande suivante :

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Si le fournisseur de ressources n’est pas encore inscrit dans votre abonnement, vous pouvez l’inscrire. Pour l’inscrire, exécutez la commande suivante.  L’exécution de cette commande peut prendre une ou deux minutes.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Créer un principal du service

Le principal du service permet de charger les données d’utilisation et des métriques.

Suivez ces commandes pour créer le principal du service de chargement de vos métriques :

> [!NOTE]
> La création d’un principal de service nécessite [certaines autorisations dans Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Pour créer un principal de service, mettez à jour l’exemple de commande suivant. Remplacez `<ServicePrincipalName>`, `SubscriptionId` et `resourcegroup` par vos valeurs et exécutez la commande :

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/<SubscriptionId>/resourceGroups/<resourcegroup>
```

Si vous avez créé le principal de service précédemment et que vous devez simplement récupérer les informations d’identification actuelles, exécutez la commande suivante pour réinitialiser les informations d’identification.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Par exemple, pour créer un principal de service nommé `azure-arc-metrics`, exécutez la commande suivante

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

Exemple de sortie :

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Enregistrez les valeurs `appId`, `password` et `tenant` dans une variable d’environnement pour une utilisation ultérieure. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Une fois que vous avez créé le principal de service, affectez le principal de service au rôle approprié. 

## <a name="assign-roles-to-the-service-principal"></a>Affecter des rôles au principal de service

Exécutez cette commande pour affecter le principal de service au rôle `Monitoring Metrics Publisher` sur l’abonnement où se trouvent les ressources de votre instance de base de données :

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Vous devez utiliser des guillemets doubles pour les noms de rôle lors de l’exécution à partir d’un environnement Windows.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

Exemple de sortie :

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="verify-service-principal-role"></a>Vérifier le rôle du principal de service

```azurecli
az role assignment list -o table
```

Une fois le principal de service affecté au rôle approprié, vous pouvez continuer à charger les métriques ou les données utilisateur. 



## <a name="upload-logs-metrics-or-usage-data"></a>Charger les journaux, les métriques ou les données d’utilisation

Les étapes spécifiques pour charger les journaux, les métriques ou les données d’utilisation varient en fonction du type d’informations que vous chargez. 

[Charger les journaux sur Azure Monitor](upload-logs.md)

[Charger des métriques sur Azure Monitor](upload-metrics.md)

[Charger des données d’utilisation sur Azure](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-and-metrics"></a>Conseils généraux sur l’exportation et le chargement de l’utilisation et des métriques

Les opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur les services de données compatibles Azure Arc sont journalisées à des fins de facturation et de surveillance. Il existe des services en arrière-plan qui surveillent ces opérations CRUD et calculent la consommation de manière appropriée. Le calcul réel de l’utilisation ou de la consommation se produit de manière planifiée et est effectué en arrière-plan. 

Chargez l’utilisation seulement une fois par jour. Lorsque les informations d’utilisation sont exportées et chargées plusieurs fois au cours de la même période de 24 heures, seul l’inventaire des ressources est mis à jour dans le Portail Azure mais pas dans l’utilisation des ressources.

Pour le chargement des mesures, Azure Monitor accepte uniquement les 30 dernières minutes de données ([En savoir plus](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). La recommandation pour le chargement des métriques est de charger les métriques immédiatement après la création du fichier d’exportation afin de pouvoir afficher l’ensemble du jeu de données dans le Portail Azure. Par exemple, supposons que vous avez exporté les métriques à 14 h et que vous avez exécuté la commande de chargement à 14 h 50. Étant donné qu’Azure Monitor n’accepte que les données des 30 dernières minutes, vous ne verrez aucune donnée dans le portail. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur les principaux de service](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Charger des données de facturation dans Azure et les afficher dans le portail Azure](view-billing-data-in-azure.md)

[Afficher la ressource de contrôleur de données Azure Arc dans le portail Azure](view-data-controller-in-azure-portal.md)
