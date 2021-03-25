---
title: Mettre à niveau un cluster Azure Red Hat OpenShift
description: Découvrez comment mettre à niveau un cluster Azure Red Hat OpenShift exécutant OpenShift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720883"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Mettre à niveau un cluster Azure Red Hat OpenShift (ARO)

Une partie du cycle de vie du cluster ARO implique l’exécution de mises à niveau périodiques vers la dernière version d’OpenShift. Il est important d’appliquer les dernières publications de sécurité ou d’opérer une mise à niveau pour obtenir les dernières fonctionnalités. Cet article explique comment mettre à niveau tous les composants d’un cluster OpenShift à l’aide de la console web OpenShift.

## <a name="before-you-begin"></a>Avant de commencer

Pour les besoins de cet article, vous devez utiliser Azure CLI version 2.0.65 ou ultérieure. Exécutez `az --version` pour rechercher votre version actuelle. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

Cet article part du principe que vous disposez de l’accès à un cluster Azure Red Hat OpenShift existant en tant qu’utilisateur avec des privilèges `admin`.

## <a name="check-for-available-aro-cluster-upgrades"></a>Rechercher les mises à niveau du cluster ARO disponibles

Dans la console web OpenShift, sélectionnez **Administration** > **Paramètres du cluster** et ouvrez l’onglet **Détails**.

Si **État de la mise à jour** pour votre cluster indique **Mises à jour disponibles**, vous pouvez mettre à jour votre cluster.

## <a name="upgrade-your-aro-cluster"></a>Mettre à niveau votre cluster ARO

Dans la console web à l’étape précédente, définissez **Canal** sur le canal approprié pour la version vers laquelle vous souhaitez effectuer la mise à jour, par exemple `stable-4.5`.

Sélectionnez une version vers laquelle effectuer la mise à jour, puis sélectionnez **Mettre à jour**. L’état de la mise à jour passe à : `Update to <product-version> in progress`. Vous pouvez examiner la progression de la mise à jour du cluster en regardant les barres de progression pour les opérateurs et les nœuds.

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur la mise à niveau d’un cluster ARO à l’aide de l’interface CLI OC](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Vous trouverez des informations sur les conseils et les mises à jour d’OpenShift Container Platform disponibles dans la [section errata](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) du portail client.
