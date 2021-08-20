---
title: Prérequis | Mode de connexion directe
description: Prérequis en vue de déployer le contrôleur de données en mode de connexion directe.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 143a601f89301d3ed4302efa6e978cba53539e2c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733919"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>Prérequis en vue de déployer le contrôleur de données en mode de connectivité directe

Cet article explique comment préparer le déploiement d’un contrôleur de données pour les services de données avec Azure Arc en mode de connexion directe. Le déploiement d’un contrôleur de données Azure Arc nécessite une compréhension et des concepts supplémentaires, comme décrit dans [Planifier le déploiement des services de données Azure Arc](plan-azure-arc-data-services.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

À un niveau élevé, les prérequis à la création d’un contrôleur de données Azure Arc en mode de connectivité **directe** sont les suivants :

1. Connecter le cluster Kubernetes à Azure à l’aide de Kubernetes avec Azure Arc
2. Créer le principal du service et configurer des rôles pour les métriques
3. Créer un contrôleur de données pour les services de données avec Azure Arc. Cette étape implique la création des éléments suivants :
    - Extension des services de données Azure Arc
    - Emplacement personnalisé
    - Contrôleur de données Azure Arc

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Connecter le cluster Kubernetes à Azure à l’aide de Kubernetes avec Azure Arc

La connexion de votre cluster Kubernetes à Azure peut être effectuée à l’aide de l’interface CLI ```az```, avec les extensions suivantes, ainsi que Helm.

#### <a name="install-tools"></a>Installer des outils

- Helm version 3.3+ ([installation](https://helm.sh/docs/intro/install/))
- Installez Azure CLI ou mettez-le à niveau vers sa version la plus récente ([installation](/sql/azdata/install/deploy-install-azdata))

#### <a name="add-extensions-for-azure-cli"></a>Ajouter des extensions pour Azure CLI

Installez les versions les plus récentes des extensions az suivantes :
- ```k8s-extension```
- ```connectedk8s```
- ```k8s-configuration```
- `customlocation`

Exécutez les commandes suivantes pour installer les extensions CLI az :

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

Si les extensions ```k8s-extension```, ```connectedk8s```, ```k8s-configuration``` et `customlocation` sont déjà installées, mettez-les à jour vers la version la plus récente à l’aide de la commande suivante :

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```
#### <a name="connect-your-cluster-to-azure"></a>Connecter votre cluster à Azure

Pour effectuer cette tâche, suivez les étapes décrites dans [Connecter un cluster Kubernetes existant à Azure Arc](../kubernetes/quickstart-connect-cluster.md).

Une fois que vous avez connecté votre cluster à Azure, continuez en créant un principal de service. 

## <a name="2-create-service-principal-and-configure-roles-for-metrics"></a>2. Créer un principal de service et configurer des rôles pour les métriques

Suivez les étapes décrites dans l’article [Charger les métriques](upload-metrics-and-logs-to-azure-monitor.md), puis créez un principal de service et accordez les rôles, tel qu’indiqué. 

Les informations SPN d’ID Client, d’ID Locataire et de Secret client seront nécessaires au moment de [déployer le contrôleur de données Azure Arc](create-data-controller-direct-azure-portal.md). 

## <a name="3-create-azure-arc-data-services"></a>3. Créer des services de données Azure Arc

Après avoir rempli ces prérequis, vous pouvez [déployer le contrôleur de données Azure Arc | Mode de connexion directe](create-data-controller-direct-azure-portal.md).


