---
title: Définir le délai d’inactivité au niveau du répertoire pour les utilisateurs du Portail Azure | Microsoft Docs
description: Les administrateurs peuvent appliquer la durée d’inactivité maximale avant qu’une session soit déconnectée. La stratégie de délai d’inactivité est définie au niveau du répertoire.
services: azure-portal
keywords: paramètres, délai d’expiration
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87de67eec9171825a1ebff9ab60782fea8836039
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310796"
---
# <a name="set-directory-level-inactivity-timeout"></a>Configurer le délai d’inactivité au niveau du répertoire

Le paramètre de délai d’inactivité vous permet de protéger vos ressources contre tout accès non autorisé si les utilisateurs oublient de sécuriser leur station de travail. Lorsqu’un utilisateur est resté inactif pendant un certain temps, sa session du Portail Azure se déconnecte automatiquement. Les administrateurs peuvent appliquer la durée d’inactivité maximale avant qu’une session soit déconnectée. Le paramètre de délai d’inactivité s’applique au niveau du répertoire. Pour plus d’informations sur les répertoires, voir [Présentation des services de domaine Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Configurer le paramètre du délai d’attente

Si vous êtes administrateur et que vous souhaitez appliquer un paramètre de délai d’inactivité pour tous les utilisateurs du Portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Sélectionnez **Paramètres** dans l’en-tête de page global.
3. Sélectionnez le texte du lien **Configurer le délai d’inactivité au niveau du répertoire**.

    ![Capture d’écran montrant les paramètres du portail avec mise en évidence du texte de lien](./media/admin-timeout/settings.png)

4. Une nouvelle page s’ouvre. Sur la page **Configurer le délai d’inactivité au niveau du répertoire**, sélectionnez **Activer le délai d’inactivité au niveau du répertoire pour le Portail Azure** pour activer le paramètre.
5. Ensuite, entrez les **heures** et les **minutes** correspondant à la durée maximale durant laquelle un utilisateur peut être inactif avant que sa session ne soit déconnectée automatiquement.
6. Sélectionnez **Appliquer**.

    ![Capture d’écran montrant la page permettant de définir un délai d’inactivité au niveau du répertoire](./media/admin-timeout/configure.png)

Pour confirmer que la stratégie de délai d’inactivité est correctement définie, sélectionnez **Notifications** à partir de l’en-tête de page globale. Vérifiez qu’une notification de réussite est indiquée.

  ![Capture d’écran montrant un message de notification de réussite pour le délai d’inactivité au niveau du répertoire](./media/admin-timeout/confirmation.png)

Le paramètre prend effet lors des nouvelles sessions. Il ne s’applique pas immédiatement à tous les utilisateurs qui sont déjà connectés.

> [!NOTE]
> Si un administrateur a configuré un paramètre de délai d’attente au niveau du répertoire, les utilisateurs peuvent remplacer la stratégie et définir leur propre durée de déconnexion inactive. Toutefois, l’utilisateur doit choisir un intervalle de temps inférieur à celui qui est défini au niveau du répertoire.
>

## <a name="next-steps"></a>Étapes suivantes

* [Définir vos préférences relatives au Portail Azure](set-preferences.md)
* [Exporter ou supprimer des paramètres utilisateur](azure-portal-export-delete-settings.md)
* [Activer le contraste élevé ou modifier le thème](azure-portal-change-theme-high-contrast.md)
