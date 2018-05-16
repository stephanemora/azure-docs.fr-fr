---
title: Utilisation d’applications dans Azure Blockchain Workbench
description: Comment utiliser des contrats d’application dans Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 35a45947c2766ccc5e64f4c3523ce163a9680680
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Utilisation d’applications dans Azure Blockchain Workbench

Vous pouvez utiliser Blockchain Workbench pour créer et exécuter des opérations sur des contrats. Vous pouvez également afficher les détails du contrat, tels que l’état et l’historique des transactions.

## <a name="prerequisites"></a>Prérequis


* Un déploiement Blockchain Workbench. Pour plus de détails sur le déploiement, consultez [Azure Blockchain Workbench deployment](blockchain-workbench-deploy.md) (Déploiement d’Azure Blockchain Workbench).
* Une application blockchain déployée dans Blockchain Workbench. Voir [Créer une application blockchain dans Azure Blockchain Workbench]()

[Ouvrez Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) dans votre navigateur.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

Vous devez vous connecter en tant que membre de Blockchain Workbench. Si aucune application n’est répertoriée, cela signifie que vous êtes bien membre de Blockchain Workbench, mais que vous n’êtes membre d’aucune application. L’administrateur Blockchain Workbench peut attribuer des membres à des applications.

## <a name="create-new-contract"></a>Créer un contrat 

Pour créer un contrat, vous devez être un membre du rôle **AllowedInstanceRoles**. 

1. Dans la section des applications Blockchain Workbench, sélectionnez la vignette d’application qui contient le contrat que vous souhaitez créer. Une liste des contrats actifs s’affiche.

2. Pour créer un contrat, sélectionnez **Nouveau contrat**.

    ![Bouton Nouveau contrat](media/blockchain-workbench-use/contract-list.png)

3. Le volet **Nouveau contrat** s’affiche. Spécifiez les valeurs de paramètres initiales. Sélectionnez **Créer**.

    ![Volet Nouveau contrat](media/blockchain-workbench-use/new-contract.png)

    Le contrat que vous venez de créer s’affiche dans la liste avec les autres contrats actifs.

    ![Liste des contrats actifs](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Effectuer une opération sur un contrat

1. Dans la section des applications Blockchain Workbench, sélectionnez la vignette d’application qui contient le contrat sur lequel vous souhaitez exécuter une action.

    ![Liste des applications](media/blockchain-workbench-use/apps-list.png)

2. Sélectionnez le contrat dans la liste.

    ![Liste des contrats](media/blockchain-workbench-use/select-contract.png)

    Les détails du contrat sont affichés dans différentes sections. 

    ![Détails du contrat](media/blockchain-workbench-use/contract-details.png)

    | Section  | Description  |
    |---------|---------|
    | Statut | Répertorie l’état d’avancement des étapes du contrat |
    | Détails | Valeurs actuelles du contrat |
    | Action | Détails de la dernière action |
    | Activité | Historique des transactions du contrat |
    
3. Dans la section **Action**, sélectionnez **Effectuer une action**.

4. Les détails de l’état actuel du contrat s’affichent affichés dans un volet. Choisissez l’action à exécuter dans la liste déroulante. 

    ![Effectuer une action](media/blockchain-workbench-use/take-action.png)

5. Sélectionnez **Exécuter** pour effectuer l’action.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Dépannage Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)