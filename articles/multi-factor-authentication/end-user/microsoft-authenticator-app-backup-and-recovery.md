---
title: Sauvegarder et récupérer des informations avec Microsoft Authenticator - Azure | Microsoft Docs
description: Découvrez comment sauvegarder et récupérer les informations d’authentification de votre compte à l’aide de l’application Microsoft Authenticator.
services: multi-factor-authentication
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: lizross
ms.reviewer: olhaun
ms.custom: end-user
ms.openlocfilehash: 0b76d2debb9814188e5551252e52bd0dae38eb50
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2018
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Sauvegarder et récupérer des informations d’authentification de compte avec l’application Microsoft Authenticator
**S’applique à :**

- Appareils iOS

L’application Microsoft Authenticator sauvegarde les informations d’identification de votre compte et les paramètres de l’application associée, comme l’ordre de vos comptes, dans le cloud. Après la sauvegarde, vous pouvez également utiliser l’application pour récupérer vos informations sur un nouvel appareil, ce qui évite de se retrouver bloqué ou d’avoir à recréer des comptes.

>[!IMPORTANT]
> Vous avez besoin d’un compte Microsoft personnel et d’un compte iCloud pour chaque emplacement de stockage de sauvegarde. Mais dans cet emplacement de stockage, vous pouvez sauvegarder plusieurs comptes. Par exemple, vous pouvez avoir un compte personnel, un compte scolaire et un compte tiers comme Facebook, Google ou autre.<br><br>Seules les informations d’identification de votre compte sont stockées, c’est-à-dire votre nom d’utilisateur et le code de vérification de compte à 8 chiffres qui est indispensable pour vous identifier auprès de l’application Microsoft Authenticator. Nous ne stockons aucune autre information liée à vos comptes, notamment des adresses e-mail ou des fichiers. De plus, nous n’associons pas et ne partageons pas vos comptes de quelque manière que ce soit, avec un autre produit ou service. Enfin, votre administrateur informatique n’aura accès à aucune information sur ces comptes.

## <a name="back-up-your-account-credentials"></a>Sauvegarder les informations d’identification de votre compte
Pour pouvoir sauvegarder vos informations d’identification, vous devez avoir à la fois :

- Un [compte Microsoft](https://account.microsoft.com/account) personnel comme votre compte de récupération.

- Un [compte iCloud](https://www.icloud.com/) comme emplacement de stockage. 

Le fait de devoir se connecter à ces deux comptes renforce la sécurité de vos informations de sauvegarde.

**Pour activer la sauvegarde dans le cloud**
-   Sur votre appareil iOS, sélectionnez **Paramètres** puis **Sauvegarde**, et activez **Sauvegarde automatique**.

    Les informations d’identification de votre compte sont sauvegardées dans votre compte iCloud.

    ![Écran des paramètres iOS affichant l’emplacement des paramètres de sauvegarde automatique](./media/authenticator-app-backup-and-recovery/backup-and-recovery-turn-on.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Récupérer les informations d’identification de votre compte sur votre nouvel appareil
Vous pouvez récupérer les informations d’identification de votre compte dans votre compte iCloud, à l’aide du compte de récupération Microsoft que vous avez créé lors de la sauvegarde de vos informations.

**Pour récupérer vos informations**
1.  Sur votre appareil iOS, ouvrez l’application Microsoft Authenticator et sélectionnez **Begin recovery (Lancer la récupération)** en bas de l’écran.

    ![Application Microsoft Authenticator, indiquant où cliquer sur Begin recovery (Lancer la récupération)](./media/authenticator-app-backup-and-recovery/backup-and-recovery-begin-recovery.png)

2.  Connectez-vous à votre compte de récupération, à l’aide du compte Microsoft personnel que vous avez utilisé pendant le processus de sauvegarde.

    Les informations d’identification de votre compte sont récupérées sur le nouvel appareil.

Une fois la récupération terminée, vous pouvez constater que les codes de vérification de votre compte Microsoft personnel dans l’application Microsoft Authenticator sont différents sur vos ancien et nouveau téléphones. Ces codes sont différents car chaque appareil a ses propres informations d’identification, mais les deux sont valides et fonctionnent lors de la connexion à l’aide du téléphone associé.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Récupérer d’autres comptes nécessitant davantage de vérifications
Si vous utilisez des notifications Push avec vos comptes personnel, professionnel ou scolaire, une alerte s’affiche à l’écran et vous demande une vérification supplémentaire pour pouvoir récupérer vos informations. Comme les notifications Push requièrent l’utilisation d’une information d’identification liée à votre appareil et ne transitant jamais sur le réseau, vous devez prouver votre identité avant la création de l’information d’identification sur votre appareil.

Pour des comptes Microsoft personnels, vous pouvez prouver votre identité en saisissant votre mot de passe ainsi qu’une autre adresse e-mail ou un autre numéro de téléphone. Pour des comptes professionnels ou scolaires, vous devez lire le code QR que le fournisseur de votre compte vous a transmis.

**Pour effectuer une vérification supplémentaire des comptes personnels**
1.  Dans l’écran **Comptes** de l’application Microsoft Authenticator, cliquez sur la flèche déroulante en regard du compte que vous souhaitez récupérer.

    ![Application Microsoft Authenticator, affichant les comptes disponibles avec leurs menus déroulants associés](./media/authenticator-app-backup-and-recovery/backup-and-recovery-arrow.png)

2.  Sélectionnez **Sign in to recover (Se connecter pour récupérer)**, saisissez votre mot de passe, puis confirmez votre adresse e-mail ou votre numéro de téléphone comme vérification complémentaire.

    ![Application Microsoft Authenticator vous permettant de saisir vos informations de connexion](./media/authenticator-app-backup-and-recovery/backup-and-recovery-sign-in.png)

**Pour effectuer une vérification supplémentaire des comptes professionnels ou scolaires**
1.  Dans l’écran **Comptes** de l’application Microsoft Authenticator, cliquez sur la flèche déroulante en regard du compte que vous souhaitez récupérer.

    ![Application Microsoft Authenticator, affichant les comptes disponibles avec leurs menus déroulants associés](./media/authenticator-app-backup-and-recovery/backup-and-recovery-additonal-accts.png)

2.  Sélectionnez **Scan QR code to recover (Lire le code QR pour récupérer)**, puis lisez le code QR que votre administrateur vous a fourni.

    ![Application Microsoft Authenticator vous permettant de lire votre code QR](./media/authenticator-app-backup-and-recovery/backup-and-recovery-scan-qr-code.png)

## <a name="troubleshooting-backup-and-recovery-problems"></a>Résoudre les problèmes de sauvegarde et de récupération
Plusieurs raisons peuvent expliquer le fait que votre sauvegarde ne soit pas disponible :

-   **Modification des systèmes d’exploitation.** Votre sauvegarde est stockée dans l’option de stockage cloud fournie par le système d’exploitation de votre téléphone. En d’autres termes, la sauvegarde est indisponible si vous basculez entre Android et iOS. Dans ce cas, vous devez recréer manuellement votre compte dans l’application.

-   **Problèmes de réseau ou de mot de passe.** Vérifiez que vous être connecté à un réseau et à votre compte iCloud à l’aide de l’AppleID que vous avez utilisé sur votre dernier iPhone.

-   **Suppression accidentelle.** Il est possible que vous ayez supprimé votre compte de sauvegarde de votre précédent appareil ou lors de la gestion de votre compte de stockage cloud. Dans ce cas, vous devez recréer manuellement votre compte dans l’application.

-   **Comptes Microsoft Authenticator existants.** Si vous avez déjà créé des comptes dans l’application Microsoft Authenticator, elle ne pourra pas récupérer vos comptes sauvegardés. Bloquer la récupération permet d’éviter que les détails de votre compte ne soient remplacés par des informations obsolètes. Dans ce cas, vous devez supprimer les informations d’identification des comptes existants créés dans votre application Authenticator pour pouvoir récupérer votre sauvegarde.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez sauvegardé et récupéré les informations d’identification de votre compte sur votre nouvel appareil, vous pouvez continuer d’utiliser l’application Microsoft Authenticator pour vérifier votre identité.

## <a name="related-topics"></a>Rubriques connexes
- [Prise en main de l’application Microsoft Authenticator](microsoft-authenticator-app-how-to.md)  

- [Se connecter sur un téléphone](microsoft-authenticator-app-phone-signin-faq.md)

- [Forum aux questions sur l’application Microsoft Authenticator](microsoft-authenticator-app-faq.md)

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)