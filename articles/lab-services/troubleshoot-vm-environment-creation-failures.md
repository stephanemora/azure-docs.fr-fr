---
title: Résoudre les échecs de création de machine virtuelle et l’environnement Azure DevTest Labs | Microsoft Docs
description: Découvrez comment résoudre les problèmes de machine virtuelle (VM) et les échecs de création d’environnement dans Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: 7baa5e4c113e6c21c6123ac7c8399533a7dfb358
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410296"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Résoudre les problèmes de machine virtuelle (VM) et les échecs de création d’environnement dans Azure DevTest Labs
DevTest Labs vous donne les avertissements si un nom d’ordinateur n’est pas valide ou si vous êtes sur le point qui viole une stratégie de laboratoire. Parfois, vous voyez red `X` en regard de votre laboratoire état de machine virtuelle ou un environnement qui vous informe que quelque chose s’est produite.  Cet article fournit quelques astuces que vous pouvez utiliser pour rechercher le problème sous-jacent et, je l’espère, éviter le problème à l’avenir.

## <a name="portal-notifications"></a>Notifications du portail
Si vous utilisez le portail Azure, la première chose à examiner est la **panneau notifications**.  Les notifications du panneau, disponible sur la barre de commandes principal en cliquant sur le **icône représentant une cloche**, vous indiquera si le laboratoire de machine virtuelle ou un environnement de création a réussi ou non.  Si une erreur s’est produite, vous consultez le message d’erreur associé à l’échec de la création. Les détails souvent donnent des informations pour vous aider à résoudre le problème. Dans l’exemple suivant, la création de la machine virtuelle a échoué en raison du manque de cœurs. Message détaillé vous indique comment résoudre le problème et demander une augmentation du quota principal.

![Notification du portail Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Journaux d’activité
Examinez les journaux d’activité si vous recherchez des informations sur une défaillance un certain temps après la tentative de la création de votre machine virtuelle ou votre environnement. Cette section vous montre comment rechercher des journaux pour les environnements et les machines virtuelles.

## <a name="activity-logs-for-virtual-machines"></a>Journaux d’activité pour les machines virtuelles

1. Sur la page d’accueil pour votre laboratoire, sélectionnez la machine virtuelle pour lancer le **Machine virtuelle** page.
2. Sur le **Machine virtuelle** page, dans le **surveillance** section du menu de gauche, sélectionnez **journal d’activité** pour voir tous les journaux associés à la machine virtuelle.
3. Dans les éléments de journal d’activité, sélectionnez l’opération ayant échoué. En règle générale, l’opération ayant échoué est appelée `Write Virtualmachines`.
4. Dans le volet droit, basculez vers l’onglet JSON. Vous consultez les détails dans la vue JSON du journal.

    ![Journal d’activité pour une machine virtuelle](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Examinez le journal JSON jusqu'à ce que vous trouviez le `statusMessage` propriété. Il vous donne le message d’erreur principale et des informations supplémentaires en détail, le cas échéant. Le code JSON suivant est une erreur d’exemple pour le noyau entre guillemets dépassé vue plus haut dans cet article.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Journal d’activité pour un environnement

Pour afficher le journal d’activité pour la création d’un environnement, procédez comme suit :

1. Sur la page d’accueil pour votre laboratoire, sélectionnez **Configuration et stratégies** sur le menu de gauche.
2. sur le **Configuration et stratégies** page, sélectionnez **journaux d’activité** dans le menu.
3. Recherchez l’échec dans la liste dans le journal d’activité et sélectionnez-le.
4. Dans le volet droit, basculez vers l’onglet JSON, puis recherchez le **statusMessage**.

    ![Journal d’activité environnement](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Journaux de déploiement de modèle Resource Manager
Si votre environnement ou la machine virtuelle a été créé via l’automatisation, il existe un dernier emplacement à consulter les informations d’erreur. C’est le journal de déploiement de modèle Azure Resource Manager. Création d’une ressource de laboratoire via automation, il est souvent effectuée via un déploiement de modèle Azure Resource Manager. Consultez[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) des exemples de modèles Azure Resource Manager qui créent les ressources DevTest Labs.

Pour afficher les journaux de déploiement du modèle lab, procédez comme suit :

1. Lancer la page du groupe de ressources dans lequel se trouve le laboratoire.
2. Sélectionnez **déploiements** dans le menu de gauche sous **paramètres**.
3. Recherchez les déploiements avec un état d’échec et sélectionnez-le.
4. Sur le **déploiement** page, sélectionnez **détails de l’opération** lien pour l’opération ayant échoué.
5. Vous consultez les détails de l’opération ayant échoué dans le **détails de l’opération** fenêtre.

## <a name="next-steps"></a>Étapes suivantes
Consultez [dépannage des échecs d’artefact](devtest-lab-troubleshoot-artifact-failure.md)