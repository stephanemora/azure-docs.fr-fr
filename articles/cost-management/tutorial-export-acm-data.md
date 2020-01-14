---
title: Tutoriel - Exporter des données depuis Azure Cost Management
description: Cet article vous montre comment créer et gérer des données Azure Cost Management exportées pour les utiliser dans des systèmes externes.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 5d5f6bc4620d60d3eb776a6229450e02035b8290
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441023"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutoriel : Créer et gérer des données exportées

Si vous avez lu le tutoriel Analyse du coût, vous êtes familiarisé avec le téléchargement manuel de vos données Cost Management. Cependant, vous pouvez créer une tâche récurrente qui exporte automatiquement sur une base quotidienne, hebdomadaire ou mensuelle vos données Cost Management dans un stockage Azure. Les données exportées sont au format CSV, et elles contiennent toutes les informations collectées par Cost Management. Vous pouvez ensuite utiliser les données exportées dans Stockage Azure avec des systèmes externes et les combiner avec vos propres données personnalisées. Vous pouvez aussi utiliser vos données exportées dans un système externe, comme un tableau de bord ou un autre système financier.

Regardez la vidéo [Comment planifier des exportations à des fins de stockage avec Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) pour créer une exportation planifiée de votre données de coût Azure vers Stockage Azure.

Les exemples de ce tutoriel montrent comment exporter vos données de gestion des coûts, puis comment vérifier que les données ont été exportées correctement.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

## <a name="prerequisites"></a>Conditions préalables requises
L’exportation des données est disponible pour divers types de comptes Azure, notamment pour les clients [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](understand-cost-mgt-data.md). Les autorisations Azure suivantes, ou étendues, sont prises en charge par abonnement pour l’exportation de données par utilisateur et par groupe. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Propriétaire : peut créer, modifier ou supprimer des exportations planifiées pour un abonnement.
- Contributeur : peut créer, modifier ou supprimer ses propres exportations planifiées. Peut modifier le nom d’exportations planifiées créées par d’autres utilisateurs.
- Lecteur : peut planifier des exportations pour lesquelles il dispose des autorisations adéquates.

Pour les comptes Stockage Azure :
- Des autorisations d’écriture sont requises pour la modification du compte de stockage configuré, quelles que soient les autorisations sur l’exportation.
- Votre compte de stockage Azure doit être configuré pour le stockage d’objets blob ou de fichiers.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Créer une exportation quotidienne

Pour créer, afficher ou planifier une exportation de données, ouvrez l’étendue souhaitée dans le portail Azure et sélectionnez **Analyse du coût** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. En haut de la page Analyse du coût, cliquez sur **Exporter**, puis choisissez une option d’exportation. Par exemple, cliquez sur **Planifier l’exportation**.  

> [!NOTE]
> - Vous pouvez non seulement créer des exportations sur des abonnements, mais aussi sur des groupes de ressources, des comptes, des services et des inscriptions. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).
>- Quand vous êtes connecté en tant que partenaire dans l’étendue du compte de facturation ou sur le locataire d’un client, vous pouvez exporter des données vers un compte de stockage Azure lié à votre compte de stockage partenaire. Toutefois, vous devez disposer d’un abonnement actif dans votre locataire CSP.
>


Cliquez sur **Ajouter**, tapez un nom pour l’exportation, puis sélectionnez l’option **Exportation quotidienne des coûts en cumul mensuel à ce jour**. Cliquez sur **Suivant**.

![Exemple de nouvelle exportation indiquant le type d’exportation](./media/tutorial-export-acm-data/basics_exports.png)

Spécifiez l’abonnement pour votre compte de stockage Azure, puis sélectionnez votre compte de stockage.  Spécifiez le conteneur de stockage et le chemin du répertoire que vous souhaitez utiliser pour le fichier d’exportation.  Cliquez sur **Suivant**.

![Exemple de nouvelle exportation indiquant les détails du compte de stockage](./media/tutorial-export-acm-data/storage_exports.png)

Vérifiez vos informations d’exportation, puis cliquez sur **Créer**.

Votre nouvelle exportation apparaît dans la liste des exportations. Par défaut, les nouvelles exportations sont activées. Si vous voulez désactiver ou supprimer une exportation planifiée, cliquez sur n’importe quel élément de la liste, puis cliquez sur **Désactiver** ou sur **Supprimer**.

Initialement, l’exportation peut s’exécuter au bout d’une ou deux heures. Jusqu’à quatre heures peuvent cependant être nécessaires avant que les données apparaissent dans les fichiers exportés.

### <a name="export-schedule"></a>Planification d’exportation

Les exportations planifiées dépendent de l’heure et du jour de la semaine de la création initiale des exportations. Quand vous créez une exportation planifiée, chacune de ses occurrences suivantes s’exécute à la même heure de la journée. Par exemple, vous créez une exportation quotidienne à 13h00. L’exportation suivante s’exécute à 13h00 le lendemain. L’heure actuelle affecte tous les autres types d’exportation de la même manière. Les exportations s’exécutent toujours à la même heure de la journée que celle à laquelle vous les avez initialement créées. Autre exemple : vous créez une exportation hebdomadaire à 16h00 le lundi. L’exportation suivante s’exécute à 16h00 le lundi suivant. *Les données exportées sont disponibles dans un délai de quatre heures après l’heure d’exécution.*

Chaque exportation crée un fichier, ce qui signifie que les exportations antérieures ne sont pas écrasées.

Il existe trois types d’options d’exportation :

**Exportation quotidienne des coûts en cumul mensuel à ce jour** : l’exportation initiale s’exécute immédiatement. Les exportations suivantes s’exécutent le lendemain à la même heure que l’exportation initiale. Les dernières données sont ajoutées aux exportations quotidiennes précédentes.

**Exportation hebdomadaire des coûts pour les 7 derniers jours** : l’exportation initiale s’exécute immédiatement. Les exportations suivantes s’exécutent à la même heure et le même jour que l’exportation initiale. Les coûts correspondent aux sept derniers jours.

**Personnalisé** : permet de planifier des exportations hebdomadaires et mensuelles avec des options de cumul hebdomadaire ou mensuel à ce jour. *L’exportation initiale s’exécute immédiatement.*

Si vous avez un abonnement de paiement à l’utilisation, MSDN ou Visual Studio, votre période de facturation peut ne pas être alignée sur le mois calendaire. Pour ces types d’abonnements et groupes de ressources, vous pouvez créer une exportation alignée sur votre période de facturation ou sur les mois calendaires. Pour créer une exportation alignée sur votre mois de facturation, accédez à **Personnalisé**, puis sélectionnez **Période de facturation à ce jour**.  Pour créer une exportation alignée sur le mois calendaire, sélectionnez **Cumul mensuel jusqu'à ce jour**.
>
>

![Nouvelle exportation - Onglet de base montrant une sélection d’exportation en cumul hebdomadaire personnalisée](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Vérifier que les données sont collectées

Vous pouvez facilement vérifier que vos données Cost Management sont collectées et visualiser le fichier CSV exporté avec l’Explorateur Stockage Azure.

Dans la liste des exportations, cliquez sur le nom du compte de stockage. Dans la page du compte de stockage, cliquez sur Ouvrir dans l’Explorateur. Si vous voyez une boîte de confirmation, cliquez sur **Oui** pour ouvrir le fichier dans l’Explorateur Stockage Azure.

![Page de compte de stockage affichant des exemples d’informations et un lien pour les ouvrir dans l’Explorateur](./media/tutorial-export-acm-data/storage-account-page.png)

Dans l’Explorateur Stockage, accédez au conteneur que vous voulez ouvrir, puis sélectionnez le dossier correspondant au mois en cours. Une liste de fichiers CSV s’affiche. Sélectionnez un fichier, puis cliquez sur **Ouvrir**.

![Exemples d’informations affichées dans l’Explorateur de stockage](./media/tutorial-export-acm-data/storage-explorer.png)

Le fichier s’ouvre avec le programme ou l’application configuré pour ouvrir les fichiers avec l’extension CSV. Voici un exemple dans Excel.

![Exemples de données CSV exportées affichées dans Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Accéder à des données exportées à partir d’autres systèmes

Un des objectifs de l’exportation de vos données Cost Management est d’accéder à ces données à partir de systèmes externes. Vous pouvez utiliser un système de tableau de bord ou un autre système financier. Ces systèmes peuvent grandement varier : montrer un exemple ne serait donc pas pratique.  Vous pouvez cependant découvrir comment accéder à vos données à partir de vos applications dans [Introduction à Stockage Azure](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

Passez au tutoriel suivant pour optimiser et améliorer l’efficacité en identifiant les ressources inactives et sous-utilisées.

> [!div class="nextstepaction"]
> [Consulter des recommandations d’optimisation et agir en fonction](tutorial-acm-opt-recommendations.md)
