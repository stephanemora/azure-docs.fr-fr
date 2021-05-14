---
title: Prérequis | Mode de connexion directe
description: Prérequis en vue de déployer le contrôleur de données en mode de connexion directe.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716290"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Déployer le contrôleur de données - Mode de connexion directe (Prérequis)

Cet article explique comment préparer le déploiement d’un contrôleur de données pour les services de données avec Azure Arc en mode de connexion directe.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Les prérequis se résument sommairement à :

1. Installer des outils
1. Ajouter des extensions
1. Créer le principal du service et configurer des rôles pour les métriques
1. Connecter le cluster Kubernetes à Azure à l’aide de Kubernetes avec Azure Arc

Après avoir rempli ces prérequis, vous pouvez [déployer le contrôleur de données Azure Arc | Mode de connexion directe](deploy-data-controller-direct-mode.md).

Les sections restantes de cet article identifient les prérequis.

## <a name="install-tools"></a>Installer des outils

- Helm version 3.3+ ([installation](https://helm.sh/docs/intro/install/))
- Azure CLI ([installation](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Ajouter des extensions pour Azure CLI

Par ailleurs, les extensions az suivantes sont également nécessaires :
- Extension Azure CLI `k8s-extension` (0.2.0)
- Azure CLI `customlocation` (0.1.0)

Voici un exemple de version `az` et ses extensions CLI :

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Créer un principal du service et configurer des rôles pour les métriques

Suivez les étapes décrites dans l’article [Charger les métriques](upload-metrics-and-logs-to-azure-monitor.md), puis créez un principal de service et accordez les rôles, tel qu’indiqué. 

Les informations SPN d’ID Client, d’ID Locataire et de Secret client seront nécessaires au moment de [déployer le contrôleur de données Azure Arc](deploy-data-controller-direct-mode.md). 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Connecter le cluster Kubernetes à Azure à l’aide de Kubernetes avec Azure Arc

Pour effectuer cette tâche, suivez les étapes décrites dans [Connecter un cluster Kubernetes existant à Azure Arc](../kubernetes/quickstart-connect-cluster.md).

## <a name="next-steps"></a>Étapes suivantes

Après avoir rempli ces prérequis, vous pouvez [déployer le contrôleur de données Azure Arc | Mode de connexion directe](deploy-data-controller-direct-mode.md).
