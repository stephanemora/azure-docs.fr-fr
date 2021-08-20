---
title: 'Tutoriel : configurer Azure Active Directory pour émettre des justificatifs vérifiables (préversion)'
description: Dans ce tutoriel, vous allez créer l’environnement nécessaire pour déployer des justificatifs vérifiables dans votre abonné.
documentationCenter: ''
author: barclayn
manager: daveba
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 06/24/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 7b50e17a8c6730aedbc8fea68a2ab4d8685b2fa5
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228975"
---
# <a name="tutorial---configure-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Tutoriel : configurer Azure Active Directory pour émettre des justificatifs vérifiables (préversion)

Dans ce tutoriel, vous allez vous appuyer sur le travail effectué dans l’article [Démarrage](get-started-verifiable-credentials.md) et configurer Azure Active Directory (Azure AD) avec son propre [identificateur décentralisé](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID). Vous utiliserez le DID pour émettre des informations d’identification vérifiables à l’aide de l’exemple d’application et de votre émetteur. Dans ce tutoriel, vous allez continuer à utiliser l’exemple d’abonné Azure B2C pour l’authentification. Dans le tutoriel suivant, vous allez prendre des mesures pour configurer l’application de manière à fonctionner avec Azure AD.

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans cet article, vous découvrirez comment :

> [!div class="checklist"]
> * Créez les services nécessaires à l’intégration d’Azure AD pour les justificatifs vérifiables.
> * Créez votre DID.
> * Personnalisez les règles et les fichiers d’affichage.
> * Vous configurez les justificatifs vérifiables dans Azure AD.

## <a name="prerequisites"></a>Prérequis

Avant de suivre ce tutoriel, il vous faut :

- Effectuez les étapes décrites dans le tutoriel [Démarrage](get-started-verifiable-credentials.md).
- Vous devez disposer d’un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD avec une [licence](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) P2. Si vous n’en avez pas, suivez les étapes dans [Créer un compte de développeur gratuit](how-to-create-a-free-developer-account.md).
- Disposez d’une instance [Azure Key Vault](../../key-vault/general/overview.md) vous permettant de créer des clés et des secrets.

## <a name="azure-active-directory"></a>Azure Active Directory

Avant de commencer, vous avez besoin d’un abonné Azure AD. Lorsque votre abonné est activé pour les justificatifs vérifiables, un DID lui est attribué. Il est également activé avec un service d’émetteur pour l’émission de justificatifs vérifiables. Tous les justificatifs vérifiables que vous émettez sont émis par votre locataire et son DID. Le DID est également utilisé lors de la vérification des justificatifs vérifiables.

Si vous venez de créer un abonnement Azure de test, votre abonné n’a pas besoin de renseigner des comptes utilisateur. Vous aurez besoin d’au moins un compte de test utilisateur pour effectuer des tutoriels ultérieurs.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Lorsque vous utilisez des justificatifs vérifiables, vous pouvez contrôler et gérer complètement les clés de chiffrement utilisées par votre abonné pour signer numériquement les justificatifs vérifiables. Pour émettre et vérifier les informations d'identification, vous devez accorder à Azure AD un accès à votre propre instance de Key Vault.

1. Dans le menu du Portail Azure ou dans la page Accueil, sélectionnez **Créer une ressource**.
1. Dans la zone de recherche, entrez **coffre de clés**.
1. Dans la liste des résultats, sélectionnez **Key Vault**.
1. Dans la section **Key Vault**, sélectionnez **Créer**.
1. Dans la section **Créer un coffre de clés**, renseignez les informations suivantes :
    - **Abonnement** : sélectionnez un abonnement.
    - **Groupe de ressources** : sélectionnez **Créer un nouveau** et entrez un nom de groupe de ressources tel que **vc-resource-group**. Nous utilisons le même nom de groupe de ressources dans plusieurs articles.
    - **Name** : un nom unique est obligatoire. Nous utilisons **woodgroup-vc-kv**, remplacez donc cette valeur par votre propre nom unique.
    - **Emplacement** : Sélectionnez un emplacement.
    - Conservez les valeurs par défaut des autres options.
1. Après avoir fourni les informations, sélectionnez **Stratégie d’accès**.

    ![Capture d’écran montrant l’écran Créer un coffre de clés.](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

1. Dans l’écran **Stratégie d’accès**, sélectionnez **Ajouter une stratégie d’accès**.

    >[!NOTE]
    > Par défaut, le compte qui crée le coffre de clés est le seul à pouvoir y accéder. Le service de justificatifs vérifiables doit avoir accès au coffre de clés. Le coffre de clés doit disposer d’une stratégie d’accès permettant à l’administrateur de créer des clés, de supprimer des clés si vous l’avez refusé et de se connecter pour créer la liaison de domaine pour les justificatifs vérifiables. Si vous utilisez le même compte lors du test, veillez à modifier la stratégie par défaut pour permettre au compte de **Signer** en plus des autorisations par défaut accordées aux créateurs de coffre.

1. Pour l’administrateur utilisateur, assurez-vous que les options **Créer**, **Supprimer** et **Signer** sont activées dans la section Autorisations de clé. Par défaut, les paramètres **Créer** et **Supprimer** sont déjà activés. **Signer** doit être la seule autorisation de clé que vous devez mettre à jour.

    ![Capture d’écran montrant les autorisations du coffre de clés.](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

1. Sélectionnez **Revoir + créer**.
1. Sélectionnez **Create** (Créer).
1. Accédez au coffre et notez son nom et son URI.

Prenez note des deux propriétés suivantes :

- **Nom du coffre** : dans l’exemple, le nom du coffre de clés est **woodgrove-vc-kv**. Vous utilisez ce nom pour les autres étapes.
- **URI du coffre** : dans l’exemple, il s’agit de https://woodgrove-vc-kv.vault.azure.net/. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

>[!NOTE]
> Chaque transaction de coffre de clés entraîne des coûts d’abonnement Azure supplémentaires. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

>[!IMPORTANT]
> Dans le cadre de la préversion des justificatifs vérifiables Azure Active Directory, ne modifiez pas les clés et secrets créés dans votre coffre. La suppression, la désactivation ou la mise à jour de vos clés et secrets invalide les justificatifs émis.

## <a name="create-modified-rules-and-display-files"></a>Créer des règles et des fichiers d’affichage modifiés

Dans cette section, vous utilisez les règles et les fichiers d’affichage de l’[exemple d’application émettrice](https://github.com/Azure-Samples/active-directory-verifiable-credentials/) et les modifiez légèrement pour créer les premiers justificatifs vérifiables de votre abonné.

1. Copiez à la fois les règles et les fichiers JSON d’affichage dans un dossier temporaire. Renommez-les **MyFirstVC-display.json** et **MyFirstVC-rules.json** respectivement. Ces deux fichiers se trouvent sous **issuer\issuer_config**.

   ![Capture d’écran montrant les règles et les fichiers d’affichage dans le cadre de l’exemple de répertoire d’application.](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![Capture d’écran montrant les règles et les fichiers d’affichage dans un dossier temporaire.](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

1. Ouvrez le fichier **MyFirstVC-rules.json** dans votre éditeur de code.

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

   Remplacez le champ « type » par **« MyFirstVC »** .

   ```json
    "type": ["MyFirstVC"]
  
   ```

   Enregistrez cette modification.

   >[!NOTE]
   > À ce stade du tutoriel, vous ne modifiez pas les valeurs « configuration » ou « client_id ». Vous utiliserez toujours l’exemple d’abonné Azure que vous avez utilisé dans le tutoriel [Démarrage](get-started-verifiable-credentials.md). Vous utiliserez Azure AD dans le tutoriel suivant.

1. Ouvrez le fichier **MyFirstVC-display.json** dans votre éditeur de code.

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

   Apportez quelques modifications de sorte que ces justificatifs vérifiables se distinguent de la version de l’exemple de code.

    ```json
         "card": {
            "title": "My First VC",
            "issuedBy": "Your Issuer Name",
            "backgroundColor": "#ffffff",
            "textColor": "#000000",
          }
    ```
 
   >[!NOTE]
   > Pour vérifier que vos informations d’identification sont lisibles et accessibles, sélectionnez des couleurs de texte et d’arrière-plan avec un [rapport de contraste](https://www.w3.org/WAI/WCAG21/Techniques/general/G18) d’au moins 4,5 pour 1.

   Enregistrez ces modifications.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Avant de créer vos premiers justificatifs vérifiables, il vous faut créer un conteneur de Stockage blob Azure destiné à vos fichiers de configuration et de règles.

1. Créez un compte de stockage à l’aide des options affichées. Pour obtenir des instructions détaillées, consultez [Créer un compte de stockage](../../storage/common/storage-account-create.md?tabs=azure-portal).

   - **Abonnement** : l’abonnement que vous utilisez pour ces tutoriels.
   - **Groupe de ressources** : le même groupe de ressources que celui utilisé dans les tutoriels précédents (**vc-resource-group**).
   - **Nom** : nom unique
   - **Emplacement** : (États-Unis) USA Est
   - **Performances** : standard
   - **Type de compte** : stockage V2
   - **Réplication** : redondante localement
 
   ![Capture d’écran montrant l’écran Créer un compte de stockage.](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

1. Après avoir créé le compte de stockage, créez un conteneur. Sous **Stockage Blob**, sélectionnez **Conteneurs**. Créez un conteneur à l’aide des valeurs suivantes :

    - **Nom** : vc-container
    - **Niveau d’accès public** : privé (aucun accès anonyme)
  
   Sélectionnez **Create** (Créer).

   ![Capture d’écran montrant la création d’un récepteur.](media/enable-your-tenant-verifiable-credentials/new-container.png)

1. Sélectionnez maintenant votre nouveau conteneur et chargez les nouvelles règles et fichiers d’affichage **MyFirstVC-display.json** et **MyFirstVC-rules.json** créés précédemment.

   ![Capture d’écran montrant le chargement des fichiers de règles.](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-a-blob-role"></a>Affecter un rôle blob

Avant de créer les justificatifs vérifiables, vous devez avant tout donner à l’utilisateur connecté le rôle qui convient pour lui permettre d’accéder aux fichiers dans le Stockage blob.

1. Accédez à **Stockage** > **Conteneur**.
1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter** > **Ajouter une attribution de rôle** pour ouvrir la page **Ajouter une attribution de rôle**.
1. Attribuez le rôle suivant. Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../role-based-access-control/role-assignments-portal.md).
    
    | Paramètre | Valeur |
    | --- | --- |
    | Role | Lecteur des données blob du stockage |
    | Attribuer l’accès à | Utilisateur, groupe ou principal de service |
    | Sélectionnez | &lt;compte que vous utilisez pour effectuer ces étapes&gt; |

    ![Capture d’écran représentant la page Ajouter une attribution de rôle dans le Portail Azure.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

  >[!IMPORTANT]
  >Par défaut, les créateurs de conteneurs se voient attribuer le rôle Propriétaire. Le rôle Propriétaire n’est pas suffisant en lui-même. Votre compte doit disposer du rôle Lecteur des données blob du stockage. Pour plus d’informations, consultez [Utiliser le portail Azure afin d’attribuer un rôle Azure pour l’accès aux données de blob et de file d’attente](../../storage/blobs/assign-azure-role-data-access.md).

## <a name="set-up-verifiable-credentials-preview"></a>Configurer les justificatifs vérifiables (préversion)

À présent, nous devons effectuer la dernière étape pour configurer votre abonné pour les justificatifs vérifiables.

1. À partir du Portail Azure, recherchez les **justificatifs vérifiables**.
1. Sélectionnez **Justificatifs vérifiables (préversion)** .
1. Sélectionnez **Prise en main**.
1. Configurez votre organisation en fournissant les informations suivantes :

      - **Nom de l’organisation** : entrez un nom pour faire référence à votre entreprise dans le cadre des justificatifs vérifiables. Cette valeur n’est pas destinée aux clients.
      - **Domaine :** entrez un domaine ajouté à un point de terminaison de service dans votre document DID. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) et d’autres portefeuilles utilisent ces informations pour s’assurer que votre DID est [lié à votre domaine](how-to-dnsbind.md). Si le portefeuille peut vérifier le DID, il affiche un symbole vérifié. Dans le cas contraire, il informe l’utilisateur que des justificatifs ont été émis par une organisation qu’il n’a pas pu valider. Le domaine est ce qui lie votre DID à un élément tangible que l’utilisateur peut connaître sur votre entreprise.
      - **Coffre de clés :** indiquez le nom du coffre de clés que vous avez créé précédemment.

   >[!IMPORTANT]
   > Le domaine ne peut pas être une redirection. Dans le cas contraire, le DID et le domaine ne peuvent pas être liés. Veillez à utiliser le format https://www.domain.com.
  
1. Sélectionnez **Enregistrer et créer des informations d’identification**.

    ![Capture d’écran montrant la configuration de l’identité de votre organisation.](media/enable-your-tenant-verifiable-credentials/save-create.png)

Félicitations, votre abonné est maintenant activé pour la préversion des justificatifs vérifiables !

## <a name="create-your-verifiable-credential-in-the-portal"></a>Créer vos justificatifs vérifiables dans le portail

Au terme de l’étape précédente, l’écran **Créer de nouvelles informations d'identification** s’affiche.

   ![Capture d’écran montrant l’écran Démarrage.](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Pour le nom des informations d’identification, entrez **MyFirstVC**. Ce nom est utilisé dans le Portail pour identifier vos justificatifs vérifiables. Il est inclus dans le cadre du contrat des justificatifs vérifiables.
1. Dans la section **Fichier d’affichage**, sélectionnez **Configurer le fichier d’affichage**.
1. Dans la section **Comptes de stockage**, sélectionnez **woodgrovevcstorage**.
1. Dans la liste des conteneurs disponibles, sélectionnez **vc-container**.
1. Sélectionnez le fichier **MyFirstVC-display.json** que vous avez créé précédemment.
1. Dans la section **Créer de nouvelles informations d’identification**, dans la section **Fichier de règles**, sélectionnez **Configurer un fichier de règles**.
1. Dans la section **Comptes de stockage**, sélectionnez **woodgrovecstorage**.
1. Sélectionnez **vc-container**.
1. Sélectionnez **MyFirstVC-rules.json**.
1. Dans l’écran **Créer de nouvelles informations d’identification**, sélectionnez **Créer**.

   ![Capture d’écran montrant la création de nouvelles informations d’identification.](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>URL des justificatifs

Maintenant que vous disposez de nouveaux justificatifs, copiez l’URL correspondante.

   ![Capture d’écran montrant l’URL des informations d'identification.](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>L’URL des justificatifs est la combinaison des fichiers de règles et d’affichage. Il s’agit de l’URL évaluée par Authenticator avant d’afficher pour l’utilisateur les exigences d’émission des justificatifs vérifiables.

## <a name="update-the-sample-app"></a>Créer un exemple d'application

À présent, vous apportez des modifications au code émetteur de l’exemple d’application pour le mettre à jour avec votre URL de justificatifs vérifiables. Vous pouvez ainsi émettre des justificatifs vérifiables à l’aide de votre propre abonné.

1. Accédez au dossier où vous avez placé les fichiers de l’exemple d’application.
1. Ouvrez le dossier émetteur, puis app.js dans Visual Studio Code.
1. Mettez à jour les **informations d’identification** constantes avec votre nouvelle URL d’informations d’identification. Définissez la constante **CredentialType** sur **« MyFirstVC »** , puis enregistrez le fichier.

    ![Capture d’écran qui montre Visual Studio Code avec les zones importantes mises en évidence.](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

1. Ouvrez une invite de commandes, puis le dossier de l’émetteur.
1. Exécutez l’application de nœud mise à jour.

    ```terminal
    node app.js
    ```

1. À l’aide d’une autre invite de commandes, exécutez ngrok pour configurer une URL sur 8081. Vous pouvez installer ngrok globalement à l’aide du [package npm ngrok](https://www.npmjs.com/package/ngrok/).

    ```terminal
    ngrok http 8081
    ```

    >[!IMPORTANT]
    > Vous pouvez voir un avertissement indiquant que « cette application ou ce site web peut être risqué ». Ce message est attendu pour l’instant, car vous n’avez pas encore lié votre DID à votre domaine. Pour en savoir plus sur la procédure de configuration, consultez [Liaison DNS](how-to-dnsbind.md).

1. Ouvrez l’URL HTTPS générée par ngrok.

    ![Capture d’écran montrant les points de terminaison de transfert NGROK.](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

1. Sélectionnez **GET CREDENTIAL**.
1. Dans Authenticator, scannez le code QR.
1. Dans l’écran **Cette application ou ce site web est peut-être risqué**, sélectionnez **Avancé**.

   ![Capture d’écran qui montre l’avertissement initial.](media/enable-your-tenant-verifiable-credentials/site-warning.png)

1. Sur l’écran **Cette application ou ce site web peut être risqué** suivant, sélectionnez **Continuer quand même (non sécurisé)** .

   ![Capture d’écran montrant le deuxième avertissement relatif à l’émetteur.](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)

1. Dans l’écran **Ajouter des informations d'identification**, notez quelques points :
    1. En haut de l’écran, vous pouvez voir un message rouge **Non vérifié**.
    1. Les informations d'identification sont personnalisées en fonction des modifications que vous avez apportées au fichier d’affichage.
    1. L’option **Se connecter à votre compte** pointe vers didplayground.b2clogin.com.
    
      ![Capture d’écran montrant l’écran Ajouter des informations d’identification avec un avertissement.](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

1. Sélectionnez **Se connecter à votre compte** et authentifiez-vous à l’aide des informations d’identification fournies dans le tutoriel [Démarrage](get-started-verifiable-credentials.md).
1. Une fois l’authentification réussie, le bouton **Ajouter** est maintenant activé. Sélectionnez **Ajouter**.

    ![Capture d’écran montrant l’écran Ajouter des informations d’identification après l’authentification.](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

   Vous avez maintenant émis des justificatifs vérifiables en utilisant notre abonné pour générer des justificatifs vérifiables tout en utilisant l’exemple d’abonné Azure B2C pour l’authentification.

     ![Capture d’écran montrant les justificatifs vérifiables émis par Azure AD et authentifiées par l’exemple d’abonné Azure B2C.](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)

## <a name="verify-the-verifiable-credential-by-using-the-sample-app"></a>Vérifier les justificatifs vérifiables à l’aide de l’exemple d’application

Maintenant que vous avez émis les justificatifs vérifiables depuis votre propre abonné, vérifiez-les à l’aide de l’exemple d’application.

>[!IMPORTANT]
> Lors du test, utilisez la même adresse de messagerie et le même mot de passe utilisés dans le tutoriel [Démarrage](get-started-verifiable-credentials.md). Pendant que votre abonné émet les justificatifs vérifiables, l’entrée provient d’un jeton d’ID émis par l’abonné Azure B2C. Dans le deuxième tutoriel, vous allez basculer l’émission du jeton vers votre abonné.

1. Dans le Portail Azure, sur la volet **Justificatifs vérifiables**, sélectionnez **Paramètres**. Copier le DID.

   ![Capture d’écran montrant la copie du DID.](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

1. Ouvrez maintenant le dossier du vérificateur dans les exemples de fichiers d’application. Vous devez mettre à jour le fichier app.js dans l’exemple de code du vérificateur et apporter les modifications suivantes :

    - **informations d'identification** : modifiez votre URL d’informations d’identification.
    - **credentialType** : entrez **« MyFirstVC »** .
    - **issuerDid** : copiez cette valeur depuis le Portail Azure > **Justificatifs vérifiables (préversion)**  > **Paramètres** > **Identificateur décentralisé (DID)** .
    
   ![Capture d’écran montrant le DID la mise à jour de l’émetteur de constante pour correspondre à l’identificateur de l’émetteur.](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

1. Arrêtez l’exécution du service ngrok de l’émetteur.

    ```cmd
    control-c
    ```

1. Exécutez maintenant ngrok avec le port vérificateur 8082.

    ```cmd
    ngrok http 8082
    ```

1. Dans une autre fenêtre de terminal, accédez à l’application du vérificateur et exécutez-la de la même façon que nous avons exécuté l’application émettrice.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

1. Ouvrez l’URL ngrok dans votre navigateur et scannez le code QR à l’aide d’Authenticator sur votre appareil mobile.
1. Sur votre appareil mobile, dans l’écran **Nouvelle demande d’autorisation**, sélectionnez **Autoriser**.

    >[!NOTE]
    > Le DID signant ces Justificatifs vérifiables est toujours Azure B2C. Le DID vérificateur est toujours issu de l’abonné de l’exemple d’application Microsoft. Étant donné que le DID Microsoft a été lié à un domaine que nous détenons, vous ne voyez pas l’avertissement comme celui que vous avez rencontré pendant le flux d’émission. Cette situation est mise à jour dans la section suivante.
    
   ![Capture d’écran qui montre l’écran Nouvelle demande d’autorisation.](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

1. Vous n’avez pas réussi à vérifier vos informations d'identification.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez de l’exemple de code qui émet des informations des justificatifs vérifiables de votre émetteur, passez à la section suivante. Vous allez utiliser votre propre fournisseur d’identité pour authentifier les utilisateurs qui essaient d’accéder à des justificatifs vérifiables. Vous utiliserez également votre DID pour signer des requêtes de présentation.

> [!div class="nextstepaction"]
> [Tutoriel : émettre et vérifier des justificatifs vérifiables à l’aide de votre abonné](issue-verify-verifiable-credentials-your-tenant.md)