---
title: Préparer l’environnement cible (VMware/physique vers Azure) | Microsoft Docs
description: Cet article décrit comment préparer votre environnement cible Azure en vue d’une réplication de machines virtuelles VMware et de serveurs physiques sur Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: bsiva
ms.openlocfilehash: 948812f05697362978ad041566d22977efec92a1
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434634"
---
# <a name="prepare-the-target-environment-vmwarephysical-to-azure"></a>Préparer l’environnement cible (VMware/physique vers Azure)

Cet article décrit comment préparer votre environnement cible Azure avant de commencer la réplication de machines virtuelles VMware ou de serveurs physiques sur Azure.

## <a name="prerequisites"></a>Prérequis

L’article suppose que :
- Vous avez créé un coffre Recovery Services sur le [portail Azure](http://portal.azure.com "portail Azure") pour protéger vos machines sources.
- Vous avez configuré votre environnement local pour répliquer les [machines virtuelles VMware](vmware-azure-set-up-source.md) ou [serveurs physiques](physical-azure-set-up-source.md) sources sur Azure.

## <a name="prepare-target"></a>Préparer la cible

Après avoir effectué **l’Étape 1 : Sélectionner l’objectif de protection** et **l’Étape 2 : Préparer la source**, vous passez à **l’Étape 3 : Préparer la cible**.

![Préparer la cible](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonnement :** dans le menu déroulant, sélectionnez l’abonnement vers lequel vous souhaitez répliquer vos machines virtuelles ou serveurs physiques.
2. **Modèle de déploiement :** sélectionnez le modèle de déploiement (Classique ou Gestionnaire de ressources).

Selon le modèle de déploiement choisi, une validation est lancée afin de vérifier que vous disposez au moins d’un compte de stockage et d’un réseau virtuel compatibles dans l’abonnement cible pour y répliquer et basculer les machines virtuelles et serveurs physiques.

Une fois les validations terminées avec succès, cliquez sur OK pour passer à l’étape suivante.

Si vous n’avez pas de compte de stockage Resource Manager ou de réseau virtuel compatible, vous pouvez en créer un en cliquant sur le bouton **+ Compte de stockage** ou sur le bouton **+ Réseau** en haut de la page.

## <a name="next-steps"></a>Étapes suivantes
[Configurer les paramètres de réplication](vmware-azure-set-up-replication.md)
