---
title: Gérer les paramètres et les préférences du portail Azure
description: Modifiez les paramètres du portail Azure tels que l’abonnement/le répertoire par défaut, les délais d’expiration, le mode de menu, le contraste, le thème, les notifications, la langue/région, et bien d’autres encore.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 285afaaf51f28b0fa53a9df2a9fa232b01d3fe24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524826"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Gérer les paramètres et les préférences du portail Azure

Vous pouvez changer les paramètres par défaut du portail Azure pour appliquer vos propres préférences.

La plupart des paramètres sont disponibles dans le menu **Paramètres** dans la section en haut à droite de l’en-tête de page global.

:::image type="content" source="media/set-preferences/settings-top-header.png" alt-text="Capture d’écran montrant l’icône des paramètres dans l’en-tête de page global.":::

> [!NOTE]
> Nous déplaçons actuellement tous les utilisateurs vers la dernière expérience des paramètres décrite dans cette rubrique. Pour plus d’informations sur l’ancienne expérience, consultez [Gérer les paramètres et les préférences du portail Azure (ancienne version)](original-preferences.md).

## <a name="directories--subscriptions"></a>Répertoires et abonnements

La page **Répertoires et abonnements**  vous permet de gérer les répertoires et de définir des filtres d’abonnement.

### <a name="switch-and-manage-directories"></a>Changer de répertoire et gérer les répertoires

Dans la section **Répertoires**, vous voyez votre **Répertoire actuel** (auquel vous êtes actuellement connecté).

**Répertoire de démarrage** affiche le répertoire par défaut lorsque vous vous connectez au portail Azure. Pour choisir un autre répertoire de démarrage, sélectionnez **Changer** pour accéder à la page [Apparence et affichages au démarrage](#appearance--startup-views) dans laquelle vous pouvez changer cette option.

Pour afficher la liste complète des répertoires auxquels vous avez accès, sélectionnez **Tous les répertoires**.

Pour marquer un répertoire en tant que favori, sélectionnez son icône en forme d’étoile. Ces répertoires sont répertoriés dans la section **Favoris**.

Pour basculer vers un autre répertoire, sélectionnez le répertoire dans lequel vous souhaitez travailler, puis sélectionnez le bouton **Changer** dans sa ligne.

:::image type="content" source="media/set-preferences/settings-directories-subscriptions-default-filter.png" alt-text="Capture d’écran montrant le volet des paramètres Répertoires.":::

### <a name="subscription-filters"></a>Filtres d’abonnement

Vous pouvez modifier les abonnements qui sont filtrés par défaut lorsque vous vous connectez au portail Azure. Cela peut être utile si vous avez une liste principale d’abonnements avec lesquels vous travaillez, mais que vous en utilisez parfois d’autres.

Pour utiliser des filtres personnalisés, sélectionnez **Filtres avancés**. Vous êtes invité à confirmer avant de continuer.

:::image type="content" source="media/set-preferences/settings-advanced-filters-enable.png" alt-text="Capture d’écran montrant la boîte de dialogue de confirmation pour Filtres avancés.":::

La page **Filtres avancés** est activée, dans laquelle vous pouvez créer et gérer plusieurs filtres d’abonnement. Les abonnements actuellement sélectionnés sont enregistrés en tant que filtre importé que vous pouvez réutiliser. Si vous souhaitez cesser d’utiliser des filtres avancés, sélectionnez à nouveau le bouton bascule pour restaurer l’affichage d’abonnement par défaut. Les filtres personnalisés que vous avez créés sont enregistrés et peuvent être utilisés si vous activez **Filtres avancés** ultérieurement.

:::image type="content" source="media/set-preferences/settings-advanced-filters-disable.png" alt-text="Capture d’écran montrant la boîte de dialogue de confirmation de désactivation des Filtres avancés.":::

## <a name="advanced-filters"></a>Filtres avancés

Sur la page **Filtres avancés**, vous pouvez créer, modifier ou supprimer des filtres d’abonnement.

:::image type="content" source="media/set-preferences/settings-advanced-filters.png" alt-text="Capture d’écran montrant l’écran Filtres avancés.":::

Le filtre **Par défaut** affiche tous les abonnements auxquels vous avez accès. Ce filtre est utilisé s’il n’y a pas d’autres filtres, ou lorsque le filtre actif ne peut pas inclure d’abonnements.

Vous pouvez également voir un filtre nommé **Imported-filter**, qui inclut tous les abonnements qui ont été sélectionnés auparavant.

Pour changer le filtre utilisé actuellement, sélectionnez-le dans la zone déroulante **Filtre avancé**. Vous pouvez également sélectionner **Modifier les filtres avancés** pour accéder à la page **Filtres avancés** dans laquelle vous pouvez créer, modifier et supprimer vos filtres.

### <a name="create-a-filter"></a>Créer un filtre

Pour créer un nouveau filtre, sélectionnez **Créer un filtre**. Vous pouvez créer jusqu’à dix filtres.

Chaque filtre doit avoir un nom unique comprenant entre 8 et 50 caractères et il ne doit contenir que des lettres, des chiffres et des traits d’Union.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create.png" alt-text="Capture d’écran montrant les options Créer un filtre.":::

Après avoir nommé votre filtre, entrez au moins une condition. Dans le champ **Type de filtre**, sélectionnez **Nom de l’abonnement**, **Identifiant d’abonnement** ou **État de l’abonnement**. Sélectionnez ensuite un opérateur et entrez une valeur sur laquelle filtrer.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create-operators.png" alt-text="Capture d’écran montrant la liste des opérateurs pour la création de filtres.":::

Lorsque vous avez terminé d’ajouter des conditions, sélectionnez **Créer**. Votre filtre s’affiche alors dans la liste des **Filtres actifs**.

### <a name="modify-or-delete-a-filter"></a>Modifier ou supprimer un filtre

Vous pouvez modifier ou renommer un filtre existant en sélectionnant l’icône du crayon dans la ligne de ce filtre. Apportez vos modifications, puis sélectionnez **Appliquer**.

> [!NOTE]
> Si vous modifiez un filtre qui est actuellement actif et que les modifications ont pour résultat 0 abonnements, le filtre **Par défaut** devient actif à la place. Vous ne pouvez pas activer un filtre qui n’inclut aucun abonnement.

Pour supprimer un filtre, sélectionnez l’icône de la corbeille dans la ligne de ce filtre. Vous ne pouvez pas supprimer le filtre **Par défaut** ni aucun filtre actuellement actif.

## <a name="appearance--startup-views"></a>Apparence et affichages au démarrage

Le volet **Apparence et affichages au démarrage** comporte deux sections. La section **Apparence** vous permet de choisir le comportement du menu, la couleur de votre thème et l’utilisation d’un thème à contraste élevé. La section **Affichages au démarrage** vous permet de définir des options sur ce qui est affiché lors de la première connexion au portail Azure.

:::image type="content" source="media/set-preferences/azure-portal-settings-appearance.png" alt-text="Capture d’écran montrant la section Apparence de Apparence et affichages au démarrage.":::

### <a name="set-menu-behavior"></a>Définir le comportement du menu

La section **Comportement du menu** vous permet de choisir le comportement du menu par défaut du portail Azure.

- **Menu volant** : le menu est masqué tant que vous n’en avez pas besoin. Vous pouvez sélectionner l’icône du menu dans le coin supérieur gauche pour ouvrir ou fermer le menu.
- **Ancré** : le menu est toujours visible. Vous pouvez réduire le menu pour libérer davantage d’espace de travail.

### <a name="choose-a-theme-or-enable-high-contrast"></a>Choisir un thème ou activer le contraste élevé

Le thème que vous choisissez affecte les couleurs d’arrière-plan et de police qui s’affichent dans le portail Azure. Dans la section **Thème**, vous pouvez sélectionner l’un des quatre thèmes de couleur prédéfinis. Sélectionnez chaque miniature pour trouver le thème qui vous convient le mieux.

Vous pouvez également choisir un thème dans la section **Thème à contraste élevé**. Ces thèmes peuvent rendre le portail Azure plus facile à lire, en particulier si vous avez un handicap visuel. La sélection du thème blanc ou noir à contraste élevé remplace toutes les autres sélections de thèmes.

### <a name="startup-page"></a>Page de démarrage

Choisissez l’une des options suivantes pour la page qui s’affiche lorsque vous vous connectez pour la première fois au portail Azure.

- **Accueil** : affiche la page d’accueil, avec des raccourcis vers les services Azure populaires, une liste des ressources que vous avez récemment utilisées et des liens utiles vers des outils, de la documentation et bien plus encore.
- **Tableau de bord** : affiche le dernier tableau de bord utilisé. Vous pouvez personnaliser les tableaux de bord pour créer un espace de travail conçu uniquement pour vous. Par exemple, vous pouvez créer un tableau de bord qui est axé sur un projet, une tâche ou un rôle. Pour plus d’informations, consultez [Créer et partager des tableaux de bord dans le portail Azure](azure-portal-dashboards.md).

### <a name="startup-directory"></a>Répertoire de démarrage

Choisissez l’une des options suivantes pour le répertoire à utiliser lorsque vous vous connectez pour la première fois au portail Azure.

- **Se connecter au dernier répertoire visité** : lorsque vous vous connectez au portail Azure, vous démarrez dans le répertoire que vous avez utilisiez la dernière fois.
- **Sélectionner un répertoire** : choisissez cette option pour sélectionner l’un de vos répertoires. Vous démarrez dans ce répertoire chaque fois que vous vous connectez au portail Azure, même si vous avez travaillé dans un autre répertoire la dernière fois.

:::image type="content" source="media/set-preferences/azure-portal-settings-startup-views.png" alt-text="Capture d’écran montrant la section Démarrage de Apparence et affichages au démarrage.":::

## <a name="language--region"></a>Langue et région

Choisissez votre langue et le format régional qui influenceront la manière dont les données telles que les dates et la devise apparaîtront dans le portail Azure.

:::image type="content" source="media/set-preferences/azure-portal-settings-language-region.png" alt-text="Capture d’écran montrant le volet des paramètres de Langue et région.":::

> [!NOTE]
> Ces paramètres linguistiques et régionaux affectent uniquement le portail Azure. Les liens de documentation qui s’ouvrent dans un nouvel onglet ou une nouvelle fenêtre utilisent les paramètres de votre navigateur pour déterminer la langue à afficher.

### <a name="language"></a>Langage

Utilisez la liste déroulante pour sélectionner dans la liste des langues disponibles. Ce paramètre contrôle la langue que vous voyez pour le texte dans le portail Azure.

### <a name="regional-format"></a>Format régional

Sélectionnez une option pour contrôler le mode d’affichage des dates, des heures, des nombres et de la devise dans le portail Azure.

Les options affichées dans la liste déroulante **Format régional** changent en fonction de l’option que vous sélectionnée pour **Langue**. Par exemple, si vous sélectionnez **Anglais** comme langue, puis que vous sélectionnez **Anglais (États-Unis)** comme format régional, la devise est indiquée en dollars américains. De même, si vous sélectionnez **Anglais** comme langue, puis que vous sélectionnez **Anglais (Europe)** comme format régional, la devise est indiquée en euros.

Sélectionnez **Appliquer** pour mettre à jour vos paramètres de langue et de format de région.

## <a name="my-information"></a>Mes informations

La page **Mes informations** vous permet de mettre à jour l’adresse e-mail utilisée pour les mises à jour sur les services Azure, la facturation, le support technique ou les problèmes de sécurité. Vous pouvez également vous abonner à d’autres e-mails relatifs à Microsoft Azure et à d’autres produits et services.

Vers le haut de la page **Mes informations**, vous voyez des options pour exporter, restaurer ou supprimer des paramètres.

:::image type="content" source="media/set-preferences/settings-my-information.png" alt-text="Capture d’écran de la page de paramètres Mes informations.":::

### <a name="export-user-settings"></a>Exporter les paramètres utilisateur

Les informations sur vos paramètres personnalisés sont stockées dans Azure. Vous pouvez exporter les données utilisateur suivantes :

- Les tableaux de bord privés sur le portail Azure
- Les paramètres utilisateur, comme les abonnements ou les répertoires favoris
- Les thèmes et autres paramètres personnalisés du portail

Nous vous recommandons d’exporter et de vérifier vos paramètres si vous prévoyez de les supprimer. Le recréation des tableaux de bord ou des paramètres peut prendre du temps.

Pour exporter vos paramètres du portail, sélectionnez **Exporter les paramètres** en haut du volet **Vue d’ensemble** des paramètres. Un fichier *.json* qui contient vos données de paramètres utilisateur est créé.

En raison de la nature dynamique des paramètres utilisateur et des risques d’altération des données, vous ne pouvez pas importer de paramètres à partir du fichier *.json*.

### <a name="restore-default-settings"></a>Restaurer les paramètres par défaut

Si vous avez apporté des modifications aux paramètres du portail Azure et que vous souhaitez les ignorer, sélectionnez **Restaurer les paramètres par défaut** en haut du volet **Vue d’ensemble** des paramètres. Vous êtes invité à confirmer cette action. Dans ce cas, toutes les modifications que vous avez apportées aux paramètres de votre portail Azure seront perdues. Cette option n’affecte pas les personnalisations des tableaux de bord.

### <a name="delete-user-settings-and-dashboards"></a>Supprimer les paramètres utilisateur et les tableaux de bord

Les informations sur vos paramètres personnalisés sont stockées dans Azure. Vous pouvez supprimer les données utilisateur suivantes :

- Les tableaux de bord privés sur le portail Azure
- Les paramètres utilisateur, comme les abonnements ou les répertoires favoris
- Les thèmes et autres paramètres personnalisés du portail

Nous vous recommandons d’exporter et de vérifier vos paramètres avant de les supprimer. En effet, la recréation des [tableaux de bord](azure-portal-dashboards.md) ou des paramètres personnalisés peut prendre du temps.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Pour supprimer les paramètres de votre portail, sélectionnez **Supprimer tous les paramètres et les tableaux de bord privés** en haut de la page **Mes informations**. Vous êtes invité à confirmer la suppression. Dans ce cas, toutes les personnalisations de paramètres reprendront les paramètres par défaut, et tous vos tableaux de bord privés seront perdus.

## <a name="signing-out--notifications"></a>Déconnexion et notifications

Ce volet vous permet de gérer les notifications contextuelles et les délais d’expiration de session.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-notifications.png" alt-text="Capture d’écran montrant le volet Déconnexion et notifications.":::

### <a name="signing-out"></a>Déconnexion

Le paramètre de délai d’inactivité vous permet de protéger les ressources contre tout accès non autorisé si vous oubliez de sécuriser votre station de travail. Une fois que vous êtes resté inactif pendant un certain temps, vous êtes déconnecté automatiquement de votre session du portail Azure. En tant qu’individu, vous pouvez modifier le paramètre de délai d’attente pour vous-même. Si vous êtes un administrateur, vous pouvez le définir au niveau de l’annuaire pour tous vos utilisateurs dans le répertoire.

### <a name="change-your-individual-timeout-setting-user"></a>Modifier le paramètre de délai d’attente individuel (utilisateur)

Dans le menu déroulant en regard de **Me déconnecter lorsque je suis inactif**, choisissez la durée au-delà de laquelle votre session de portail Azure est déconnectée si vous êtes inactif.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive.png" alt-text="Capture d’écran montrant l’option des paramètres de délai d’expiration utilisateur.":::

Sélectionnez **Appliquer** pour enregistrer vos modifications. Après cela, si vous êtes inactif pendant la session du portail, le portail Azure est déconnecté après la durée que vous avez définie.

Si votre administrateur a activé une stratégie de délai d’expiration d’inactivité, vous pouvez toujours définir votre propre stratégie, à condition qu’elle soit plus courte que le paramètre au niveau du répertoire. Pour ce faire, sélectionnez **Remplacer la stratégie de délai d’inactivité du répertoire**, puis définissez un intervalle de temps pour la **Valeur de remplacement**.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-user.png" alt-text="Capture d’écran montrant le paramètre de remplacement du délai d’inactivité de répertoire.":::

### <a name="change-the-directory-timeout-setting-admin"></a>Modifier le paramètre de délai d’attente du répertoire (admin)

Les administrateurs dans le [rôle administrateur général](../active-directory/roles/permissions-reference.md#global-administrator) peuvent appliquer la durée d’inactivité maximale avant qu’une session soit déconnectée. Ce paramètre de délai d’inactivité s’applique au niveau du répertoire. Le paramètre prend effet lors des nouvelles sessions. Il ne s’applique pas immédiatement à tous les utilisateurs qui sont déjà connectés. Pour plus d’informations sur les répertoires, voir [Présentation des services de domaine Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Si vous êtes administrateur général et que vous voulez appliquer un paramètre de délai d’inactivité pour tous les utilisateurs du portail Azure, sélectionnez **Activer le délai d’inactivité du niveau du répertoire** pour désactiver le paramètre. Ensuite, entrez les **Heures** et les **Minutes** pour la durée maximale d’inactivité d’un utilisateur avant que sa session ne soit automatiquement déconnectée. Lorsque vous sélectionnez **Applique**, ce paramètre s’applique à tous les utilisateurs dans le répertoire.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-admin.png" alt-text="Capture d’écran montrant les options de délai d’inactivité du niveau de répertoire.":::

Pour confirmer que la stratégie de délai d’inactivité est correctement définie, sélectionnez **Notifications** à partir de l’en-tête de page global et vérifiez qu’une notification de réussite s’affiche.

:::image type="content" source="media/set-preferences/confirmation.png" alt-text="Capture d’écran montrant une notification de réussite de la stratégie de délai d’inactivité.":::

### <a name="enable-or-disable-pop-up-notifications"></a>Activer ou désactiver les notifications contextuelles

Les notifications sont des messages système liés à votre session active. Elles fournissent des informations telles que l’indication de votre solde de crédit actuel, la confirmation de votre dernière action ou le moment où les ressources que vous avez créées sont disponibles. Quand les notifications contextuelles sont activées, les messages s’affichent brièvement dans le coin supérieur de l’écran.

Pour activer ou désactiver les notifications contextuelles, cochez ou décochez **Activer les notifications contextuelles**.

Pour lire toutes les notifications reçues pendant votre session active, sélectionnez **Notifications** dans l’en-tête global.

:::image type="content" source="media/set-preferences/read-notifications.png" alt-text="Capture d’écran montrant l’icône des notifications dans l’en-tête global.":::

Pour afficher les notifications liées à des sessions antérieures, recherchez les événements dans le journal d’activité. Pour plus d’informations, consultez [Voir le journal d’activité](../azure-monitor/essentials/activity-log.md#view-the-activity-log).

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les raccourcis clavier dans le portail Azure](azure-portal-keyboard-shortcuts.md)
- [Afficher les navigateurs et appareils pris en charge](azure-portal-supported-browsers-devices.md)
- [Ajouter, supprimer et réorganiser des favoris](azure-portal-add-remove-sort-favorites.md)
- [Créer et partager des tableaux de bord personnalisés](azure-portal-dashboards.md)
- [Regarder des vidéos de procédures du portail Azure](azure-portal-video-series.md)
