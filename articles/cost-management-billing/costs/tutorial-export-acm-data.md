---
title: 'Tutoriel : Créer et gérer des données exportées depuis Azure Cost Management'
description: Cet article vous montre comment créer et gérer des données Azure Cost Management exportées pour les utiliser dans des systèmes externes.
author: bandersmsft
ms.author: banders
ms.date: 05/27/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 90334d29ed2f649854863f9ad86f03811728a945
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142307"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutoriel : Créer et gérer des données exportées

Si vous avez lu le tutoriel Analyse du coût, vous êtes familiarisé avec le téléchargement manuel de vos données Cost Management. Cependant, vous pouvez créer une tâche récurrente qui exporte automatiquement sur une base quotidienne, hebdomadaire ou mensuelle vos données Cost Management dans un stockage Azure. Les données exportées sont au format CSV, et elles contiennent toutes les informations collectées par Cost Management. Vous pouvez ensuite utiliser les données exportées dans Stockage Azure avec des systèmes externes et les combiner avec vos propres données personnalisées. Vous pouvez aussi utiliser vos données exportées dans un système externe, comme un tableau de bord ou un autre système financier.

Regardez la vidéo [Comment planifier des exportations à des fins de stockage avec Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) pour créer une exportation planifiée de votre données de coût Azure vers Stockage Azure. Pour regarder d’autres vidéos, consultez la [chaîne YouTube relative à Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Les exemples de ce tutoriel montrent comment exporter vos données de gestion des coûts, puis comment vérifier que les données ont été exportées correctement.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

## <a name="prerequisites"></a>Prérequis
L’exportation des données est disponible pour divers types de comptes Azure, notamment pour les clients [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) et [Contrat client Microsoft](get-started-partners.md). Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](understand-cost-mgt-data.md). Les autorisations Azure suivantes, ou étendues, sont prises en charge par abonnement pour l’exportation de données par utilisateur et par groupe. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Propriétaire : peut créer, modifier ou supprimer des exportations planifiées pour un abonnement.
- Contributeur : peut créer, modifier ou supprimer ses propres exportations planifiées. Peut modifier le nom d’exportations planifiées créées par d’autres utilisateurs.
- Lecteur : peut planifier des exportations pour lesquelles il dispose des autorisations adéquates.

Pour les comptes Stockage Azure :
- Des autorisations d’écriture sont requises pour la modification du compte de stockage configuré, quelles que soient les autorisations sur l’exportation.
- Votre compte de stockage Azure doit être configuré pour le stockage d’objets blob ou de fichiers.

Si vous disposez d’un nouvel abonnement, vous ne pouvez pas utiliser les fonctionnalités de Cost Management tout de suite. Vous risquez de devoir attendre jusqu’à 48 heures avant de pouvoir utiliser toutes les fonctionnalités de Cost Management.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Créer une exportation quotidienne

Pour créer, afficher ou planifier une exportation de données, ouvrez l’étendue souhaitée dans le portail Azure et sélectionnez **Analyse du coût** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. En haut de la page Analyse du coût, sélectionnez **Paramètres**, puis **Exportations** et choisissez ensuite une option d’exportation.

> [!NOTE]
> - Vous pouvez non seulement créer des exportations sur des abonnements, mais aussi sur des groupes de ressources, des comptes, des services et des inscriptions. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).
>- Quand vous êtes connecté en tant que partenaire dans l’étendue du compte de facturation ou sur le locataire d’un client, vous pouvez exporter des données vers un compte de stockage Azure lié à votre compte de stockage partenaire. Toutefois, vous devez disposer d’un abonnement actif dans votre locataire CSP.

Sélectionnez **Ajouter**, tapez un nom pour l’exportation, puis sélectionnez l’option **Exportation quotidienne des coûts en cumul mensuel à ce jour**. Sélectionnez **Suivant**.

[![Exemple de nouvelle exportation indiquant le type d’exportation](./media/tutorial-export-acm-data/basics_exports.png)](./media/tutorial-export-acm-data/basics_exports.png#lightbox)

Spécifiez l’abonnement pour votre compte de stockage Azure, puis sélectionnez votre compte de stockage.  Spécifiez le conteneur de stockage et le chemin du répertoire que vous souhaitez utiliser pour le fichier d’exportation. Sélectionnez **Suivant**.

![Exemple de nouvelle exportation indiquant les détails du compte de stockage](./media/tutorial-export-acm-data/storage_exports.png)

Vérifiez vos informations d’exportation, puis sélectionnez **Créer**.

Votre nouvelle exportation apparaît dans la liste des exportations. Par défaut, les nouvelles exportations sont activées. Si vous voulez désactiver ou supprimer une exportation planifiée, sélectionnez n’importe quel élément de la liste, puis sélectionnez **Désactiver** ou **Supprimer**.

Initialement, l’exportation peut s’exécuter au bout d’une ou deux heures. Jusqu’à quatre heures peuvent cependant être nécessaires avant que les données apparaissent dans les fichiers exportés.

### <a name="export-schedule"></a>Planification d’exportation

Les exportations planifiées dépendent de l’heure et du jour de la semaine de la création initiale des exportations. Quand vous créez une exportation planifiée, chacune de ses occurrences suivantes s’exécute à la même fréquence. Par exemple, pour une exportation en cumul mensuel à ce jour définie selon une fréquence quotidienne, l’exportation s’exécute tous les jours. De même, pour une exportation hebdomadaire, l’exportation s’exécute toutes les semaines le même jour que celui planifié. Le temps de remise exact de l’exportation n’est pas garanti et les données exportées sont disponibles dans un délai de quatre heures.
Chaque exportation crée un fichier, ce qui signifie que les exportations antérieures ne sont pas écrasées.

Il existe deux types d’options d’exportation :

**Exportation quotidienne des coûts en cumul mensuel à ce jour** : l’exportation initiale s’exécute immédiatement. Les exportations suivantes s’exécutent le lendemain à la même heure que l’exportation initiale. Les dernières données sont ajoutées aux exportations quotidiennes précédentes.

**Personnalisé** : permet de planifier des exportations hebdomadaires et mensuelles avec des options de cumul hebdomadaire ou mensuel à ce jour. *L’exportation initiale s’exécute immédiatement.*

Si vous avez un abonnement de paiement à l’utilisation, MSDN ou Visual Studio, votre période de facturation peut ne pas être alignée sur le mois calendaire. Pour ces types d’abonnements et groupes de ressources, vous pouvez créer une exportation alignée sur votre période de facturation ou sur les mois calendaires. Pour créer une exportation alignée sur votre mois de facturation, accédez à **Personnalisé**, puis sélectionnez **Période de facturation à ce jour**.  Pour créer une exportation alignée sur le mois calendaire, sélectionnez **Cumul mensuel jusqu'à ce jour**.

![Nouvelle exportation - Onglet de base montrant une sélection d’exportation en cumul hebdomadaire personnalisée](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

#### <a name="create-an-export-for-multiple-subscriptions"></a>Création d’une exportation pour plusieurs abonnements

Si vous possédez un Accord Entreprise, vous pouvez utiliser un groupe d’administration pour agréger les informations sur les coûts d’abonnement dans un seul conteneur. Vous pouvez ensuite exporter les données de gestion des coûts pour le groupe d’administration.

Les exportations pour les groupes d’administration d’autres types d’abonnement ne sont pas prises en charge.

1. Créez un groupe d’administration et affectez-lui des abonnements.
1. Dans Exportations, sélectionnez **Étendue**.
1. Sélectionnez **Sélectionner ce groupe d’administration**.
1. Créez une exportation selon l’étendue pour obtenir les données de gestion des coûts pour les abonnements dans le groupe d’administration.

## <a name="verify-that-data-is-collected"></a>Vérifier que les données sont collectées

Vous pouvez facilement vérifier que vos données Cost Management sont collectées et visualiser le fichier CSV exporté avec l’Explorateur Stockage Azure.

Dans la liste des exportations, sélectionnez le nom du compte de stockage. Dans la page du compte de stockage, sélectionnez Ouvrir dans l’Explorateur. Si vous voyez une boîte de confirmation, sélectionnez **Oui** pour ouvrir le fichier dans l’Explorateur Stockage Azure.

![Page de compte de stockage affichant des exemples d’informations et un lien pour les ouvrir dans l’Explorateur](./media/tutorial-export-acm-data/storage-account-page.png)

Dans l’Explorateur Stockage, accédez au conteneur que vous voulez ouvrir, puis sélectionnez le dossier correspondant au mois en cours. Une liste de fichiers CSV s’affiche. Sélectionnez-en un, puis sélectionnez **Ouvrir**.

![Exemples d’informations affichées dans l’Explorateur de stockage](./media/tutorial-export-acm-data/storage-explorer.png)

Le fichier s’ouvre avec le programme ou l’application configuré pour ouvrir les fichiers avec l’extension CSV. Voici un exemple dans Excel.

![Exemples de données CSV exportées affichées dans Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Télécharger un fichier de données CSV exporté

Vous pouvez également télécharger le fichier CSV exporté dans le Portail Azure. Les étapes suivantes expliquent comment le trouver dans l’analyse des coûts.

1. Dans l’analyse des coûts, sélectionnez **Paramètres**, puis **Exportations**.
1. Dans la liste des exportations, sélectionnez le compte de stockage pour une exportation.
1. Dans votre compte de stockage, cliquez sur **Conteneurs**.
1. Dans la liste des conteneurs, sélectionnez le conteneur souhaité.
1. Parcourez les répertoires et les objets BLOB de stockage jusqu’à la date de votre choix.
1. Sélectionnez le fichier CSV, puis **Télécharger**.

[![Exemple de téléchargement d’exportation](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="access-exported-data-from-other-systems"></a>Accéder à des données exportées à partir d’autres systèmes

Un des objectifs de l’exportation de vos données Cost Management est d’accéder à ces données à partir de systèmes externes. Vous pouvez utiliser un système de tableau de bord ou un autre système financier. Ces systèmes peuvent grandement varier : montrer un exemple ne serait donc pas pratique.  Vous pouvez cependant découvrir comment accéder à vos données à partir de vos applications dans [Introduction à Stockage Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

Passez au tutoriel suivant pour optimiser et améliorer l’efficacité en identifiant les ressources inactives et sous-utilisées.

> [!div class="nextstepaction"]
> [Consulter des recommandations d’optimisation et agir en fonction](tutorial-acm-opt-recommendations.md)
