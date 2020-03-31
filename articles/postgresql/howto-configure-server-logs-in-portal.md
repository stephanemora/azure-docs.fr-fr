---
title: Gérer les journaux - Portail Azure - Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment configurer et accéder aux journaux de serveur (fichiers .log) dans Azure Database pour PostgreSQL – Serveur unique à partir du portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763689"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configurer et consulter les journaux Azure Database pour PostgreSQL – Serveur unique à partir du portail Azure

Vous pouvez configurer, répertorier et télécharger les [journaux d’activité Azure Database pour PostgreSQL](concepts-server-logs.md) à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis
Les étapes décrites dans cet article supposent que vous disposez d’un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Configuration de la journalisation
Configurer l’accès aux journaux d’activité des requêtes et journaux d’activité des erreurs. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez votre serveur Azure Database pour PostgreSQL.

3. Dans la section **Supervision** de la barre latérale, sélectionnez **Journaux du serveur**. 

   ![Capture d’écran des options Journaux du serveur](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Pour afficher les paramètres du serveur, sélectionnez **Cliquez ici pour activer les journaux et configurer les paramètres associés**.

5. Modifiez les paramètres que vous devez ajuster. Toutes les modifications que vous apportez dans cette session sont surlignées en violet.

   Après avoir changé les paramètres, sélectionnez **Enregistrer**. Vous pouvez aussi abandonner vos changements. 

   ![Capture d’écran des options Paramètres du serveur](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

À partir de la page **Paramètres du serveur**, vous pouvez revenir à la liste des journaux en fermant la page.

## <a name="view-list-and-download-logs"></a>Afficher la liste et télécharger les journaux d’activité
Une fois que la journalisation a commencé, vous pouvez voir la liste des journaux disponibles et télécharger des fichiers journaux individuels. 

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database pour PostgreSQL.

3. Dans la section **Supervision** de la barre latérale, sélectionnez **Journaux du serveur**. La page présente la liste de vos fichiers journaux.

   ![Capture d’écran de la page Journaux du serveur, avec la liste des journaux mise en évidence](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convention d’affectation des noms de journaux est **postgresql-aaaa-mm-dd_hh0000.log**. La date et l’heure utilisées dans le nom de fichier correspondent au moment où le journal a été émis. Les fichiers journaux font l’objet d’une rotation toutes les heures ou par tranche de 100 Mo, selon la limite atteinte en premier.

4. Si nécessaire, utilisez la zone de recherche pour trouver rapidement un journal spécifique en fonction d’une date et d’une heure. La recherche est effectuée sur le nom du journal.

   ![Capture d’écran de la page Journaux du serveur, avec la zone de recherche et les résultats mis en évidence](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Pour téléchargez un fichier journal en particulier, sélectionnez l’icône de flèche bas à côté du fichier journal dans la ligne du tableau.

   ![Capture d’écran de la page Journaux du serveur, avec l’icône de flèche bas mise en évidence](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Étapes suivantes
- Pour savoir comment télécharger des journaux par programmation, consultez [Accéder aux journaux du serveur dans l’interface CLI](howto-configure-server-logs-using-cli.md).
- Découvrez plus en détail les [journaux du serveur](concepts-server-logs.md) dans Azure Database pour PostgreSQL. 
- Pour plus d’informations sur les définitions de paramètres et la journalisation PostgreSQL, consultez la documentation PostgreSQL sur [le signalement et la journalisation des erreurs](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

