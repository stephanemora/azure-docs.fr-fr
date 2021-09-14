---
title: FAQ sur la fonctionnalité de démarrage de machine virtuelle lors de la connexion de Azure Virtual Desktop – Azure
description: Forum aux questions (FAQ) et meilleures pratiques pour l’utilisation de la fonctionnalité de démarrage de machine virtuelle lors de la connexion.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ba2fcbc404a17b4b31d48b4e7d2e540a875f4f1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122564056"
---
# <a name="start-vm-on-connect-faq"></a>FAQ sur Démarrer la machine virtuelle à la connexion

Cet article présente les Questions fréquentes (FAQ) sur la fonctionnalité de démarrage de machine virtuelle au moment de la connexion pour les pools d’hôtes Azure Virtual Desktop.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Les machines virtuelles sont-elles automatiquement libérées quand un utilisateur cesse de les utiliser ?

Non. Vous devez configurer des stratégies supplémentaires pour déconnecter les utilisateurs de leurs sessions et exécuter des scripts Azure Automation pour libérer des machines virtuelles.

Pour configurer la stratégie de désallocation :

1. Connectez-vous à distance à la machine virtuelle pour laquelle vous souhaitez définir la stratégie.

2. Ouvrez l’**éditeur de stratégie de groupe**, puis accédez à **Stratégie de l’ordinateur local** > **Configuration de l’ordinateur** > **Modèles d’administration** > **composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Délais d’expiration des sessions**.

3. Recherchez la stratégie **Définir le délai d’expiration des sessions déconnectées**, puis modifiez sa valeur en **Activé**.

4. Une fois la stratégie activée, sélectionnez **Mettre fin à une session déconnectée**.

>[!NOTE]
>Veillez à définir la limite de temps pour la stratégie « Mettre fin à une session déconnectée » sur une valeur supérieure à cinq minutes. Une limite de temps basse peut avoir pour effet que des sessions d’utilisateurs prennent fin si leur réseau perd la connexion pendant trop longtemps, entraînant ainsi une perte de travail.

La déconnexion d’utilisateurs n’a pas pour effet de libérer leurs machines virtuelles. Pour découvrir comment libérer les machines virtuelles, consultez [Démarrer ou arrêter des machines virtuelles pendant les heures creuses](../automation/automation-solution-vm-management.md) pour les pools d’hôtes personnels et [Procéder à la mise à l’échelle des hôtes de session à l’aide d’Azure Automation](set-up-scaling-script.md) pour les pools d’hôtes groupés.

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Les utilisateurs peuvent-ils désactiver la machine virtuelle à partir de leurs clients ?

Oui. Les utilisateurs peuvent arrêter la machine virtuelle via le menu Démarrer dans leur session, comme ils le feraient avec un ordinateur physique. Toutefois, l’arrêt de la machine virtuelle n’a pas pour effet de la libérer. Pour découvrir comment libérer les machines virtuelles, consultez [Démarrer ou arrêter des machines virtuelles pendant les heures creuses](../automation/automation-solution-vm-management.md) pour les pools d’hôtes personnels et [Procéder à la mise à l’échelle des hôtes de session à l’aide d’Azure Automation](set-up-scaling-script.md) pour les pools d’hôtes groupés.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le démarrage d’une machine virtuelle au moment de la connexion, consultez [Démarrer une machine virtuelle lors de la connexion](start-virtual-machine-connect.md).

Si vous avez des questions plus générales sur Azure Virtual Desktop, consultez notre [FAQ](faq.yml) général.
