---
title: Configurer des appareils inscrits à Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des appareils inscrits à Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 0c38c0160cea51940ac5b04ee64095c6a6f25b5d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414672"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurer des appareils Windows 10 inscrits à Azure Active Directory

La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que vos utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md).

Pour le scénario **BYOD (Bring Your Own Device)**, configurez les appareils inscrits à Azure AD. Dans Azure AD, vous pouvez configurer les appareils inscrits à Azure AD pour Windows 10, iOS, Android et macOS. Cet article contient les étapes à suivre pour les appareils Windows 10. 


## <a name="before-you-begin"></a>Avant de commencer

Pour inscrire un appareil Windows 10, le service d’inscription des appareils doit être configuré pour vous permettre d’inscrire des appareils. En outre, vous devez avoir moins d’appareils inscrits que la valeur maximale configurée. Pour plus d’informations, consultez [Configurer les paramètres de l’appareil](../devices/device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Ce que vous devez savoir

Lorsque vous inscrivez un appareil, gardez à l’esprit les éléments suivants :

- Windows inscrit l’appareil à l’annuaire de l’entreprise dans Azure AD

- Vous devrez peut-être répondre à une demande d’authentification multifacteur. Cette demande est configurable par votre administrateur informatique.

- Azure Active vérifie si l’appareil nécessite une inscription à la gestion des appareils mobiles, et l’inscrit si nécessaire.

- Si vous êtes un utilisateur géré, Windows vous dirige vers le bureau par le biais de la connexion automatique.

- Si vous êtes un utilisateur fédéré, vous accédez à l’écran d’ouverture de session Windows pour entrer vos informations d’identification.


## <a name="registering-a-device"></a>Inscription d’un appareil

Cette section fournit les étapes permettant d’inscrire votre appareil Windows 10 dans Azure AD. Un appareil inscrit correctement s’affiche avec une entrée **Compte professionnel ou scolaire**.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Pour inscrire votre appareil Windows 10 :**

1. Dans le menu **Démarrer**, cliquez sur **Paramètres**.

    ![Paramètres](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Cliquez sur **Comptes**.

    ![Comptes](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Cliquez sur **Accès Professionnel ou Scolaire**.

    ![Accès Professionnel ou Scolaire](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Dans la boîte de dialogue **Accès Professionnel ou Scolaire**, cliquez sur **Se connecter**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Dans la boîte de dialogue **Configurer un compte professionnel ou scolaire**, entrez le nom de votre compte (par exemple, someone@example.com), puis cliquez sur **Suivant**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Dans la boîte de dialogue **Saisie du mot de passe**, entrez votre mot de passe, puis cliquez sur **Suivant**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Dans la boîte de dialogue **Vous avez terminé**, cliquez sur **Terminé**.

    ![Connecter](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Vérification

Pour vérifier si un appareil est joint à un annuaire Azure AD, consultez la boîte de dialogue **Accès Professionnel ou Scolaire** sur votre appareil.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Vous pouvez également passer en revue les paramètres de l’appareil dans le portail Azure AD.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md).

- Pour plus d’informations sur la gestion des appareils dans le portail Azure AD, consultez [Gestion des appareils avec le portail Azure](../device-management-azure-portal.md).




