---
title: Configurer Azure Multi-Factor Authentication pour Windows Virtual Desktop - Azure
description: Comment configurer Azure Multi-Factor Authentication pour une sécurité accrue dans Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16abe8d155a0d7d7f65c69e6305da62bd8813ea4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361147"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Activer Azure Multi-Factor Authentication pour Windows Virtual Desktop

Le client Windows pour Windows Virtual Desktop est une bonne option pour l’intégration de Windows Virtual Desktop à votre machine locale. Cependant, quand vous configurez votre compte Windows Virtual Desktop dans le client Windows, vous devez prendre certaines mesures pour garantir votre propre sécurité et celle de vos utilisateurs.

Quand vous vous connectez pour la première fois, le client demande votre nom d’utilisateur, votre mot de passe et votre authentification multifacteur Azure. Après cela, la prochaine fois que vous vous connectez, le client a mémorisé votre jeton pour votre application d’entreprise Azure Active Directory (AD). Quand vous sélectionnez **Mémoriser mes informations**, vos utilisateurs peuvent se connecter après le redémarrage du client sans devoir réentrer leurs informations d’identification.

Bien que la mémorisation des informations d’identification soit pratique, elle peut également rendre moins sécurisés les déploiements sur des scénarios d’entreprise ou des appareils personnels. Pour protéger vos utilisateurs, vous devez faire en sorte que le client demande des informations d’identification Azure Multi-Factor Authentication (MFA). Cet article vous montre comment configurer la stratégie d’accès conditionnel pour Windows Virtual Desktop de façon à activer ce paramètre.

## <a name="prerequisites"></a>Prérequis

Voici ce dont vous avez besoin pour commencer :

- Attribuez aux utilisateurs une licence qui comprend Azure Active Directory Premium P1 ou P2.
- Un groupe Azure Active Directory avec les utilisateurs affectés en tant que membres du groupe.
- Activez Azure MFA pour tous vos utilisateurs. Pour plus d’informations sur la façon de le faire, consultez [Comment exiger la vérification en deux étapes pour un utilisateur](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Le paramètre suivant s’applique également au [client web Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Cette section vous montre comment créer une stratégie d’accès conditionnel qui nécessite l’authentification multifacteur lors de la connexion à Windows Virtual Desktop.

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
2. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
3. Sélectionnez **Nouvelle stratégie**.
4. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
5. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**.
   - Sous **Inclure**, sélectionnez **Sélectionner des utilisateurs et des groupes** > **Utilisateurs et groupes** > Choisissez le groupe créé lors de la phase des prérequis.
   - Sélectionnez **Terminé**.
6. Sous **Applications ou actions cloud** > **Inclure**, sélectionnez **Sélectionner les applications**.
   - Choisissez **Windows Virtual Desktop** (ID d'application 9cdead84-a844-4324-93f2-b2e6bb768d07), puis **Sélectionner** et **Terminé**.

     > [!div class="mx-imgBorder"]
     > ![Capture d’écran de la page Applications ou actions cloud. Les applications Windows Virtual Desktop et Client Windows Virtual Desktop sont mises en surbrillance en rouge.](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >Pour rechercher l'ID de l’application que vous souhaitez sélectionner, accédez à **Applications d’entreprise**, puis sélectionnez **Applications Microsoft** dans le menu déroulant correspondant au type d’application.

7. Sous **Contrôles d’accès** > **Accorder**, sélectionnez **Accorder l’accès**, **Exiger une authentification multifacteur**, puis **Sélectionner**.
8. Sous **Contrôles d’accès** > **Session**, sélectionnez **Fréquence de connexion**, définissez la valeur **1** et l’unité **Heures**, puis sélectionnez **Sélectionner**.
9. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
10. Sélectionnez **Créer** pour activer votre stratégie.

## <a name="next-steps"></a>Étapes suivantes

- [Apprenez-en davantage sur les stratégies d’accès conditionnel](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Apprenez-en davantage sur la fréquence de connexion des utilisateurs](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
