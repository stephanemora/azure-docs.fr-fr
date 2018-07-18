---
title: Interface de ligne de commande Azure Service Fabric - sfctl sa-cluster | Microsoft Docs
description: Décrit les commandes de cluster autonome sfctl de l’interface de ligne de commande Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763936"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Permet de gérer les clusters Service Fabric autonomes.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| config | Permet d’obtenir la configuration du cluster autonome Service Fabric. |
| config-upgrade | Commence la mise à niveau de la configuration d’un cluster autonome Service Fabric. |
| upgrade-status | Permet d’obtenir l’état de mise à niveau de la configuration d’un cluster autonome Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Permet d’obtenir la configuration du cluster autonome Service Fabric.

Permet d’obtenir la configuration du cluster autonome Service Fabric. La configuration du cluster contient les propriétés du cluster qui incluent des types de nœud différents sur le cluster, les configurations de sécurité, l’erreur et les topologies des domaines de mise à niveau, etc.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --configuration-api-version [requis] | Version de l’API de la configuration json du cluster autonome. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Commence la mise à niveau de la configuration d’un cluster autonome Service Fabric.

Valide les paramètres de mise à niveau de la configuration fournis et démarre la mise à niveau de la configuration du cluster si les paramètres sont valides.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --cluster-config [requis] | Configuration du cluster. |
| --delta-unhealthy-nodes | Pourcentage maximal autorisé de dégradation de l’intégrité des nœuds delta pendant la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --health-check-retry | Délai séparant les tentatives d’exécution des contrôles d’intégrité si l’application ou le cluster n’est pas intègre.  Valeur par défaut \: PT0H0M0S. |
| --health-check-stable | Durée pendant laquelle l’application ou le cluster doivent rester intègres.  Valeur par défaut \: PT0H0M0S. |
| --health-check-wait | Délai d’attente entre l’achèvement d’un domaine de mise à niveau et le démarrage du processus des contrôles d’intégrité.  Valeur par défaut \: PT0H0M0S. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |
| --unhealthy-applications | Pourcentage maximal autorisé d’applications non saines au cours de la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --unhealthy-nodes | Pourcentage maximal autorisé de nœuds non sains au cours de la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --upgrade-domain-delta-unhealthy-nodes | Pourcentage maximal autorisé de dégradation de l’intégrité des nœuds delta de domaine de mise à niveau pendant la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --upgrade-domain-timeout | Délai d’expiration du domaine de mise à niveau.  Valeur par défaut \: PT0H0M0S. |
| --upgrade-timeout | Délai d’expiration de la mise à niveau.  Valeur par défaut \: PT0H0M0S. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Permet d’obtenir l’état de mise à niveau de la configuration d’un cluster autonome Service Fabric.

Permet d’obtenir les détails de l’état de mise à niveau de la configuration d’un cluster autonome Service Fabric.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets. |

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).