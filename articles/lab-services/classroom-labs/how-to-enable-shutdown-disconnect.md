---
title: Activer l’arrêt des machines virtuelles lors de la déconnexion d'Azure Lab Services | Microsoft Docs
description: Découvrez comment activer ou désactiver l’arrêt automatique des machines virtuelles lorsqu'une connexion Bureau à distance est déconnectée.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117137"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Activer l’arrêt automatique des machines virtuelles lors de la déconnexion
Cet article vous explique comment activer ou désactiver l’arrêt automatique des machines virtuelles Lab **Windows 10** (modèle ou étudiant) après la déconnexion d’une connexion Bureau à distance. Vous pouvez également spécifier la durée pendant laquelle les machines virtuelles doivent attendre que l’utilisateur se reconnecte avant de s’arrêter automatiquement.

Un administrateur de compte lab peut configurer ce paramètre pour le compte lab dans lequel vous créez des labs. Pour plus d’informations, consultez [Configurer l’arrêt automatique des machines virtuelles lors de la déconnexion d’un compte lab](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). En tant que propriétaire de lab, vous pouvez remplacer le paramètre lors de la création d’un lab ou après la création de celui-ci. 

## <a name="configure-when-creating-a-lab"></a>Effectuer la configuration lors de la création d'un lab
Sur la page de l'étape 3 de l’Assistant Création de lab, vous pouvez activer ou désactiver cette fonctionnalité et spécifier la durée pendant laquelle la machine virtuelle doit attendre que l’utilisateur se reconnecte avant de s’arrêter automatiquement. 

![Effectuer la configuration au moment de la création du lab](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Effectuer la configuration une fois le compte lab créé
Vous pouvez configurer ce paramètre sur la page **Paramètres**, comme le montre l’image suivante : 

![Effectuer la configuration une fois le compte lab créé](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Si vous arrêtez le système d’exploitation Windows d’une machine virtuelle avant de vous déconnecter d’une session RDP sur cette machine virtuelle, la fonctionnalité d’arrêt automatique ne marche pas correctement.  

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [Tableau de bord pour les labs de classe](use-dashboard.md)