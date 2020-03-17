---
title: Définir le délai d’inactivité au niveau du répertoire pour les utilisateurs du Portail Azure | Microsoft Docs
description: Les administrateurs peuvent appliquer la durée d’inactivité maximale avant qu’une session soit déconnectée. La stratégie de délai d’inactivité est définie au niveau du répertoire.
services: azure-portal
keywords: paramètres, délai d’expiration
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096610"
---
# <a name="set-directory-level-inactivity-timeout"></a>Configurer le délai d’inactivité au niveau du répertoire

Le paramètre de délai d’inactivité vous permet de protéger vos ressources contre tout accès non autorisé si les utilisateurs oublient de sécuriser leur station de travail. Lorsqu’un utilisateur est resté inactif pendant un certain temps, sa session du Portail Azure se déconnecte automatiquement. Les administrateurs dans le [rôle administrateur général](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) peuvent appliquer la durée d’inactivité maximale avant qu’une session soit déconnectée. Le paramètre de délai d’inactivité s’applique au niveau du répertoire. Pour plus d’informations sur les répertoires, voir [Présentation des services de domaine Active Directory](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Configurer le paramètre du délai d’attente

Si vous êtes administrateur général et que vous voulez appliquer un paramètre de délai d’inactivité pour tous les utilisateurs du portail Azure, effectuez ces étapes :

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
> Si un administrateur général a configuré un paramètre de délai d’attente au niveau de l’annuaire, les utilisateurs peuvent remplacer la stratégie et définir leur propre durée de déconnexion inactive. Toutefois, l’utilisateur doit choisir un intervalle de temps inférieur à celui défini au niveau de l’annuaire par l’administrateur général.
>

## <a name="next-steps"></a>Étapes suivantes

* [Définir vos préférences relatives au Portail Azure](set-preferences.md)
* [Exporter ou supprimer des paramètres utilisateur](azure-portal-export-delete-settings.md)
* [Activer le contraste élevé ou modifier le thème](azure-portal-change-theme-high-contrast.md)
