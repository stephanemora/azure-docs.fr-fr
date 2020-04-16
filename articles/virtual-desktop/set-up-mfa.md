---
title: Configurer Azure Multi-Factor Authentication pour Windows Virtual Desktop - Azure
description: Comment configurer Azure Multi-Factor Authentication pour une sécurité accrue dans Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998474"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Configurer Azure Multi-Factor Authentication

Le client Windows pour Windows Virtual Desktop est une bonne option pour l’intégration de Windows Virtual Desktop à votre machine locale. Cependant, quand vous configurez votre compte Windows Virtual Desktop dans le client Windows, vous devez prendre certaines mesures pour garantir votre propre sécurité et celle de vos utilisateurs.

Quand vous vous connectez pour la première fois, le client demande votre nom d’utilisateur, votre mot de passe et votre authentification multifacteur Azure. Après cela, la prochaine fois que vous vous connectez, le client a mémorisé votre jeton pour votre application d’entreprise Azure Active Directory (AD). Quand vous sélectionnez **Mémoriser mes informations**, vos utilisateurs peuvent se connecter après le redémarrage du client sans devoir réentrer leurs informations d’identification.

Bien que la mémorisation des informations d’identification soit pratique, elle peut également rendre moins sécurisés les déploiements sur des scénarios d’entreprise ou des appareils personnels. Pour protéger vos utilisateurs, vous devez faire en sorte que le client demande des informations d’identification Azure Multi-Factor Authentication (MFA). Cet article vous montre comment configurer la stratégie d’accès conditionnel pour Windows Virtual Desktop de façon à activer ce paramètre.

## <a name="prerequisites"></a>Prérequis

Voici ce dont vous avez besoin pour commencer :

- Attribuez à tous vos utilisateurs une des licences suivantes :
  - Microsoft 365 E3 ou E5
  - Azure Active Directory Premium P1 ou P2
  - Enterprise Mobility + Security E3 ou E5
- Un groupe Azure Active Directory avec les utilisateurs affectés en tant que membres du groupe.
- Activez Azure MFA pour tous vos utilisateurs. Pour plus d’informations sur la façon de le faire, consultez [Comment exiger la vérification en deux étapes pour un utilisateur](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>Le paramètre suivant s’applique également au [client web Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Adhérer à la stratégie d’accès conditionnel

1. Ouvrez **Azure Active Directory**.

2. Accédez à l’onglet **Toutes les applications**. Dans le menu déroulant « Type d’application », sélectionnez **Applications d’entreprise**, puis recherchez **Client Windows Virtual Desktop**.

    ![Capture d’écran de l’onglet Toutes les applications. L’utilisateur a entré « Client Windows Virtual Desktop » dans la barre de recherche et l’application est apparue dans les résultats de la recherche.](media/all-applications-search.png)

3. Sélectionnez **Accès conditionnel**.

    ![Capture d’écran montrant l’utilisateur pointant le curseur de la souris sur l’onglet Accès conditionnel.](media/conditional-access-location.png)

4. Sélectionnez **+ Nouvelle stratégie**.

   ![Capture d’écran de la page Accès conditionnel. L’utilisateur pointe le curseur de la souris sur le bouton Nouvelle stratégie.](media/new-policy-button.png)

5. Entrez un **nom** pour la **règle**, puis **sélectionnez** le *nom du **groupe** vous avez créé dans les prérequis.

6. Sélectionnez **Sélectionner**, puis **Terminé**.

7. Ensuite, ouvrez **Applications ou actions cloud**.

8. Dans le panneau **Sélectionner**, sélectionnez l’application d’entreprise **Windows Virtual Desktop**.

    ![Capture d’écran de la page Applications ou actions cloud. L’utilisateur a sélectionné l’application Windows Virtual Desktop en cochant la case en regard de celle-ci. L’application sélectionnée est mise en surbrillance en rouge.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Vous voyez aussi l’application Client Windows Virtual Desktop sélectionnée sur le côté gauche de l’écran, comme illustré dans l’image suivante. Pour que la stratégie fonctionne, vous avez besoin à la fois des applications d’entreprise Windows Virtual Desktop et Client Windows Virtual Desktop.
    >
    > ![Capture d’écran de la page Applications ou actions cloud. Les applications Windows Virtual Desktop et Client Windows Virtual Desktop sont mises en surbrillance en rouge.](media/cloud-apps-enterprise-selected.png)

9. Sélectionnez **Sélectionner**.

10. Ensuite, ouvrez **Octroyer**. 

11. Sélectionnez **Exiger l’authentification multifacteur**, puis **Demander un des contrôles sélectionnés**.
   
    ![Capture écran de la page Octroyer. L’option « Exiger l’authentification multifacteur » est sélectionnée.](media/grant-page.png)

    >[!NOTE]
    >Si vous avez des appareils inscrits à MDM dans votre organisation et que vous ne voulez pas qu’ils affichent l’invite MFA, vous pouvez aussi sélectionner **Exiger que l’appareil soit marqué comme conforme**.

12. Sélectionnez **Session**.

13. Définissez **Fréquence de connexion** sur **Actif**, puis changez sa valeur en **1 heures**.

    ![Capture d’écran de la page Session. Le menu Session montre les menus déroulants de la fréquence de connexion qui ont été changés en « 1 » et en « heures ».](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Les sessions actives dans votre environnement Windows Virtual Desktop continuent de fonctionner quand vous modifiez la stratégie. Cependant, si vous vous déconnectez, vous devrez fournir à nouveau vos informations d’identification après 60 minutes. Quand vous modifiez les paramètres, vous pouvez augmenter la période du délai d’expiration comme vous le souhaitez (à condition qu’elle s’aligne avec la stratégie de sécurité de votre organisation).
    >
    >La valeur par défaut est une fenêtre glissante de 90 jours, ce qui signifie que le client demande aux utilisateurs de se reconnecter quand ils essaient d’accéder à une ressource après avoir été inactifs sur leur machine pendant 90 jours ou plus.

14. Activez la stratégie.

15. Sélectionnez **Créer** pour confirmer la stratégie.

Vous avez tout terminé ! N’hésitez pas à tester la stratégie pour vérifier que votre liste verte fonctionne comme prévu.
