---
title: Tutoriel pour configurer Azure Active Directory B2C avec Jumio
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer Azure Active Directory B2C avec Jumio pour la vérification d’ID automatisée tout en protégeant les données du client
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817061"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Tutoriel de configuration de Jumio avec Azure Active Directory B2C

Dans cet exemple de tutoriel, nous fournissons des conseils sur l’intégration d’Azure AD B2C avec [Jumio](https://www.jumio.com/). Jumio est un service de vérification d’ID, qui permet de vérifier l’ID automatisé en temps réel et de protéger les données client.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous avez :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Le locataire est lié à votre abonnement Azure.

## <a name="scenario-description"></a>Description du scénario

L’intégration de Jumio inclut les composants suivants :

- Azure AD B2C : il s’agit du serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur. Il est également connu sous le nom de fournisseur d’identité.

- Jumio – le service qui prend les détails d’ID fournis par l’utilisateur et les vérifie.

- API REST intermédiaire : cette API implémente l’intégration entre Azure AD B2C et le service Jumio.

- Stockage blob : utilisé pour fournir des fichiers d’interface utilisateur personnalisés aux stratégies d’Azure AD B2C.

Le diagramme d’architecture suivant illustre l’implémentation.

![Capture d’écran du diagramme d’architecture de jumio](./media/partner-jumio/jumio-architecture-diagram.png)

|Étape | Description |
|:-----| :-----------|
| 1. | L’utilisateur accède à une page de connexion. Les utilisateurs sélectionnent l’option d’inscription pour créer un compte et entrer des informations sur la page. Azure AD B2C collecte les attributs de l’utilisateur.
| 2. | Azure AD B2C appelle l’API de couche intermédiaire et transmet les attributs de l’utilisateur.
| 3. | Une API de couche intermédiaire collecte des attributs d’utilisateur et les convertit dans un format consommable par l’API Jumio. Ensuite, elle les envoie à Jumio.
| 4. | Une fois que Jumio consomme les informations et les traite, il envoie le résultat à l’API de couche intermédiaire.
| 5. | L’API de couche intermédiaire traite les informations et renvoie les informations pertinentes à Azure AD B2C.
| 6. | Azure AD B2C reçoit les informations envoyées par l’API de couche intermédiaire. S’il affiche une réponse Échec, un message d’erreur est affiché à l’utilisateur. S’il affiche une réponse Réussite, l’utilisateur est authentifié et écrit dans le répertoire.

## <a name="onboard-with-jumio"></a>Intégration avec Jumio

1. Pour créer un compte Jumio, contactez [Jumio](https://www.jumio.com/contact/)

2. Une fois qu’un compte est créé, les informations sont utilisées dans la configuration de l’API. Les sections suivantes décrivent le processus.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Configurer Azure AD B2C avec Jumio

### <a name="part-1---deploy-the-api"></a>Partie 1 : Déploiement de l’API

Déployez le [code d’API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) fourni sur un service Azure. Le code peut être publié à partir de Visual Studio, en suivant ces [instructions](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Vous aurez besoin de l’URL du service déployé pour configurer Azure AD avec les paramètres requis.

### <a name="part-2---deploy-the-client-certificate"></a>Partie 2 : Déploiement du certificat client

1. L’appel de l’API Jumio est protégé par un certificat client. Créez un certificat auto-signé à l’aide de l’exemple de code PowerShell mentionné ci-dessous :

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Le certificat sera ensuite exporté à l’emplacement spécifié pour {``your-local-path``}.

3. Importez le certificat dans le service Azure App conformément aux instructions mentionnées dans ce [document](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate).

### <a name="part-3---create-a-signingencryption-key"></a>Partie 3 : créer une clé de signature/de chiffrement

Créez une chaîne aléatoire de plus de 64 caractères qui contient uniquement des lettres ou des chiffres.

Par exemple : ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Utilisez le script PowerShell ci-dessous pour créer une valeur alphanumérique de 64 caractères.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Partie 4 : configurer l’API

Les paramètres d’application peuvent être [configurés dans le service d’application dans Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Cette méthode permet de les configurer de façon sécurisée sans les archiver dans un référentiel. Vous devrez fournir les paramètres suivants à l’API REST :

| Paramètres de l’application | Source | Notes |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Configuration du compte Jumio |     |
|JumioSettings:AuthPassword | Configuration du compte Jumio |     |
|AppSettings:SigningCertThumbprint|Empreinte du certificat auto-signé créé|  |
|AppSettings:IdTokenSigningKey| Clé de signature créée à l’aide de PowerShell | |
| AppSettings:IdTokenEncryptionKey |Clé de chiffrement créée à l’aide de PowerShell
| AppSettings:IdTokenIssuer | Émetteur de certificat à utiliser pour le jeton JWT (une valeur GUID est recommandée) |
| AppSettings:IdTokenAudience  | Audience à utiliser pour le jeton JWT (une valeur GUID est recommandée) |
|AppSettings:BaseRedirectUrl | URL de base de la stratégie B2C | https://{your-tenant-name}.b2clogin.com/{your-application-id}|
| WEBSITE_LOAD_CERTIFICATES| Empreinte du certificat auto-signé créé |

### <a name="part-5---deploy-the-ui"></a>Partie 5 : déployer l’interface utilisateur

1. Configurer un [conteneur de stockage blob dans votre compte de stockage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Stockez les fichiers d’interface utilisateur du [dossier d’interface utilisateur](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) dans votre conteneur d’objets blob.

#### <a name="update-ui-files"></a>Mettre à jour les fichiers d’interface utilisateur

1. Dans les fichiers de l’interface utilisateur, accédez au dossier [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Ouvrez chaque fichier HTML.

3. Recherchez et remplacez {your-ui-blob-container-url} par l’URL de votre conteneur d’objets blob.

4. Recherchez et remplacez {your-intermediate-api-url} par l’URL du service d’application API intermédiaire.

>[!NOTE]
> Nous recommandons aux clients d’ajouter une notification de consentement sur la page de collection d’attributs. Informez les utilisateurs que des informations seront envoyées à des services tiers à des fins de vérification d’identité.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Partie 6 : Configuration de la stratégie Azure AD B2C

1. Accédez à la [stratégie d’Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) dans le dossier stratégies.

2. Suivez ce [document](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) pour télécharger le [Pack de démarrage LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configurez la stratégie pour le locataire Azure AD B2C.

>[!NOTE]
>Mettez à jour les stratégies fournies en fonction de votre locataire spécifique.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Ouvrez le locataire Azure AD B2C locataire, puis, sous Stratégies, sélectionnez **Identity Experience Framework**.

2. Sélectionnez votre **SignUpSignIn** créé précédemment.

3. Sélectionnez **Exécuter le flux d’utilisateurs**, puis sélectionnez les paramètres :

   a. **Application** : sélectionnez l’application inscrite (JWT dans l’exemple).

   b. **URL de réponse** : sélectionnez l’**URL de redirection**.

   c. Sélectionnez **Exécuter le flux utilisateur**.

4. Suivez le processus d’inscription et créez un compte

5. Le service Jumio est appelé pendant le flux, après la création de l’attribut utilisateur. Si le flux est incomplet, vérifiez que l’utilisateur n’est pas enregistré dans le répertoire.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
