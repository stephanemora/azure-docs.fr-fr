---
title: Accéder aux journaux des requêtes lentes - Portail Azure - Azure Database for MariaDB
description: Cet article explique comment configurer et accéder aux journaux des requêtes lentes dans Azure Database for MariaDB à partir du portail Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 69a01ec021ecbade235a693b1be502353420fde0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767464"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configurer et consulter les journaux des requêtes lentes à partir du portail Azure

Vous pouvez configurer, lister et télécharger les [journaux des requêtes lentes Azure Database for MariaDB](concepts-server-logs.md) à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis
Les étapes décrites dans cet article supposent que vous disposez d’un [serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configuration de la journalisation
Configurer l’accès au journal des requêtes lentes. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Sélectionnez votre serveur Azure Database for MariaDB.

3. Dans la section **Supervision** de la barre latérale, sélectionnez **Journaux du serveur**. 
   ![Capture d’écran des options Journaux du serveur](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Pour afficher les paramètres du serveur, sélectionnez **Cliquez ici pour activer les journaux et configurer les paramètres associés**.

5. Changez les paramètres que vous devez ajuster. Activez notamment **slow_query_log** (**ON**). Toutes les modifications que vous apportez dans cette session sont surlignées en violet. 

   Après avoir changé les paramètres, sélectionnez **Enregistrer**. Vous pouvez aussi abandonner vos changements.

   ![Capture d’écran des options Paramètres du serveur](./media/howto-configure-server-logs-portal/3-save-discard.png)

À partir de la page **Paramètres du serveur**, vous pouvez revenir à la liste des journaux en fermant la page.

## <a name="view-list-and-download-logs"></a>Afficher la liste et télécharger les journaux d’activité
Une fois que la journalisation a commencé, vous pouvez afficher la liste des journaux des requêtes lentes disponibles et télécharger des fichiers journaux individuels. 

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database for MariaDB.

3. Dans la section **Supervision** de la barre latérale, sélectionnez **Journaux du serveur**. La page présente la liste de vos fichiers journaux.

   ![Capture d’écran de la page Journaux du serveur, avec la liste des journaux mise en évidence](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > La convention d’affectation des noms de journaux est **mysql-slow-<nom de votre serveur>-aaaammjjhh.log**. La date et l’heure utilisées dans le nom de fichier correspondent au moment où le journal a été émis. Les fichiers journaux font l’objet d’une rotation toutes les 24 heures ou une fois qu’ils ont atteint la taille de 7,5 Go, selon ce qui se produit en premier.

4. Si nécessaire, utilisez la zone de recherche pour trouver rapidement un journal spécifique en fonction d’une date et d’une heure. La recherche est effectuée sur le nom du journal.

5. Pour télécharger un fichier journal en particulier, sélectionnez l’icône de flèche bas en regard du fichier journal dans la ligne du tableau.

   ![Capture d’écran de la page Journaux du serveur, avec l’icône de flèche bas mise en évidence](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic

1. En dessous de la section **Supervision** dans la barre latérale, sélectionnez **Paramètres de diagnostic** > **Ajouter un paramètre de diagnostic**.

   ![Capture d’écran des options Paramètres de diagnostic](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Entrez un nom de paramètre de diagnostic.

1. Spécifiez les récepteurs de données auxquels envoyer les journaux des requêtes lentes (compte de stockage, hub d’événements ou espace de travail Log Analytics).

1. Sélectionnez **MySqlSlowLogs** comme type de journal.
![Capture d’écran des options de configuration des paramètres de diagnostic](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Après avoir configuré les récepteurs de données auxquels envoyer les journaux des requêtes lentes, sélectionnez **Enregistrer**.
![Capture d’écran des options de configuration des paramètres de diagnostic, avec l’option Enregistrer mise en évidence](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Accédez aux journaux des requêtes lentes en les explorant dans les récepteurs de données que vous avez configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

## <a name="next-steps"></a>Étapes suivantes
- Pour découvrir comment télécharger les journaux des requêtes lentes par programmation, consultez [Accéder aux journaux des requêtes lentes dans l’interface CLI](howto-configure-server-logs-cli.md).
- Découvrez les [journaux des requêtes lentes](concepts-server-logs.md) dans Azure Database for MariaDB.
- Pour plus d’informations sur les définitions de paramètres et la journalisation, consultez la documentation MariaDB relative aux [journaux](https://mariadb.com/kb/en/library/slow-query-log-overview/).