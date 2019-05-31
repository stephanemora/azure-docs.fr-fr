---
title: Ressources prises en charge pour Azure Red Hat OpenShift | Microsoft Docs
description: Comprendre les régions Azure et les tailles de machine virtuelle sont prises en charge par Microsoft Azure Red Hat OpenShift.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/15/2019
ms.openlocfilehash: c226227797802ab58d1bcbaadb7e97e780b30560
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306217"
---
# <a name="azure-red-hat-openshift-resources"></a>Ressources Azure de Red Hat OpenShift

Cette rubrique répertorie les régions Azure et les tailles de machine virtuelle prises en charge par le service Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Régions Azure

Consultez [des produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) pour obtenir la liste actuelle des régions où vous pouvez déployer Azure Red Hat OpenShift les clusters.

## <a name="virtual-machine-sizes"></a>Tailles de machines virtuelles

Voici les tailles de machine virtuelle prises en charge que vous pouvez spécifier pour les nœuds de calcul dans votre cluster Azure Red Hat OpenShift.

> [!Important]
> Chaque machine virtuelle a un nombre différent de disques pouvant être attachés. Cela peut être pas aussi immédiatement clair en tant que la mémoire ou la taille de l’UC.
> Certaines tailles de machine virtuelle ne sont pas disponibles dans toutes les régions. Même si l’API prend en charge la taille spécifiée, vous pouvez obtenir une erreur si la taille n’est pas disponible dans la région que vous spécifiez.
> Consultez [liste actuelle de la machine virtuelle pris en charge les tailles par région](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) pour plus d’informations.

## <a name="compute-node-sizes"></a>Tailles de nœud de calcul

Les tailles de nœud de calcul suivantes sont prises en charge par l’API REST de Azure Red Hat OpenShift :

|Taille|Processeurs virtuels|RAM|
|-|-|-|
|Standard D4s v3|4|16 Go|
|Standard D8s v3|8|32 Go|
|Standard D16s v3|16|64 Go|
|Standard D32s v3|32|128 Go|
|-|-|-|
|Standard E4s v3|4|32 Go|
|Standard E8s v3|8|64 Go|
|Standard E16s v3|16|128 Go|
|Standard E32s v3|32|256 Go|
|-|-|-|
|Standard F8s v2|8|16 Go|
|Standard F16s v2|16|32 Go|
|Standard F32s v2|32|64 Go|

## <a name="master-node-sizes"></a>Tailles de nœud principal

Le maître suivant / tailles de nœuds d’infrastructure sont pris en charge par l’API REST de Azure Red Hat OpenShift :

|Taille|Processeurs virtuels|RAM|
|-|-|-|
|Standard D4s v3|4|16 Go|
|Standard D8s v3|8|32 Go|
|Standard D16s v3|16|64 Go|
|Standard D32s v3|32|128 Go|

## <a name="next-steps"></a>Étapes suivantes

Essayez le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.