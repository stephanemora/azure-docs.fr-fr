---
title: Augmenter automatiquement le stockage - Portail Azure - Azure Database pour MySQL
description: Cet article explique comment activer l’augmentation automatique du stockage pour Azure Database pour MySQL à l’aide du portail Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7be3e7e1a0d2e48b37c319f67e3b382f825469df
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770609"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Augmenter automatiquement le stockage dans Azure Database pour MySQL à l’aide du portail Azure
Cet article explique comment vous pouvez configurer l’augmentation d’un stockage de serveur Azure Database pour MySQL sans affecter la charge de travail.

Quand un serveur atteint la limite de stockage alloué, le serveur est marqué comme étant en lecture seule. Toutefois, si vous activez l’augmentation automatique du stockage, le stockage du serveur augmente pour prendre en charge le volume croissant de données. Pour les serveurs avec moins de 100 Go de stockage provisionnés, la taille de stockage provisionnée augmente de 5 Go dès que l’espace de stockage libre est inférieur à 1 Go ou 10 % (selon la valeur la plus élevée) du stockage provisionné. Pour les serveurs avec plus de 100 Go de stockage provisionné, la taille de stockage provisionnée augmente de 5 % quand l’espace de stockage libre est inférieur à 5 % de la taille de stockage provisionnée. Les limites de stockage maximales spécifiées [ici](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) s’appliquent.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Activer l’augmentation automatique du stockage 

Suivez ces étapes pour définir l’augmentation automatique du stockage de serveur MySQL :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour MySQL existant.

2. Dans la page de serveur MySQL, sous le titre **Paramètres**, cliquez sur **Niveau tarifaire** pour ouvrir la page Niveau tarifaire.

3. Dans la section Augmentation automatique, sélectionnez **Oui** pour activer l’augmentation automatique du stockage.

    ![Azure Database pour MySQL - Settings_Pricing_tier - Augmentation automatique](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Cliquez sur **OK** pour enregistrer les modifications.

5. Une notification confirme que l’augmentation automatique a été correctement activée.

    ![Azure Database pour MySQL - Réussite de l’augmentation automatique](./media/howto-auto-grow-storage-portal/5-auto-grow-success.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md).
