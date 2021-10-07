---
title: Utiliser Azure Purview Studio
description: Cet article explique comment utiliser Azure Purview Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 2e3bcd7e51226f437f42f03fa43b144bee434f33
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456135"
---
# <a name="use-purview-studio"></a>Utiliser Purview Studio

Cet article donne une vue d’ensemble des principales fonctionnalités d’Azure Purview.

## <a name="prerequisites"></a>Prérequis

* Un compte Purview actif est déjà créé sur le Portail Azure. L’utilisateur dispose des autorisations nécessaires pour accéder à [Purview Studio](https://web.purview.azure.com/resource/).

## <a name="launch-purview-account"></a>Lancement du compte Purview

* Pour lancer votre compte Purview, accédez à Comptes Purview sur le Portail Azure, sélectionnez le compte souhaité et lancez-le.

  :::image type="content" source="./media/use-purview-studio/open-purview-studio.png" alt-text="Capture d’écran de la fenêtre Purview dans le portail Azure, avec le bouton Purview Studio encadré." border="true":::

* Pour lancer un compte Purview, vous pouvez également accéder à `https://web.purview.azure.com`, puis sélectionner **Azure Active Directory** et le nom du compte.

## <a name="home-page"></a>page d'accueil

**Accueil** est la page de démarrage du client Azure Purview.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Capture d’écran de la page d’accueil":::

Dans la liste suivante figurent les principales fonctionnalités de la **page d’accueil**. Chacun des numéros de la liste correspond à un nombre encadré dans la capture d’écran précédente.

1. Nom convivial du catalogue. Vous pouvez définir le nom du catalogue dans **Gestion** > **Informations sur le compte**.

2. L’analytique du catalogue indique les volumes suivants :

   * Sources de données
   * Éléments multimédias
   * Termes de glossaire

3. La zone de recherche permet de rechercher des ressources de données dans le catalogue de données.

4. Les boutons Accès rapide donnent accès aux fonctions fréquemment utilisées de l’application. Les boutons qui sont présentés dépendent du rôle attribué à votre compte utilisateur dans la collection racine.

   * Pour l' *administrateur de collection*, le bouton disponible est **Centre des connaissances**.
   * Pour le *conservateur de données*, les boutons sont **Parcourir les ressources**, **Gérer le glossaire** et **Centre de connaissances**.
   * Pour le *lecteur de données*, les boutons sont **Parcourir les ressources**, **Afficher le glossaire** , et **Centre de connaissances**.
   * Pour l'*administrateur de la source de données* + *du conservateur de données*, les boutons sont **Parcourir les ressources**, **Gérer le glossaire** et **Centre de connaissances**.
   * Pour la *source de données admin* +  *lecteur de données* , les boutons sont **Parcourir les éléments**, **Afficher le glossaire**, et **Centre de connaissances**.
  
   > [!NOTE]
   > Pour plus d’informations sur les rôles Purview, consultez [Contrôle d’accès dans Azure Purview](catalog-permissions.md).

5. La barre de navigation de gauche aide à localiser les pages principales de l’application.   
6. L’onglet **Récemment consultés** donne la liste des ressources de données récemment consultées. Pour plus d’informations sur l’accès aux ressources, consultez [Rechercher dans le catalogue de données](how-to-search-catalog.md) et [Parcourir par type de ressource](how-to-browse-catalog.md).  L’onglet **Mes éléments** correspond à la liste des ressources de données détenues par l’utilisateur connecté.
7. **Liens** contient des liens vers l’état de la région, la documentation, les tarifs, la vue d’ensemble et l’état Purview.
8. La barre de navigation supérieure contient des informations sur les sections Notes de publication/mises à jour, Modifier le compte Purview, Notifications, Aide et Commentaires.

## <a name="knowledge-center"></a>Centre des connaissances

Dans le Centre des connaissances se trouvent toutes les vidéos et tous les tutoriels relatifs à Purview.

## <a name="guided-tours"></a>Visites guidées

Chacune des expériences utilisateur d’Azure Purview Studio comporte une visite guidée qui donne une vue d’ensemble de la page. Pour commencer la visite guidée, sélectionnez **Aide** dans la barre supérieure, puis sélectionnez **Visites guidées**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Capture d’écran de la visite guidée":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajout d’un principal de sécurité](tutorial-scan-data.md)
