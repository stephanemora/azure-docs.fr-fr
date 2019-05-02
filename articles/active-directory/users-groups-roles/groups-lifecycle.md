---
title: Définir l’expiration des groupes Office 365 - Azure Active Directory | Microsoft Docs
description: Comment configurer l’expiration des groupes Office 365 dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1be88f0938a16302be4cf2308ba463900c067104
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920149"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurer la stratégie d’expiration pour les groupes Office 365

Vous pouvez désormais gérer le cycle de vie des groupes Office 365 en définissant une stratégie d’expiration pour ces derniers. Vous pouvez définir la stratégie d’expiration uniquement pour les groupes Office 365 d’Azure Active Directory (Azure AD). 

Une fois que vous avez défini l’expiration d’un groupe :

- Les propriétaires du groupe sont invités à renouveler le groupe à l’approche de l’expiration
- Les groupes non renouvelés sont supprimés
- Les groupes Office 365 qui sont supprimés peuvent être restaurés dans les 30 jours qui suivent par les propriétaires des groupes ou l’administrateur

Une seule stratégie d’expiration peut être configurée pour les groupes Office 365 sur un même locataire.

> [!NOTE]
> La configuration et l’utilisation de la stratégie d’expiration pour les groupes Office 365 nécessitent que vous disposiez de licences Azure AD Premium pour tous les membres des groupes auxquels la stratégie d’expiration est appliquée.

Pour plus d’informations sur le téléchargement et l’installation des applets de commande Azure AD PowerShell, consultez [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Rôles et autorisations

Voici les rôles pour lesquels vous pouvez configurer et utiliser l’expiration de groupes Office 365 dans Azure AD.

Rôle | Autorisations
-------- | --------
Administrateur général ou administrateur d’utilisateurs | Peut créer, lire, mettre à jour ou supprimer les paramètres de stratégie d’expiration de groupes Office 365<br>Peut renouveler n’importe quel groupe Office 365
Utilisateur | Peut renouveler un groupe Office 365 dont il est propriétaire<br>Peut restaurer un groupe Office 365 dont il est propriétaire<br>Peut lire les paramètres de stratégie d’expiration

Pour plus d’informations sur les autorisations nécessaires pour restaurer un groupe supprimé, consultez [Restaurer un groupe Office 365 supprimé dans Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Définir l’expiration d’un groupe

1. Ouvrez le [centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte qui est un administrateur général dans votre locataire Azure AD.

2. Sélectionnez **Groupes**, puis **Expiration**, pour ouvrir les paramètres d’expiration.
  
   ![Paramètres d’expiration pour les groupes](./media/groups-lifecycle/expiration-settings.png)

3. Dans le panneau **Expiration**, vous pouvez :

  * Définir la durée de vie du groupe en jours. Vous pouvez sélectionner l’une des valeurs prédéfinies ou une valeur personnalisée (31 jours ou plus). 
  * Spécifier une adresse de messagerie à laquelle les notifications de renouvellement et d’expiration doivent être envoyées lorsqu’un groupe n’a pas de propriétaire. 
  * Sélectionnez les groupes Office 365 qui expirent. Vous pouvez activer l’expiration pour **tous** les groupes Office 365, vous pouvez choisir d’activer les groupes Office 365 **sélectionnés** uniquement, ou vous pouvez sélectionner **Aucun** pour désactiver l’expiration de tous les groupes.
  * Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.

> [!NOTE]
> * Quand vous définissez l’expiration pour la première fois, les groupes qui sont plus anciens que l’intervalle d’expiration bénéficient d’un délai de 30 jours avant expiration. La première notification par e-mail est envoyée dans la journée qui suit. Par exemple, le groupe A a été créé il y a 400 jours et l’intervalle d’expiration est défini sur 180 jours. Lorsque vous appliquez les paramètres d’expiration, il reste 30 jours au groupe A avant qu’il ne soit supprimé, sauf si le propriétaire le renouvelle.
> * Lorsqu’un groupe dynamique est supprimé et restauré, il est considéré comme un groupe nouveau, complété conformément à la règle. Ce processus peut prendre jusqu’à 24 heures.

## <a name="email-notifications"></a>Notifications par e-mail

Les notifications par e-mail comme celle-ci sont envoyées aux propriétaires de groupes Office 365 30 jours, 15 jours et 1 jour avant l’expiration du groupe. La langue de l’e-mail est fonction de la langue préférée du propriétaire du groupe ou la langue du locataire. Si le propriétaire du groupe a défini une langue préférée ou si plusieurs propriétaires ont la même langue préférée, alors cette langue est utilisée. Dans tous les autres cas, la langue du locataire est utilisée.

![Notifications par courrier électronique de d’expiration](./media/groups-lifecycle/expiration-notification.png)

Dans l’e-mail de notification **Renouveler le groupe**, les propriétaires de groupes peuvent accéder directement à la page d’informations du groupe dans le volet d’accès. Dans cette page, les utilisateurs peuvent obtenir plus d’informations sur le groupe, comme sa description, la date de son dernier renouvellement, sa date d’expiration, ainsi que la possibilité de son renouvellement. La page d’informations du groupe comprend également des liens vers les ressources de groupe Office 365, pour que le propriétaire du groupe puisse voir facilement le contenu et les activités dans son groupe.

Quand un groupe expire, il est supprimé un jour après la date d’expiration. Une notification par e-mail comme celle-ci est envoyée aux propriétaires de groupes Office 365 pour les informer de l’expiration et de la suppression qui en découle de leur groupe Office 365.

![Notifications par courrier électronique de suppression groupe](./media/groups-lifecycle/deletion-notification.png)

Le groupe peut être restauré dans les 30 jours suivant sa suppression. Pour cela, vous pouvez sélectionner **Restaurer le groupe** ou utiliser les cmdlets PowerShell, comme décrit dans [Restaurer un groupe Office 365 supprimé dans Azure Active Directory](groups-restore-deleted.md). Veuillez noter que la période de restauration des groupes de 30 jours n’est pas personnalisable.
    
Si le groupe que vous restaurez contient des documents, des sites SharePoint ou d’autres objets persistants, la restauration du groupe et de son contenu peut nécessiter jusqu’à 24 heures.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Comment récupérer la date d’expiration du groupe Office 365
En plus du volet d’accès où les utilisateurs peuvent afficher les détails du groupe, y compris la date d’expiration et la dernière date de renouvellement, la date d’expiration d’un groupe Office 365 peut être récupérée à partir de la version bêta de Microsoft Graph REST API. expirationDateTime comme une propriété de groupe a été activée dans la version bêta de Microsoft Graph. Il peut être récupéré avec une demande GET. Pour plus d’informations, reportez-vous à [cet exemple](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Pour gérer les appartenances aux groupes dans le volet d’accès, « Restreindre l’accès à des groupes dans le volet d’accès » doit être définie sur « Non » dans le paramètre général Azure Active Directory groupes.


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Fonctionnement de l’expiration des groupes Office 365 avec une boîte aux lettres en conservation légale
Quand un groupe expire et est supprimé, les données du groupe issues des applications comme Planner, Sites ou Teams sont supprimées définitivement au bout de 30 jours, ce qui n’est pas le cas de la boîte aux lettres de groupe en conservation légale. L’administrateur peut utiliser les applets de commande Exchange pour restaurer la boîte aux lettres afin d’en extraire les données. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Fonctionnement de l’expiration des groupes Office 365 avec la stratégie de conservation
La stratégie de conservation est configurée dans le Centre de conformité et de sécurité. Si vous avez configuré une stratégie de conservation pour des groupes Office 365, lorsqu’un groupe expire et est supprimé, les conversations de groupe situées dans la boîte aux lettres de groupe et les fichiers situés dans le site de groupe sont conservés dans le conteneur de conservation pendant le nombre de jours défini dans la stratégie de conservation. Les utilisateurs ne verront pas le groupe et son contenu après son expiration. Toutefois, ils peuvent récupérer les données de site et de boîte aux lettres via découverte électronique.

## <a name="powershell-examples"></a>Exemples PowerShell
Voici des exemples d’utilisation des applets de commande PowerShell permettant de configurer les paramètres d’expiration des groupes Office 365 de votre locataire :

1. Installer le module de v2.0 PowerShell et connectez-vous à l’invite PowerShell :
   ```powershell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```
2. Configurez les paramètres d’expiration. New-AzureADMSGroupLifecyclePolicy :  cette applet de commande définit a durée de vie de tous les groupes Office 365 du locataire sur 365 jours. Les notifications de renouvellement pour les groupes Office 365 sans propriétaires sont envoyées à « emailaddress@contoso.com ».
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. Récupérez la stratégie existante. Get-AzureADMSGroupLifecyclePolicy : cette applet de commande récupère les paramètres d’expiration actuels du groupe Office 365 qui ont été configurés. Dans cet exemple, vous pouvez voir ce qui suit :
   * L’ID de la stratégie 
   * La durée de vie de tous les groupes Office 365 du locataire est définie sur 365 jours
   * Les notifications de renouvellement pour les groupes Office 365 sans propriétaires sont envoyées à « emailaddress@contoso.com ».
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. Mettez à jour la stratégie existante. Set-AzureADMSGroupLifecyclePolicy : cette applet de commande sert à mettre à jour une stratégie existante. Dans l’exemple ci-dessous, la durée de vie du groupe dans la stratégie existante est passée de 365 jours à 180 jours. 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. Ajoutez des groupes spécifiques à la stratégie. Add-AzureADMSLifecyclePolicyGroup : cette applet de commande ajoute un groupe à la stratégie du cycle de vie. Par exemple :
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. Supprimez la stratégie existante. Remove-AzureADMSGroupLifecyclePolicy : cette applet de commande supprime les paramètres d’expiration du groupe Office 365, mais nécessite l’ID de stratégie. Cela va désactiver l’expiration des groupes Office 365. 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Les applets de commande suivantes peuvent être utilisées pour configurer la stratégie plus en détail. Pour plus d’informations, consultez [documentation PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes Azure AD.

* [Consulter les groupes existants](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](groups-dynamic-membership.md)
