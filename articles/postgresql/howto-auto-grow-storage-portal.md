---
title: Augmenter automatiquement le stockage à l’aide du portail Azure dans Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment vous pouvez activer l’augmentation automatique du stockage à l’aide du portail Azure dans Azure Database pour PostgreSQL (serveur unique)
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676757"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Augmenter automatiquement le stockage à l’aide du portail Azure dans Azure Database pour PostgreSQL - Serveur unique
Cet article explique comment vous pouvez configurer l’augmentation d’un stockage de serveur Azure Database pour PostgreSQL sans affecter la charge de travail.

Quand un serveur atteint la limite de stockage alloué, le serveur est marqué comme étant en lecture seule. Toutefois, si vous activez l’augmentation automatique du stockage, le stockage du serveur augmente pour prendre en charge le volume croissant de données. Pour les serveurs avec moins de 100 Go de stockage provisionnés, la taille de stockage provisionnée augmente de 5 Go dès que l’espace de stockage libre est inférieur à 1 Go ou 10 % (selon la valeur la plus élevée) du stockage provisionné. Pour les serveurs avec plus de 100 Go de stockage provisionné, la taille de stockage provisionnée augmente de 5 % quand l’espace de stockage libre est inférieur à 5 % de la taille de stockage provisionnée. Les limites de stockage maximales spécifiées [ici](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) s’appliquent.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Activer l’augmentation automatique du stockage 

Suivez ces étapes pour définir l’augmentation automatique du stockage de serveur PostgreSQL :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour PostgreSQL.

2. Dans la page de serveur PostgreSQL, sous **Paramètres**, cliquez sur **Niveau tarifaire** pour ouvrir la page de niveau tarifaire.

3. Dans la section **Augmentation automatique**, sélectionnez **Oui** pour activer l’augmentation automatique du stockage.

    ![Azure Database pour PostgreSQL - Settings_Pricing_tier - Augmentation automatique](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Cliquez sur **OK** pour enregistrer les modifications.

5. Une notification confirme que l’augmentation automatique a été correctement activée.

    ![Azure Database pour PostgreSQL - Réussite de l’augmentation automatique](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md).
