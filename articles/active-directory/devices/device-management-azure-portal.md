---
title: Guide pratique pour la gestion des appareils dans le portail Azure | Microsoft Docs
description: Découvrez comment utiliser le portail Azure pour gérer les appareils.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb9bc0adeaff8fa6e0f0298782d6f3fca35058cf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87025963"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gérer les identités de l’appareil à l’aide du portail Microsoft Azure

La fonction de gestion des identités des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que vos utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité.

Cet article :

- Suppose que vous avez lu la [Présentation de la gestion des identités des appareils dans Azure Active Directory](overview.md)
- Fournit des informations sur la gestion des identités des appareils avec le portail Azure AD

![Vue Tous les appareils dans le portail Azure](./media/device-management-azure-portal/all-devices-azure-portal.png)

## <a name="manage-device-identities"></a>Gérer les identités des appareils

Le portail Azure AD centralise les opérations de gestion des identités de vos appareils. Pour accéder à cet emplacement, utilisez un [lien direct](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) ou :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Appareils**.

La page **Appareils** vous permet :

- Configurer les paramètres de vos appareils
- Localiser les appareils
- Effectuer les tâches de gestion des identités relatives aux appareils
- Examiner les journaux d’audit liés à l’appareil  
  
## <a name="configure-device-settings"></a>Configurer les paramètres de l’appareil

Pour gérer les identités de vos appareils avec le portail Azure AD, vous devez [les inscrire ou les joindre](overview.md) à Azure AD. En tant qu’administrateur, vous pouvez affiner le processus d’inscription et de jonction des appareils en configurant les paramètres de l’appareil.

La page des paramètres de l’appareil vous permet de configurer les paramètres relatifs aux identités des appareils :

![Paramètres d’appareil associés à Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Les utilisateurs peuvent joindre des appareils à Azure AD** : ce paramètre vous permet de sélectionner les utilisateurs qui peuvent inscrire leurs appareils en tant qu’appareils joints à Azure AD. La valeur par défaut est **Tous**.

> [!TIP]
> Le paramètre **Les utilisateurs peuvent joindre des appareils à Azure AD** s’applique uniquement à Azure AD Join sous Windows 10.

- **Administrateurs locaux supplémentaires sur les appareils joints à Azure AD** : vous pouvez sélectionner les utilisateurs qui peuvent disposer de droits d’administrateur local sur un appareil. Les utilisateurs ajoutés ici sont ajoutés au rôle *Administrateurs d’appareils* dans Azure AD. Les administrateurs généraux Azure AD et les propriétaires d’appareils bénéficient de droits d’administrateur local par défaut. Cette option est une fonctionnalité de l’édition Premium disponible dans les produits comme Azure AD Premium ou EMS (Enterprise Mobility Suite).
- **Les utilisateurs peuvent inscrire leurs appareils sur Azure AD** : vous devez configurer ce paramètre pour permettre l’inscription des appareils Windows 10 Personnel, iOS, Android et macOS dans Azure AD. Si vous sélectionnez **Aucun**, les appareils ne peuvent pas être inscrits dans Azure AD. L’inscription auprès de Microsoft Intune ou de la Gestion des appareils mobiles (MDM) pour Office 365 nécessite l’enregistrement de l’appareil. Si vous avez configuré l’un de ces services, l’option **TOUS** est sélectionnée et l’option **AUCUN** est désactivée.
- **Exiger Multi-factor Auth pour joindre des appareils** : vous pouvez déterminer si les utilisateurs doivent fournir un facteur d’authentification supplémentaire lorsqu’ils veulent joindre leurs appareils à Azure AD. La valeur par défaut est **No**. Il est recommandé d’exiger une authentification multifacteur au moment de l’inscription d’un appareil. Avant d’activer l’authentification multifacteur pour ce service, vous devez vérifier que l’authentification multifacteur est configurée pour les utilisateurs qui inscrivent leurs appareils. Pour plus d’informations sur les différents services Azure Multi-Factor Authentication, consultez [Bien démarrer avec Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> Le paramètre **Exiger Multi-factor Auth pour joindre des appareils** s'applique aux appareils joints à Azure AD ou inscrits à Azure AD. Ce paramètre ne s’applique pas aux appareils hybrides joints à Azure AD.

- **Nombre maximal d’appareils par utilisateur** : ce paramètre vous permet de sélectionner le nombre maximal d’appareils joints à Azure AD ou inscrits à Azure AD et qu’un utilisateur peut avoir dans Azure AD. Si un utilisateur atteint ce quota, il ne pourra pas ajouter d’autres appareils tant qu’un ou plusieurs appareils existants n’auront pas été supprimés. La valeur par défaut est de **20** appareils.

> [!NOTE]
> Le paramètre **Nombre maximal d’appareils par utilisateur** s’applique aux appareils joints à Azure AD ou inscrits à Azure AD. Ce paramètre ne s’applique pas aux appareils hybrides joints à Azure AD.

- **Les utilisateurs peuvent synchroniser les paramètres et les données d’application sur différents appareils** : par défaut, ce paramètre est défini sur **AUCUN**. La sélection de certains utilisateurs ou groupes, ou de TOUS, permet aux paramètres et aux données d’application de l’utilisateur d’être synchronisés sur ses appareils Windows 10. Découvrez comment fonctionne la synchronisation dans Windows 10.
Cette option est une fonctionnalité de l’édition Premium disponible dans les produits comme Azure AD Premium ou EMS (Enterprise Mobility Suite).

## <a name="locate-devices"></a>Localiser les appareils

Deux options vous permettent de localiser les appareils inscrits et joints :

- **Tous les appareils** dans la section **Gérer** de la page **Appareils**  
- **Appareils** dans la section **Gérer** de la page **Utilisateur**

Ces deux options permettent d’accéder à une vue qui :

- Permet de rechercher des appareils en utilisant leur nom d’affichage ou leur identité d’appareil comme filtre
- Fournit une vue d’ensemble détaillée des appareils inscrits et joints
- Permet d’effectuer les tâches courantes de gestion des appareils

>[!TIP]
>
>* Si vous voyez un appareil « joint à une version hybride d’Azure AD » avec l’état « En attente » dans la colonne REGISTERED (Inscrit), cela signifie que l’appareil a été synchronisé à partir d’Azure AD Connect et qu’il attend de terminer l’inscription à partir du client. Pour en savoir plus, consultez [Planifier votre implémentation de la jointure d’Azure AD Hybride](hybrid-azuread-join-plan.md). Pour plus d’informations, consultez le [Forum aux questions sur les appareils](faq.md).
>
>* Pour certains appareils iOS, les noms d’appareils qui contiennent des apostrophes peuvent utiliser d’autres caractères qui ressemblent à des apostrophes. La recherche de ces appareils peut donc être difficile. Si les résultats que vous attendez ne s’affichent pas, vérifiez que la chaîne de recherche contient les caractères d’apostrophe attendus.

## <a name="device-identity-management-tasks"></a>Tâches de gestion des identités des appareils

En tant qu'administrateur général ou administrateur d’appareil cloud, vous pouvez gérer les appareils inscrits ou joints. Les administrateurs de service Intune peuvent :

- Mettre à jour les appareils – Les opérations quotidiennes telles que l’activation et la désactivation d’appareils en sont des exemples
- Supprimer des appareils – Quand un appareil est mis hors service et doit être supprimé dans Azure AD

Cette section fournit des informations sur les tâches courantes de gestion des identités des appareils.

### <a name="manage-an-intune-device"></a>Gérer un appareil Intune

Si vous êtes administrateur Intune, vous pouvez gérer les appareils marqués comme étant des appareils **Microsoft Intune**. Si l’appareil n’est pas inscrit auprès de Microsoft Intune, l’option « Gérer » est grisée.

![Gérer un appareil Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Activer/désactiver un appareil Azure AD

Pour activer/désactiver un appareil, vous avez deux options :

- Le menu de tâches (« … ») dans la page **Tous les appareils**

   ![Gérer un appareil Intune](./media/device-management-azure-portal/71.png)

- La barre d’outils dans la page **Appareils**

   ![Gérer un appareil Intune](./media/device-management-azure-portal/32.png)

**Remarques :**

- Vous devez être administrateur général ou administrateur d’appareil cloud dans Azure AD pour activer/désactiver un appareil. 
- La désactivation d’un périphérique empêche celui-ci de s’authentifier à Azure AD et, par conséquent, d’accéder à vos ressources Azure AD qui sont protégées par l’autorité de certification du périphérique ou d’utiliser vos informations d’identification WH4B.
- La désactivation de l’appareil entraîne la révocation du jeton d’actualisation principal (PRT) et des éventuels jetons d’actualisation (RT) sur l’appareil.

### <a name="delete-an-azure-ad-device"></a>Supprimer un appareil Azure AD

Pour supprimer un appareil, vous avez deux options :

- Le menu de tâches (« … ») dans la page **Tous les appareils**

   ![Gérer un appareil Intune](./media/device-management-azure-portal/72.png)

- La barre d’outils dans la page **Appareils**

   ![Supprimer une unité](./media/device-management-azure-portal/34.png)

**Remarques :**

- Il faut être administrateur général ou administrateur Intune dans Azure AD pour pouvoir supprimer un appareil.
- La suppression d’un appareil :
   - Empêche celui-ci d’accéder à vos ressources Azure AD.
   - Supprime toutes les informations associées à l’appareil, par exemple, les clés BitLocker des appareils Windows.  
   - Est une action irréversible et donc non recommandée, sauf si elle est absolument nécessaire.

Si un appareil est géré par une autre autorité de gestion (par exemple, Microsoft Intune), vérifiez que l’appareil a été réinitialisé ou mis hors service avant de le supprimer d’Azure AD. Lisez comment [gérer des appareils obsolètes](manage-stale-devices.md) avant de supprimer des appareils.

### <a name="view-or-copy-device-id"></a>Afficher ou copier l’ID de l’appareil

Vous pouvez utiliser un ID d’appareil pour vérifier les informations d’ID de l’appareil ou utiliser PowerShell durant le dépannage. Pour accéder à l’option de copie, cliquez sur l’appareil.

![Afficher un ID d’appareil](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Afficher ou copier des clés BitLocker

Vous pouvez afficher et copier les clés BitLocker pour permettre aux utilisateurs de récupérer leur lecteur chiffré. Ces clés sont uniquement disponibles pour les appareils Windows chiffrés dont les clés sont stockées dans Azure AD. Vous pouvez copier ces clés lorsque vous accédez aux informations de l’appareil.

![Afficher les clés BitLocker](./media/device-management-azure-portal/36.png)

Pour afficher ou copier les clés BitLocker, vous devez être le propriétaire de l’appareil ou un utilisateur possédant au moins l’un des rôles suivants attribués :

- Administrateur d’appareil cloud
- Administrateur général
- Administrateur du support technique
- Administrateur de services Intune
- Security Administrator
- Lecteur de sécurité

> [!NOTE]
> Les appareils Windows 10 hybrides joints à Azure AD n’ont pas de propriétaire. Par conséquent, si vous cherchiez un appareil à l’aide de son propriétaire et ne l’avez pas trouvé, recherchez-le à l’aide de son ID.

### <a name="device-list-filtering-preview"></a>Filtrage de la liste des appareils (préversion)

Auparavant, vous pouviez uniquement filtrer la liste des appareils par activité et état activé. Cette préversion vous permet désormais de filtrer la liste des appareils en fonction des attributs suivants sur un appareil :

- État activé
- État conforme
- Type de jonction (jonction Azure AD, jonction Azure AD Hybride, inscrit sur Azure AD)
- Timestamp d’activité
- Système d’exploitation
- Type d’appareil (imprimantes, machines virtuelles sécurisées, appareils partagés, appareils inscrits)

Pour activer la fonctionnalité de filtrage en préversion dans la vue **Tous les appareils** :

![Activer la fonctionnalité de filtrage en préversion](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Appareils**.
1. Sélectionnez la bannière qui indique : **Essayez les améliorations apportées au filtrage des appareils. Cliquez pour activer la préversion.**

Vous avez maintenant la possibilité d’**Ajouter des filtres** à votre vue **Tous les appareils**.

## <a name="audit-logs"></a>Journaux d’audit

Des activités de l’appareil sont disponibles dans les journaux d’activité. Ces journaux comprennent les activités déclenchées par le service d’inscription des appareils et par les utilisateurs :

- Création d’un appareil et ajout de propriétaires/d’utilisateurs sur l’appareil
- Modification des paramètres de l’appareil
- Opérations concernant les appareils, telles que la suppression ou la mise à jour d’un appareil

Les **Journaux d’audit** dans la section **Activité** de la page **Appareils** constituent le point d’entrée des données d’audit.

Un journal d’audit inclut une vue Liste par défaut, qui indique les informations suivantes :

- La date et l’heure de l’occurrence
- Les cibles
- L’initiateur/intervenant d’une activité (qui)
- L’activité (quoi)

![Journaux d’audit](./media/device-management-azure-portal/63.png)

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Journaux d’audit](./media/device-management-azure-portal/64.png)

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données d’audit à l’aide des champs suivants :

- Category
- Type de ressource d’activité
- Activité
- Plage de dates
- Cible
- Initié par (intervenant)

Vous pouvez filtrer les entrées, mais également rechercher des entrées spécifiques.

![Journaux d’audit](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Étapes suivantes

[Guide pratique pour gérer les appareils obsolètes dans Azure AD](manage-stale-devices.md)
