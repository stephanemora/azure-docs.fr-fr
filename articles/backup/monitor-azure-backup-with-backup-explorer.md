---
title: Superviser vos sauvegardes avec l’Explorateur de sauvegarde
description: Article qui explique comment utiliser l’Explorateur de sauvegarde pour effectuer une supervision en temps réel des sauvegardes dans l’ensemble des coffres, abonnements, régions et locataires.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992004"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Superviser vos sauvegardes avec l’Explorateur de sauvegarde

Plus les organisations sauvegardent des machines dans le cloud, plus il est important de disposer d’un emplacement central à partir duquel consulter des informations opérationnelles sur ces sauvegardes formant un large parc, afin de les superviser avec efficacité.

Le classeur de l’Explorateur de sauvegarde est un classeur Azure Monitor prédéfini qui permet aux clients d’utiliser un seul volet pour effectuer des activités de supervision opérationnelle liées à la sauvegarde dans l’ensemble du parc de sauvegarde sur Azure (à savoir les locataires, emplacements, abonnements, groupes de ressources et coffres), le tout à partir d’un emplacement central. En gros, il offre les fonctionnalités suivantes :

* **Perspective à grande échelle** : vue agrégée des éléments, travaux, alertes, stratégies et ressources de sauvegarde non configurés pour la sauvegarde dans l’ensemble du parc de sauvegarde. 
* **Analyse au niveau du détail** : vous pouvez choisir d’obtenir des informations détaillées sur chacune des entités (travaux, alertes, stratégies et éléments de sauvegarde), le tout à partir d’un seul emplacement.
* **Interfaces actionnables** : une fois que vous avez identifié un problème, vous pouvez choisir d’effectuer des actions en accédant naturellement à l’élément de sauvegarde ou à la ressource Azure.

Les fonctionnalités ci-dessus sont fournies prêtes à l’emploi par une intégration native à Azure Resource Graph et aux classeurs Azure Monitor.

> [!NOTE]
> * Pour l’instant, l’Explorateur de sauvegarde est uniquement disponible pour les données de machines virtuelles Azure.
> * L’Explorateur de sauvegarde a vocation à servir de tableau de bord opérationnel facilitant l’affichage d’informations sur vos sauvegardes effectuées lors des 7 derniers jours (maximum).
> * Actuellement, la personnalisation du modèle de l’Explorateur de sauvegarde n’est pas prise en charge. De plus, nous vous déconseillons d’écrire des automatisations personnalisées sur des données Azure Resource Graph pour le moment.

## <a name="getting-started"></a>Prise en main

Vous pouvez ouvrir l’Explorateur de sauvegarde en accédant à l’un de vos coffres Recovery Services, puis en cliquant sur le lien **Explorateur de sauvegarde** dans la page **Vue d’ensemble**.

![Lien rapide de coffre](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Cliquez sur le lien pour ouvrir l’Explorateur de sauvegarde qui offre une vue agrégée de tous les coffres et abonnements auxquels vous avez accès. Si vous utilisez un compte Azure Lighthouse, vous pouvez voir les données de tous les locataires auxquels vous avez accès (pour plus d’informations, consultez la section ci-dessous sur les vues multilocataires).

![Page d’arrivée de l’Explorateur](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Cas d’usage de l’Explorateur de sauvegarde

L’Explorateur de sauvegarde comprend plusieurs onglets, chacun fournissant des informations détaillées sur un type spécifique d’artefact de sauvegarde, par exemple, des éléments, travaux, stratégies de sauvegarde, etc. Cette section fournit une brève présentation de chacun des onglets. Les vidéos donnent des exemples de cas d’usage pour chacun des onglets, ainsi qu’une description des différentes commandes à la disposition de l’utilisateur.

### <a name="summary"></a>Résumé

L’onglet Résumé vous permet d’obtenir un aperçu rapide de l’état global de votre parc de sauvegarde. Vous pouvez voir des informations telles que le nombre d’éléments protégés, le nombre d’éléments pour lesquels la protection n’a pas été activée, le nombre de travaux ayant réussi au cours des dernières 24 heures, etc. 

Chacun des autres onglets représente une entité distincte, à savoir : Éléments de sauvegarde, Travaux de sauvegarde, Alertes de sauvegarde et Stratégies de sauvegarde. Vous pouvez également voir des informations sur les machines pour lesquelles la sauvegarde n’a pas été configurée. Cliquez sur l’un de ces onglets pour voir des informations propres à cette entité.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Éléments de sauvegarde

Vous pouvez voir chacun de vos éléments de sauvegarde filtré par abonnement, par coffre et selon d’autres paramètres. Cliquer sur le nom d’un élément de sauvegarde vous permet d’ouvrir le panneau Azure de cet élément de sauvegarde. Par exemple, dans la table, vous pouvez remarquer que la dernière sauvegarde a échoué pour l’élément « X ». Cliquez sur « X » pour ouvrir le panneau Sauvegarde de cet élément, dans lequel vous pouvez déclencher une opération de sauvegarde à la demande.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Travaux

Vous pouvez voir les détails de tous les travaux qui ont été déclenchés au cours des sept derniers jours, en accédant à l’onglet Travaux. Ici, vous pouvez filtrer par opération, état et code d’erreur (pour les travaux ayant échoué).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Alertes

Vous pouvez voir les détails de toutes les alertes qui ont été déclenchées dans vos coffres au cours des sept derniers jours, en cliquant sur l’onglet Alertes. Ici, vous pouvez filtrer les enregistrements par type d’alerte (par exemple, échec de la sauvegarde, échec de la restauration), par état actuel de l’alerte (par exemple, active ou résolue) et par gravité de l’alerte (par exemple, critique, avertissement, informations). Vous pouvez également accéder à la machine virtuelle Azure en cliquant sur le lien vers cette machine virtuelle afin d’effectuer l’action nécessaire.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Stratégies

Le fait de cliquer sur l’onglet Stratégies vous permet de voir des informations clés sur toutes les stratégies de sauvegarde qui ont été créées dans votre parc de sauvegarde. Vous pouvez voir le nombre d’éléments associés à chaque stratégie, ainsi que la durée de conservation et la fréquence de sauvegarde spécifiées par chaque stratégie.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Sauvegarde non activée

Il est important que l’administrateur de sauvegarde d’une organisation puisse rapidement identifier les machines de l’organisation pour lesquelles la sauvegarde n’est pas encore activée. Il peut ainsi activer rapidement la sauvegarde pour toutes les machines qui ont besoin d’une protection. Le fait de cliquer sur l’onglet **Sauvegarde non activée** vous facilite la tâche.

Sous cet onglet, vous voyez une table contenant la liste des éléments qui ne sont pas protégés. Si votre organisation a pour pratique d’attribuer des étiquettes différentes aux ordinateurs de production et aux ordinateurs de test, ou encore aux machines remplissant d’autres fonctions, chacune pouvant nécessiter une stratégie de sauvegarde distincte, un filtrage par étiquette vous permet de voir les informations propres à une certaine classe de machines. Si vous cliquez sur le nom d’un élément, vous êtes redirigé vers le panneau **Configurer la sauvegarde** de cet élément, où vous pouvez choisir de sauvegarder cet élément avec une stratégie de sauvegarde appropriée.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Exportation vers Excel

Le fait de cliquer sur la flèche Bas située dans le coin supérieur droit d’un widget (tableau/graphique) exporte le contenu de ce widget sous la forme d’une feuille Excel, en l’état avec les filtres existants appliqués. Pour exporter d’autres lignes d’une table vers Excel, vous pouvez augmenter le nombre de lignes affichées dans la page à l’aide de la liste déroulante **Lignes par page** située en haut de chaque onglet.

## <a name="pinning-to-dashboard"></a>Épinglage au tableau de bord

Vous pouvez cliquer sur l’icône Épingler en haut de chaque widget pour épingler ce widget sur le tableau de bord de votre portail Azure. Ainsi, vous pouvez créer des tableaux de bord personnalisés pour voir les informations les plus importantes dont vous avez besoin.

## <a name="cross-tenant-views"></a>Vues multilocataires

Si vous êtes utilisateur Azure Lighthouse avec un accès délégué à des abonnements dans plusieurs environnements locataires, vous pouvez utiliser le filtre d’abonnement par défaut (en cliquant sur l’icône de filtre dans le coin supérieur droit du portail Azure) pour sélectionner tous les abonnements dont vous voulez consulter les données. Ainsi, vous permettez à l’Explorateur de sauvegarde d’agréger des informations sur tous les coffres inclus dans tous ces abonnements. Apprenez-en davantage sur Azure Lighthouse [ici](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Étapes suivantes

[Découvrir comment utiliser Azure Monitor pour obtenir des insights sur vos données de sauvegarde](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)