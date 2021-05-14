---
title: Déployer le contrôleur de données Azure Arc | Mode de connexion directe
description: Explique comment déployer le contrôleur de données en mode de connexion directe.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 05/04/2021
ms.topic: overview
ms.openlocfilehash: 2cd9e1b0f3ba11eb051eb8d7f829b0c3b8b77c73
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748112"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Déployer le contrôleur de données Azure Arc | Mode de connexion directe

Cet article décrit comment déployer le contrôleur de données Azure Arc en mode de connexion directe pendant la préversion actuelle de cette fonctionnalité. 

Actuellement, vous pouvez créer le contrôleur de données Azure Arc à partir du portail Azure. Les autres outils des services de données compatibles avec Azure Arc ne prennent pas en charge la création du contrôleur de données en mode de connexion directe. Pour plus d’informations, consultez les [notes de publication](release-notes.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Répondre aux prérequis

Avant de commencer, vérifiez que vous répondez aux prérequis dans [Déployer le contrôleur de données – Mode de connexion directe – Prérequis](deploy-data-controller-direct-mode-prerequisites.md).

Globalement, cet article explique comment effectuer ces tâches :

1. Créer une extension des services de données avec Azure Arc. 
1. Créer un emplacement personnalisé.
1. Déployer le contrôleur de données à partir du portail.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Créer une extension des services de données avec Azure Arc

Utilisez l’interface CLI k8s-extension pour créer une extension des services de données.

### <a name="set-environment-variables"></a>Définir des variables d’environnement

Définissez les variables d’environnement suivantes qui seront utilisées à la prochaine étape.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Créer l’extension des services de données Arc

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> L’installation de l’extension des services de données Arc peut prendre quelques minutes.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Vérifier que l’extension des services de données Arc est créée

Vous pouvez vérifier que l’extension des services de données avec Arc est créée, à partir du portail ou en vous connectant directement au cluster Kubernetes avec Arc. 

#### <a name="azure-portal"></a>Portail Azure
1. Connectez-vous au portail Azure et accédez au groupe de ressources dans lequel se trouve la ressource de cluster connectée Kubernetes.
1. Sélectionnez le cluster Kubernetes avec Arc (Type = « Kubernetes - Azure Arc ») dans lequel l’extension a été déployée.
1. Dans la partie gauche du volet de navigation, sous **Paramètres**, sélectionnez « Extensions (préversion) ».
1. Vous devez voir l’extension qui vient d’être créée, dans l’état « Installée ».

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Tableau de bord des extensions":::

#### <a name="kubectl-cli"></a>Interface CLI kubectl

1. Connectez-vous à votre cluster Kubernetes au moyen d’une fenêtre de terminal.
1. Exécutez la commande ci-dessous et veillez à ce que (1) l’espace de noms mentionné plus haut soit créé et (2) que le pod `bootstrapper` se trouve dans l’état En cours d’exécution avant de passer à l’étape suivante.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Par exemple, le code suivant obtient les pods de l’espace de noms `arc`.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Créer un emplacement personnalisé à l’aide de l’extension CLI d’emplacement personnalisé

Un emplacement personnalisé est une ressource Azure comparable à un espace de noms dans un cluster Kubernetes.  Les emplacements personnalisés sont utilisés en tant que cibles pour déployer des ressources vers ou à partir d’Azure. Apprenez-en davantage sur les emplacements personnalisés dans la [Documentation relative aux emplacements personnalisés s’appuyant sur Kubernetes avec Azure Arc](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Définir des variables d’environnement

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Valider la création de l’emplacement personnalisé

À partir du terminal, exécutez la commande ci-dessous pour lister les emplacements personnalisés, puis vérifiez que l’**État de provisionnement** indique Opération réussie :

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Créer le contrôleur de données Azure Arc

Après avoir créé l’extension et l’emplacement personnalisé, poursuivez sur le portail Azure pour déployer le contrôleur de données Azure Arc.

1. Connectez-vous au portail Azure.
1. Recherchez le « contrôleur de données Azure Arc » dans la Place de marché Azure et lancez le processus de création.
1. Dans la section **Prérequis**, vérifiez que le cluster Kubernetes avec Azure Arc (mode direct) est bien sélectionné et passez à l’étape suivante.
1. Dans la section **Détails du contrôleur de données**, choisissez un abonnement et un groupe de ressources.
1. Indiquez un nom pour le contrôleur de données.
1. Choisissez un profil de configuration en fonction du fournisseur de distribution Kubernetes sur lequel vous effectuez le déploiement.
1. Choisissez l’emplacement personnalisé que vous avez créé à l’étape précédente.
1. Fournissez les informations de connexion et le mot de passe de l’administrateur du contrôleur de données.
1. Renseignez l’Id Client, l’Id Locataire et le secret client du principal de service qui sera utilisé pour créer les objets Azure. Pour obtenir des instructions détaillées sur la création d’un compte de principal de service, et sur les rôles devant être accordés au compte, consultez [Charger les métriques](upload-metrics-and-logs-to-azure-monitor.md).
1. Cliquez sur **Suivant**, passez en revue la page récapitulative de tous les détails, puis cliquez sur **Créer**.

## <a name="monitor-the-creation"></a>Superviser la création

Lorsque l’état du déploiement sur le portail Azure indique que le déploiement a réussi, vous pouvez vérifier l’état du déploiement du contrôleur de données Arc sur le cluster, de la manière suivante :

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Étapes suivantes

[Créer un groupe de serveurs PostgreSQL Hyperscale compatibles avec Azure Arc](create-postgresql-hyperscale-server-group.md)

[Créer une instance gérée Azure SQL sur Azure Arc](create-sql-managed-instance.md)
