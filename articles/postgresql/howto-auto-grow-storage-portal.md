---
title: Stockage à l’aide du portail Azure dans Azure Database pour PostgreSQL - monoserveur la croissance automatique
description: Cet article décrit comment vous pouvez activer automatiquement la croissance de stockage à l’aide du portail Azure dans la base de données Azure pour PostegreSQL - serveur unique
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676757"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Stockage à l’aide du portail Azure dans Azure Database pour PostgreSQL - monoserveur la croissance automatique
Cet article décrit comment vous pouvez configurer une base de données Azure pour PostgreSQL stockage de serveur à croître sans affecter la charge de travail.

Lorsqu’un serveur atteint la limite de stockage alloué, le serveur est marqué comme étant en lecture seule. Toutefois, si vous activez la croissance automatique du stockage, le stockage du serveur augmente pour prendre en charge les données en pleine croissance. Pour les serveurs avec moins de 100 Go mis en service de stockage, la taille de stockage approvisionné est augmentée de 5 Go dès que l’espace de stockage est ci-dessous supérieur à 1 Go ou 10 % du stockage approvisionné. Pour les serveurs avec plus de 100 Go de stockage approvisionné, la taille de stockage approvisionné est augmentée de 5 % lorsque l’espace de stockage libre est inférieur à 5 % de la taille de stockage approvisionné. Limite de stockage maximale comme spécifié [ici](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) s’appliquent.

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Activer le stockage automatique la croissance 

Suivez ces étapes pour définir la croissance automatique de stockage de serveur de PostgreSQL :

1. Dans le [Azure portal](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour PostgreSQL.

2. Dans la page de serveur PostgreSQL, sous **paramètres**, cliquez sur **niveau tarifaire** pour ouvrir la page de niveau tarifaire.

3. Dans le **la croissance automatique** section, sélectionnez **Oui** pour activer la croissance automatique du stockage.

    ![Azure Database pour PostgreSQL - Settings_Pricing_tier - croissance automatique](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Cliquez sur **OK** pour enregistrer les modifications.

5. Une notification confirme la croissance auto a été activée avec succès.

    ![Azure Database pour PostgreSQL - réussite de la croissance automatique](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [comment créer des alertes sur les mesures](howto-alert-on-metric.md).
