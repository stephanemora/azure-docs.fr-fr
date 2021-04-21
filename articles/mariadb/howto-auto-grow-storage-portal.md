---
title: Augmenter automatiquement le stockage - Portail Azure - Azure Database for MariaDB
description: Cet article explique comment activer l’augmentation automatique du stockage pour Azure Database for MariaDB à l’aide du portail Azure
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366111"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Augmenter automatiquement le stockage dans Azure Database for MariaDB à l’aide du portail Azure
Cet article explique comment vous pouvez configurer l’augmentation d’un stockage de serveur Azure Database for MariaDB sans affecter la charge de travail.

Quand un serveur atteint la limite de stockage alloué, le serveur est marqué comme étant en lecture seule. Toutefois, si vous activez l’augmentation automatique du stockage, le stockage du serveur augmente pour prendre en charge le volume croissant de données. Pour les serveurs avec moins de 100 Go de stockage approvisionnés, la taille de stockage approvisionné augmente de 5 Go dès que l’espace de stockage libre est inférieur à 1 Go ou 10 % (selon la valeur la plus élevée) du stockage approvisionné. Pour les serveurs avec plus de 100 Go de stockage approvisionnés, la taille de stockage approvisionnée augmente de 5 % lorsque l’espace de stockage libre est inférieur à 10 Go de taille de stockage approvisionnée. Les limites de stockage maximales spécifiées [ici](concepts-pricing-tiers.md#storage) s’appliquent.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database for MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Activer l’augmentation automatique du stockage 

Suivez ces étapes pour définir l’augmentation automatique du stockage de serveur MariaDB :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database for MariaDB existant.

2. Dans la page de serveur MariaDB, sous le titre **Paramètres**, cliquez sur **Niveau tarifaire** pour ouvrir la page de niveau tarifaire.

3. Dans la section Augmentation automatique, sélectionnez **Oui** pour activer l’augmentation automatique du stockage.

    ![Azure Database for MariaDB - Settings_Pricing_tier - Augmentation automatique](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Cliquez sur **OK** pour enregistrer les modifications.

5. Une notification confirme que l’augmentation automatique a été correctement activée.

    ![Azure Database for MariaDB - Réussite de l’augmentation automatique](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-metric.md).
