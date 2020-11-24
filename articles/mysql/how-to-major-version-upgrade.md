---
title: Mise à niveau de version majeure – Portail Azure – Base de données Azure pour MySQL – Serveur unique
description: Cet article décrit comment mettre à niveau une version majeure pour un serveur unique Azure Database pour MySQL à l’aide du portail Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 78c35e42cefa8897d9f93c3a941b4c0e8b81e5f9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686422"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Mise à niveau de version majeure dans un serveur unique Azure Database pour MySQL à l’aide du portail Azure

> [!IMPORTANT]
> La mise à niveau de version majeure pour un serveur unique Azure Database pour MySQL est en préversion publique.

Cet article décrit comment vous pouvez mettre à niveau votre version majeure de MySQL sur place dans un serveur unique Azure Database pour MySQL.

Cette fonctionnalité permet aux clients d’effectuer des mises à niveau sur place de leurs serveurs MySQL 5.6 vers MySQL 5.7 en un clic de bouton sans devoir déplacer des données ou modifier une chaîne de connexion d’application.

> [!Note]
> * La mise à niveau de version majeure n’est disponible que pour une mise à niveau de version majeure de MySQL 5.6 vers MySQL 5.7<br>
> * La mise à niveau de version majeure n’est pas encore prise en charge sur un serveur réplica.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [Serveur unique Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Effectuer une mise à niveau de version majeure de MySQL 5.6 vers MySQL 5.7

Pour effectuer une mise à niveau de version majeure de votre serveur Azure Database pour MySQL 5.6, procédez comme suit

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour MySQL 5.6 existant.

2. Dans la page **Vue d’ensemble**, cliquez sur le bouton **Mise à niveau** dans la barre d’outils.

3. Dans la section **Mise à niveau**, sélectionnez **OK** pour mettre à niveau le serveur Azure Database pour MySQL de la version 5.6 vers la version 5.7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database pour MySQL – Vue d’ensemble – Mise à niveau":::

4. Une notification confirme la réussite de la mise à niveau.

## <a name="next-steps"></a>Étapes suivantes

Découvrez la [Stratégie de contrôle de version Azure Database pour MySQL](concepts-version-policy.md).