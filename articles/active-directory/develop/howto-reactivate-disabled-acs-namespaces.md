---
title: Réactiver des espaces de noms Azure Access Control Service (ACS) désactivés
description: Découvrez comment trouver et activer vos espaces de noms Azure Access Control Service (ACS) et demander une extension de leur activation jusqu'au 4 février 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 298afda7549690a9ea0314bff63a714be50a33b9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019784"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Comment : réactiver des espaces de noms Access Control Service désactivés

En novembre 2017, nous avons annoncé la mise hors service de Microsoft Azure Access Control Service (ACS), un service d’Azure Active Directory (Azure AD), le 7 novembre 2018.

Depuis, nous avons envoyé plusieurs e-mails aux administrateurs des abonnements ACS sur cette mise hors service : 12 mois, 9 mois, 6 mois, 3 mois, 1 mois, 2 semaines, 1 semaine et 1 jour avant la date de mise hors service du 7 novembre 2018.

Le 3 octobre 2018, nous avons annoncé (par e-mail et dans [un billet de blog](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) une offre d’extension pour les clients n'étant pas en mesure de terminer leur migration avant le 7 novembre 2018. Cette annonce contenait également les instructions relatives à la demande d'extension.

## <a name="why-your-namespace-is-disabled"></a>Pourquoi votre espace de noms est-il désactivé ?

Si vous n’avez pas opté pour l'extension, nous avons commencé à désactiver les espaces de noms ACS à partir du 7 novembre 2018. Si vous n'avez pas pris connaissance de différentes communications et souhaitez profiter de l'extension jusqu'au 4 février 2019, suivez les instructions des sections ci-dessous.

> [!NOTE]
> Vous devez être un administrateur de l'abonnement pour exécuter les commandes PowerShell et demander une extension.

## <a name="find-and-enable-your-acs-namespaces"></a>Rechercher et activer vos espaces de noms ACS

Vous pouvez utiliser ACS PowerShell pour répertorier tous vos espaces de noms ACS et réactiver ceux qui ont été désactivés.

1. Télécharger et installer ACS PowerShell :
    1. Accédez à PowerShell Gallery et téléchargez [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installer le module :

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Obtenez la liste de toutes les commandes possibles :

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Pour obtenir de l’aide sur une commande spécifique, exécutez la commande suivante :

        ```
        Get-Help [Command-Name] -Full
        ```
    
        où `[Command-Name]` est le nom de la commande ACS.
1. Obtenez la liste de vos abonnements Azure disponibles à l’aide de l’applet de commande **Get-AcsSubscription**.
1. Obtenez la liste de vos espaces de noms ACS en utilisant l’applet de commande **Get-AcsNamespace**.
1. Assurez-vous que les espaces de noms sont désactivés en vérifiant que `State` est `Disabled`.

    [![Vérifier que les espaces de noms sont désactivés](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Vous pouvez également utiliser `nslookup {your-namespace}.accesscontrol.windows.net` pour vérifier si le domaine est toujours actif.

1. Activez la liste de vos espaces de noms ACS en utilisant l’applet de commande **Enable-AcsNamespace**.

    Après avoir activé vos espaces de noms, vous pouvez demander une extension afin de veiller à ce qu'ils ne soient pas une nouvelle fois désactivés avant le 4 février 2019. Passée cette date, toutes les demandes vers ACS échoueront.

## <a name="request-an-extension"></a>Demander une extension

1. Accédez à votre portail de gestion des espaces de noms ACS via `https://{your-namespace}.accesscontrol.windows.net`.
1. Sélectionnez le bouton **Lire des conditions** pour accéder à une page relative aux [Conditions d'utilisation mises à jour](https://azure.microsoft.com/support/legal/access-control/).

    [![Sélectionner le bouton Lire les conditions](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Sélectionnez **Demander une extension** dans la bannière située en haut de la page. Le bouton est activé uniquement après que vous ayez lu les [Conditions d’utilisation mises à jour](https://azure.microsoft.com/support/legal/access-control/).

    [![Sélectionner le bouton Demander une extension](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. Une fois la demande d’extension enregistrée, la page est actualisée et une nouvelle bannière s'affiche.

    [![Page mise à jour avec bannière actualisée](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Aide et support

- Si vous rencontrez des problèmes après avoir suivi cette procédure, contactez le [support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Si vous avez des questions ou des commentaires sur la mise hors service d’ACS, contactez-nous à l’adresse acsfeedback@microsoft.com.

## <a name="next-steps"></a>Étapes suivantes

- Consultez les informations relatives à la mise hors service d'ACS dans [Comment : Effectuer une migration à partir d’Azure Access Control Service](active-directory-acs-migration.md).
