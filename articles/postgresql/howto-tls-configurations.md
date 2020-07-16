---
title: Configuration de TLS - Portail Azure - Azure Database pour PostgreSQL - Serveur unique
description: Découvrez comment paramétrer TLS à l’aide du portail Azure pour votre Azure Database pour PostgreSQL Serveur unique
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 4cf491a27fbe53a5f5bf0e8351e5bb684b3492f1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101985"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Configurer les paramètres de TLS dans Azure Database pour PostgreSQL – Serveur unique à l’aide du portail Azure

Cet article explique comment configurer un serveur Azure Database pour PostgreSQL afin d’imposer une version de TLS minimale autorisée pour les connexions et refuser toutes les connexions dont la version de TLS est inférieure à la version de TLS minimale configurée, ce qui améliore la sécurité du réseau.

Vous pouvez imposer la version de TLS permettant la connexion à la base de données Azure pour PostgreSQL. Les clients ont désormais la possibilité de définir la version de TLS minimale pour leur serveur de base de données. Par exemple, la définition de la version minimale du paramètre TLS sur TLS 1.0 signifie que votre serveur autorisera les connexions en provenance de clients utilisant TLS 1.0, 1.1 et 1.2+. Si vous choisissez la valeur 1.2, en revanche, cela signifie que vous autorisez uniquement les connexions en provenance des clients utilisant TLS 1.2+ et que toutes les connexions à l’aide des protocoles TLS 1.0 et TLS 1.1 sont rejetées.

## <a name="prerequisites"></a>Prérequis

Pour utiliser ce guide pratique, il vous faut :

* Un [Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Définir les configurations de TLS pour Azure Database pour PostgreSQL - Serveur unique

Procédez comme suit pour définir la version TLS minimale de PostgreSQL :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre Azure Database pour PostgreSQL existant.

1.  Sur la page d’Azure Database pour PostgreSQL - Serveur unique, sous **Paramètres**, cliquez sur **Sécurité des connexions** afin d’ouvrir la page de configuration de la sécurité des connexions.

1. Dans **Version TLS minimale**, sélectionnez **1.2** pour refuser les connexions dont la version TLS est antérieure à TLS 1.2 pour votre serveur unique PostgreSQL.

    ![Azure Database pour PostgreSQL Serveur unique - Configuration de TLS](./media/howto-tls-configurations/setting-tls-value.png)

1. Cliquez sur **Enregistrer** pour enregistrer les modifications.

1. Une notification confirme que le paramètre de sécurité de la connexion a bien été activé.

    ![Azure Database pour PostgreSQL - Serveur unique configuration de TLS réussie](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez [comment créer des alertes sur des métriques](howto-alert-on-metric.md)