---
title: Attribuer des étiquettes de sensibilité à des groupes-Azure AD | Microsoft Docs
description: Comment créer des règles d’appartenance pour remplir automatiquement des groupes, et documentation de référence sur les règles.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/19/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07859299805c5f7be869350adbdbfa675775888c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404804"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Attribuer des étiquettes de sensibilité aux groupes Office 365 dans Azure Active Directory (préversion)

Azure Active Directory (Azure AD) prend en charge l’application d’étiquettes de sensibilité publiées par le [Centre de conformité Microsoft 365](https://sip.protection.office.com/homepage) dans les groupes Office 365. Les étiquettes de sensibilité s’appliquent au groupe parmi des services tels qu’Outlook, Microsoft Teams et SharePoint. Cette fonctionnalité est actuellement disponible en préversion publique.

> [!IMPORTANT]
> L’utilisation d’étiquettes de sensibilité Azure AD pour les groupes Office 365 requiert une licence Azure Active Directory Premium P1.

## <a name="group-settings-controlled-by-labels"></a>Paramètres de groupe contrôlés par des étiquettes

Deux paramètres peuvent être associés à une étiquette :

- **Confidentialité** : Les administrateurs peuvent associer un paramètre de confidentialité à l’étiquette pour déterminer si un groupe est public ou privé.
- **Accès invité** : Les administrateurs peuvent appliquer la stratégie invité pour tous les groupes auxquels l’étiquette est attribuée. Cette stratégie spécifie si les invités peuvent être ajoutés en tant que membres ou non. Si la stratégie d’invité est configurée pour une étiquette, tous les groupes auxquels vous attribuez l’étiquette n’autorisent pas la modification du paramètre AllowToAddGuests.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Activer la prise en charge des étiquettes de sensibilité dans PowerShell

Pour appliquer des étiquettes publiées à des groupes, vous devez d’abord activer la fonctionnalité. Ces étapes activent la fonctionnalité dans Azure AD.

1. Ouvrez une fenêtre Windows PowerShell sur votre ordinateur. Vous pouvez l’ouvrir sans privilèges élevés.
1. Exécutez les commandes suivantes pour préparer l’exécution des applets de commande.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Dans la page **Connectez-vous à votre compte**, entrez votre compte d’administrateur et votre mot de passe pour vous connecter à votre service, puis sélectionnez **Se connecter**.
1. Récupérez les paramètres de groupe actuels pour l’organisation Azure AD.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Si aucun paramètre de groupe n’a été créé pour cette organisation Azure AD, vous devez d’abord créer les paramètres. Suivez les étapes de [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) pour créer des paramètres de groupe pour cette organisation Azure AD.

1. Ensuite, affichez les paramètres de groupe actuels.

    ```PowerShell
    $Setting.Values
    ```

1. Activez ensuite la fonctionnalité :

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Ensuite, enregistrez les modifications et appliquez les paramètres :

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Vous avez terminé. Vous avez activé la fonctionnalité et vous pouvez appliquer des étiquettes publiées à des groupes.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Attribuer une étiquette à un nouveau groupe dans le Portail Azure

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com).
1. Sélectionnez **Groupes**, puis sélectionnez **Nouveau groupe**.
1. Dans la page **Nouveau groupe**, sélectionnez **Office 365**, puis remplissez les informations requises pour le nouveau groupe et sélectionnez une étiquette de sensibilité dans la liste.

   ![Attribuer une étiquette de sensibilité à la page Nouveaux groupes](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Enregistrez vos modifications, puis sélectionnez **Créer**.

Votre groupe est créé et les stratégies associées à l’étiquette sélectionnée sont ensuite automatiquement appliquées.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Attribuer une étiquette à un groupe existant dans le Portail Azure

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte administrateur général ou administrateur de groupes, ou en tant que propriétaire du groupe.
1. Sélectionnez **Groupes**.
1. Dans la page **Tous les groupes**, sélectionnez le groupe que vous souhaitez étiqueter.
1. Sur la page du groupe sélectionné, sélectionnez **Propriétés** et sélectionnez une étiquette de sensibilité dans la liste.

   ![Attribuer une étiquette de sensibilité à la page Vue d’ensemble d’un groupe](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Retirer une étiquette d’un groupe existant dans le Portail Azure

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte administrateur général ou administrateur de groupes, ou en tant que propriétaire du groupe.
1. Sélectionnez **Groupes**.
1. Dans la page **Tous les groupes**, sélectionnez le groupe duquel vous souhaitez supprimer l’étiquette.
1. Dans la page **Groupe**, sélectionnez **Propriétés**.
1. Sélectionnez **Supprimer**.
1. Sélectionnez **Enregistrer** pour enregistrer vos modifications.

## <a name="office-365-app-support-for-sensitivity-labels"></a>Prise en charge des applications Office 365 pour les étiquettes de sensibilité

Les applications et services Office 365 suivants prennent en charge les étiquettes de sensibilité dans cette préversion :

- Centre d’administration Azure AD
- Centre de conformité Microsoft 365
- SharePoint
- Outlook sur le web
- Teams
- Centre d’administration SharePoint

Pour plus d’informations sur la prise en charge des applications Office 365, consultez la [prise en charge d’Office 365 pour les étiquettes de sensibilité](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-new-sensitivity-labels).

## <a name="using-classic-azure-ad-classifications"></a>Utilisation des classifications Azure AD classiques

Une fois cette fonctionnalité activée, Office 365 ne prend plus en charge les classifications « classiques » pour les nouveaux groupes. Les classifications classiques sont les anciennes classifications que vous avez configurées en définissant des valeurs pour le paramètre `ClassificationList` dans Azure AD PowerShell. Lorsque cette fonctionnalité est activée, ces classifications ne sont pas appliquées aux groupes.

## <a name="troubleshooting-issues"></a>Résolution des problèmes

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Les étiquettes de sensibilité ne peuvent pas être attribuées à un groupe

L’option Étiquette de sensibilité s’affiche uniquement pour les groupes lorsque toutes les conditions suivantes sont remplies :

1. Les étiquettes sont publiées dans le centre de conformité Microsoft 365 pour ce locataire.
1. La fonctionnalité est activée, EnableMIPLabels possède la valeur true dans PowerShell.
1. Le groupe est un groupe Office 365.
1. Le locataire dispose d’une licence active Azure Active Directory Premium P1.
1. L’utilisateur actuellement connecté a accès aux étiquettes publiées.
1. L’utilisateur actuellement connecté dispose de privilèges suffisants pour attribuer des étiquettes. L’utilisateur doit être un administrateur général, un administrateur de groupe ou le propriétaire du groupe.
1. Une licence Office 365 est attribuer à l’utilisateur actuellement connecté. Pour plus d’informations sur les conditions requises pour les licences, consultez [Étiquettes de sensibilité dans les applications Office](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-office-apps).

Assurez-vous que toutes les conditions sont remplies pour attribuer des étiquettes à un groupe.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>L’étiquette que je souhaite attribuer ne figure pas dans la liste

Si l’étiquette que vous recherchez ne figure pas dans la liste, cela peut être le cas pour l’une des raisons suivantes :

- L’étiquette n’est peut-être pas publiée dans le centre de conformité Microsoft 365. Cela peut également s’appliquer aux étiquettes qui ne sont plus publiées. Pour plus d’informations, contactez votre administrateur.
- L’étiquette peut être publiée, mais elle n’est pas disponible pour l’utilisateur qui est connecté. Contactez votre administrateur pour plus d’informations sur la façon d’obtenir l’accès à l’étiquette.

### <a name="how-can-i-change-the-label-on-a-group"></a>Comment puis-je modifier l’étiquette d’un groupe ?

Les étiquettes peuvent être échangées à tout moment à l’aide des mêmes étapes que l’attribution d’une étiquette à un groupe existant, comme suit :

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte administrateur général ou administrateur de groupes, ou en tant que propriétaire du groupe.
1. Sélectionnez **Groupes**.
1. Dans la page **Tous les groupes**, sélectionnez le groupe que vous souhaitez étiqueter.
1. Dans la page du groupe sélectionné, sélectionnez **Propriétés** et sélectionnez une nouvelle étiquette de sensibilité dans la liste.
1. Sélectionnez **Enregistrer**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Les modifications apportées aux paramètres de groupe sur les étiquettes publiées ne sont pas mises à jour sur les groupes

Il est recommandé de ne pas modifier les paramètres de groupe pour une étiquette une fois l’étiquette appliquée aux groupes. Lorsque vous apportez des modifications aux paramètres de groupe associés aux étiquettes publiées dans le [Centre de conformité Microsoft 365](https://sip.protection.office.com/homepage), ces modifications de stratégie ne sont pas appliquées automatiquement sur les groupes concernés.

Si vous devez apporter une modification, utilisez un [script PowerShell Azure AD](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) pour appliquer manuellement les mises à jour aux groupes concernés. Cette méthode garantit que tous les groupes existants appliquent le nouveau paramètre.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des étiquettes de sensibilité avec Microsoft Teams, les groupes Office 365 et les sites SharePoint](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Mettre à jour les groupes après la modification de la stratégie d’étiquette manuellement avec un script PowerShell Azure AD](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Modifier vos paramètres de groupe](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Gérer des groupes au moyen de commandes PowerShell](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
