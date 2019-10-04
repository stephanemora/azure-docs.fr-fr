---
title: Configurer et consulter les journaux des serveurs pour Azure Database for MariaDB dans le portail Azure
description: Cet article décrit comment configurer et consulter les journaux des serveurs dans Azure Database for MariaDB à partir du portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 3dbf7064e409230916668e62ef861c0ce149fdbb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065637"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurer et consulter les journaux d’activité du serveur dans le portail Azure

Vous pouvez configurer, lister et télécharger les [journaux des requêtes lentes Azure Database for MariaDB](concepts-server-logs.md) à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configuration de la journalisation
Configurer l’accès au journal des requêtes lentes. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Sélectionnez votre serveur Azure Database for MariaDB.

3. Dans la section **Supervision** dans la barre latérale, sélectionnez **Journaux d’activité du serveur**. 
   ![Sélectionnez Journaux d’activité du serveur, cliquez pour configurer](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Sélectionnez le titre **Cliquez ici pour activer les journaux d’activité et configurer les paramètres associés** pour afficher les paramètres du serveur.

5. Changez les paramètres que vous devez ajuster, notamment la définition de « slow_query_log » sur « ON ». Toutes les modifications que vous apportez dans cette session sont surlignées en violet. 

   Une fois que vous avez modifié les paramètres, vous pouvez cliquer sur **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.

   ![Cliquez sur Enregistrer ou Abandonner](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Revenez à la liste des journaux d’activité en cliquant sur le **bouton Fermer** (icône de X) dans la page **Paramètres serveur**.

## <a name="view-list-and-download-logs"></a>Afficher la liste et télécharger les journaux d’activité
Une fois que la journalisation commence, vous pouvez afficher la liste des journaux des requêtes lentes disponibles et télécharger des fichiers journaux individuels dans le volet Journaux du serveur. 

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database for MariaDB.

3. Dans la section **Supervision** dans la barre latérale, sélectionnez **Journaux d’activité du serveur**. La page affiche la liste de vos fichiers journaux, comme illustré :

   ![Liste des journaux d’activité](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > La convention d’affectation des noms de journaux est **mysql-slow-<nom de votre serveur>-aaaammjjhh.log**. La date et l’heure utilisées dans le nom de fichier correspondent au moment d’émission du journal. Les fichiers journaux d’activité font l’objet d’une rotation toutes les 24 heures ou une fois atteint le volume de 7,5 Go, selon ce qui se produit en premier.

4. Si nécessaire, utilisez la **zone de recherche** pour circonscrire rapidement un journal spécifique en fonction d’une date et d’une heure. La recherche est effectuée sur le nom du journal.

5. Téléchargez des fichiers journaux à l’aide du bouton **Télécharger** (icône de flèche bas) en regard de chaque fichier journal figurant dans la ligne de table, comme illustré :

   ![Cliquez sur l’icône Télécharger](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>Étapes suivantes
- Découvrez les [journaux des requêtes lentes](concepts-server-logs.md) dans Azure Database for MariaDB.
- Pour plus d’informations sur les définitions de paramètres et la journalisation, consultez la documentation MariaDB relative aux [journaux d’activité](https://mariadb.com/kb/en/library/slow-query-log-overview/).

<!--- See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->
