---
title: Définir vos préférences pour le portail Azure | Microsoft Docs
description: Vous pouvez changer les paramètres par défaut du portail Azure pour appliquer vos propres préférences. Les paramètres incluent le délai d’expiration des sessions inactives, la vue par défaut, le mode de menu, le contraste, le thème, les notifications et les formats de langue et de région
services: azure-portal
keywords: paramètres, délai d’expiration, langue, région
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: d4c675ab18a7a9231229ce0c7de9a7450dc1baa8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763956"
---
# <a name="set-your-azure-portal-preferences"></a>Définir vos préférences relatives au portail Azure

Vous pouvez changer les paramètres par défaut du portail Azure pour appliquer vos propres préférences. Chacun des paramètres listés ci-dessous peut être changer :

* [Délai d’expiration de session inactive](#change-the-idle-duration-for-inactive-sign-out)
* [Vue par défaut](#choose-your-default-view)
* [Mode de menu pour le portail](#choose-a-portal-menu-mode)
* [Couleur et thème à contraste élevé](#choose-a-theme)
* [Notifications contextuelles](#enable-or-disable-pop-up-notifications)
* [Langue et format de région](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Changer les paramètres généraux du portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Paramètres** dans l’en-tête de page global.

    ![Capture d’écran montrant les icônes de l’en-tête de page global avec mise en évidence des paramètres](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Changer la durée d’inactivité pour une déconnexion inactive

Le paramètre de délai d’inactivité vous permet de protéger les ressources contre tout accès non autorisé si vous oubliez de sécuriser votre station de travail. Une fois que vous êtes resté inactif pendant un certain temps, vous êtes déconnecté automatiquement de votre session du portail Azure.

Sélectionnez la liste déroulante sous **Me déconnecter lorsque je suis inactif**. Choisissez la durée au-delà de laquelle votre session du portail Azure est déconnectée si vous êtes inactif.

   ![Capture d’écran montrant les paramètres du portail avec mise en évidence des paramètres de délai d’inactivité](./media/set-preferences/inactive-signout-user.png)

La modification est enregistrée automatiquement. Si vous êtes inactif, votre session du portail Azure se déconnecte au terme de la durée que vous avez définie.

Ce paramétrage peut également être effectué par un administrateur au niveau du répertoire pour appliquer une durée d’inactivité maximale. Si un administrateur a défini un délai d’expiration au niveau du répertoire, vous pouvez toujours définir votre propre durée de déconnexion inactive. Choisissez une durée inférieure à celle qui est définie au niveau du répertoire.

Si votre administrateur a activé une stratégie de délai d’inactivité, cochez la case **Remplacer la stratégie de délai d’inactivité du répertoire**. Définissez un intervalle de temps inférieur au paramètre de stratégie.

   ![Capture d’écran montrant les paramètres du portail avec mise en évidence du paramètre Remplacer la stratégie de délai d’inactivité du répertoire](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Si vous êtes administrateur et que vous souhaitez appliquer un paramètre de délai d’inactivité pour tous les utilisateurs du portail Azure, consultez [Définir le délai d’inactivité au niveau du répertoire pour les utilisateurs du portail Azure](admin-timeout.md).
>

### <a name="choose-your-default-view"></a>Choisir votre vue par défaut 

Vous pouvez changer la page qui s’ouvre par défaut quand vous vous connectez au portail Azure.

   ![Capture d’écran montrant les paramètres du portail Azure avec mise en évidence de la vue par défaut](./media/set-preferences/default-view.png)

Le paramètre de vue par défaut contrôle la vue du portail Azure qui s’affiche quand vous vous connectez. Vous pouvez choisir d’ouvrir la page d’accueil Azure par défaut ou la vue Tableau de bord.

* La **page d’accueil** ne peut pas être personnalisée.  Elle affiche des raccourcis vers les services Azure populaires et liste les ressources que vous avez utilisées en dernier. Nous vous proposons également des liens utiles vers des ressources comme Microsoft Learn et la feuille de route Azure.
* Vous pouvez personnaliser les tableaux de bord pour créer un espace de travail conçu uniquement pour vous. Par exemple, vous pouvez créer un tableau de bord qui est axé sur un projet, une tâche ou un rôle. Si vous sélectionnez **Tableau de bord**, votre vue par défaut est celle du dernier tableau de bord utilisé.

### <a name="choose-a-portal-menu-mode"></a>Choisir un mode de menu pour le portail

Le mode par défaut pour le menu du portail contrôle l’espace occupé par le menu du portail dans la page.

* Quand le menu du portail est en mode **volant**, il est masqué jusqu’à ce que vous en ayez besoin. Sélectionnez l’icône de menu pour ouvrir ou fermer le menu.
* Si vous choisissez le mode **ancré** pour le menu du portail, il est toujours visible. Vous pouvez réduire le menu pour libérer davantage d’espace de travail. 

### <a name="choose-a-theme"></a>Choisir un thème

Le thème que vous choisissez affecte les couleurs d’arrière-plan et de police qui s’affichent dans le portail Azure. Vous pouvez sélectionner l’un des quatre thèmes de couleur prédéfinis. Sélectionnez chaque miniature pour trouver le thème qui vous convient le mieux.

   ![Capture d’écran montrant les paramètres du portail Azure avec mise en évidence des thèmes](./media/set-preferences/theme.png)

Vous pouvez choisir l’un des thèmes à contraste élevé à la place. Les paramètres de contraste élevé facilitent la lecture du portail Azure pour les utilisateurs malvoyants et remplacent toutes les autres sélections de thèmes. Pour plus d’informations, consultez [Activer le contraste élevé ou modifier le thème](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Activer ou désactiver les notifications contextuelles

Les notifications sont des messages système liés à votre session active. Elles fournissent des informations telles que votre solde de crédit, la disponibilité des ressources que vous venez de créer ou la confirmation de votre dernière action. Quand les notifications contextuelles sont activées, les messages s’affichent brièvement dans le coin supérieur de l’écran. 

Pour activer ou désactiver les notifications contextuelles, cochez ou décochez la case **Activer les notifications contextuelles**.

   ![Capture d’écran montrant les paramètres du portail Azure avec mise en évidence des notifications contextuelles](./media/set-preferences/popup-notifications.png)

Pour lire toutes les notifications reçues pendant votre session active, sélectionnez **Notifications** dans l’en-tête global.

   ![Capture d’écran montrant l’en-tête global du portail Azure avec mise en évidence des notifications](./media/set-preferences/read-notifications.png)

Si vous souhaitez lire les notifications liées à des sessions antérieures, recherchez les événements dans le journal d’activité. Pour plus d’informations, consultez [Afficher et récupérer les événements du journal d’activité Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Paramètres sous les liens utiles

Si vous avez apporté des modifications aux paramètres du portail Azure et que vous souhaitez les ignorer, sélectionnez **Restaurer les paramètres par défaut**. Toutes les modifications que vous avez apportées aux paramètres du portail seront perdues. Cette option n’affecte pas les personnalisations des tableaux de bord.

Pour plus d’informations sur **Exporter tous les paramètres** ou **Supprimer tous les paramètres et les tableaux de bord privés**, consultez [Exporter ou supprimer des paramètres utilisateur](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Changer la langue et les paramètres régionaux

Il existe deux paramètres qui contrôlent le mode d’affichage du texte du portail Azure. Le paramètre **Langue** contrôle la langue que vous voyez pour le texte dans le portail Azure. Le paramètre **Format de région** contrôle la manière dont les dates, les heures, les nombres et les devises sont affichés.

Pour changer la langue utilisée dans le portail Azure, utilisez la liste déroulante afin d’effectuer une sélection dans la liste des langues disponibles.

La sélection du format de région change afin d’afficher les options régionales uniquement pour la langue que vous avez sélectionnée. Pour changer cette sélection automatique, utilisez la liste déroulante afin de choisir le format de région souhaité.

Par exemple, si vous sélectionnez Anglais comme langue, puis que vous sélectionnez États-Unis comme format de région, la devise est indiquée en dollars américains. De même, si vous sélectionnez Anglais comme langue, puis que vous sélectionnez Europe comme format de région, la devise est indiquée en euros.

Sélectionnez **Appliquer** pour mettre à jour vos paramètres de langue et de format de région.

   ![Capture d’écran montrant les paramètres de langue et de format de région](./media/set-preferences/language.png)

>[!NOTE]
>Ces paramètres linguistiques et régionaux affectent uniquement le portail Azure. Les liens de documentation qui s’ouvrent dans un nouvel onglet ou une nouvelle fenêtre utilisent les paramètres linguistiques de votre navigateur pour déterminer la langue à afficher.
>

## <a name="next-steps"></a>Étapes suivantes

* [Créer et partager des tableaux de bord personnalisés](azure-portal-dashboards.md)
* [Série de vidéos de procédures sur le portail Azure](azure-portal-video-series.md)
