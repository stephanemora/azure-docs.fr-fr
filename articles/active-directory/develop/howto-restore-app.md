---
title: Guide pratique pour restaurer ou supprimer une application récemment supprimée avec la plateforme d’identités Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Dans ce guide pratique, vous allez découvrir comment restaurer ou supprimer définitivement une application récemment supprimée inscrite auprès de la plateforme d’identités Microsoft.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079723"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Restaurer ou supprimer une application récemment supprimée avec la plateforme d’identités Microsoft
Lorsque vous supprimez une inscription d’application, l’application reste à l’état suspendu pendant 30 jours. Pendant ces 30 jours, l’inscription d’application peut être restaurée avec l’ensemble de ses propriétés. Une fois cette période de 30 jours écoulée, les inscriptions d’applications ne peuvent plus être restaurées, et le processus de suppression permanente peut être démarré automatiquement.  Cette fonctionnalité s’applique uniquement aux applications associées à un annuaire.  Elle n’est pas disponible pour les applications d’un compte Microsoft personnel, qui ne peuvent pas être restaurées.

Vous pouvez afficher vos applications supprimées, restaurer une application supprimée, ou supprimer définitivement une application à l’aide de l’expérience d’inscriptions d’applications sous Azure Active Directory (Azure AD) dans le portail Azure.

Notez que ni vous ni le support technique Microsoft ne pouvez restaurer une application définitivement supprimée ou une application supprimée il y a plus de 30 jours.

> [!IMPORTANT]
> La fonctionnalité d’interface utilisateur du portail des applications supprimées [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Autorisations requises
Vous devez disposer de l’un des rôles suivants pour supprimer définitivement des applications.

- Administrateur général
- Administrateur d’application
- Administrateur d’application cloud
- Administrateur d’identité hybride
- Propriétaire de l’application

Vous devez disposer de l’un des rôles suivants pour restaurer des applications.

- Administrateur général
- Propriétaire de l’application

### <a name="view-your-deleted-applications"></a>Afficher les applications supprimées
Vous pouvez voir toutes les applications qui sont à l’état de suppression réversible.  Seules les applications supprimées il y a moins de 30 jours peuvent être restaurées.

#### <a name="to-view-your-restorable-applications"></a>Pour afficher les applications pouvant être restaurées
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Recherchez et sélectionnez **Azure Active Directory**, sélectionnez **Inscriptions d’applications**, puis sélectionnez l’onglet **Applications supprimées (préversion)** .

Passez en revue la liste d’applications. Seules les applications qui ont été supprimées au cours des 30 derniers jours sont disponibles pour la restauration. Si vous utilisez l’aperçu de la recherche Inscriptions d’applications, vous pouvez filtrer sur la colonne « Date de suppression » pour afficher uniquement ces applications.

## <a name="restore-a-recently-deleted-application"></a>Restaurer une application supprimée récemment

Lorsqu’une inscription d’application est supprimée de l’organisation, l’application est à l’état suspendu et ses configurations sont conservées. Lorsque vous restaurez une inscription d’application, ses configurations sont également restaurées.  Toutefois, s’il existait des paramètres propres à l’organisation dans **Applications d’entreprise** pour le locataire d’accueil de l’application, ils ne seront pas restaurés.  

Cela est dû au fait que les paramètres propres à l’organisation sont stockés dans un objet distinct, appelé principal de service.  Les paramètres contenus dans le principal de service incluent les consentements d’autorisation et les attributions d’utilisateurs et de groupes pour une certaine organisation. Ces configurations ne seront pas restaurées lors de la restauration de l’application. Pour plus d’informations, consultez [Objets application et principal du service](app-objects-and-service-principals.md). 


### <a name="to-restore-an-application"></a>Pour restaurer une application
1. Sous l’onglet **Applications supprimées (préversion)** , recherchez et sélectionnez l’une des applications supprimées il y a moins de 30 jours.
2. Sélectionnez **Restaurer l’inscription d’application**.

## <a name="permanently-delete-an-application"></a>Supprimer définitivement une application
Vous pouvez supprimer définitivement une application de votre organisation manuellement. Une application supprimée définitivement ne peut pas être restaurée (ni par vous, ni par un autre administrateur, ni par le support technique de Microsoft).

### <a name="to-permanently-delete-an-application"></a>Pour supprimer définitivement une application

1. Sous l’onglet **Applications supprimées (préversion)** , recherchez et sélectionnez l’une des applications disponibles.
2. Sélectionnez **Supprimer définitivement**.
3. Lisez le texte d’avertissement et sélectionnez **Oui**.

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez restauré ou supprimé définitivement votre application, vous pouvez :

- [Ajouter une application](quickstart-register-app.md).
- Apprenez-en davantage sur les [objets d’application et de principal de service](app-objects-and-service-principals.md) dans la plateforme d’identités Microsoft.
