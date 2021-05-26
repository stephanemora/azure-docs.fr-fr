---
title: 'Tutoriel : Configurer Azure Active Directory pour émettre des justificatifs vérifiables (préversion)'
description: Dans ce tutoriel, vous allez créer l’environnement nécessaire pour déployer des justificatifs vérifiables dans votre locataire.
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 05/18/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 4b48d23a7122c82cf149bdd80335afe0aaf896cb
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466403"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Tutoriel : Configurer Azure Active Directory pour émettre des justificatifs vérifiables (préversion)

Dans ce tutoriel, nous allons nous appuyer sur le travail effectué dans l’article [Prise en main](get-started-verifiable-credentials.md) et configurer Azure Active Directory (Azure AD) avec son propre [identificateur décentralisé](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID). Nous utilisons l’identificateur décentralisé pour émettre des justificatifs vérifiables à l’aide de l’exemple d’application et de votre émetteur. Toutefois, dans ce tutoriel, nous utilisons toujours l’exemple de locataire Azure B2C à des fins d’authentification.  Dans le tutoriel suivant, nous allons suivre des étapes supplémentaires pour configurer l’application de manière à fonctionner avec Azure AD.

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Contenu de cet article :

> [!div class="checklist"]
> * Vous créez les services nécessaires à l’intégration d’Azure AD pour les justificatifs vérifiables. 
> * Nous créons votre DID.
> * Nous personnalisons les règles et les fichiers d’affichage.
> * Vous configurez les justificatifs vérifiables dans Azure AD.


## <a name="prerequisites"></a>Prérequis

Avant de suivre ce tutoriel, il vous faut :

- Terminer la [prise en main](get-started-verifiable-credentials.md).
- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD avec une [licence](https://azure.microsoft.com/pricing/details/active-directory/) P2. Suivre la [procédure de création d’un compte de développeur gratuit](how-to-create-a-free-developer-account.md), si vous n’en avez pas.
- Une instance d’[Azure Key Vault](../../key-vault/general/overview.md) vous permettant de créer des clés et des secrets.

## <a name="azure-active-directory"></a>Azure Active Directory

Avant de commencer, il nous faut un locataire Azure AD. Une fois votre locataire activé pour les justificatifs vérifiables, il se voit attribuer un identificateur décentralisé (DID) et est activé avec un service émetteur pour émettre des justificatifs vérifiables. Tous les justificatifs vérifiables que vous émettez sont émis par votre locataire et son DID. Le DID est également utilisé lors de la vérification des justificatifs vérifiables.
Si vous venez de créer un abonnement Azure de test, votre locataire ne doit pas être renseigné avec des comptes d’utilisateur, mais il vous faut avoir au moins un compte de test utilisateur pour suivre les tutoriels ultérieurs.

## <a name="create-a-key-vault"></a>Créer un coffre de clés

Les justificatifs vérifiables vous permettent de contrôler et de gérer les clés de chiffrement utilisées par votre locataire pour signer numériquement les justificatifs vérifiables. Pour émettre et vérifier les justificatifs, vous devez accorder à Azure AD un accès à votre propre instance Azure Key Vault.

1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**.
2. Dans la zone de recherche, entrez **key vault**.
3. Dans la liste des résultats, choisissez **Key Vault**.
4. Dans la section Key Vault, choisissez **Créer**.
5. Dans la section **Créer un coffre de clés**, renseignez les informations suivantes :
    - **Abonnement**: Choisissez un abonnement.
    - Sous **Groupe de ressources**, sélectionnez **Créer** et entrez un nom de groupe de ressources tel que **vc-resource-group**. Nous utilisons le même nom de groupe de ressources dans plusieurs articles.
    - **Name** : un nom unique est obligatoire. Nous utilisons **woodgroup-vc-kv** et dès lors, remplacez cette valeur par votre propre nom unique.
    - Dans le menu déroulant **Emplacement**, choisissez un emplacement.
    - Conservez les valeurs par défaut des autres options.
6. Après avoir renseigné les informations ci-dessus, sélectionnez **Stratégie d’accès**.

    ![Créer une page Key Vault](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Dans l’écran Stratégie d’accès, sélectionnez **Ajouter une stratégie d’accès**.

    >[!NOTE]
    > Par défaut, le compte qui crée le coffre de clés est le seul à pouvoir y accéder. Le service de justificatifs vérifiables doit avoir accès au coffre de clés. Le coffre de clés doit disposer d’une stratégie d’accès permettant à l’administrateur de **créer des clés**, de **supprimer des clés** si vous l’avez refusé et de **signer** pour créer la liaison de domaine pour les justificatifs vérifiables. Si vous utilisez le même compte lors du test, veillez à modifier la stratégie par défaut pour permettre au compte de **se connecter** en plus des autorisations par défaut accordées aux créateurs de coffre.

8. Pour l’administrateur utilisateur, assurez-vous que les options **Créer**, **Supprimer** et **Signer** sont activées pour la section Autorisations de clé. Par défaut, les options Créer et Supprimer sont déjà activées et Signer doit être la seule autorisation de clé à mettre à jour. 

    ![Autorisations d’accès au coffre de clés](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Sélectionnez **Revoir + créer**.
10. Sélectionnez **Create** (Créer).
11. Accédez au coffre et notez son nom et son URI.

Notez les deux propriétés ci-dessous :

- **Nom du coffre** : dans l’exemple, il s’agit de **woodgrove-vc-kv**. Vous utilisez ce nom pour les autres étapes.
- **URI du coffre** : dans l’exemple, il s’agit de https://woodgrove-vc-kv.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

>[!NOTE]
> Chaque transaction de coffre de clés entraîne des coûts d’abonnement Azure supplémentaires. Pour plus d’informations, consultez la [page de tarification Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

>[!IMPORTANT]
> Dans le cadre de la préversion des justificatifs vérifiables Azure Active Directory, les clés et secrets créés dans votre coffre ne doivent pas être modifiés. La suppression, la désactivation ou la mise à jour de vos clés et secrets invalide les justificatifs émis. Ne modifiez pas vos clés ou secrets dans le cadre de la préversion.

## <a name="create-a-modified-rules-and-display-file"></a>Créer un fichier de règles et d’affichage modifié

Dans cette section, nous utilisons les fichiers de règles et d’affichage de l’[exemple d’application émettrice](https://github.com/Azure-Samples/active-directory-verifiable-credentials/) et les modifions légèrement pour créer les premiers justificatifs vérifiables de votre locataire.

1. Copiez les fichiers de règles et d’affichage JSON dans un dossier temporaire, puis renommez-les respectivement **MyFirstVC-display.json** et **MyFirstVC-rules.json**. Ces deux fichiers se trouvent sous **issuer\issuer_config**.

   ![Fichiers de règles et d’affichage dans l’exemple de répertoire d’application](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![Fichiers de règles et d’affichage dans un dossier temporaire](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Ouvrez le fichier MyFirstVC-rules.json dans votre éditeur de code. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

   Remplacez le champ de type par « MyFirstVC ». 

   ```json
    "type": ["MyFirstVC"]
  
   ```

   Enregistrez cette modification.

   >[!NOTE]
   > À ce stade du tutoriel, nous ne modifions pas la **« configuration »** ou le **« client_id »** . Nous utilisons le même locataire Microsoft B2C que celui utilisé lors de la [Prise en main](get-started-verifiable-credentials.md). Nous utiliserons votre instance Azure AD dans le tutoriel suivant.

3. Ouvrez le fichier MyFirstVC-display.json dans votre éditeur de code.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

   Apportons-y quelques modifications de sorte que ces justificatifs vérifiables se distinguent de la version de l’exemple de code. 

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > Pour vérifier que vos informations d’identification sont lisibles et accessibles, nous vous recommandons vivement de sélectionner des couleurs de texte et d’arrière-plan avec un [rapport de contraste](https://www.w3.org/WAI/WCAG21/Techniques/general/G18) d’au moins 4,5 pour 1.  

   Enregistrez ces modifications.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Avant de créer nos premiers justificatifs vérifiables, il nous faut créer un conteneur Stockage Blob destiné à nos fichiers de configuration et de règles.

1. Créez un compte de stockage à l’aide des options ci-dessous. Pour obtenir des instructions détaillées, consultez l’article [Créer un compte de stockage](../../storage/common/storage-account-create.md?tabs=azure-portal).

   - **Abonnement :** sélectionnez l’abonnement que nous utilisons pour ces tutoriels.
   - **Groupe de ressources :** sélectionnez le même groupe de ressources que celui utilisé dans les tutoriels précédents (**vc-resource-group**).
   - **Nom** : nom unique.
   - **Emplacement :** (États-Unis) USA Est
   - **Performances :** standard.
   - **Type de compte :** stockage V2.
   - **Réplication :** redondante localement.
 
   ![Création d’un nouveau compte de stockage](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Après avoir créé le compte de stockage, nous devons créer un conteneur. Sélectionnez **Conteneurs** sous **Stockage Blob** et créez un conteneur à l’aide des valeurs fournies ci-dessous :

    - **Nom :** vc-container
    - **Niveau d’accès public :** privé (aucun accès anonyme)

   ![Créez un conteneur.](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Sélectionnez maintenant votre nouveau conteneur et chargez les nouveaux fichiers de règles et d’affichage **MyFirstVC-display.json** et **MyFirstVC-rules.json** que nous avons créés précédemment.

   ![Charger le fichier des règles](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Affecter un rôle blob

Avant de créer les justificatifs vérifiables, il nous faut donner à l’utilisateur connecté le rôle qui convient pour lui permettre d’accéder aux fichiers dans le Stockage Blob.

1. Accédez à **Stockage** > **Conteneur**.
2. Sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche.
3. Sélectionnez **Attributions de rôles**.
4. Sélectionnez **Ajouter**.
5. Dans la section **Rôle**, sélectionnez **Lecteur des données blob du stockage**.
6. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal de service**.
7. Dans **Sélectionner**, choisissez le compte que vous utilisez pour effectuer ces étapes.
8. Sélectionnez **Enregistrer** pour terminer l’attribution de rôle.


   ![Ajouter une attribution de rôle](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Par défaut, les créateurs de conteneurs se voient attribuer le rôle **Propriétaire**. Le rôle **Propriétaire** n’est pas suffisant. Votre compte doit disposer du rôle **Lecteur des données blob du stockage**. Pour plus d’informations, consultez [Utiliser le portail Azure afin d’attribuer un rôle Azure pour l’accès aux données de blob et de file d’attente](../../storage/common/storage-auth-aad-rbac-portal.md).

## <a name="set-up-verifiable-credentials-preview"></a>Configurer les justificatifs vérifiables (préversion)

À présent, nous devons effectuer la dernière étape pour configurer votre locataire pour les justificatifs vérifiables.

1. À partir du portail Azure, recherchez des **justificatifs vérifiables**.
2. Sélectionnez **Justificatifs vérifiables (préversion)** .
3. Sélectionnez **Démarrage**.
4. Nous devons configurer votre organisation et indiquer le nom, le domaine et le coffre de clés correspondants. Intéressons-nous à chacune des valeurs.

      - **Nom de l’organisation** : ce nom correspond à la manière dont vous faites référence à votre entreprise dans le service de justificatifs vérifiables. Cette valeur n’est pas destinée aux clients.
      - **Domaine :** le domaine entré est ajouté à un point de terminaison de service dans votre document DID. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) et d’autres portefeuilles utilisent ces informations pour s’assurer que votre DID est [lié à votre domaine](how-to-dnsbind.md). Si le portefeuille peut vérifier le DID, il affiche un symbole vérifié. Dans le cas contraire, il informe l’utilisateur que des justificatifs ont été émis par une organisation qu’il n’a pas pu valider. Le domaine est ce qui lie votre DID à un élément tangible que l’utilisateur peut connaître sur votre entreprise.
      - **Coffre de clés :** indiquez le nom du coffre de clés que nous avons créé précédemment.
 
   >[!IMPORTANT]
   > Le domaine ne peut pas correspondre à une redirection, sinon, le DID et le domaine ne peuvent pas être liés. Veillez à utiliser le format https://www.domain.com.
    
5. Sélectionnez **Enregistrer et créer des justificatifs**.

    ![Configurer l’identité de votre organisation](media/enable-your-tenant-verifiable-credentials/save-create.png)

Félicitations, votre locataire est maintenant activé pour la préversion des justificatifs vérifiables.

## <a name="create-your-vc-in-the-portal"></a>Créer vos justificatifs vérifiables dans le portail

Au terme de l’étape précédente, vous vous trouvez dans la page **Créer des justificatifs**. 

   ![Prise en main des justificatifs vérifiables](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Sous Nom des justificatifs, ajoutez le nom **MyFirstVC**. Dans le portail, ce nom permet d’identifier vos justificatifs vérifiables et fait partie du contrat en lien avec les justificatifs vérifiables.
2. Dans la section Fichier d’affichage, sélectionnez **Configurer le fichier d’affichage**.
3. Dans la section **Comptes de stockage**, sélectionnez **woodgrovevcstorage**.
4. Dans la liste des conteneurs disponibles, sélectionnez **vc-container**.
5. Sélectionnez le fichier **MyFirstVC-display.json** que nous avons créé précédemment.
6. Dans la section **Fichier de règles**, sous **Créer des justificatifs**, sélectionnez **Configurer un fichier de règles**.
7. Dans la section **Comptes de stockage**, sélectionnez **woodgrovecstorage**.
8. Sélectionnez **vc-container**.
9. Sélectionnez **MyFirstVC-rules.json**.
10. Dans l’écran **Créer des justificatifs**, sélectionnez **Créer**.

   ![Créer des justificatifs](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>URL des justificatifs

Maintenant que vous disposez de nouveaux justificatifs, copiez l’URL correspondante.

   ![URL des justificatifs](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>L’URL des justificatifs est la combinaison des fichiers de règles et d’affichage. Il s’agit de l’URL évaluée par Authenticator avant d’afficher à l’utilisateur les exigences d’émission des justificatifs vérifiables.  

## <a name="update-the-sample-app"></a>Créer un exemple d'application

À présent, nous apportons des modifications au code émetteur de l’exemple d’application pour le mettre à jour avec votre URL de justificatifs vérifiables. Vous pouvez ainsi émettre des justificatifs vérifiables à l’aide de votre propre locataire.

1. Accédez au dossier où vous avez placé les fichiers de l’exemple d’application.
2. Ouvrez le dossier émetteur, puis app.js dans Visual Studio Code.
3. Mettez à jour la constante « credential » avec votre nouvelle URL de justificatifs et définissez la constante credentialType sur « MyFirstVC », puis enregistrez le fichier.

    ![Image de Visual Studio Code présentant les zones pertinentes mises en surbrillance](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Ouvrez une invite de commandes, puis le dossier de l’émetteur.
5. Exécutez l’application de nœud mise à jour.

    ```terminal
    node app.js
    ```

6. À l’aide d’une autre invite de commandes, exécutez ngrok pour configurer une URL sur 8081. Vous pouvez installer ngrok globalement à l’aide du [package npm ngrok](https://www.npmjs.com/package/ngrok/).

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Vous pouvez également remarquer un avertissement indiquant que cette application ou ce site web peut être risqué. Ce message est attendu pour l’instant, car nous n’avons pas encore lié votre DID à votre domaine. Suivez les instructions de [Liaison DNS](how-to-dnsbind.md) pour configurer cela.

    
7. Ouvrez l’URL HTTPS générée par ngrok.

    ![Points de terminaison de transfert NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Sélectionnez **GET CREDENTIAL**
9. Dans Authenticator, scannez le code QR.
10. Lorsque le message d’avertissement **Cette application ou ce site web peut être risqué** s’affiche, sélectionnez **Avancé**.

  ![Avertissement initial](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. Dans l’avertissement de site web risqué, sélectionnez **Continuer quand même (non sécurisé)** .

  ![Deuxième avertissement relatif à l’émetteur](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. Dans l’écran **Ajouter des justificatifs**, notez quelques points : 
    1. En haut de l’écran, vous pouvez voir un message rouge **Non vérifié**
    1. Les justificatifs sont personnalisés en fonction des modifications que nous avons apportées au fichier d’affichage.
    1. L’option **Se connecter à votre compte** pointe vers **didplayground.b2clogin.com**.
    
   ![Écran Ajouter des justificatifs avec avertissement](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Sélectionnez **Se connecter à votre compte** et authentifiez-vous à l’aide des informations d’identification fournies dans le [tutoriel de prise en main](get-started-verifiable-credentials.md).
14. Une fois l’authentification réussie, le bouton **Ajouter** n’est plus grisé. Sélectionnez **Ajouter**.

  ![Écran Ajouter des justificatifs après authentification](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Nous avons maintenant publié des justificatifs vérifiables à l’aide de notre locataire pour générer nos justificatifs vérifiables tout en utilisant notre locataire B2C à des fins d’authentification.

  ![Justificatifs vérifiables émis par votre locataire Azure AD et authentifiés par notre instance Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Tester la vérification des justificatifs vérifiables à l’aide de l’exemple d’application

Maintenant que nous avons émis les justificatifs vérifiables depuis notre propre locataire, nous allons les vérifier à l’aide de notre exemple d’application.

>[!IMPORTANT]
> Lors du test, utilisez l’adresse de messagerie et le mot de passe que vous avez utilisés dans l’article [Prise en main](get-started-verifiable-credentials.md). Pendant que votre locataire émet les justificatifs vérifiables, l’entrée provient d’un jeton d’ID émis par le locataire Microsoft B2C. Dans le tutoriel deux, nous basculons l’émission de jetons vers votre locataire

1. Ouvrez **Paramètres** dans le panneau des justificatifs vérifiables du portail Azure. Copiez l’identificateur décentralisé (DID).

   ![Copier le DID](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Ouvrez maintenant le dossier du vérificateur dans les exemples de fichiers d’application. Nous devons mettre à jour le fichier app.js dans l’exemple de code du vérificateur et apporter les modifications suivantes :

    - **credential** : entrez l’URL de vos justificatifs
    - **credentialType** : « MyFirstVC »
    - **issuerDid** : copiez cette valeur depuis le portail Azure>Justificatifs vérifiables (préversion)>Paramètres>Identificateur décentralisé (DID)
    
   ![Mettre à jour la constante issuerDid pour qu’elle corresponde à l’identificateur de l’émetteur](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Arrêtez l’exécution du service ngrok de l’émetteur.

    ```cmd
    control-c
    ```

4. Exécutez maintenant ngrok avec le port vérificateur 8082.

    ```cmd
    ngrok http 8082
    ```

5. Dans une autre fenêtre de terminal, accédez à l’application du vérificateur et exécutez-la de la même façon que nous avons exécuté l’application émettrice.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Ouvrez l’URL ngrok dans votre navigateur et scannez le code QR à l’aide d’Authenticator sur votre appareil mobile.
7. Sur votre appareil mobile, sélectionnez **Autoriser** dans l’écran **Nouvelle demande d’autorisation**.

    >[!NOTE]
    > Le DID qui signe ces justificatifs vérifiables est toujours Microsoft B2C. Le DID vérificateur est toujours issu du locataire de l’exemple d’application Microsoft. Le DID Microsoft ayant été lié à un domaine dont nous sommes propriétaires, aucun avertissement similaire à celui affiché lors du processus d’émission n’apparaît. Il sera mis à jour dans la section suivante.
    
   ![Nouvelle demande d’autorisation](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Vous n’avez pas réussi à vérifier vos justificatifs.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez de l’exemple de code émettant des justificatifs vérifiables à partir de votre émetteur, passez à la section suivante où vous utiliserez votre propre fournisseur d’identité pour authentifier les utilisateurs qui tentent d’obtenir des justificatifs vérifiables et utiliser votre DID pour signer les demandes de présentation.

> [!div class="nextstepaction"]
> [Tutoriel : Émettre et vérifier des justificatifs vérifiables à l’aide de votre locataire](issue-verify-verifiable-credentials-your-tenant.md)


