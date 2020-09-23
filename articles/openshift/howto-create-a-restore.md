---
title: Créer une restauration d’application de cluster Azure Red Hat OpenShift 4 à l’aide de Velero
description: Découvrez comment créer une restauration de vos applications de cluster Azure Red Hat OpenShift à l’aide de Velero
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 0cd6797bcdfadca807e25f8b3decf34bd553fc56
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89470049"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Créer une restauration d’application de cluster Azure Red Hat OpenShift 4

Dans cet article, vous allez préparer votre environnement pour créer une restauration d’application de cluster Azure Red Hat OpenShift 4. Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer les composants requis et installer les outils nécessaires
> * Créer une restauration d’application Azure Red Hat OpenShift 4

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce tutoriel demande au minimum la version 2.6.0 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Avant de commencer

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Créer une sauvegarde d’application Azure Red Hat OpenShift 4

Pour créer une sauvegarde d’application Azure Red Hat OpenShift 4, consultez [Créer une sauvegarde d’application Azure Red Hat OpenShift 4](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Restaurer une application Azure Red Hat OpenShift 4

Ces étapes vous permettront de restaurer une application précédemment sauvegardée avec Velero.
Vous pouvez consulter la liste des sauvegardes actuellement reconnues par le cluster pour connaître les sauvegardes disponibles pour la restauration.  Pour effectuer cette étape, vous devez exécuter la commande suivante :

_(Cette étape suppose que vous avez installé Velero dans un projet nommé « velero »)_

```bash
oc get backups -n velero
```

Une fois que vous avez la sauvegarde que vous souhaitez restaurer, vous devez effectuer la restauration à l’aide de la commande suivante :

```bash
velero restore create --from-backup <name of backup from above output list>
```

Cette étape permet de créer les objets Kubernetes qui ont été sauvegardés lors de l’étape précédente dédiée à la création d’une sauvegarde.

Pour afficher l’état de la restauration, procédez comme suit :

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Lorsque la phase indique `Completed`, votre application Azure Red Hat 4 doit être restaurée.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, une application de cluster Azure Red Hat OpenShift 4 a été restaurée. Vous avez appris à :

> [!div class="checklist"]
> * Créer une restauration d’application de cluster OpenShift v4 à l’aide de Velero


Passez à l’article suivant pour en savoir plus sur les ressources prises en charge par Azure Red Hat OpenShift 4.

* [Ressources prises en charge pour Azure Red Hat OpenShift v4](supported-resources.md)