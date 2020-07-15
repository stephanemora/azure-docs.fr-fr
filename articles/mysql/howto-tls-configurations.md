---
title: Configuration TLS – Portail Azure – Azure Database pour MySQL
description: Découvrez comment paramétrer la configuration TLS à l’aide du portail Azure pour Azure Database pour MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 46eaa6a3b97967da9c4743d0cf1f6edc8f90b1ce
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119782"
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

    ![Configuration TLS d’Azure Database pour MySQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Cliquez sur **Enregistrer** pour enregistrer les modifications.

1. Une notification confirme que le paramètre de sécurité de la connexion a bien été activé.

    ![Configuration TLS réussie pour Azure Database pour MySQL](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md).