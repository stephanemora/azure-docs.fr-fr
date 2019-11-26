---
title: Interface de ligne de commande Azure Service Fabric - sfctl sa-cluster | Microsoft Docs
description: Décrit les commandes de cluster autonome sfctl de l’interface de ligne de commande Azure Service Fabric.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: ecdd288d7cb320b91ab4c69697d334f8d9459e62
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901012"
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

La configuration du cluster contient les propriétés du cluster qui incluent des types de nœud différents sur le cluster, les configurations de sécurité, l’erreur et les topologies des domaines de mise à niveau, etc.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --configuration-api-version [requis] | Version de l’API de la configuration json du cluster autonome. |
| --timeout -t | Délai d’attente du serveur pour l’exécution de l’opération en secondes. Il spécifie la durée pendant laquelle le client attend la fin de l’opération demandée. La valeur par défaut de ce paramètre est de 60 secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Commence la mise à niveau de la configuration d’un cluster autonome Service Fabric.

Valide les paramètres de mise à niveau de la configuration fournis et démarre la mise à niveau de la configuration du cluster si les paramètres sont valides.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --cluster-config [requis] | Configuration du cluster. |
| --application-health-policies | Dictionnaire au format JSON comprenant des paires Nom de type d’application/Pourcentage maximal de non intégrité avant déclenchement d’une erreur. |
| --delta-unhealthy-nodes | Pourcentage maximal autorisé de dégradation de l’intégrité des nœuds delta pendant la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --health-check-retry | Délai séparant les tentatives d'exécution des contrôles d'intégrité si l'application ou le cluster n'est pas sain.  Valeur par défaut \: PT0H0M0S. |
| --health-check-stable | Durée pendant laquelle l’application ou le cluster doivent rester sains avant que la mise à niveau ne passe au domaine de mise à niveau suivant.  Valeur par défaut \: PT0H0M0S. <br><br> Elle est d’abord interprétée en tant que chaîne représentant une durée ISO 8601. Si cette tentative échoue, elle est interprétée comme un nombre représentant le nombre total de millisecondes. |
| --health-check-wait | Délai d’attente entre l’achèvement d’un domaine de mise à niveau et le démarrage du processus des contrôles d’intégrité.  Valeur par défaut \: PT0H0M0S. |
| --timeout -t | Valeur par défaut \: 60. |
| --unhealthy-applications | Pourcentage maximal autorisé d’applications non saines au cours de la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --unhealthy-nodes | Pourcentage maximal autorisé de nœuds non sains au cours de la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --upgrade-domain-delta-unhealthy-nodes | Pourcentage maximal autorisé de dégradation de l’intégrité des nœuds delta de domaine de mise à niveau pendant la mise à niveau. Les valeurs autorisées sont les valeurs entières comprises entre zéro et 100. |
| --upgrade-domain-timeout | Durée d’exécution de chaque domaine de mise à niveau avant l’exécution de FailureAction.  Valeur par défaut \: PT0H0M0S. <br><br> Elle est d’abord interprétée en tant que chaîne représentant une durée ISO 8601. Si cette tentative échoue, elle est interprétée comme un nombre représentant le nombre total de millisecondes. |
| --upgrade-timeout | Durée d’exécution de l’ensemble de la mise à niveau avant l’exécution de FailureAction.  Valeur par défaut \: PT0H0M0S. <br><br> Elle est d’abord interprétée en tant que chaîne représentant une durée ISO 8601. Si cette tentative échoue, elle est interprétée comme un nombre représentant le nombre total de millisecondes. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

### <a name="examples"></a>Exemples

Lancer une mise à jour de la configuration du cluster
``` 
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-    
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"   
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Permet d’obtenir l’état de mise à niveau de la configuration d’un cluster autonome Service Fabric.

Permet d’obtenir les détails de l’état de mise à niveau de la configuration d’un cluster autonome Service Fabric.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --timeout -t | Délai d’attente du serveur pour l’exécution de l’opération en secondes. Il spécifie la durée pendant laquelle le client attend la fin de l’opération demandée. La valeur par défaut de ce paramètre est de 60 secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).