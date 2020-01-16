---
title: Interface CLI Azure Service Fabric - sfctl rpm
description: Apprenez-en davantage sur sfctl, l’interface de ligne de commande d’Azure Service Fabric. Contient la liste des commandes liées au service gestionnaire de réparation.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 674970276046034d13801db7c1bb4ab5175385fb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639086"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Interroge et envoie des commandes vers le service gestionnaire de réparation.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| approve-force | Force l’approbation de la tâche de réparation donnée. |
| delete | Supprime une tâche de réparation terminée. |
| list | Permet d’obtenir une liste de tâches de réparation correspondant aux filtres donnés. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
Force l’approbation de la tâche de réparation donnée.

Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --task-id [Requis] | ID de la tâche de réparation. |
| --version | Numéro de version actuelle de la tâche de réparation. Si autre que zéro, la requête aboutit uniquement si cette valeur correspond à la version actuelle réelle de la tâche de réparation. Si égale à zéro, aucune vérification de version n’est effectuée. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Supprime une tâche de réparation terminée.

Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --task-id [Requis] | ID de la tâche de réparation terminée à supprimer. |
| --version | Numéro de version actuelle de la tâche de réparation. Si autre que zéro, la requête aboutit uniquement si cette valeur correspond à la version actuelle réelle de la tâche de réparation. Si égale à zéro, aucune vérification de version n’est effectuée. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
Permet d’obtenir une liste de tâches de réparation correspondant aux filtres donnés.

Cette API prend en charge la plateforme Service Fabric ; elle n’est pas censée être utilisée directement à partir de votre code.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --executor-filter | Nom de l’Exécuteur de réparations dont les tâches revendiquées doivent être incluses dans la liste. |
| --state-filter | OR au niveau du bit des valeurs suivantes, qui spécifie les états de tâches qu’il convient d’inclure dans la liste des résultats. <br> 1 - Créé <br>2 - Revendiqué  <br>4 - Préparation  <br>8 - Approuvé  <br>16 - En cours d'exécution  <br>32 - Restauration  <br>64 - Terminé |
| --task-id-filter | Préfixe d’ID de tâche de réparation à mettre en correspondance. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmentez le niveau de détail de la journalisation pour afficher tous les journaux de débogage. |
| --help -h | Affichez ce message d’aide et quittez. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requêtes JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmentez le niveau de détail de la journalisation. Utilisez --debug pour des journaux de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).