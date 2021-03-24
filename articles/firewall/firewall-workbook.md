---
title: Monitoring des journaux avec un classeur Pare-feu Azure
description: Les classeurs Pare-feu Azure constituent un canevas flexible pour l’analyse de données du Pare-feu Azure et la création de rapports visuels enrichis au sein du Portail Azure.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831839"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Monitoring des journaux avec un classeur Pare-feu Azure

Le classeur Pare-feu Azure constitue un canevas flexible pour l’analyse de données du Pare-feu Azure. Il permet de créer des rapports visuels enrichis au sein du Portail Azure. Vous pouvez exploiter plusieurs pare-feu déployés à travers l’écosystème Azure et les combiner dans des expériences interactives unifiées.

Vous pouvez en tirer des insights sur les événements Pare-feu Azure, en savoir plus sur vos règles d’application et de réseau et voir les statistiques des activités de pare-feu à travers plusieurs URL, ports et adresses. Le classeur Pare-feu Azure vous permet de filtrer vos pare-feu et groupes de ressources, y compris dynamiquement et par catégorie avec des jeux de données faciles à lire lorsque vous examinez un problème dans vos journaux. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer, [activez la journalisation des diagnostics](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) à l’aide du Portail Azure. Consultez également [Journaux et métriques du Pare-feu Azure](logs-and-metrics.md) pour une vue d’ensemble des journaux de diagnostic et des métriques disponibles pour le Pare-feu Azure.

## <a name="get-started"></a>Prise en main

Pour déployer le classeur, accédez à [Classeur Azure Monitor pour le Pare-feu Azure](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) et suivez les instructions de la page. Le classeur Pare-feu Azure est conçu pour fonctionner sur plusieurs locataires et plusieurs abonnements. Il peut être filtré à travers plusieurs pare-feu.

## <a name="overview-page"></a>Page Vue d’ensemble

La page de présentation offre un moyen de filtrer les données à travers plusieurs espaces de travail, dates et pare-feu. Elle montre les événements en fonction du temps tous pare-feu et types de journaux confondus (applications, réseaux, renseignement sur les menaces et proxy DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Classeur Pare-feu Azure – Présentation":::

## <a name="application-rule-log-statistics"></a>Statistiques du journal des règles d’application

Cette page indique les sources uniques d’adresse IP dans le temps, le nombre de règles d’application utilisées, les noms FQDN refusés/autorisés dans le temps et les données filtrées. Vous pouvez filtrer les données en fonction de l’adresse IP.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Classeur Pare-feu Azure – Journal des règles d’application":::

## <a name="network-rule-log-statistics"></a>Statistiques du journal des règles de réseau

Cette page propose un affichage par action de règle : autoriser/refuser, port cible par IP et DNAT dans le temps. Vous pouvez également filtrer les données par action, par port et par type de destination.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Classeur Pare-feu Azure – Journal des règles de réseau":::

Vous pouvez également filtrer les journaux en fonction de la fenêtre de temps :

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Classeur Pare-feu Azure – Fenêtre de temps du journal des règles de réseau":::

## <a name="investigations"></a>Enquêtes

Vous pouvez consulter les journaux pour en savoir plus sur la ressource en fonction de l’adresse IP source. Vous récupérerez ainsi des informations comme le nom de la machine virtuelle et celui de l’interface réseau. Il est facile de filtrer les données sur la ressource à partir des journaux.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Classeur Pare-feu Azure – Enquête":::

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Diagnostics du Pare-feu Azure](firewall-diagnostics.md)
