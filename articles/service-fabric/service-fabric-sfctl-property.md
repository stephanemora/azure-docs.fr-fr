---
title: 'CLI Azure Service Fabric : sfctl property | Microsoft Docs'
description: Décrit les commandes sfctl property de l’interface CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556589"
---
# <a name="sfctl-property"></a>sfctl property
Stocke et interroge des propriétés avec des noms Service Fabric.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Permet de supprimer la propriété Service Fabric spécifiée. |
| get | Permet d’obtenir la propriété Service Fabric spécifiée. |
| list | Permet d’obtenir des informations sur toutes les propriétés Service Fabric sous un nom donné. |
| put | Permet de créer ou mettre à jour une propriété Service Fabric. |

## <a name="sfctl-property-delete"></a>sfctl property delete
Permet de supprimer la propriété Service Fabric spécifiée.

Supprime la propriété Service Fabric spécifiée sous un nom donné. Une propriété doit être créée avant de pouvoir être supprimée.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name-id       [Requis] | Nom Service Fabric, sans le schéma d’URI 'fabric\:'. |
| --property-name [Requis] | Spécifie le nom de la propriété à obtenir. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-property-get"></a>sfctl property get
Permet d’obtenir la propriété Service Fabric spécifiée.

Obtient la propriété Service Fabric spécifiée sous un nom donné. Retourne toujours à la fois la valeur et les métadonnées.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name-id       [Requis] | Nom Service Fabric, sans le schéma d’URI 'fabric\:'. |
| --property-name [Requis] | Spécifie le nom de la propriété à obtenir. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-property-list"></a>sfctl property list
Permet d’obtenir des informations sur toutes les propriétés Service Fabric sous un nom donné.

Un nom Service Fabric peut avoir une ou plusieurs propriétés nommées qui stockent des informations personnalisées. Cette opération obtient les informations sur ces propriétés dans une liste paginée. Les informations comprennent le nom, la valeur et les métadonnées de chacune des propriétés.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name-id [Requis] | Nom Service Fabric, sans le schéma d’URI 'fabric\:'. |
| --continuation-token | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de continuation avec une valeur non vide est inclus dans la réponse de l’API quand les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de continuation ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL. |
| --include-values | Permet de spécifier s’il faut inclure les valeurs des propriétés retournées. True si les valeurs doivent être retournées avec les métadonnées ; False pour retourner uniquement les métadonnées de propriété. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-property-put"></a>sfctl property put
Permet de créer ou mettre à jour une propriété Service Fabric.

Crée ou met à jour la propriété Service Fabric spécifiée sous un nom donné.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --name-id       [Requis] | Nom Service Fabric, sans le schéma d’URI 'fabric\:'. |
| --property-name [Requis] | Nom de la propriété Service Fabric. |
| --value         [Requis] | Décrit une valeur de propriété Service Fabric. Il s’agit d’une chaîne JSON. <br><br> La chaîne json a deux champs, le type ('kind') de données et la valeur ('value') des données. La valeur 'Kind' doit être le premier élément à afficher dans la chaîne JSON et peut être les valeurs 'Binary', 'Int64', 'Double', 'String' ou 'Guid'. La valeur doit pouvoir être sérialisée pour les types donnés. Les valeurs 'Kind' et 'Data' doivent être fournies sous forme de chaînes. |
| --custom-id-type | ID du type personnalisé de la propriété. Avec cette propriété, l’utilisateur peut marquer le type de la valeur de la propriété. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

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