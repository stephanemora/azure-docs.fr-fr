---
title: Mise à niveau des agents Kubernetes avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Contrôler les mises à niveau des agents Kubernetes avec Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, conteneurs, agent, mise à niveau
ms.openlocfilehash: 172654f655e594c295a8807b417c32d7849bd4b1
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121913"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Mise à niveau des agents Kubernetes avec Azure Arc

Kubernetes avec Azure Arc fournit des fonctionnalités de mise à niveau automatique et de mise à niveau manuelle pour ses agents. Si vous désactivez la mise à niveau automatique pour utiliser à la place la mise à niveau manuelle, la politique de support des versions s’applique aux agents Arc et au cluster Kubernetes sous-jacent.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Activer ou désactiver la mise à niveau automatique au moment de la connexion du cluster à Azure Arc

Kubernetes avec Azure Arc fournit à ses agents des fonctionnalités de mise à niveau automatique prêtes à l’emploi.

La commande suivante permet de connecter un cluster à Azure Arc avec la mise à niveau automatique **activée** :

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Quand la mise à niveau automatique est activée, l’agent interroge Azure toutes les heures pour déterminer si une version plus récente des agents est disponible. Si l’agent trouve une version plus récente, il déclenche une mise à niveau du chart Helm pour les agents Azure Arc.

Pour refuser la mise à niveau automatique, spécifiez le paramètre `--disable-auto-upgrade` au moment de la connexion du cluster à Azure Arc. La commande suivante permet de connecter un cluster à Azure Arc avec la mise à niveau automatique **désactivée** :

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Si vous comptez désactiver la mise à niveau automatique, consultez la [politique de support des versions](#version-support-policy) pour Kubernetes avec Azure Arc.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Activer/désactiver la mise à niveau automatique après la connexion du cluster à Azure Arc

Une fois que vous avez connecté un cluster à Azure Arc, vous pouvez activer/désactiver la fonctionnalité de mise à niveau automatique à l’aide de la commande `az connectedk8s update`, comme indiqué ci-dessous :

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Mettre à niveau manuellement les agents

Si vous avez désactivé la mise à niveau automatique des agents, vous pouvez lancer manuellement la mise à niveau de ces agents à l’aide de la commande `az connectedk8s upgrade`, comme indiqué ci-dessous :

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Kubernetes avec Azure Arc suit le [schéma de gestion sémantique de versions](https://semver.org/) standard de `MAJOR.MINOR.PATCH` pour la gestion de versions de ses agents. 

Chaque chiffre de la version indique la compatibilité générale avec la version précédente :

* Les **versions majeures** changent en cas de mises à jour d’API incompatibles ou quand la compatibilité descendante est éventuellement rompue.
* Les **versions mineures** changent quand les changements apportés aux fonctionnalités ont une compatibilité descendante avec d’autres versions mineures.
* Les **versions des patchs** changent quand des résolutions de bogues ont une compatibilité descendante.

## <a name="version-support-policy"></a>Politique de support des versions

Quand vous créez des tickets de support, la politique suivante de support des versions est appliquée à Kubernetes avec Azure Arc :

* Les agents Kubernetes avec Azure Arc ont une fenêtre de support de « N-2 », où « N » correspond à la dernière version mineure des agents. 
  * Par exemple, si Kubernetes avec Azure Arc introduit la version 0.28.a aujourd’hui, les versions 0.28.a, 0.28.b, 0.27.c, 0.27.d, 0.26.e et 0.26.f sont prises en charge par Azure Arc.

* Les clusters Kubernetes qui se connectent à Azure Arc ont une fenêtre de support de « N-2 », où « N » correspond à la dernière version mineure stable du [Kubernetes amont](https://github.com/kubernetes/kubernetes/releases). 
  * Par exemple, si Kubernetes introduit la version 1.20.a aujourd’hui, les versions 1.20.a, 1.20.b, 1.19.c, 1.19.d, 1.18.e et 1.18.f sont prises en charge.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>À quelle fréquence les versions mineures de Kubernetes avec Azure Arc sont-elles disponibles ?

Une version mineure des agents Kubernetes avec Azure Arc est publiée environ une fois par mois.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Que se passe-t-il si j’utilise une version d’agent ou une version de Kubernetes en dehors de la fenêtre de support officielle ?

« Hors support » signifie que les versions que vous exécutez sont exclues des versions « N-2 » prises en charge pour les agents et les clusters Kubernetes amont. Pour permettre le suivi du ticket de support, vous êtes invité à mettre à niveau le cluster et les agents vers une version prise en charge.

## <a name="next-steps"></a>Étapes suivantes

* Parcourez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./connect-cluster.md).
* Un cluster Kubernetes est-il déjà connecté à Azure Arc ? [Créez des configurations sur votre cluster Kubernetes compatible avec Arc](./use-gitops-connected-cluster.md).
* Découvrez comment [utiliser Azure Policy pour appliquer des configurations à grande échelle](./use-azure-policy.md).