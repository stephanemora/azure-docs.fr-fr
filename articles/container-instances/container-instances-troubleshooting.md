---
title: Résolution des problèmes liés à Azure Container Instances
description: Découvrez comment résoudre les problèmes liés à Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 01/08/2019
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 609d52f9f2c5dce1bbfd668e94db25aca3d52f69
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119048"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Résoudre les problèmes courants dans Azure Container Instances

Cet article explique comment résoudre les problèmes courants de gestion ou de déploiement de conteneurs dans Azure Container Instances.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Lorsque vous définissez la spécification du conteneur, certains paramètres requièrent le respect des restrictions en matière d’affectation de noms. Le tableau suivant indique les exigences spécifiques des propriétés du groupe de conteneurs. Pour plus d’informations sur les conventions d’affectation de noms Azure, consultez [Conventions d’affectation de noms][azure-name-restrictions] dans Azure Architecture Center.

| Étendue | Longueur | Casse | Caractères valides | Modèle suggéré | Exemples |
| --- | --- | --- | --- | --- | --- | --- |
| Nom du groupe de conteneurs | 1-64 |Non-respect de la casse |Caractères alphanumériques et traits d’union n’importe où sauf en première ou dernière position |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nom du conteneur | 1-64 |Non-respect de la casse |Caractères alphanumériques et traits d’union n’importe où sauf en première ou dernière position |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Ports du conteneur | Entre 1 et 65 535 |Entier  |Entier compris entre 1 et 65 535 |`<port-number>` |`443` |
| Étiquette du nom DNS | 5 à 63 |Non-respect de la casse |Caractères alphanumériques et traits d’union n’importe où sauf en première ou dernière position |`<name>` |`frontend-site1` |
| Variable d’environnement | 1-63 |Non-respect de la casse |Caractères alphanumériques et trait de soulignement (_) n’importe où sauf en première ou dernière position |`<name>` |`MY_VARIABLE` |
| Nom du volume | 5 à 63 |Non-respect de la casse |Lettres minuscules, chiffres et traits d’union n’importe où sauf en première ou dernière position. Ne peut pas contenir deux traits d’union consécutifs. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>La version du système d’exploitation de l’image n’est pas prise en charge

Si l’image spécifiée n’est pas prise en charge par Azure Container Instances, une erreur `OsVersionNotSupported` est renvoyée. L’erreur est semblable à ce qui suit, où `{0}` est le nom de l’image que vous avez tentée de déployer :

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Cette erreur se produit généralement lorsque vous déployez des images Windows qui sont basées sur une version du canal semi-annuel (SAC). Par exemple, les versions Windows 1709 et 1803 sont des versions du SAC et génèrent cette erreur lors du déploiement.

Actuellement, Azure Container Instances prend en charge les images Windows basées sur la version du **canal de maintenance à long terme (LTSC) Windows Server 2016**. Pour résoudre ce problème lorsque vous déployez les conteneurs Windows, déployez toujours les images basées sur Windows Server 2016 (LTSC). Les images basées sur Windows Server 2019 (LTSC) ne sont pas prises en charge.

Pour plus d’informations sur les versions de LTSC et du SAC Windows, consultez [Vue d’ensemble du canal semi-annuel du serveur Windows][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Impossible d’extraire l’image

Si Azure Container Instances ne parvient pas à extraire votre image initialement, il réessaie pendant une certaine période, sans succès. Si l’opération d’extraction image continue à échouer, le déploiement ACI risque d’échouer, et vous verrez une erreur `Failed to pull image`.

Pour résoudre ce problème, supprimez l’instance de conteneur et réessayez votre déploiement. Vérifiez que l’image existe dans le Registre, et que vous avez correctement tapé le nom de l’image.

Des événements tels que les suivants sont alors affichés dans la sortie de [az container show][az-container-show] :

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Le conteneur s’arrête et redémarre en permanence (pas de processus au long cours)

Les groupes de conteneurs sont définis par défaut sur la [stratégie de redémarrage](container-instances-restart-policy.md) **Toujours**, de sorte que les conteneurs du groupe de conteneurs redémarrent toujours après avoir été exécutés. Vous devrez peut-être définir ce paramètre sur **OnFailure** ou **Jamais** si vous envisagez d’exécuter des conteneurs basés sur des tâches. Si vous spécifiez **OnFailure** et constatez encore des redémarrages continus, il peut y avoir un problème avec l’application ou le script exécutés dans votre conteneur.

Lors de l’exécution de groupes de conteneurs sans processus au long cours, vous pouvez observer de multiples arrêts et redémarrages avec des images telles que Ubuntu ou Alpine. La connexion via [EXEC](container-instances-exec.md) ne va pas fonctionner car le conteneur n’a aucun processus pour le maintenir actif. Pour résoudre ce problème, ajoutez une commande de démarrage (voir exemple ci-dessous) au déploiement de votre groupe de conteneurs pour que le conteneur continue à s’exécuter.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image microsoft/windowsservercore:ltsc2016
 --command-line "ping -t localhost"
```

L’API Container Instances et le portail Azure incluent une propriété `restartCount`. Pour vérifier le nombre de redémarrages d’un conteneur, vous pouvez utiliser la commande [az container show][az-container-show] dans Azure CLI. Dans l’exemple de sortie suivant (qui a été tronqué par souci de concision), vous pouvez voir la propriété `restartCount` à la fin de la sortie.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La plupart des images conteneur pour les distributions Linux définissent un interpréteur de commandes, tel que bash, comme commande par défaut. Un interpréteur de commandes n’étant pas en soi un service de longue durée, ces conteneurs quittent la procédure immédiatement et entrent dans une boucle de redémarrage lorsqu’ils sont configurés avec la stratégie de redémarrage par défaut (**Always**).

## <a name="container-takes-a-long-time-to-start"></a>Le démarrage du conteneur prend beaucoup de temps

Les deux principaux facteurs qui contribuent à l’heure de démarrage dans Azure Container Instances sont :

* [Taille de l'image](#image-size)
* [Emplacement de l’image](#image-location)

Les images Windows ont des [considérations supplémentaires](#cached-windows-images).

### <a name="image-size"></a>Taille de l'image

Si le démarrage de votre conteneur prend beaucoup de temps, commencez par examiner la taille de votre image conteneur. Étant donné qu’Azure Container Instances extrait l’image conteneur à la demande, le temps de démarrage est directement lié à la taille de cette image.

Vous pouvez afficher la taille de l’image de votre conteneur à l’aide de la commande `docker images` dans l’interface CLI Docker :

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Pour que l’image conserve une petite taille, faites en sorte que l’image finale ne contienne aucun élément qui soit superflu au moment de l’exécution. Pour ce faire, vous pouvez utiliser des [builds à plusieurs étapes][docker-multi-stage-builds]. Grâce aux builds à plusieurs étapes, vous pouvez facilement faire en sorte que l’image finale ne contienne que les artefacts nécessaires à votre application, à l’exclusion de tout contenu supplémentaire qui était requis au moment de la génération.

### <a name="image-location"></a>Emplacement de l’image

Il existe une autre façon de réduire l’impact de l’extraction de l’image sur le temps de démarrage de votre conteneur. Elle consiste à héberger l’image du conteneur dans [Azure Container Registry](/azure/container-registry/) dans la région où vous envisagez de déployer Azure Container Instances. Cette opération raccourcit le chemin réseau que l’image conteneur doit parcourir, réduisant considérablement le temps de téléchargement.

### <a name="cached-windows-images"></a>Images Windows mises en cache

Azure Container Instances utilise un mécanisme de mise en cache pour aider à accélérer le démarrage du conteneur pour des images basées sur certaines images Windows.

Pour garantir le meilleur temps de démarrage du conteneur Windows, utilisez une des **trois dernières** versions des **deux images** suivantes en tant qu’image de base :

* [Windows Server 2016][docker-hub-windows-core] (LTS uniquement)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Disponibilité lente du réseau des conteneurs Windows

Lors de la création initiale, les conteneurs Windows peuvent n’avoir aucune connectivité entrante ou sortante pendant 30 secondes (ou plus longtemps dans de rares cas). Si votre application conteneur requiert une connexion Internet, ajoutez un retard et réessayez la logique afin de laisser 30 secondes pour établir la connectivité Interne. Après l’installation initiale, la mise en réseau de conteneur devrait reprendre correctement.

## <a name="resource-not-available-error"></a>Erreur : ressource non disponible

En raison d’une charge de ressources régionales variable dans Azure, vous pouvez recevoir l’erreur suivante quand vous tentez de déployer une instance de conteneur :

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Cette erreur indique qu’en raison d’une charge importante dans la région dans laquelle vous tentez de déployer, les ressources spécifiées pour votre conteneur ne peuvent pas être allouées à ce moment-là. Utilisez une ou plusieurs des étapes d’atténuation suivantes pour vous aider à résoudre votre problème.

* Vérifier que les paramètres de votre déploiement de conteneur correspondent à ceux définis dans [Quotas et disponibilité des régions pour Azure Container Instances](container-instances-quotas.md#region-availability)
* Spécifier des paramètres de processeur et de mémoire inférieurs pour le conteneur
* Déployer sur une autre région Azure
* Déployer plus tard

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Ne peut pas se connecter à l’API Docker sous-jacent ou exécuter des conteneurs privilégiés

Azure Container Instances n’expose pas un accès direct à l’infrastructure sous-jacente qui héberge les groupes de conteneurs. Cela inclut l’accès à l’API Docker en cours d’exécution sur l’hôte du conteneur et les conteneurs privilégiés en cours d’exécution. Si vous avez besoin d’interaction avec le Docker, vérifiez la [Documentation de référence REST](https://aka.ms/aci/rest) pour voir ce que l’API ACI prend en charge. S’il manque des informations, envoyez une requête sur le [Forum Internet de commentaires ACI](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>Adresses IP éventuellement inaccessibles en raison de ports non correspondants
Azure Container Instances ne prend pas en charge le mappage de ports, contrairement à la configuration Docker classique. Toutefois, ce correctif est sur la feuille de route. Si vous constatez que des adresses IP ne sont pas accessibles alors qu’elles le devraient, vérifiez que vous avez configuré votre image conteneur pour écouter les mêmes ports que ceux que vous exposez dans votre groupe de conteneurs avec la propriété `ports`.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [récupérer les journaux et événements de conteneur](container-instances-get-logs.md) pour aider à déboguer vos conteneurs.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
