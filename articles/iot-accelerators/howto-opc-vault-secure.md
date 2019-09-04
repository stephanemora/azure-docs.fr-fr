---
title: Sécuriser la communication d’appareils OPC UA avec OPC Vault - Azure | Microsoft Docs
description: Guide pratique pour inscrire des applications OPC UA et émettre des certificats d’application signés pour vos appareils OPC UA avec OPC Vault.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996155"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>Guide pratique pour utiliser le service de gestion de certificats OPC Vault

Cet article explique comment inscrire des applications et émettre des certificats d’application signés pour vos appareils OPC UA.

## <a name="prerequisites"></a>Prérequis

### <a name="deploy-the-certificate-management-service"></a>Déployer le service de gestion de certificats

Tout d’abord, le service doit être déployé dans le cloud Azure.
Consultez l’article expliquant comment [déployer le service de gestion de certificats OPC Vault](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Créer le certificat d’autorité de certification d’émetteur

Si vous ne l’avez pas encore fait, créez le certificat d’autorité de certification d’émetteur.

Consultez l’article expliquant comment [créer et gérer le certificat d’émetteur pour OPC Vault](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Sécuriser des applications OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Étape 1 : Inscrire votre application OPC UA 

> [!IMPORTANT]
> Le rôle « Rédacteur » est exigé pour inscrire une application.

1. Ouvrez votre service de certificats à l’adresse `https://myResourceGroup-app.azurewebsites.net`, puis connectez-vous.
2. Accédez à la page `Register New`.
1. Pour une inscription d’application, un utilisateur doit disposer au moins du rôle « Rédacteur ».
2. Le formulaire d’entrée suit les conventions de nommage du monde OPC UA. À titre d’exemple, dans l’image ci-dessous, les paramètres de l’exemple de [Serveur de référence OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) de la pile .NetStandard OPC UA sont indiqués :

   ![Inscription d’un serveur de référence UA](media/howto-opc-vault-secure/reference-server-registration.png "Inscription d’un serveur de référence UA")

5. Appuyez sur le bouton `Register` pour inscrire l’application dans la base de données d’application de service de certificats. Le workflow guide l’utilisateur directement vers l’étape suivante destinée à demander un certificat signé pour l’application.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Étape 2 : Sécuriser votre application avec un certificat d’application signé par une autorité de certification

Une application OPC UA peut être sécurisée en émettant un certificat signé basé sur une demande de signature de certificat (CSR) ou en demandant une nouvelle paire de clés, qui inclut également une nouvelle clé privée au format PFX ou PEM. Suivez la documentation de votre appareil OPC UA sur lequel la méthode est prise en charge pour votre application. En général, la méthode CSR est recommandée car elle ne nécessite pas le transfert d’une clé privée sur un réseau.

- Si votre appareil exige l’émission d’une nouvelle paire de clés, suivez l’[étape 3a](##step-3a-request-a-new-certificate-with-a-new-keypair).
- Si votre appareil prend en charge l’émission d’une demande de signature de certificat (CSR), suivez l’[étape 3b](#step-3b-request-a-new-certificate-with-a-csr).

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>Étape 3a : Demander un nouveau certificat avec une nouvelle paire de clés

1. Accédez à la page `Applications`.
3. Démarrez le workflow de demande de certificat en choisissant `New Request` pour une application listée.

   ![Demander un nouveau certificat](media/howto-opc-vault-secure/request-new-certificate.png "Demander un nouveau certificat")

3. Appuyez sur « Request new KeyPair and Certificate » (Demander une nouvelle paire de clés et un nouveau certificat) pour demander une clé privée et un nouveau certificat signé avec la clé publique pour votre application.

   ![Générer une nouvelle paire de clés](media/howto-opc-vault-secure/generate-new-key-pair.png "Générer une nouvelle paire de clés")

4. Remplissez le formulaire avec un objet et les noms de domaine, puis choisissez PEM ou PFX avec un mot de passe pour la clé privée. Appuyez sur le bouton `Generate New Certificate` pour créer la demande de certificat.

   ![Approuver le certificat](media/howto-opc-vault-secure/approve-reject.png "Approuver le certificat")

5. L’étape d’approbation exige un utilisateur disposant du rôle « Approbateur » et d’autorisations de signature dans Azure Key Vault. Dans le workflow classique, les rôles Approbateur et Demandeur doivent être attribués à des utilisateurs distincts.
6. Approuvez (Approve) ou rejetez (Reject) la demande de certificat pour démarrer ou annuler la création réelle de la paire de clés et l’opération de signature. La nouvelle paire de clés est créée et stockée de manière sécurisé dans Azure Key Vault jusqu’à ce qu’elle soit téléchargée par le demandeur de certificat. Le certificat obtenu avec la clé publique est signé par l’autorité de certification. Ces opérations peuvent prendre quelques secondes.

   ![Afficher la paire de clés](media/howto-opc-vault-secure/view-key-pair.png "Afficher la paire de clés")

7. La clé privée (PFX ou PEM) et le certificat (DER) obtenus peuvent être téléchargés à partir de là au format sélectionné en tant que téléchargement de fichiers binaires. Une version encodée en base64 est également disponible, par exemple pour copier-coller le certificat dans une ligne de commande ou une entrée de texte. 
8. Une fois la clé privée téléchargée et stockée de manière sécurisée, elle peut être supprimée du service à l’aide du bouton `Delete Private Key`. Le certificat avec la clé publique reste disponible pour une utilisation ultérieure.
9. En raison de l’utilisation d’un certificat signé par une autorité de certification, le certificat et la liste de révocation de certificats de l’autorité de certification doivent également être téléchargés ici.
10. À présent, la façon dont la nouvelle paire de clés est appliquée dépend de l’appareil OPC UA. En général, le certificat et la liste de révocation de certificats de l’autorité de certification sont copiés dans un dossier `trusted`, tandis que la clé publique et la clé privée du certificat d’application sont appliquées à un dossier `own` dans le magasin de certificats. Certains appareils peuvent déjà prendre en charge la transmission des messages par le serveur (« Server Push ») pour les mises à jour des certificats. Reportez-vous à la documentation de votre appareil OPC UA.

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>Étape 3b : Demander un nouveau certificat avec une demande de signature de certificat (CSR) 

1. Accédez à la page `Applications`.
3. Démarrez le workflow de demande de certificat en choisissant `New Request` pour une application listée.

   ![Demander un nouveau certificat](media/howto-opc-vault-secure/request-new-certificate.png "Demander un nouveau certificat")

3. Appuyez sur « Request new Certificate with Signing Request » (Demander un nouveau certificat avec demande de signature) pour demander un nouveau certificat signé pour votre application.

   ![Générer un nouveau certificat](media/howto-opc-vault-secure/generate-new-certificate.png "Générer un nouveau certificat")

4. Chargez la demande de signature de certificat (CSR) en sélectionnant un fichier local ou en collant une demande de signature de certificat encodée en base64 dans le formulaire. Appuyez sur le bouton `Generate New Certificate` pour créer la demande de certificat.

   ![Approuver la demande de signature de certificat (CSR)](media/howto-opc-vault-secure/approve-reject-csr.png "Approuver la demande de signature de certificat (CSR)")

5. L’étape d’approbation exige un utilisateur disposant du rôle « Approbateur » et d’autorisations de signature dans Azure Key Vault. Approuvez (Approve) ou rejetez (Reject) la demande de certificat pour démarrer ou annuler la l’opération de signature réelle. Le certificat obtenu avec la clé publique est signé par l’autorité de certification. Cette opération peut prendre quelques secondes.

   ![Afficher le certificat](media/howto-opc-vault-secure/view-cert-csr.png "Afficher le certificat")

6. Le certificat (DER) obtenu peut être téléchargé à partir de là en tant que fichier binaire. Une version encodée en base64 est également disponible, par exemple pour copier-coller le certificat dans une ligne de commande ou une entrée de texte. 
10. Une fois le certificat téléchargé et stocké de manière sécurisée, il peut être supprimé du service à l’aide du bouton `Delete Certificate`.
11. En raison de l’utilisation d’un certificat signé par une autorité de certification, le certificat et la liste de révocation de certificats de l’autorité de certification doivent également être téléchargés ici.
12. À présent, la façon dont le nouveau certificat est appliqué dépend de l’appareil OPC UA. En général, le certificat et la liste de révocation de certificats de l’autorité de certification sont copiés dans un dossier `trusted`, tandis que le certificat d’application est appliqué à un dossier `own` dans le magasin de certificats. Certains appareils peuvent déjà prendre en charge la transmission des messages par le serveur (« Server Push ») pour les mises à jour des certificats. Reportez-vous à la documentation de votre appareil OPC UA.

### <a name="step-4-device-secured"></a>Étape 4 : Appareil sécurisé

L’appareil OPC UA est maintenant prêt à communiquer avec d’autres appareils OPC UA sécurisés par des certificats signés par l’autorité de certification sans configuration supplémentaire. Vous n’avez plus qu’à l’utiliser !


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à sécuriser des appareils OPC UA, voici la prochaine étape suggérée :

> [!div class="nextstepaction"]
> [Exécuter un service de gestion de certificats sécurisés](howto-opc-vault-secure-ca.md)