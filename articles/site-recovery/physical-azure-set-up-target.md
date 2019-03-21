---
title: Configurer l’environnement cible pour la récupération d’urgence des serveurs physiques locaux sur Azure | Microsoft Docs'
description: Cet article décrit comment configurer l’environnement Azure cible pour la récupération d’urgence des serveurs physiques à l’aide d’Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 41220ccdca945610d7d8ca87af0857114e2cef85
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57866290"
---
# <a name="prepare-target-vmware-to-azure"></a>Préparer la cible (VMware vers Azure)

Cet article décrit comment préparer votre environnement Azure pour lancer la réplication des serveurs physiques (x64) exécutant Windows ou Linux vers Azure.

## <a name="prerequisites"></a>Conditions préalables

L’article suppose que :
- Vous avez créé un coffre Recovery Services pour protéger vos serveurs physiques. Vous pouvez créer un coffre Recovery Services dans le [portail Azure](https://portal.azure.com "portail Azure").
- Vous avez [configuré votre environnement local](physical-azure-disaster-recovery.md) pour répliquer les serveurs physiques vers Azure.

## <a name="prepare-target"></a>Préparer la cible

Après avoir effectué l’**Étape 1 : Sélectionner l’objectif de protection** et l’**Étape 2 : Préparer la source**, vous passez à l’**Étape 3 : Cible**

![Préparer la cible](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Abonnement :** dans le menu déroulant, sélectionnez l’abonnement vers lequel vous souhaitez répliquer vos serveurs physiques.
2. **Modèle de déploiement :** sélectionnez le modèle de déploiement (Classique ou Resource Manager).

Selon le modèle de déploiement choisi, une validation est exécutée pour vérifier que vous disposez au moins d’un compte de stockage et d’un réseau virtuel compatibles dans l’abonnement cible pour la réplication et le basculement de vos serveurs physiques.

Une fois les validations terminées avec succès, cliquez sur OK pour passer à l’étape suivante.

Si vous n’avez pas de compte de stockage Resource Manager ou de réseau virtuel compatible, vous pouvez en créer un en cliquant sur le bouton **+ Compte de stockage** ou sur le bouton **+ Réseau** en haut de la page.

## <a name="next-steps"></a>Étapes suivantes
[Configurer les paramètres de réplication](vmware-azure-set-up-replication.md)
