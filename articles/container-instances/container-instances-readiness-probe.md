---
title: Configurer la probe readiness sur l’instance de conteneur
description: Découvrez comment configurer une sonde afin que les conteneurs dans Azure Container Instances ne reçoivent des demandes que quand ils sont prêts
ms.topic: article
ms.date: 10/17/2019
ms.openlocfilehash: 5ebbcdeee231e3e67abd6758485a12984137997e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533559"
---
# <a name="configure-readiness-probes"></a>Configurer les probe readiness

Pour les applications conteneurisées qui traitent le trafic, vous pouvez vérifier que votre conteneur est prêt à gérer les demandes entrantes. Azure Container Instances prend en charge les sondes probe readiness pour inclure des configurations afin que votre conteneur ne soit pas accessible sous certaines conditions. La sonde probe readiness se comporte comme une [sonde probe readiness Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Par exemple, une application de conteneur peut avoir besoin de charger un jeu de données volumineux au démarrage, et vous ne souhaitez pas qu’elle reçoive de demandes pendant cette période.

Cet article explique comment déployer un groupe de conteneurs qui comprend une sonde probe readiness, afin qu’un conteneur reçoive uniquement du trafic quand la sonde réussit.

Azure Container Instances prend également en charge les [sondes probe liveness](container-instances-liveness-probe.md), que vous pouvez configurer pour provoquer le redémarrage automatique d’un conteneur défectueux.

## <a name="yaml-configuration"></a>Configuration YAML

Par exemple, créez un fichier `readiness-probe.yaml` avec l’extrait de code suivant qui comprend une sonde probe readiness. Ce fichier définit un groupe de conteneurs qui se compose d’un conteneur exécutant une petite application web. L’application est déployée à partir de l’image `mcr.microsoft.com/azuredocs/aci-helloworld` publique. Cette application de conteneur est également illustrée dans des guides de démarrage rapide, tels que [Déployer une instance de conteneur dans Azure à l’aide d’Azure CLI](container-instances-quickstart.md).

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Commande de démarrage

Le fichier YAML comprend une commande de démarrage à exécuter au démarrage du conteneur, définie par la propriété `command` qui prend en entrée un tableau de chaînes. Cette commande simule une heure d’exécution de l’application web, mais le conteneur n’est pas prêt. Tout d’abord, elle démarre une session de l’interpréteur de commandes et exécute une commande `node` pour démarrer l’application web. Elle démarre également une commande pour se mettre en veille pendant 240 secondes, délai au terme duquel elle crée un fichier appelé `ready` dans le répertoire `/tmp` :

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Commande de disponibilité

Ce fichier YAML définit une `readinessProbe` prenant en charge une commande de disponibilité `exec` qui joue le rôle de vérification de la disponibilité. Cet exemple de commande de disponibilité teste l’existence du fichier `ready` dans le répertoire `/tmp`.

Si le fichier `ready` n’existe pas, la commande de disponibilité se termine avec une valeur différente de zéro ; le conteneur continue de s’exécuter, mais il n’est pas accessible. Quand la commande s’arrête correctement avec le code de sortie 0, le conteneur est prêt à être utilisé. 

La propriété `periodSeconds` signifie que la commande de disponibilité doit s’exécuter toutes les 5 secondes. La sonde probe readiness est exécutée pendant la durée de vie du groupe de conteneurs.

## <a name="example-deployment"></a>Exemple de déploiement

Exécutez la commande suivante pour déployer un groupe de conteneurs avec la configuration YAML précédente :

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Afficher les vérifications de la disponibilité

Dans cet exemple, pendant les 240 premières secondes, la commande de disponibilité échoue quand elle recherche l’existence du fichier `ready`. Le code d’état retourné signale que le conteneur n’est pas prêt.

Vous pouvez voir ces événements dans le portail Azure et dans Azure CLI. Par exemple, le portail indique que les événements de type `Unhealthy` sont déclenchés à la suite de l’échec de la commande de disponibilité. 

![Événement non intègre sur le Portail][portal-unhealthy]

## <a name="verify-container-readiness"></a>Vérifier la disponibilité du conteneur

Après avoir démarré le conteneur, vous pouvez vérifier qu’il n’est pas accessible au départ. Après le provisionnement, récupérez l’adresse IP du groupe de conteneurs :

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Essayez d’accéder au site pendant que la sonde probe readiness échoue :

```bash
wget <ipAddress>
```

La sortie indique que le site n’est pas accessible au départ :
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Après 240 secondes, la commande de disponibilité réussit, signalant que le conteneur est prêt. Désormais, quand vous exécutez la commande `wget`, elle réussit :

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Quand le conteneur est prêt, vous pouvez également accéder à l’application web en accédant à l’adresse IP à l’aide d’un navigateur web.

> [!NOTE]
> La sonde probe readiness continue de s’exécuter pendant la durée de vie du groupe de conteneurs. Si la commande de disponibilité échoue ultérieurement, le conteneur devient inaccessible. 
> 

## <a name="next-steps"></a>Étapes suivantes

Une sonde probe readiness peut être utile dans les scénarios impliquant des groupes multiconteneurs composés de conteneurs dépendants. Pour plus d’informations sur les scénarios multiconteneurs, consultez [Groupes de conteneurs dans Azure Container Instances](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
