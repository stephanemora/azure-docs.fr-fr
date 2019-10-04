---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446561"
---
## <a name="generate-the-certificate-signing-request-file"></a>Générer le fichier de demande de signature de certificat

Le service de notification Push Apple (APNs) utilise des certificats pour authentifier vos notifications Push. Suivez ces instructions pour créer le certificat push nécessaire pour envoyer et recevoir des notifications. Pour plus d’informations sur ces concepts, consultez la documentation officielle [d’Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Générez le fichier de demande de signature de certificat (CSR, Certificate Signing Request) utilisé par Apple pour générer un certificat Push signé.

1. Sur votre Mac, exécutez l’outil Trousseaux d’accès. Vous pouvez l’ouvrir à partir du dossier **Utilitaires** ou du dossier **Autre** sur Launchpad.

1. Sélectionnez **Trousseaux d’accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

    ![Utiliser l’accès au trousseau pour demander un nouveau certificat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Sélectionnez votre **adresse e-mail d’utilisateur**, entrez votre **nom commun**, veillez à spécifier **Enregistré sur le disque**, puis sélectionnez **Continuer**. Laissez le champ **Adresse de messagerie d’autorité de certification** vide, car il n’est pas requis.

    ![Informations requises sur le certificat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Entrez un nom pour le fichier CSR dans **Enregistrer en tant que**, sélectionnez l’emplacement dans **Où**, puis sélectionnez **Enregistrer**.

    ![Choisir un nom de fichier pour le certificat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Cette action enregistre le fichier CSR à l’emplacement sélectionné. L’emplacement par défaut est **Bureau**. Notez l’emplacement choisi pour ce fichier.

Ensuite, inscrivez votre application auprès d’Apple, activez les notifications Push, puis téléchargez le fichier de demande de signature de certificat exporté pour créer un certificat Push.

## <a name="register-your-app-for-push-notifications"></a>Inscription de votre application pour les notifications Push

Pour envoyer des notifications Push vers une application iOS, inscrivez votre application auprès d’Apple ainsi qu’aux notifications Push.  

1. Si vous n’avez pas encore inscrit votre application, accédez au [portail de provisionnement iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) dans le Centre pour développeurs Apple. Après cela, connectez-vous avec votre ID Apple, sélectionnez **Identifiers**, **App IDs**, puis **+** pour inscrire une nouvelle application.

    ![Page d’ID d’application Portail d’approvisionnement iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Mettez à jour les trois valeurs suivantes pour votre nouvelle application, puis sélectionnez **Continue** :

   * **Nom** : tapez un nom descriptif pour votre application dans la zone **Name** de la section **App ID Description**.

   * **Identificateur d’offres groupées** : dans la section **Explicit App ID**, entrez une valeur **Bundle Identifier** au format `<Organization Identifier>.<Product Name>` comme indiqué dans le [Guide de distribution d’application](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Les valeurs *Organization Identifier* et *Product Name* doivent correspondre à l’identificateur d’organisation et au nom de produit que vous utiliserez pour créer le projet Xcode. Dans la capture d’écran ci-dessous, la valeur *NotificationHubs* est utilisée comme identificateur d’organisation, tandis que la valeur *GetStarted* correspond au nom du produit. Vérifiez que la valeur **Bundle Identifier** correspond à celle de votre projet Xcode, afin que Xcode utilise le profil de publication correct.

   * **Notifications Push** : cochez l’option **Notifications Push** dans la section **App Services**.

     ![Formulaire pour inscrire un nouvel ID d’application](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     L’ID de votre application est généré et vous êtes invité à confirmer les informations. Sélectionnez **Register** pour confirmer le nouvel ID d’application.

     Une fois que vous avez sélectionné **Register**, le message **Registration complete** s’affiche, comme indiqué dans l’image suivante. Sélectionnez **Terminé**.

     ![Inscription d’ID d’application terminée affichant les droits](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. Dans le centre de développement, sous **App IDs**, recherchez l’ID de l’application que vous avez créée et sélectionnez la ligne correspondante.

    ![Liste d’ID d’application](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Sélectionnez l’ID d’application pour afficher les détails de l’application, puis sélectionnez le bouton **Edit** en bas.

    ![Page de modification d’ID d’application](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Faites défiler jusqu’en bas de l’écran, puis sélectionnez le bouton **Create Certificate** dans la section **Development Push SSL Certificate**.

    ![Créer un certificat pour le bouton d’ID d’application](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    L’Assistant **Add iOS Certificate** est maintenant visible.

    > [!NOTE]
    > Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l’inscription d’un certificat de production. Assurez-vous simplement que vous utilisez le même type de certificat quand vous envoyez des notifications.

1. Sélectionnez **Choose File**, accédez à l’emplacement où vous avez enregistré le fichier de demande de signature de certificat créé lors de la première tâche, puis sélectionnez **Generate**.

    ![Page de téléchargement du certificat généré CSR](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Une fois que le portail a créé un certificat, sélectionnez le bouton **Download**, puis **Done**.

    ![Page de téléchargement du certificat généré](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Le certificat est téléchargé et enregistré sur votre ordinateur dans le dossier **Téléchargements**.

    ![Recherchez le fichier de certificat dans le dossier Téléchargements](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Par défaut, le certificat de développement téléchargé se nomme **aps_development.cer**.

1. Sélectionnez le certificat Push téléchargé **aps_development.cer**.

    Cette opération installe le nouveau certificat dans le Trousseau, comme indiqué dans l’image suivante :

    ![Liste de certificats d’accès Keychain montrant le nouveau certificat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Bien que le nom de votre certificat puisse être différent, il portera toutefois le préfixe **Apple Development iOS Push Notification Services**.

1. Dans Trousseau d’accès, cliquez avec le bouton droit sur le certificat push que vous avez créé dans la catégorie **Certificats** . Sélectionnez **Exporter**, nommez le fichier, sélectionnez le format **.p12**, puis sélectionnez **Enregistrer**.

    ![Exportation du certificat au format p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Notez le nom du fichier et l’emplacement du certificat .p12 exporté. Ces informations sont nécessaires pour activer l’authentification avec APNs.

    > [!NOTE]
    > Ce tutoriel crée un fichier nommé **QuickStart.p12**. Il est possible que le nom de votre fichier et son emplacement sont différents.

## <a name="create-a-provisioning-profile-for-the-app"></a>Création d’un profil d’approvisionnement pour l’application

1. Dans le [portail de provisionnement iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), sélectionnez **Provisioning Profiles**, **All**, puis sélectionnez **+** pour créer un profil. L’Assistant **Add iOS Provisioning Profile** apparaît.

    ![Liste du profil d'approvisionnement](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Sélectionnez **iOS App Development** sous **Development** en tant que type de profil de provisionnement, puis sélectionnez **Continue**.

1. Ensuite, dans la liste déroulante **App ID**, sélectionnez l’ID d’application que vous avez créé, puis sélectionnez **Continue**.

    ![Sélectionner l’ID de l’application](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Dans la fenêtre **Select certificates**, sélectionnez le certificat de développement que vous utilisez habituellement pour la signature de code, puis sélectionnez **Continue**. Ce certificat n’est pas le certificat Push que vous avez créé.

    ![Sélectionner le certificat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Sélectionnez les appareils à utiliser pour le test, puis sélectionnez **Continue**.

    ![Sélectionner les appareils](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Pour finir, sélectionnez un nom pour le profil dans **Profile Name**, puis sélectionnez **Generate**.

    ![Choisir un nom du profil d'approvisionnement](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quand le nouveau profil de provisionnement est créé, choisissez de le télécharger et de l’installer sur votre ordinateur de développement Xcode. Ensuite, sélectionnez **Terminé**.

    ![Télécharger le profil d’approvisionnement](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Création d’un hub de notifications

Dans cette section, vous créez un hub de notification et configurez l’authentification avec APNs à l’aide du certificat Push .p12 que vous venez de créer. Si vous souhaitez utiliser un hub de notification que vous avez déjà créé, vous pouvez passer directement à l’étape 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configurer votre hub de notification avec des informations APNs

1. Sous **Services de notification**, sélectionnez **Apple (APNS)** .

1. Sélectionnez **Certificate**.

1. Sélectionnez l’icône du fichier.

1. Sélectionnez le fichier .p12 exporté précédemment.

1. Spécifiez le mot de passe correct.

1. Sélectionnez le mode **Bac à sable**. Utilisez uniquement le mode **Production** si vous souhaitez envoyer des notifications Push aux utilisateurs ayant acheté votre application dans Windows Store.

    ![Configurer la certification APNS dans le portail Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Vous avez maintenant configuré votre hub de notification avec APNs. Vous disposez aussi des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.
