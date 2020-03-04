---
title: Configurer les rapports de la Sauvegarde Azure
description: Configurer et afficher les rapports de la Sauvegarde Azure à l’aide de Log Analytics et dAzure Workbooks
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: cefe81e53e89b8d7903469e836f3c5d2665febea
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582702"
---
# <a name="configure-azure-backup-reports"></a>Configurer les rapports de la Sauvegarde Azure

Les administrateurs de sauvegarde sont souvent amenés à obtenir des insights sur les sauvegardes, en fonction de données couvrant une longue période de temps. Il peut y avoir plusieurs cas d’usage pour une solution de ce type : allocation et prévision du stockage cloud consommé, audit des sauvegardes et des restaurations et identification de tendances clés à différents niveaux de précision.

De nos jours, la Sauvegarde Azure fournit une solution de création de rapports qui tire parti de [Journaux Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) et d’[Azure Workbooks](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks), vous aidant à obtenir des insights détaillés sur vos sauvegardes dans l’ensemble de votre espace de sauvegarde. Cet article explique comment configurer et afficher des rapports de sauvegarde.

## <a name="supported-scenarios"></a>Scénarios pris en charge

* Les rapports de sauvegarde sont pris en charge pour les machines virtuelles Azure, SQL dans les machines virtuelles Azure, SAP HANA/ASE dans les machines virtuelles Azure, l’agent Sauvegarde Azure (MARS), le serveur de sauvegarde Azure (MABS) et System Center DPM.
* Concernant les charges de travail DPM, les rapports de sauvegarde sont pris en charge pour DPM versions 5.1.363.0 et ultérieures ainsi que pour l’agent versions 2.0.9127.0 et ultérieures.
* Concernant les charges de travail MABS, les rapports de sauvegarde sont pris en charge pour MABS versions 13.0.415.0 et ultérieures ainsi que pour l’agent versions 2.0.9170.0 et ultérieures.
* Les rapports de sauvegarde peuvent être affichés dans l’ensemble des éléments de sauvegarde, des coffres, des abonnements et des régions, à condition que leurs données soient envoyées à un espace de travail Log Analytics (LA) auquel l’utilisateur a accès. 
* Si vous êtes utilisateur [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) avec un accès délégué aux abonnements, vous pouvez utiliser ces rapports avec Azure Lighthouse afin de les consulter sur tous vos locataires.
* Les données des travaux de sauvegarde de fichier journal ne sont pas affichées dans les rapports.

## <a name="getting-started"></a>Prise en main

Pour commencer à utiliser les rapports, suivez les trois étapes décrites ci-dessous :

1. **Créer un espace de travail Log Analytics (LA) (ou utiliser un espace de travail existant) :**

Vous devez configurer un ou plusieurs espaces de travail pour stocker vos données de rapport de sauvegarde. L’emplacement et l’abonnement dans lesquels cet espace de travail peut être créé dépendent de l’emplacement et de l’abonnement où se trouvent vos coffres. 

Consultez l’article suivant : [Créer un espace de travail Log Analytics dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) pour configurer un espace de travail LA.

Par défaut, les données d’un espace de travail LA sont conservées pendant 30 jours. Pour que les données couvrent un horizon temporel plus long, changez la période de conservation de l’espace de travail LA. Pour ce faire, reportez-vous à l’article suivant : [Gérer l’utilisation et les coûts avec Journaux Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Configurer les paramètres de diagnostic de vos coffres :**

Des ressources Azure Resource Manager, comme les coffres Recovery Services, enregistrent des informations sur les opérations planifiées et sur les opérations déclenchées par l’utilisateur au titre de données de diagnostics. 

Dans la section Supervision de votre coffre Recovery Services, sélectionnez **Paramètres de diagnostic** et spécifiez la cible pour les données de diagnostic du coffre Recovery Services. [Découvrez l’utilisation des événements de diagnostic](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events).

![Panneau Paramètres de diagnostic](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

La Sauvegarde Azure fournit également une stratégie Azure Policy intégrée, qui automatise la configuration des paramètres de diagnostic pour tous les coffres dans une étendue donnée. Reportez-vous à l’article suivant pour apprendre à utiliser cette stratégie : [Configurer les paramètres de diagnostic de coffre à grande échelle](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

3. **Afficher les rapports sur le portail Azure :**

Une fois que vous avez configuré vos coffres pour envoyer des données à LA, affichez vos rapports de sauvegarde en accédant au panneau de n’importe quel coffre et en cliquant sur l’élément de menu **Rapports de sauvegarde**. 

![Tableau de bord du coffre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Le fait de cliquer sur ce lien ouvre le classeur des rapports de sauvegarde.

> [!NOTE]
> Le chargement initial du rapport peut prendre jusqu’à 1 minute.

Vous trouverez ci-dessous une description des différents onglets du rapport :

* **Résumé** : l’onglet Résumé fournit une vue d’ensemble de votre parc de sauvegarde. Sous l’onglet Résumé, vous pouvez obtenir un aperçu rapide du nombre total d’éléments de sauvegarde, du stockage cloud total consommé, du nombre d’instances protégées et du taux de réussite des travaux par type de charge de travail. Pour obtenir des informations plus détaillées sur un type d’artefact de sauvegarde spécifique, accédez aux onglets respectifs.

![Onglet Résumé](./media/backup-azure-configure-backup-reports/summary.png)

* **Éléments de sauvegarde** : l’onglet Éléments de sauvegarde vous permet de voir des informations et des tendances sur le stockage cloud consommé au niveau d’un élément de sauvegarde. Par exemple, si vous utilisez une sauvegarde de type SQL la machine virtuelle Azure, vous pouvez voir le stockage cloud consommé pour chaque base de données SQL en cours de sauvegarde. Vous pouvez également choisir de voir les données des éléments de sauvegarde ayant un état de protection particulier. Par exemple, si vous cliquez sur la vignette **Protection arrêtée** en haut de l’onglet, tous les widgets situés au-dessous sont filtrés pour n’afficher que les données des éléments de sauvegarde dont l’état est Protection arrêtée.

![Onglet Éléments de sauvegarde](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Utilisation** : l’onglet Utilisation vous aide à voir les paramètres de facturation clés pour vos sauvegardes. Les informations affichées dans cet onglet se trouvent au niveau d’une entité de facturation (conteneur protégé). Par exemple, dans le cas d’un serveur DPM en cours de sauvegarde sur Azure, vous pouvez voir la tendance des instances protégées et du stockage cloud consommé pour ce serveur. De même, si vous utilisez une sauvegarde de type SQL dans Azure ou SAP HANA dans Azure, cet onglet fournit des informations relatives à l’utilisation au niveau de la machine virtuelle qui contient ces bases de données.

![Onglet Utilisation](./media/backup-azure-configure-backup-reports/usage.png)

* **Travaux** : l’onglet Travaux vous permet de voir les tendances durables sur les travaux, telles que le nombre de travaux ayant échoué par jour et les principales causes d’échec des travaux. Vous pouvez voir ces informations à la fois au niveau d’un agrégat et d’un élément de sauvegarde. En cliquant sur un élément de sauvegarde particulier dans une grille, vous pouvez voir des informations détaillées sur chaque travail qui a été déclenché sur cet élément de sauvegarde dans l’intervalle de temps sélectionné.

![Onglet Travaux](./media/backup-azure-configure-backup-reports/jobs.png)

* **Stratégies** : l’onglet Stratégies vous permet de voir des informations sur toutes vos stratégies actives, telles que le nombre d’éléments associés ainsi que le stockage cloud total consommé par les éléments sauvegardés dans le cadre d’une stratégie donnée. En cliquant sur une stratégie particulière, vous pouvez voir des informations sur chacun de ses éléments de sauvegarde associés.

![Onglet Stratégies](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportation vers Excel

Le fait de cliquer sur la flèche Bas située dans le coin supérieur droit d’un widget (tableau/graphique) exporte le contenu de ce widget sous la forme d’une feuille Excel, en l’état avec les filtres existants appliqués. Pour exporter d’autres lignes d’une table vers Excel, vous pouvez augmenter le nombre de lignes affichées dans la page à l’aide de la liste déroulante **Lignes par page** située en haut de chaque grille.

## <a name="pinning-to-dashboard"></a>Épinglage au tableau de bord

Cliquez sur l’icône Épingler en haut de chaque widget pour épingler le widget sur le tableau de bord de votre portail Azure. Ainsi, vous pouvez créer des tableaux de bord personnalisés pour voir les informations les plus importantes dont vous avez besoin.

## <a name="cross-tenant-reports"></a>Rapports multilocataires

Si vous êtes utilisateur [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/) avec un accès délégué à des abonnements dans plusieurs environnements locataires, vous pouvez utiliser le filtre d’abonnement par défaut (en cliquant sur l’icône de filtre dans le coin supérieur droit du portail Azure) pour choisir tous les abonnements dont vous voulez consulter les données. Cela vous permet de sélectionner des espaces de travail LA dans vos locataires pour afficher des rapports multilocataires.

## <a name="conventions-used-in-backup-reports"></a>Conventions utilisées dans les rapports de sauvegarde

* Les filtres fonctionnent de gauche à droite et de haut en bas sous chaque onglet ; autrement dit, un filtre donné s’applique uniquement à tous les widgets qui sont situés à sa droite ou au-dessous. 
* Le fait de cliquer sur une vignette de couleur filtre les widgets situés sous la vignette de manière à récupérer les enregistrements appartenant à la valeur de cette vignette. Par exemple, le fait de cliquer sur la vignette *Protection arrêtée* sous l’onglet Éléments de sauvegarde filtre les grilles et les graphiques situés au-dessous pour afficher les données des éléments de sauvegarde dont l’état est « Protection arrêtée ».
* Les vignettes qui ne sont pas en couleur ne sont pas cliquables.
* Les données du jour partiel actuel ne sont pas montrées dans les rapports. Ainsi, quand la valeur sélectionnée pour **Intervalle de temps** est ***7 derniers jours***, le rapport affiche les enregistrements des 7 derniers jours révolus (ce qui exclut le jour en cours).
* Le rapport affiche les détails des travaux (hormis les travaux de journalisation) qui ont été **déclenchés** dans l’intervalle de temps sélectionné. 
* Les valeurs affichées pour Stockage cloud et Instances protégées se trouvent à la **fin** de l’intervalle de temps sélectionné.
* Les éléments de sauvegarde affichés dans les rapports sont ceux qui existent à la **fin** de l’intervalle de temps sélectionné. Les éléments de sauvegarde qui ont été supprimés au milieu de l’intervalle de temps sélectionné ne sont pas affichés. La même convention vaut pour les stratégies de sauvegarde.

## <a name="query-load-times"></a>Temps de chargement des requêtes

Les widgets du rapport de sauvegarde reposent sur des requêtes Kusto, qui s’exécutent sur les espaces de travail LA de l’utilisateur. Étant donné que ces requêtes impliquent généralement le traitement de grandes quantités de données, avec plusieurs jointures pour permettre des insights plus riches, les widgets peuvent ne pas se charger instantanément quand l’utilisateur consulte des rapports sur un grand parc de sauvegarde. Le tableau ci-dessous fournit une estimation approximative du temps que peut prendre le chargement des différents widgets, en fonction du nombre d’éléments de sauvegarde et de l’intervalle de temps pendant lequel le rapport est affiché :

| **Nombre de sources de données**                         | **Horizon temporel** | **Temps de chargement (approx.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| Environ 5 K                       | 1 mois          | Vignettes : 5 à 10 s <br> Grilles : 5 à 10 s <br> Graphiques : 5 à 10 s <br> Filtres au niveau du rapport : 5 à 10 s|
| Environ 5 K                       | 3 mois          | Vignettes : 5 à 10 s <br> Grilles : 5 à 10 s <br> Graphiques : 5 à 10 s <br> Filtres au niveau du rapport : 5 à 10 s|
| Environ 10 K                       | 3 mois          | Vignettes : 15 à 20 s <br> Grilles : 15 à 20 s <br> Graphiques : 1 à 2 minutes <br> Filtres au niveau du rapport : 25 à 30 s|
| Environ 15 K                       | 1 mois          | Vignettes : 15 à 20 s <br> Grilles : 15 à 20 s <br> Graphiques : 50 à 60 s <br> Filtres au niveau du rapport : 20 à 25 s|
| Environ 15 K                       | 3 mois          | Vignettes : 20 à 30 s <br> Grilles : 20 à 30 s <br> Graphiques : 2 à 3 minutes <br> Filtres au niveau du rapport : 50 à 60 s |

## <a name="what-happened-to-the-power-bi-reports"></a>Que sont devenus les rapports Power BI ?
* Notre ancienne application de modèle Power BI pour la création de rapports (dont les données provenaient d’un compte Stockage Azure) est en voie de dépréciation. Nous vous recommandons de commencer à envoyer les données de diagnostic de coffre à Log Analytics comme décrit ci-dessus, pour afficher des rapports.

* En outre, le schéma v1 d’envoi de données de diagnostic à un compte de stockage ou à un espace de travail LA est également en voie de dépréciation. Cela signifie que si vous avez écrit des automatisations ou des requêtes personnalisées basées sur le schéma v1, nous vous recommandons de les mettre à jour afin qu’elles utilisent le schéma v2 pris en charge.

## <a name="next-steps"></a>Étapes suivantes
[Découvrez la supervision et la création de rapports avec Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)