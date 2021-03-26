---
title: Refus d’accès au réseau public - Portail Azure - Azure Database pour MySQL
description: Découvrez comment configurer le refus d’accès au réseau public avec le portail Azure pour votre serveur Azure Database pour MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: b6fd5b5f70eb813792be003836790752db1d071f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732817"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Refus d’accès au réseau public dans Azure Database pour MySQL avec le portail Azure

Cet article explique comment configurer un serveur Azure Database pour MySQL pour refuser toutes les configurations publiques et n’autoriser que les connexions établies par le biais de points de terminaison privés afin d’améliorer la sécurité réseau.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

* Un niveau de tarification [Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) avec Usage général ou Mémoire optimisée

## <a name="set-deny-public-network-access"></a>Définir le refus d’accès au réseau public

Suivez les étapes ci-dessous pour définir le refus d’accès au réseau public pour un serveur MySQL :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour MySQL existant.

1. Dans la page du serveur MySQL, cliquez sur **Sécurité de la connexion** sous **Paramètres** pour ouvrir la page de configuration de la sécurité de la connexion.

1. Dans **Refus d’accès au réseau public**, sélectionnez **Oui** afin d’activer le refus d’accès au réseau public pour votre serveur MySQL.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Azure Database pour MySQL - Refus d’accès réseau":::

1. Cliquez sur **Enregistrer** pour enregistrer les modifications.

1. Une notification confirme que le paramètre de sécurité de la connexion a bien été activé.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database pour MySQL - Refus d’accès réseau réussi":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md).
