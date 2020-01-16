---
title: Créer et partager des tableaux de bord du portail Azure | Documents Microsoft
description: Cet article décrit comment créer, personnaliser, publier et partager des tableaux de bord dans le portail Azure.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/01/2019
ms.author: mblythe
ms.openlocfilehash: a3b4d7cb33bf0da0c4431d76a54644208ea6468f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640454"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Créer et partager des tableaux de bord dans le portail Azure

Avec les tableaux de bord du portail Azure, vous pouvez créer une vue ciblée et organisée de vos ressources cloud. Servez-vous des tableaux de bord comme d’un espace de travail où vous pouvez rapidement lancer des tâches pour vos opérations quotidiennes ainsi que superviser vos ressources.  Créez des tableaux de bord personnalisés en fonction des projets, des tâches ou des rôles utilisateur, par exemple.  Le portail Azure propose un tableau de bord par défaut comme point de départ. Vous pouvez modifier ce tableau de bord par défaut, créer et personnaliser des tableaux de bord supplémentaires, et publier et partager vos tableaux de bord afin de les mettre à la disposition d’autres utilisateurs. Cet article montre comment créer un tableau de bord et personnaliser son interface, puis comment publier et partager ce tableau de bord.

## <a name="create-a-new-dashboard"></a>Créer un tableau de bord

Dans cet exemple, nous créons un tableau de bord privé auquel nous donnons un nom. Effectuez d’abord ces étapes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Tableau de bord** dans la section supérieure de la barre latérale gauche. Votre vue par défaut peut déjà être définie sur le tableau de bord.
1. Sélectionnez **+ Nouveau tableau de bord**.

    ![Capture d’écran du tableau de bord par défaut](./media/azure-portal-dashboards/dashboard-new.png)

4. Cette action affiche la **Galerie de vignettes** dans laquelle vous sélectionnez les vignettes, ainsi qu’une grille vide sur laquelle vous disposez les vignettes.

    ![Capture d’écran de la Galerie de vignettes et de la grille vide](./media/azure-portal-dashboards/dashboard-name.png)

5. Sélectionnez le texte **Mon tableau de bord** dans l’étiquette du tableau de bord et entrez un nom permettant d’identifier facilement le tableau de bord personnalisé.
1. Sélectionnez **Personnalisation terminée**  dans l’en-tête de page pour quitter le mode d’édition.

La vue du tableau de bord affiche maintenant votre tableau de bord vide. Sélectionnez la liste déroulante à côté du nom du tableau de bord pour afficher tous les tableaux de bord à votre disposition. En effet, la liste peut inclure des tableaux de bord que d’autres utilisateurs ont créés et partagés.

## <a name="edit-a-dashboard"></a>Modifier un tableau de bord

Maintenant, nous allons modifier le tableau de bord pour ajouter, redimensionner et réorganiser les vignettes qui représentent vos ressources Azure.

### <a name="add-tiles"></a>Ajouter des vignettes

Pour ajouter des vignettes à un tableau de bord, effectuez les étapes suivantes :
1. Sélectionnez ![icône d’édition](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifier** dans l’en-tête de page.

    ![Capture d’écran du tableau de bord montrant l’icône d’édition sélectionnée](./media/azure-portal-dashboards/dashboard-edit.png)

2. Parcourez la **Galerie de vignettes** ou utilisez le champ de recherche pour trouver la vignette souhaitée.
1. Sélectionnez **Ajouter** pour ajouter automatiquement la vignette au tableau de bord, dans la taille de vignette et à l’emplacement par défaut. Vous pouvez aussi faire glisser la vignette dans la grille jusqu’à l’emplacement voulu.

Dans beaucoup de pages de ressources (également appelées « panneaux »), la barre de commandes comporte une icône de punaise. Si vous sélectionnez cette icône, une vignette représentant la page source est épinglée au tableau de bord actif. Cette méthode est une autre façon d’ajouter des vignettes à un tableau de bord.

![Capture d’écran de la barre de commandes d’une page avec l’icône de punaise](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Si vous travaillez avec plusieurs organisations, ajoutez la vignette **Identité d’organisation** au tableau de bord pour montrer clairement à quelle organisation les ressources appartiennent.
>
>
### <a name="resize-or-rearrange-tiles"></a>Redimensionner ou réorganiser des vignettes
Pour changer la taille d’une vignette ou réorganiser les vignettes dans un tableau de bord, effectuez les étapes suivantes :

1. Sélectionnez ![icône d’édition](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifier** dans l’en-tête de page.
1. Sélectionnez le menu contextuel dans le coin supérieur droit d’une vignette. Ensuite, choisissez une taille de vignette. Les vignettes qui prennent en charge toutes les tailles présentent aussi une « poignée » dans le coin inférieur droit, que vous pouvez faire glisser pour changer la taille de la vignette.

   ![Capture d’écran du tableau de bord avec le menu des tailles de vignette ouvert](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Sélectionnez une vignette et faites-la glisser vers un nouvel emplacement sur la grille pour réorganiser votre tableau de bord.

### <a name="additional-tile-configuration"></a>Configuration supplémentaire des vignettes

Vous devrez peut-être configurer d’autres paramètres des vignettes pour afficher les informations dont vous avez besoin. Par exemple, la vignette **Graphique des métriques** doit être configurée pour afficher une métrique d’**Azure Monitor**. Vous pouvez également personnaliser les données des vignettes pour remplacer les paramètres de date et d’heure par défaut du tableau de bord.

Une bannière **Configurer la vignette** reste affichée sur chaque vignette qui n’a pas encore été personnalisée. Sélectionnez cette bannière, puis effectuez la configuration requise.

![Capture d’écran d’une vignette à configurer](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Utilisez une étiquette Markdown pour afficher un contenu statique personnalisé dans votre tableau de bord. Par exemple, vous pouvez afficher des instructions de base, une image, un ensemble de liens hypertexte ou même des informations de contact. Pour plus d’informations sur l’utilisation d’une vignette Markdown, consultez [Utiliser une vignette Markdown sur les tableaux de bord Azure pour afficher un contenu personnalisé](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Personnaliser les données de vignette

Le tableau de bord affiche automatiquement les données d’activité des dernières 24 heures. Pour changer l’intervalle de temps des données de cette vignette, effectuez les étapes suivantes :

1. Sélectionnez l’icône de filtre ![icône de filtre](./media/azure-portal-dashboards/dashboard-filter.png) dans le coin supérieur gauche de la vignette ou sélectionnez **Personnaliser les données de vignette** dans le menu contextuel.

   ![Capture d’écran du menu contextuel de la vignette](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Cochez la case **Remplacer les paramètres d’heure du tableau de bord au niveau de la vignette**.

   ![Capture d’écran de la boîte de dialogue où configurer les paramètres d’heure de la vignette](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Choisissez l’intervalle de temps des données à afficher sur cette vignette. Vous pouvez choisir un intervalle allant des 30 dernières minutes aux 30 derniers jours, ou définir une plage personnalisée.
1. Choisissez la granularité temporelle de l’affichage. Vous pouvez choisir d’afficher les données par incréments d’une minute à un mois.
1. Sélectionnez **Appliquer**.

## <a name="delete-a-tile"></a>Supprimer une vignette

Pour supprimer une vignette d’un tableau de bord, effectuez les étapes suivantes :

* Sélectionnez le menu contextuel dans le coin supérieur droit de la vignette, puis sélectionnez **Supprimer du tableau de bord**. Ou,
* Sélectionnez ![icône d’édition](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Modifier** pour passer en mode de personnalisation. Pointez sur le coin supérieur droit de la vignette, puis sélectionnez l’icône de suppression ![icône de suppression](./media/azure-portal-dashboards/dashboard-delete-icon.png) pour supprimer la vignette du tableau de bord.

   ![Capture d’écran montrant comment supprimer une vignette du tableau de bord](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Cloner un tableau de bord

Pour utiliser un tableau de bord existant comme modèle dans un nouveau tableau de bord, effectuez les étapes suivantes :

1. Assurez-vous que la vue de tableau de bord affiche le tableau de bord que vous souhaitez copier.
1. Dans l’en-tête de page, sélectionnez ![icône de clonage](./media/azure-portal-dashboards/dashboard-clone.png) **Cloner**.
1. Une copie du tableau de bord, nommée « Clone de *nom de votre tableau de bord* » s’ouvre en mode d’édition. Suivez les étapes précédentes dans cet article pour renommer et personnaliser le tableau de bord.

## <a name="publish-and-share-a-dashboard"></a>Publier et partager un tableau de bord

Quand vous créez un tableau de bord, celui-ci est privé par défaut, ce qui signifie que vous êtes la seule personne à pouvoir le voir. Pour rendre des tableaux de bord visibles par d’autres que vous, partagez-les avec d’autres utilisateurs. Tout d’abord, vous devez publier le tableau de bord en tant que ressource Azure. Pour publier et partager un tableau de bord personnalisé, effectuez les étapes suivantes :

1. Sélectionnez ![icône de partage](./media/azure-portal-dashboards/dashboard-share-icon.png) **Partager** dans l’en-tête de page. Le formulaire **Partage + contrôle d’accès** apparaît.
1. Vérifiez que le nom de tableau de bord indiqué est correct.
1. Sélectionnez un abonnement dans **Nom de l’abonnement**. Les utilisateurs ayant accès à l’abonnement peuvent utiliser le tableau de bord partagé. L’accès aux ressources représentées par les différentes vignettes est déterminé par le contrôle d’accès en fonction du rôle Azure.
1. Cochez la case pour publier ce tableau de bord dans le groupe de ressources « tableaux de bord » pour l’abonnement sélectionné. Vous pouvez aussi décocher la case et choisir de le publier dans un groupe de ressources existant à la place.
1. Choisissez un emplacement pour la ressource du tableau de bord. Nous vous recommandons de placer le tableau de bord avec les autres ressources. Remarque : si vous choisissez un groupe de ressources parmi les groupes de ressources existants, le tableau de bord est automatiquement placé dans ce groupe de ressources.
1. Sélectionnez **Publier**.

   ![Capture d’écran de la boîte de dialogue permettant de publier un tableau de bord](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Définir le contrôle d’accès sur un tableau de bord partagé

Une fois le tableau de bord publié, déterminez quels utilisateurs auront accès au tableau de bord en suivant ces étapes :

1. Dans le volet **Partage + contrôle d’accès**, sélectionnez **Gérer les utilisateurs**.

   ![Capture d’écran de la boîte de dialogue Partage + contrôle d’accès du tableau de bord](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. La page **Contrôle d’accès** s’ouvre. Dans cette page, vous pouvez vérifier le niveau d’accès d’un utilisateur ou ajouter une nouvelle attribution de rôle. Quand vous ajoutez ici une attribution de rôle, vous accordez des autorisations d’accès au tableau de bord.

> [!NOTE]
> Les vignettes sont des vues représentant les ressources dans votre organisation. L’accès aux ressources est géré par l’attribution de contrôle d’accès en fonction du rôle, et les autorisations sont héritées de l’abonnement associé à la ressource. L’octroi de l’accès à un tableau de bord ne donne pas automatiquement des autorisations sur les ressources affichées dans le tableau de bord. Pour plus d’informations sur les autorisations sur les tableaux de bord partagés et le contrôle d’accès aux ressources en fonction du rôle, consultez [Partager des tableaux de bord Azure à l’aide du contrôle d’accès en fonction du rôle](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Ouvrir un tableau de bord partagé

Pour rechercher et ouvrir un tableau de bord partagé, effectuez les étapes suivantes :

1. Sélectionnez la liste déroulante à côté du nom du tableau de bord.
1. Sélectionnez un tableau de bord dans la liste des tableaux de bord disponibles ou sélectionnez **Parcourir tous les tableaux de bord** si le tableau de bord recherché n’est pas listé.

   ![Capture d’écran du menu de sélection du tableau de bord](./media/azure-portal-dashboards/dashboard-browse.png)

3. Dans le champ **Type**, sélectionnez **Tableaux de bord partagés**.
1. Sélectionnez un ou plusieurs abonnements. Vous pouvez également entrer du texte pour filtrer les tableaux de bord par nom.
1. Sélectionnez un tableau de bord dans la liste des tableaux de bord partagés.

## <a name="delete-a-dashboard"></a>Supprimer un tableau de bord

Pour supprimer définitivement un tableau de bord privé ou partagé, procédez comme suit :

1. Sélectionnez le tableau de bord à supprimer dans la liste déroulante à côté du nom de tableau de bord.
1. Sélectionnez ![icône de suppression](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Supprimer** dans l’en-tête de page.
1. S’il s’agit d’un tableau de bord privé, sélectionnez **OK** dans la boîte de dialogue de confirmation pour le supprimer. Si c’est un tableau de bord partagé, dans la boîte de dialogue de confirmation, cochez la case pour confirmer que le tableau de bord publié ne sera plus visible par d’autres utilisateurs. Ensuite, sélectionnez **OK**.

   ![Capture d’écran de la confirmation de la suppression](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Étapes suivantes

* [Partager des tableaux de bord à l’aide du contrôle d’accès en fonction du rôle](azure-portal-dashboard-share-access.md)
* [Créer par programmation des tableaux de bord Azure](azure-portal-dashboards-create-programmatically.md)
