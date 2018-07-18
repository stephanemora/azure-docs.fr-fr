---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/11/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 10ccb80dd74606d2ad40ab5d7993aed8cd71725e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36329669"
---
## <a name="generate-the-certificate-signing-request-file"></a>Générer le fichier de demande de signature de certificat
Le service de notification Push Apple (APNS) utilise des certificats pour authentifier vos notifications Push. Suivez ces instructions pour créer le certificat push nécessaire pour envoyer et recevoir des notifications. Pour plus d’informations sur ces concepts, consultez la documentation officielle [d’Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request) qu’Apple utilise pour générer un certificat Push signé.

1. Sur votre Mac, exécutez l’outil Trousseaux d’accès. Il peut être ouvert à partir de dossiers **Utilitaires** ou du dossier **Autre** sur Launch Pad.
2. Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Sélectionnez **Adresse électronique de l’utilisateur** et **Nom commun**, vérifiez que la case **Enregistré sur le disque** est bien cochée, puis cliquez sur **Continuer**. Laissez le champ **Adresse de messagerie d'autorité de certification** vide, car il n'est pas requis.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Cette action enregistre le fichier de demande de signature de certificat à l’emplacement sélectionné. L’emplacement par défaut se trouve quant à lui sur le Bureau. Notez l’emplacement choisi pour ce fichier.

Ensuite, vous allez inscrire votre application auprès d’Apple, activer les notifications Push, puis télécharger le fichier de demande de signature de certificat exporté pour créer un certificat Push.

## <a name="register-your-app-for-push-notifications"></a>Inscription de votre application pour les notifications Push
Pour pouvoir envoyer des notifications Push vers une application iOS, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push.  

1. Si vous n’avez pas encore inscrit votre application, accédez au <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portail de provisionnement iOS</a> du centre de développement Apple, connectez-vous avec votre identifiant Apple, cliquez sur **Identifiers**, sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Mettez à jour les trois champs suivants pour votre nouvelle application, puis cliquez sur **Continuer**:
   
   * **Nom** : tapez un nom descriptif pour votre application dans le champ **Nom** de la section **Description de l’ID d’application**.
   * **Identificateur d'offres groupées** : sous la section **ID d'application spécifique**, saisissez une valeur **Identificateur d'offres groupées** sous la forme `<Organization Identifier>.<Product Name>` comme indiqué dans le [Guide de distribution d'application](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Les valeurs *Identificateur de l’organisation* et *Nom du produit* que vous indiquez doivent correspondre à l'identificateur d’organisation et au nom de produit que vous utiliserez pour créer le projet XCode. Dans la capture d’écran ci-dessous, *NotificationHubs* est utilisé comme identificateur d’organisation, tandis que *GetStarted* correspond au nom du produit. En vous assurant que ces valeurs correspondent à celles que vous utiliserez dans le projet XCode, vous avez la garantie d’utiliser le profil de publication qui convient avec XCode. 
   * **Notifications Push** : cochez l’option **Notifications Push** dans la section **App Services**.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      L’identifiant de votre application est généré et vous êtes invité à confirmer les informations. Cliquez sur **S’inscrire** pour confirmer le nouvel ID d’application.
     
      À partir du moment où vous cliquez sur **S’inscrire**, le message **Inscription terminée** s’affiche, comme indiqué dans l’image suivante. Cliquez sur **Done**.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. Dans le centre de développement, recherchez les identifiants d’application que vous avez créés, puis cliquez sur la ligne correspondante.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Cliquez sur l’identifiant de l’application pour afficher les informations détaillées de cette application. Cliquez sur le bouton **Modifier** au bas de l’écran.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      Vous visualisez alors l’Assistant « Add iOS Certificate ».
   
   > [!NOTE]
   > Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l’inscription d’un certificat de production. Assurez-vous simplement que vous utilisez le même type de certificat quand vous envoyez des notifications.
   > 
   > 
3. Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Cette opération entraîne le téléchargement du certificat et l’enregistre sur votre ordinateur, dans le dossier Téléchargements.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Par défaut, le fichier téléchargé est un certificat de développement nommé **aps_development.cer**.
   > 
   > 
5. Double-cliquez sur le certificat Push téléchargé **aps_development.cer**.
   
      Cette opération installe le nouveau certificat dans le Trousseau, comme indiqué dans l’image suivante :
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > Il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe **Apple Development iOS Push Notification Services**.   
6. Dans Trousseau d’accès, cliquez avec le bouton droit sur le certificat push que vous avez créé dans la catégorie **Certificats** . Cliquez sur **Exporter**, nommez le fichier, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Notez le nom du fichier et l’emplacement du certificat .p12 exporté. Il est utilisé pour activer l’authentification avec APNS.
   
   > [!NOTE]
   > Ce didacticiel permet de créer un fichier QuickStart.p12. Il est possible que le nom de votre fichier et son emplacement sont différents.
   
## <a name="create-a-provisioning-profile-for-the-app"></a>Création d’un profil d’approvisionnement pour l’application
1. Une fois de retour dans le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le bouton **+** pour créer un profil. Vous affichez l’Assistant **Ajouter le profil d'approvisionnement iOS**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Sélectionnez **Développement d’application iOS** sous **Développement** en tant que type de profil d’approvisionnement, puis cliquez sur **Continuer**. 
3. Ensuite, dans la liste déroulante **ID d’application**, sélectionnez l’identifiant d’application que vous avez créé, puis cliquez sur **Continuer**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. Dans l'écran **Select certificates**, sélectionnez le certificat de développement habituellement utilisé pour la signature de code, puis cliquez sur **Continue**. Ce certificat n’est pas le certificat Push que vous avez créé.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continuer**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Sélectionnez ensuite un nom pour le profil dans le **Nom du profil**, cliquez sur **Générer**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Lorsque le nouveau profil de configuration est créé, cliquez pour le télécharger et l’installer sur votre ordinateur de développement Xcode. Cliquez ensuite sur **Terminé**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
