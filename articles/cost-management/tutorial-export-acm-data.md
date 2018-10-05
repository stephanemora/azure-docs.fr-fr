---
title: 'Tutoriel : Créer et gérer des données exportées depuis Azure Cost Management | Microsoft Docs'
description: Cet article vous montre comment vous pouvez créer et gérer des données Azure Cost Management pour les utiliser dans des systèmes externes.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0765e79b90eed49742f5eead33063907eb1db1f4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47030844"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutoriel : Créer et gérer des données exportées

Si vous avez lu le tutoriel Analyse du coût, vous êtes familiarisé avec le téléchargement manuel de vos données Cost Management. Cependant, vous pouvez créer une tâche périodique quotidienne qui exporte automatiquement tous les jours vos données Cost Management dans un stockage Azure. Les données exportées sont au format CSV, et elles contiennent toutes les informations collectées par Cost Management. Vous pouvez ensuite utiliser les données exportées dans Stockage Azure avec des systèmes externes et les combiner avec vos propres données personnalisées. Vous pouvez aussi utiliser vos données exportées dans un système externe, comme un tableau de bord ou un autre système financier.

Les exemples de ce tutoriel montrent comment exporter vos données de gestion des coûts, puis comment vérifier que les données ont été exportées correctement.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

## <a name="prerequisites"></a>Prérequis

L’exportation de données est disponible pour tous les clients détenant un [Accord Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Les autorisations Azure suivantes sont prises en charge par abonnement chaque pour l’exportation de données par utilisateur et par groupe :

- Propriétaire : peut créer, modifier ou supprimer des exportations planifiées pour un abonnement.
- Contributeur : peut créer, modifier ou supprimer ses propres exportations planifiées. Peut modifier le nom d’exportations planifiées créées par d’autres utilisateurs.
- Lecteur : peut planifier des exportations pour lesquelles il dispose des autorisations adéquates.

Pour les comptes Stockage Azure :
- Des autorisations d’écriture sont requises pour la modification du compte de stockage configuré, quelles que soient les autorisations sur l’exportation.
- Votre compte de stockage Azure doit être configuré pour le stockage d’objets blob ou de fichiers.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Créer une exportation quotidienne

Gestion des coûts + Facturation &gt; sélectionnez un abonnement ou un groupe de ressources dans un abonnement &gt; Exporter &gt; **Ajouter**.

Tapez un nom pour l’exportation et spécifiez l’abonnement, le compte de stockage Azure, le conteneur, et le répertoire de stockage des fichiers ou le conteneur d’objets blob, puis cliquez sur **Créer**.

![Nouvelle exportation](./media/tutorial-export-acm-data/new-export01.png)

Votre nouvelle exportation apparaît dans la liste des exportations. Par défaut, les nouvelles exportations sont activées et s’exécutent tous les jours. Si vous voulez désactiver ou supprimer une exportation planifiée, cliquez sur n’importe quel élément de la liste, puis cliquez sur **Désactiver** ou sur **Supprimer**.

Initialement, l’exportation peut s’exécuter au bout d’une ou deux heures. Jusqu’à quatre heures peuvent cependant être nécessaires avant que les données apparaissent dans les fichiers exportés.

## <a name="verify-that-data-is-collected"></a>Vérifier que les données sont collectées

Vous pouvez facilement vérifier que vos données Cost Management sont collectées et visualiser le fichier CSV exporté avec l’Explorateur Stockage Azure.

Dans la liste des exportations, cliquez sur le nom du compte de stockage. Dans la page du compte de stockage, cliquez sur Ouvrir dans l’Explorateur. Si vous voyez une boîte de confirmation, cliquez sur **Oui** pour ouvrir le fichier dans l’Explorateur Stockage Azure.

![Page Compte de stockage](./media/tutorial-export-acm-data/storage-account-page.png)

Dans l’Explorateur Stockage, accédez au conteneur que vous voulez ouvrir, puis sélectionnez le dossier correspondant au mois en cours. Une liste de fichiers CSV s’affiche. Sélectionnez un fichier, puis cliquez sur **Ouvrir**.

![Explorateur Stockage](./media/tutorial-export-acm-data/storage-explorer.png)

Le fichier s’ouvre avec le programme ou l’application configuré pour ouvrir les fichiers avec l’extension CSV. Voici un exemple dans Excel.

![Exemple de données exportées](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Accéder à des données exportées à partir d’autres systèmes

Un des objectifs de l’exportation de vos données Cost Management est d’accéder à ces données à partir de systèmes externes. Vous pouvez utiliser un système de tableau de bord ou un autre système financier. Ces systèmes peuvent grandement varier : montrer un exemple ne serait donc pas pratique.  Vous pouvez cependant découvrir comment accéder à vos données à partir de vos applications dans [Introduction à Stockage Azure](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une exportation quotidienne
> * Vérifier que les données sont collectées

Passez au tutoriel suivant pour optimiser et améliorer l’efficacité en identifiant les ressources inactives et sous-utilisées.

> [!div class="nextstepaction"]
> [Consulter des recommandations d’optimisation et agir en fonction](tutorial-acm-opt-recommendations.md)
