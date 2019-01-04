---
title: Préparation de l’environnement cible en vue de la réplication VMware sur Azure | Microsoft Docs
description: Cet article décrit comment préparer votre environnement cible Azure en vue d’une réplication de machines virtuelles VMware sur Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837509"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Préparer l’environnement cible pour la récupération d’urgence des machines virtuelles VMware ou serveurs physiques vers Azure

Cet article décrit comment préparer votre environnement cible Azure avant de commencer la réplication de machines virtuelles VMware ou de serveurs physiques sur Azure.

## <a name="prerequisites"></a>Prérequis

L’article suppose que :
- Vous avez créé un coffre Recovery Services sur le [portail Azure](http://portal.azure.com "portail Azure") pour protéger vos machines sources.
- Vous avez configuré votre environnement local pour répliquer les [machines virtuelles VMware](vmware-azure-set-up-source.md) ou [serveurs physiques](physical-azure-set-up-source.md) sources sur Azure.

## <a name="prepare-target"></a>Préparer la cible

Après avoir réalisé l’**étape 1 : sélection de l’objectif de la protection** et l’**étape 2 : préparation de la source**, vous passez à l’**étape 3 : cible**.

![Préparer la cible](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonnement :** dans le menu déroulant, sélectionnez l’abonnement vers lequel vous souhaitez répliquer vos machines virtuelles ou serveurs physiques.
2. **Modèle de déploiement :** sélectionnez le modèle de déploiement (Classique ou Resource Manager).

Selon le modèle de déploiement choisi, une validation est lancée afin de vérifier que vous disposez au moins d’un compte de stockage et d’un réseau virtuel compatibles dans l’abonnement cible pour y répliquer et basculer les machines virtuelles et serveurs physiques.

Une fois les validations terminées avec succès, cliquez sur OK pour passer à l’étape suivante.

Si vous n’avez pas de compte de stockage Resource Manager ou de réseau virtuel compatible, vous pouvez en créer un en cliquant sur le bouton **+ Compte de stockage** ou sur le bouton **+ Réseau** en haut de la page.

## <a name="next-steps"></a>Étapes suivantes
[Configurer les paramètres de réplication](vmware-azure-set-up-replication.md)
