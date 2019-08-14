---
title: Déplacement de la configuration Azure Site Recovery vers une autre région Azure | Microsoft Docs
description: Conseils pour le déplacement de la configuration Site Recovery vers une autre région Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708462"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Déplacement du coffre Recovery Services et de la configuration Azure Site Recovery vers une autre région Azure

Il existe plusieurs scénarios dans lesquels vous pouvez souhaiter déplacer vos ressources Azure existantes d’une région à une autre, que ce soit pour des raisons de gestion, de gouvernance ou suite à des fusions et des acquisitions d’entreprise, etc. L’une des ressources associées que vous aimerez peut-être emmener lors du déplacement des machines virtuelles Azure est la configuration de reprise d’activité de celles-ci. Il n’existe pas de bon moyen permettant de déplacer une configuration de reprise d’activité d’une région à une autre. Cela est essentiellement dû au fait que vous avez configuré votre région cible en fonction de votre région source pour les machines virtuelles, et que lorsque vous décidez de modifier cela, les précédentes configurations existantes de la région cible ne sont pas réutilisables mais doivent être redéfinies. Cet article définit le processus détaillé par étapes de la reconfiguration de la reprise d’activité, et de son déplacement dans une autre région.

Dans ce document, vous allez :

> [!div class="checklist"]
> * Vérifier les prérequis pour le déplacement
> * Identifier les ressources qui ont été utilisées par Azure Site Recovery 
> * Désactiver la réplication
> * Supprimer les ressources 
> * Reconfigurer la récupération de site en fonction de la nouvelle région source des machines virtuelles.

> [!IMPORTANT]
> Il n’existe actuellement aucun moyen satisfaisant de déplacer en l’état un coffre Recovery Services et la configuration de la reprise d’activité dans une autre région. Ce document guide le client tout au long du processus de désactivation de la réplication et de sa reconfiguration dans la nouvelle région.

## <a name="prerequisites"></a>Prérequis

- Veillez à bien retirer et supprimer la configuration de la reprise d’activité avant d’essayer de déplacer les machines virtuelles Azure vers une autre région. 

> [!NOTE]
> Si votre nouvelle région cible pour la machine virtuelle Azure est la même que la région actuelle, vous pouvez utiliser votre configuration de réplication existante et effectuer le déplacement selon les étapes mentionnées [ici](azure-to-azure-tutorial-migrate.md). 

- Assurez-vous de prendre une décision fondée, et que les parties prenantes soient informées, car votre machine virtuelle ne sera pas protégée contre les incidents tant que le déplacement de la machine virtuelle ne sera pas terminé. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifier les ressources qui ont été utilisées par Azure Site Recovery
Nous vous conseillons d’effectuer cette étape avant de passer à la suivante, car il est plus facile d’identifier les ressources pertinentes lorsque les machines virtuelles sont encore répliquées.

Pour chacune des machines virtuelles Azure répliquées, accédez à **Éléments protégés** > **Éléments répliqués**>**Propriétés** et identifiez les ressources suivantes :

- Groupe de ressources cible
- Compte de stockage de cache
- Compte de stockage cible (dans le cas d’une machine virtuelle Azure sur disque non managé) 
- Réseau cible


## <a name="disable-existing-disaster-recovery-configuration"></a>Désactiver la configuration de reprise d’activité existante

1. Accédez au **Coffre Recovery Services**. 
2.  Dans **Éléments protégés** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine et sélectionnez **Désactiver la réplication**.
3. Répétez cette opération pour toutes les machines virtuelles que vous souhaitez déplacer.
> [!NOTE]
> Le service Mobilité des serveurs protégés ne sera pas désinstallé, vous devez le faire manuellement. Si vous envisagez de reprotéger le serveur, vous pouvez ignorer la désinstallation du service Mobilité.

## <a name="delete-the-resources"></a>Supprimer les ressources

1. Accédez au **coffre Recovery Services**.
2. Cliquez sur **Supprimer**.
3. Procédez à la suppression de toutes les autres ressources identifiées à l’[étape précédente](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Déplacer les machines virtuelles Azure vers la nouvelle région cible

Suivez les étapes indiquées ci-dessous, en fonction de vos besoins, pour déplacer les machines virtuelles Azure vers la région cible.

- [Déplacer des machines virtuelles Azure vers une autre région](azure-to-azure-tutorial-migrate.md)
- [Déplacer des machines virtuelles Azure vers des zones de disponibilité](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Reconfigurer Site Recovery en fonction de la nouvelle région source pour les machines virtuelles

Configurez la reprise d’activité pour les machines virtuelles Azure qui ont été déplacées vers la nouvelle région en suivant les étapes mentionnées [ici](azure-to-azure-tutorial-enable-replication.md).
