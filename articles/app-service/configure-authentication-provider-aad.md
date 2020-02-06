---
title: Configurer l’authentification Azure AD
description: Découvrez comment configurer l’authentification Azure Active Directory pour en faire un fournisseur d’identité pour votre application App Service.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 69959418c52eb7324efe19ca41481e426b822ab4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842353"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Configurer votre application App Service pour utiliser une connexion Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cet article vous montre comment configurer Azure App Service pour utiliser Azure Active Directory (Azure AD) en tant que fournisseur d’authentification.

Adoptez ces bonnes pratiques pour configurer votre application et l’authentification :

- Donnez à chaque application App Service ses propres autorisations et son propre consentement.
- Configurez chaque application App Service avec sa propre inscription.
- Évitez de partager des autorisations entre des environnements en utilisant des inscriptions d’application distinctes pour des emplacements de déploiement distincts. Dans le cadre des tests d’un nouveau code, cette pratique peut permettre d’éviter que des problèmes n’affectent l’application de production.

## <a name="express"> </a>Configurer avec des paramètres express

1. Dans le [Azure portal], recherchez et sélectionnez **App Services**, puis sélectionnez votre application.
2. Dans la barre de navigation gauche, sélectionnez **Authentification/Autorisation**  > **sur**.
3. Sélectionnez **Azure Active Directory** > **Express**.

   Si vous préférez choisir une inscription d’application existante :

   1. Cliquez sur **Sélectionner une application AD existante**, puis sur **Azure AD App**.
   2. Sélectionnez une inscription d’application existante et cliquez sur **OK**.

3. Cliquez sur **OK** pour inscrire l'application App Service auprès d'Azure Active Directory. Une nouvelle inscription d’application est créée.
   
    ![Paramètres Express d'Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Facultatif) Par défaut, App Service fournit une authentification, mais ne restreint pas l’accès autorisé au contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application. Pour restreindre l’accès à l’application aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter quand une requête n’est pas authentifiée** sur **Se connecter avec Azure Active Directory**. Quand vous définissez cette fonctionnalité, votre application exige que toutes les demandes soient authentifiées. Elle redirige également toutes les demandes non authentifiées vers Azure Active Directory à des fins d’authentification.

    > [!CAUTION]
    > Cette manière de restreindre l’accès s’applique à tous les appels à votre application qui peuvent ne pas être souhaitables pour les applications qui ont une page d’accès publique disponible, comme dans de nombreuses applications monopages. Pour de telles applications, préférez **Autoriser les requêtes anonymes (aucune action)** . L’application démarre alors elle-même manuellement la connexion. Pour plus d’informations, consultez [Flux d’authentification](overview-authentication-authorization.md#authentication-flow).
5. Sélectionnez **Enregistrer**.

## <a name="advanced"></a>Configurer avec des paramètres avancés

Vous pouvez configurer les paramètres de l’application manuellement si vous souhaitez utiliser une inscription d’application à partir d’un locataire Azure AD différent. Pour effectuer cette configuration personnalisée :

1. Créer une inscription dans Azure AD.
2. Fournir certains des détails d’inscription à App Service.

### <a name="register"></a>Créer une inscription d’application dans Azure AD pour votre application App Service

Vous avez besoin des informations suivantes quand vous configurez votre application App Service :

- ID client
- ID client
- Clé secrète client (facultative)
- URI d’ID d’application

Procédez comme suit :

1. Connectez-vous au [Azure portal], recherchez et sélectionnez **App Services**, puis sélectionnez votre application. Notez l’**URL** de votre application. Vous allez l’utiliser pour configurer l’inscription de votre application Azure Active Directory.
1. Sélectionnez **Azure Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Sous **URI de redirection**, sélectionnez **Web**, puis entrez `<app-url>/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Sélectionnez **Create** (Créer).
1. Une fois l’inscription d’application créée, copiez l’**ID de l’application (client)** et l’**ID de l’annuaire (locataire)** pour plus tard.
1. Sélectionnez **Personnalisation**. Dans **URL de la page d’accueil**, entrez l’URL de votre application App Service, puis sélectionnez **Enregistrer**.
1. Sélectionnez **Exposer une API** > **Définir**. Collez l’URL de votre application App Service, puis sélectionnez **Enregistrer**.

   > [!NOTE]
   > Cette valeur correspond à l'**URI d'ID d'application** de votre inscription d'application. Si votre application web nécessite un accès à une API dans le cloud, vous avez besoin de l’**URI d’ID d’application** de l’application web lorsque vous configurez la ressource App Service cloud. Vous pouvez utiliser cette valeur, par exemple, si vous souhaitez que le service cloud accorde explicitement l’accès à l’application web.

1. sélectionner **Ajouter une étendue**.
   1. Dans **Nom de l’étendue**, entrez *user_impersonation*.
   1. Dans les zones de texte, entrez le nom et la description de l’étendue de consentement que vous voulez que les utilisateurs voient dans la page de consentement. Par exemple, entrez *Accéder à mon application*. 
   1. Sélectionnez **Ajouter une étendue**.
1. (Facultatif) Pour créer une clé secrète client, sélectionnez **Certificats et secrets** > **Nouvelle clé secrète client** > **Ajouter**. Copiez la valeur de la clé secrète client qui s'affiche sur la page. Elle ne s’affichera plus.
1. (Facultatif) Pour ajouter plusieurs **URL de réponse**, sélectionnez **Authentification**.

### <a name="secrets"> </a>Activez Azure Active Directory dans votre Azure App Service

1. Dans le [Azure portal], recherchez et sélectionnez **App Services**, puis sélectionnez votre application. 
1. Dans le volet gauche, sous **Paramètres**, sélectionnez **Authentification/Autorisation** > **sur**.
1. (Facultatif) Par défaut, l’authentification App Service autorise l’accès non authentifié à votre application. Pour appliquer l'authentification utilisateur, définissez **Mesure à prendre quand une requête n’est pas authentifiée**, sur **Se connecter avec Azure Active Directory**.
1. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.
1. Dans **Mode d'administration**, sélectionnez **Avancé** et configurez l'authentification App Service selon le tableau suivant :

    |Champ|Description|
    |-|-|
    |ID client| Utilisez l'**ID d’application (client)** de l’inscription de l’application. |
    |ID d'émetteur| Utilisez `https://login.microsoftonline.com/<tenant-id>` et remplacez *\<tenant-id>* par l'**ID de l'annuaire (locataire)** de l'inscription de l'application. |
    |Clé secrète client (facultative)| Utilisez la clé secrète client que vous avez générée lors de l’inscription de l’application.|
    |Audiences de jeton autorisées| S’il s’agit d’une application cloud ou serveur et que vous souhaitez autoriser les jetons d’authentification d’une application web, ajoutez l’**URI d’ID d’application** de l’application web ici. **L’ID client** configuré est *toujours* implicitement considéré comme une audience autorisée. |

2. Sélectionnez **OK**, puis cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory à des fins d’authentification dans votre application App Service.

## <a name="configure-a-native-client-application"></a>Configurer une application cliente native

Vous pouvez inscrire des clients natifs pour autoriser l’authentification à l’aide d’une bibliothèque de client comme la **Bibliothèque d’authentification Active Directory**.

1. Dans le [Azure portal], sélectionnez **Active Directory** > **Inscriptions d’applications** > **Nouvelle inscription**.
1. Sur la page **Inscrire une application**, entrez un **Nom** pour votre inscription d'application.
1. Dans **URI de redirection**, sélectionnez **Client public (mobile et bureau)** et entrez l’URL `<app-url>/.auth/login/aad/callback`. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Pour une application Windows, utilisez plutôt le [SID de package](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) en tant qu’URI.
1. Sélectionnez **Create** (Créer).
1. Une fois l’inscription d’application créée, copiez la valeur de l’**ID d’application (client)** .
1. Sélectionnez **Autorisations des API** > **Ajouter une autorisation** > **Mes API**.
1. Sélectionnez l’inscription d'application que vous avez créée précédemment pour votre application App Service. Si celle-ci n’apparaît pas, vérifiez que vous avez ajouté l’étendue **user_impersonation** dans [Créer une inscription d’application dans Azure AD pour votre application App Service](#register).
1. Sélectionnez **user_impersonation**, puis **Ajouter des autorisations**.

Vous avez maintenant configuré une application cliente native qui peut accéder à votre application App Service.

## <a name="related-content"> </a>Étapes suivantes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
