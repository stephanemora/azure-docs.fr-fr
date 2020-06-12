---
title: 'Démarrage rapide : Liaison d’un espace de travail Power BI à un espace de travail Synapse'
description: Liez un espace de travail Power BI à un espace de travail Azure Synapse Analytics en suivant les étapes décrites dans ce guide.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/29/2020
ms.author: jocaplan
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3317410408754519bcd8b52e78dee4a81b3ab070
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609486"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Démarrage rapide : Liaison d’un espace de travail Power BI à un espace de travail Synapse

Dans ce guide de démarrage rapide, vous allez apprendre à connecter un espace de travail Power BI à un espace de travail Synapse Analytics pour créer des rapports et des jeux de données Power BI à partir de Synapse Studio (préversion).

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit avant de commencer](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prérequis

- [Créer un espace de travail Azure Synapse et un compte de stockage associé](quickstart-create-workspace.md)
- [Un espace de travail Power BI Professional ou Premium](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Lier un espace de travail Power BI à votre espace de travail Synapse

1. Dans Synapse Studio, cliquez sur **Gérer**.

    ![Cliquez sur Gérer dans Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. Sous **Connexions externes**, cliquez sur **Services liés**.

    ![Option Services liés mise en surbrillance.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Cliquez sur **+ Nouveau**.

    ![+ Nouveaux services liés est mis en surbrillance.](media/quickstart-link-powerbi/new-highlighted.png)

4. Cliquez sur **Power BI**, puis sur **Continuer**.

    ![Affichage d’un service lié Power BI.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Entrez un nom pour le service lié et sélectionnez un espace de travail dans la liste déroulante.

    ![Affichage de la configuration Power BI du service lié.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Cliquez sur **Créer**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Afficher l’espace de travail Power BI dans Synapse Studio

Une fois vos espaces de travail liés, vous pouvez parcourir vos jeux de données Power BI et modifier/créer des rapports Power BI dans Synapse Studio.

1. Cliquez sur **Développer**.

    ![Dans Synapse Studio, cliquez sur Développer.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Développez Power BI et l’espace de travail que vous souhaitez utiliser.

    ![Développez Power BI et l’espace de travail.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Vous pouvez créer des rapports en cliquant sur **+** en haut de l’onglet **Développer**. Vous pouvez modifier des rapports existants en cliquant sur leur nom. Toutes les modifications enregistrées seront réécrites dans l’espace de travail Power BI.

![Affichez et modifiez un rapport Power BI.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [création d’un rapport Power BI sur des fichiers stockés dans Stockage Azure](sql/tutorial-connect-power-bi-desktop.md).
