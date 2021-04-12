---
title: FAQ sur la fonctionnalité de démarrage de machine virtuelle lors de la connexion de Windows Virtual Desktop – Azure
description: Forum aux questions (FAQ) et meilleures pratiques pour l’utilisation de la fonctionnalité de démarrage de machine virtuelle lors de la connexion.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0a35d0541358af2f5fac5e4c7486a1be93797922
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445582"
---
# <a name="start-vm-on-connect-faq-preview"></a>FAQ sur le démarrage de machine virtuelle lors de la connexion (préversion)

> [!IMPORTANT]
> La fonctionnalité de démarrage de machine virtuelle lors de la connexion est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article contient les questions fréquemment posées sur la fonctionnalité de démarrage de machine virtuelle lors de la connexion (préversion) pour des pools hôtes Windows Virtual Desktop.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Les machines virtuelles sont-elles automatiquement libérées quand un utilisateur cesse de les utiliser ?

Non. Vous devez configurer des stratégies supplémentaires pour déconnecter les utilisateurs de leurs sessions et exécuter des scripts Azure Automation pour libérer des machines virtuelles.

Pour configurer la stratégie de désallocation :

1. Connectez-vous à distance à la machine virtuelle pour laquelle vous souhaitez définir la stratégie.

2. Ouvrez l’**éditeur de stratégie de groupe**, puis accédez à **Stratégie de l’ordinateur local** > **Configuration de l’ordinateur** > **Modèles d’administration** > **composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Délais d’expiration des sessions**.

3. Recherchez la stratégie **Définir le délai d’expiration des sessions déconnectées**, puis modifiez sa valeur en **Activé**.

4. Une fois la stratégie activée, sélectionnez **Mettre fin à une session déconnectée**.

>[!NOTE]
>Veillez à définir la limite de temps pour la stratégie « Mettre fin à une session déconnectée » sur une valeur supérieure à cinq minutes. Une limite de temps basse peut avoir pour effet que des sessions d’utilisateurs prennent fin si leur réseau perd la connexion pendant trop longtemps, entraînant ainsi une perte de travail.

La déconnexion d’utilisateurs n’a pas pour effet de libérer leurs machines virtuelles. Pour découvrir comment libérer des machines virtuelles, consultez [Démarrer ou arrêter des machines virtuelles pendant les heures creuses](../automation/automation-solution-vm-management.md).

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Les utilisateurs peuvent-ils désactiver la machine virtuelle à partir de leurs clients ?

Oui. Les utilisateurs peuvent arrêter la machine virtuelle via le menu Démarrer dans leur session, comme ils le feraient avec un ordinateur physique. Toutefois, l’arrêt de la machine virtuelle n’a pas pour effet de la libérer. Pour découvrir comment libérer des machines virtuelles, consultez [Démarrer ou arrêter des machines virtuelles pendant les heures creuses](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le démarrage de machine virtuelle lors de la connexion, consultez [Démarrer une machine virtuelle lors de la connexion (préversion)](start-virtual-machine-connect.md).

Si vous avez des questions plus générales sur Windows Virtual Desktop, consultez notre [FAQ](faq.md) général.
