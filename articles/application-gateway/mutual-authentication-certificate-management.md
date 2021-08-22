---
title: Exporter une chaîne de certificats d'autorité de certification clients approuvés pour l'authentification client
titleSuffix: Azure Application Gateway
description: Apprenez à exporter une chaîne de certificats d'autorité de certification clients approuvés pour l'authentification client sur Azure Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 15655ba313a3a3cba23289c6d829523db6735129
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532211"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Exporter une chaîne de certificats d'autorité de certification clients approuvés à utiliser avec l'authentification client
Pour configurer l'authentification mutuelle avec le client, ou l'authentification client, Application Gateway exige qu'une chaîne de certificats d'autorité de certification clients approuvés soit chargée sur la passerelle. Si vous disposez de plusieurs chaînes de certificats, vous devez créer les chaînes séparément et les charger sous forme de fichiers distincts sur l'instance d'Application Gateway. Dans cet article, vous allez apprendre à exporter une chaîne de certificats d'autorité de certification clients approuvés que vous pourrez utiliser dans votre configuration d'authentification client sur votre passerelle.  

## <a name="prerequisites"></a>Prérequis

Un certificat client existant est requis pour générer la chaîne de certificats d'autorité de certification clients approuvés. 

## <a name="export-trusted-client-ca-certificate"></a>Exporter un certificat d'autorité de certification client approuvé

Un certificat d'autorité de certification client approuvé est requis pour activer l'authentification client sur Application Gateway. Dans cet exemple, nous allons utiliser un certificat TLS/SSL pour le certificat client, exporter sa clé publique, puis exporter les certificats d'autorité de certification à partir de la clé publique afin d'obtenir les certificats d'autorité de certification clients approuvés. Nous allons ensuite concaténer tous les certificats d'autorité de certification clients en une seule chaîne de certificats d'autorité de certification clients approuvés. 

Les étapes suivantes vous aideront à exporter le fichier .pem ou .cer de votre certificat :

### <a name="export-public-certificate"></a>Exporter un certificat public 

1. Pour obtenir un fichier .cer du certificat, ouvrez **Gérer les certificats utilisateur**. Recherchez le certificat, généralement dans « Certificats - Utilisateur actuel\Personnel\Certificats », puis cliquez dessus avec le bouton droit. Cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter**. Cette opération ouvre **l’Assistant Exportation de certificat**. Si vous ne trouvez pas le certificat sous Current User\Personal\Certificates, il est possible que vous ayez ouvert par erreur « Certificats – Ordinateur local » et non « Certificats – Utilisateur actuel ». Si vous voulez ouvrir le Gestionnaire de certificats dans le champ d’application utilisateur actuel en utilisant PowerShell, tapez *certmgr* dans la fenêtre de la console.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant le Gestionnaire de certificats avec l’option Certificats sélectionnée, et un menu contextuel avec les options Toutes les tâches, puis Exporter sélectionnées.](./media/certificates-for-backend-authentication/export.png)

2. Dans l’assistant, cliquez sur **Suivant**.
    > [!div class="mx-imgBorder"]
    > ![Exporter le certificat](./media/certificates-for-backend-authentication/exportwizard.png)

3. Sélectionnez **Non, ne pas exporter la clé privée**, puis cliquez sur **Suivant**.
    > [!div class="mx-imgBorder"]
    > ![N’exportez pas la clé privée](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Sur la page **Format de fichier d’exportation**, sélectionnez **Codé à base 64 X.509 (.cer).** , puis cliquez sur **Suivant**.
    > [!div class="mx-imgBorder"]
    > ![Encodage en base 64](./media/certificates-for-backend-authentication/base64.png)

5. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

    > [!div class="mx-imgBorder"]
   > ![Capture d’écran montrant l’Assistant Exportation de certificat dans lequel vous spécifiez un fichier à exporter.](./media/certificates-for-backend-authentication/browse.png)

6. Cliquez sur **Terminer** pour exporter le certificat.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant l’Assistant Exportation de certificat une fois l’exportation de fichier terminée.](./media/certificates-for-backend-authentication/finish-screen.png)

7. Votre certificat est correctement exporté.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant l’Assistant Exportation de certificat affichant un message de réussite.](./media/certificates-for-backend-authentication/success.png)

   Le certificat exporté ressemble à ceci :

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran montrant un symbole de certificat.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>Exporter des certificats d'autorité de certification à partir du certificat public

Maintenant que vous avez exporté votre certificat public, vous allez exporter le ou les certificats d'autorité de certification à partir de votre certificat public. Si vous ne disposez que d'une autorité de certification racine, seul ce certificat doit être exporté. En revanche, si vous disposez de plusieurs autorités de certification intermédiaires, vous devez également exporter chacune d'elles. 

1. Une fois la clé publique exportée, ouvrez le fichier.

    > [!div class="mx-imgBorder"]
    > ![certificat Open Authorization](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![à propos du certificat](./media/mutual-authentication-certificate-management/general.png)

1. Sélectionnez l'onglet Chemin d'accès de certification pour afficher l'autorité de certification.

    > [!div class="mx-imgBorder"]
    > ![détails du certificat](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Sélectionnez le certificat racine, puis cliquez sur **Afficher le certificat**.

    > [!div class="mx-imgBorder"]
    > ![chemin du certificat](./media/mutual-authentication-certificate-management/root-cert.png) 

   Vous devez pouvoir voir les détails du certificat racine.

    > [!div class="mx-imgBorder"]
    > ![informations du certificat](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Sélectionnez l'onglet **Détails**, puis cliquez sur **Copier dans un fichier...**

    > [!div class="mx-imgBorder"]
    > ![copier le certificat racine](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. À ce stade, vous avez extrait les détails du certificat d'autorité de certification racine à partir du certificat public. Vous voyez s’ouvrir l’**Assistant Exportation du certificat**. Suivez les étapes 2 à 7 de la section précédente ([Exporter un certificat public](./mutual-authentication-certificate-management.md#export-public-certificate)) pour terminer l'Assistant Exportation de certificat. 

1. Répétez ensuite les étapes 2 à 6 de la section actuelle ([Exporter des certificats d'autorité de certification à partir du certificat public](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) pour que toutes les autorités de certification intermédiaires exportent l'ensemble des certificats d'autorité de certification intermédiaires au format X.509 (.CER) encodé en base 64.

    > [!div class="mx-imgBorder"]
    > ![certificat intermédiaire](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Par exemple, vous pouvez répéter les étapes 2 à 6 de cette section sur l'autorité de certification intermédiaire *MSIT CAZ2* pour l'extraire en tant que certificat propre. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Concaténer tous vos certificats d'autorité de certification en un seul fichier

1. Exécutez la commande suivante avec tous les certificats d'autorité de certification que vous avez extraits précédemment. 

    Windows :
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux :
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Le certificat combiné obtenu doit être semblable au suivant :
    
    > [!div class="mx-imgBorder"]
    > ![certificat combiné](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Étapes suivantes

Vous disposez à présent de la chaîne de certificats d'autorité de certification clients approuvés. Vous pouvez l'ajouter à votre configuration d'authentification client sur l'instance d'Application Gateway afin d'activer l'authentification mutuelle sur votre passerelle. Consultez [Configurer l'authentification mutuelle sur Application Gateway via le portail](./mutual-authentication-portal.md) ou [Configurer l'authentification mutuelle sur Application Gateway via PowerShell](./mutual-authentication-powershell.md).

