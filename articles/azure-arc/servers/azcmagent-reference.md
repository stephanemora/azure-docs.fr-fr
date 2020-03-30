---
title: Azure Connected Machine Agent – Interface CLI
description: Documentation de référence sur l’interface CLI de l’agent connecté à Azure
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73510394"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure Connected Machine Agent – Interface CLI

L’outil `Azcmagent` (Azure Connected Machine Agent) est utilisé pour configurer et résoudre un problème de connexion d’ordinateurs non Azure sur Azure.

L’agent lui-même est un processus démon appelé `himdsd` sur Linux, et un service Windows appelé `himds` sur Windows.

Dans un usage normal, `azcmagent connect` est utilisé pour établir une connexion entre cet ordinateur et Azure, et `azcmagent disconnect` si vous décidez que vous ne souhaitez plus utiliser cette connexion. Les autres commandes concernent la résolution des problèmes ou d’autres cas spéciaux.

## <a name="options"></a>Options

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>VOIR AUSSI

* [azcmagent connect](#azcmagent-connect) : connecte cet ordinateur à Azure
* [azcmagent disconnect](#azcmagent-disconnect) : déconnecte cet ordinateur d’Azure
* [azcmagent reconnect](#azcmagent-reconnect) : reconnecte cet ordinateur à Azure
* [azcmagent show](#azcmagent-show) : obtient les métadonnées de l’ordinateur et l’état de l’agent. Cette option est principalement utilisée pour la résolution des problèmes.
* [azcmagent version](#azcmagent-version) : afficher la version de l’agent de gestion hybride

## <a name="azcmagent-connect"></a>azcmagent connect

Connecte cet ordinateur à Azure

### <a name="synopsis"></a>Synopsis

Crée une ressource dans Azure représentant cet ordinateur.

Cela utilise les options d’authentification fournies pour créer une ressource dans Azure Resource Manager représentant cet ordinateur. La ressource se trouve dans l’abonnement et le groupe de ressources demandé, et les données relatives à l’ordinateur sont stockées dans la région Azure spécifiée par le paramètre d’emplacement.
Le nom de ressource par défaut est le nom d’hôte de cet ordinateur s’il n’est pas substitué.

Un certificat correspondant à l’identité attribuée par le système de cet ordinateur est ensuite téléchargé et stocké localement. Une fois cette étape terminée, le service de **métadonnées de machine connectée à Azure** et l’agent de configuration invité commencent la synchronisation avec Azure Cloud.

Options d’authentification :

* Jeton d’accès `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* ID et le secret du principal de service `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Connexion d’appareil (interactive) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Syntaxe

```none
azcmagent connect [flags]
```

### <a name="options"></a>Options

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent disconnect

Déconnecte cet ordinateur d’Azure

### <a name="synopsis"></a>Synopsis

Supprime la ressource dans Azure qui représente ce serveur.

Cette commande utilise les options d’authentification fournies pour supprimer une ressource dans Azure Resource Manager représentant cet ordinateur. Après ce point, le **service de métadonnées de machine connectée à Azure** et l’agent de configuration invité seront déconnectés. Cette commande ne permet pas d’arrêter ou de supprimer les services : supprimez le package pour effectuer cette opération.

Cette commande requiert des autorisations plus élevées que le rôle « intégration d’ordinateurs connectés à Azure ».

Une fois qu’une machine est déconnectée, utilisez `azcmagent connect` et non `azcmagent reconnect` si vous souhaitez créer une nouvelle ressource pour celle-ci dans Azure.

Options d’authentification :

* Jeton d’accès `azcmagent disconnect --access-token <>`
* ID et le secret du principal de service `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Connexion d’appareil interactive `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Syntaxe

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Options

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent reconnect

Reconnecte cet ordinateur à Azure

### <a name="synopsis"></a>Synopsis

Reconnectez l’ordinateur avec des informations d’identification non valides à Azure.

Si un ordinateur a déjà une ressource dans Azure, mais n’est pas en mesure de s’authentifier auprès de celui-ci, il peut être reconnecté à l’aide de cette commande. Cela est possible si un ordinateur a été mis hors tension suffisamment longtemps pour que son certificat expire (au moins 45 jours).

Si un ordinateur a été déconnecté avec `azcmagent disconnect`, utilisez `azcmagent connect` à la place.

Cette commande utilise les options d’authentification fournies pour récupérer de nouvelles informations d'identification correspondant à une ressource dans Azure Resource Manager représentant cet ordinateur.

Cette commande requiert des autorisations plus élevées que le rôle**Intégration d’ordinateurs connectés à Azure**.

Options d’authentification

* Jeton d’accès `azcmagent reconnect --access-token <>`
* ID et le secret du principal de service `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Connexion d’appareil interactive `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Syntaxe

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Options

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent show

Obtient les métadonnées de l’ordinateur et l’état de l’agent. Cette option est principalement utilisée pour la résolution des problèmes.

### <a name="synopsis"></a>Synopsis

Obtient les métadonnées de l’ordinateur et l’état de l’agent. Cette option est principalement utilisée pour la résolution des problèmes.


### <a name="syntax"></a>Syntaxe

```
azcmagent show [flags]
```

### <a name="options"></a>Options

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent version

Afficher la version de l’agent de gestion hybride

### <a name="synopsis"></a>Synopsis

Afficher la version de l’agent de gestion hybride

### <a name="syntax"></a>Syntaxe

```none
azcmagent version [flags]
```

### <a name="options"></a>Options

```none
  -h, --help   help for version
```
