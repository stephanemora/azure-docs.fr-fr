---
title: 'CLI Azure Service Fabric : sfctl store | Microsoft Docs'
description: Décrit les commandes sfctl store de l’interface de ligne de commande (CLI) Service Fabric.
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
ms.openlocfilehash: 65dcceb2e55ec0927630b32670d2f915a01903bf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667945"
---
# <a name="sfctl-store"></a>sfctl store
Effectue des opérations élémentaires au niveau des fichiers dans le magasin d’images de cluster.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| delete | Supprime le contenu du magasin d’images. |
| root-info | Permet d’obtenir les informations de contenu à la racine du magasin d’images. |
| stat | Permet d’obtenir les informations de contenu du magasin d’images. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Supprime le contenu du magasin d’images.

Supprime le contenu du magasin d’images trouvé dans le chemin d’accès relatif du magasin d’images donné. Cette commande permet de supprimer les packages d’application chargés une fois qu’ils ont été approvisionnés.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --content-path [Requis] | Chemin d’accès relatif au fichier ou au dossier dans le magasin d’images à partir de sa racine. |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
Permet d’obtenir les informations de contenu à la racine du magasin d’images.

Retourne les informations sur le contenu du magasin d’images à la racine du magasin d’images.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --timeout -t | Délai d’attente du serveur en secondes.  Valeur par défaut \: 60. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Permet d’obtenir les informations de contenu du magasin d’images.

Retourne les informations sur le contenu du magasin d’images dans le chemin d’accès de contenu spécifié. Le chemin d’accès de contenu varie selon la racine du magasin d’images.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --content-path [Requis] | Chemin d’accès relatif au fichier ou au dossier dans le magasin d’images à partir de sa racine. |
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
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).