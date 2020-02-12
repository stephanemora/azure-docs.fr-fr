---
title: Azure VMware Solutions (AVS) - Présentation des machines virtuelles
description: Découvrez les machines virtuelles AVS et leurs avantages.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024906"
---
# <a name="avs-virtual-machines-overview"></a>Présentation des machines virtuelles AVS

AVS vous permet de gérer des machines virtuelles VMware depuis le portail Azure. Un cluster ou une liste de ressources partagées à partir de votre cluster vSphere sont gérés via Azure, grâce à leur mappage à votre abonnement.

Pour créer une machine virtuelle AVS depuis Azure, un modèle de machine virtuelle doit exister sur votre serveur vCenter de cloud privé AVS. Le modèle est utilisé pour personnaliser le système d’exploitation et les applications. Le modèle de machine virtuelle peut être renforcé pour répondre aux stratégies de sécurité d’entreprise. Vous pouvez utiliser le modèle pour créer des machines virtuelles, puis les utiliser dans le portail Azure à l’aide d’un modèle en libre-service.

## <a name="benefits"></a>Avantages

Les machines virtuelles AVS du portail Azure fournissent un mécanisme de libre-service pour que les utilisateurs puissent créer et gérer des machines virtuelles VMware.

* Créer une machine virtuelle AVS sur votre serveur vCenter de cloud privé AVS
* Gérer les propriétés de la machine virtuelle
  * Ajouter/supprimer des disques
  * Ajouter/supprimer des cartes d’interface réseau
* Opérations d’alimentation de votre machine virtuelle AVS
  * Mise sous tension et mise hors tension
  * Réinitialiser une machine virtuelle
* Supprimer une machine virtuelle

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* Découvrez comment [Mapper votre abonnement Azure](azure-subscription-mapping.md)
