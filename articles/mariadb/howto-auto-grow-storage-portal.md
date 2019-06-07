---
title: La croissance automatique stockage dans Azure Database pour MariaDB avec le portail Azure
description: Cet article décrit comment vous pouvez activer automatiquement la croissance de stockage pour Azure Database pour MariaDB à l’aide du portail Azure
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: bb3291b66776a5f0f6be16069b2d6a999b2d1f32
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676877"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>La croissance automatique stockage dans Azure Database pour MariaDB avec le portail Azure
Cet article décrit comment vous pouvez configurer une base de données Azure pour le stockage de serveur MariaDB de croître sans affecter la charge de travail.

Lorsqu’un serveur atteint la limite de stockage alloué, le serveur est marqué comme étant en lecture seule. Toutefois, si vous activez la croissance automatique du stockage, le stockage du serveur augmente pour prendre en charge les données en pleine croissance. Pour les serveurs avec moins de 100 Go mis en service de stockage, la taille de stockage approvisionné est augmentée de 5 Go dès que l’espace de stockage est ci-dessous supérieur à 1 Go ou 10 % du stockage approvisionné. Pour les serveurs avec plus de 100 Go de stockage approvisionné, la taille de stockage approvisionné est augmentée de 5 % lorsque l’espace de stockage libre est inférieur à 5 % de la taille de stockage approvisionné. Limite de stockage maximale comme spécifié [ici](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) s’appliquent.

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser ce guide pratique, il vous faut :
- Un [Azure Database pour MariaDB server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Activer le stockage automatique la croissance 

Suivez ces étapes pour définir la croissance automatique de stockage de serveur de MariaDB :

1. Dans le [Azure portal](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour MariaDB.

2. Dans la page de serveur MariaDB, sous **paramètres** titre, cliquez sur **niveau tarifaire** pour ouvrir la page de niveau tarifaire.

3. Dans la section de la croissance automatique, sélectionnez **Oui** pour activer la croissance automatique du stockage.

    ![Azure Database pour MariaDB - Settings_Pricing_tier - croissance automatique](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Cliquez sur **OK** pour enregistrer les modifications.

5. Une notification confirme la croissance auto a été activée avec succès.

    ![Azure Database pour MariaDB - réussite de la croissance automatique](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [comment créer des alertes sur les mesures](howto-alert-metric.md).
