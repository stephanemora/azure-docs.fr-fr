---
title: Configuration TLS – Portail Azure – Azure Database pour MySQL
description: Découvrez comment paramétrer la configuration TLS à l’aide du portail Azure pour Azure Database pour MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 5ecf2992fa9ea56f73748a9f1f98c75f9076c68f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656887"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>Configuration des paramètres TLS dans Azure Database pour MySQL à l’aide du portail Azure

Cet article explique comment configurer un serveur Azure Database pour MySQL pour imposer une version TLS minimale permettant d’autoriser le trafic des connexions et de refuser toutes les connexions dont la version TLS est inférieure à la version TLS minimale configurée, ce qui améliore la sécurité du réseau.

Vous pouvez imposer la version TLS permettant la connexion à leur instance Azure Database pour MySQL. Les clients ont désormais la possibilité de définir la version TLS minimale pour leur serveur de base de données. Par exemple, la définition de cette version TLS minimale sur 1.0 signifie que vous autorisez les clients qui se connectent à l’aide de TLS 1.0, 1.1 et 1.2. Si vous choisissez la valeur 1.2, en revanche, cela signifie que vous autorisez uniquement les clients qui se connectent en utilisant TLS 1.2+ et que toutes les connexions entrantes avec TLS 1.0 et TLS 1.1 sont rejetées.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

* Un serveur [Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>Définir des configurations TLS pour Azure Database pour MySQL

Procédez comme suit pour définir la version TLS minimale du serveur MySQL :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur Azure Database pour MySQL existant.

1. Dans la page du serveur MySQL, cliquez sur **Sécurité de la connexion** sous **Paramètres** pour ouvrir la page de configuration de la sécurité de la connexion.

1. Dans **Version TLS minimale**, sélectionnez **1.2** pour refuser les connexions dont la version TLS est antérieure à TLS 1.2 pour votre serveur MySQL.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Configuration TLS d’Azure Database pour MySQL":::

1. Cliquez sur **Enregistrer** pour enregistrer les modifications. 

1. Une notification confirme que le paramètre de sécurité de la connexion a bien été activé et a pris effet immédiatement. **Aucun redémarrage** du serveur n’est nécessaire ou effectué. Une fois les modifications enregistrées, toutes les nouvelles connexions au serveur sont acceptées uniquement si la version TLS est supérieure ou égale à la version minimale de TLS définie sur le portail.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Configuration TLS réussie pour Azure Database pour MySQL":::

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md)
