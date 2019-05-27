---
title: Authentification basée sur un en-tête avec PingAccess pour le proxy d’application Azure AD | Microsoft Docs
description: Publiez des applications avec PingAccess et Application Proxy pour prendre en charge l’authentification basée sur un en-tête.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 365f017fe7d71500c17d0a9ccd9c5a0a26a78b75
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989572"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Authentification basée sur l’en-tête pour une authentification unique avec le proxy d’application et PingAccess

Proxy d’Application Azure Active Directory (Azure AD) a conclu un partenariat avec PingAccess afin que vos clients Azure AD peuvent accéder à plus de vos applications. PingAccess développe les [offres de proxy d’application existantes](application-proxy.md) en intégrant l’accès par authentification unique aux applications qui utilisent des en-têtes d’authentification.

## <a name="whats-pingaccess-for-azure-ad"></a>Qu’est PingAccess pour Azure AD ?

Avec PingAccess pour Azure AD, vous pouvez donner aux utilisateurs un accès et authentification unique (SSO) aux applications qui utilisent des en-têtes pour l’authentification. Le proxy d’application traite ces applications comme n’importe quelle autre application, en utilisant Azure AD pour authentifier l’accès, puis pour faire transiter le trafic via le service de connecteur. PingAccess se trouve devant les applications et le jeton d’accès d’Azure AD traduit par un en-tête. L’application reçoit l’authentification dans le format que peut lire.

Vos utilisateurs ne voient pas de différence quand ils se connectent pour utiliser vos applications d’entreprise. Ils pourront toujours travailler depuis n’importe où et sur n’importe quel appareil. Les connecteurs de Proxy d’Application dirigent le trafic distant à toutes les applications quel que soit leur type d’authentification, afin qu’ils amèneront toujours équilibrer les charges automatiquement.

## <a name="how-do-i-get-access"></a>Comment puis-je avoir accès ?

Étant donné que ce scénario proviennent d’un partenariat entre Azure Active Directory et PingAccess, vous avez besoin de licences pour les deux services. Toutefois, les abonnements Azure Active Directory Premium incluent une licence PingAccess de base qui couvre jusqu’à 20 applications. Si vous devez publier plus de 20 applications basées sur un en-tête, vous pouvez acheter une licence supplémentaire auprès de PingAccess.

Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publier votre application dans Azure

Cet article est destiné aux utilisateurs de publier une application avec ce scénario pour la première fois. En plus de détailler la procédure de publication, il vous guide dans la mise en route avec le Proxy d’Application et PingAccess. Si vous avez déjà configuré les deux services mais un rappel de la procédure de publication, passez à la [ajouter votre application à Azure AD avec le Proxy d’Application](#add-your-application-to-azure-ad-with-application-proxy) section.

> [!NOTE]
> Comme ce cas de figure repose sur un partenariat entre Azure AD et PingAccess, certaines de ces instructions figurent sur le site Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Installer un connecteur Application Proxy

Si vous avez activé le Proxy d’Application activé et qu’il est déjà installé un connecteur, vous pouvez ignorer cette section et accédez à [ajouter votre application à Azure AD avec le Proxy d’Application](#add-your-application-to-azure-ad-with-application-proxy).

Le connecteur de Proxy d’Application est un service Windows Server qui dirige le trafic à partir de vos employés à distance à vos applications publiées. Pour plus d’instructions d’installation, consultez [didacticiel : Ajouter une application en local pour l’accès à distance via le Proxy d’Application dans Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/) en tant qu’application administrateur. Le **centre d’administration Azure Active Directory** page s’affiche.
2. Sélectionnez **Azure Active Directory** > **proxy d’Application** > **télécharger service du connecteur**. Le **téléchargement du connecteur Application Proxy** page s’affiche.

   ![Téléchargement du connecteur application proxy](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. Suivez les instructions d’installation.

Télécharger le connecteur doit activer automatiquement le Proxy d’Application pour votre annuaire, mais dans le cas contraire, vous pouvez sélectionner **activer le Proxy d’Application**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Ajouter l’application Azure AD avec le Proxy d’Application

Vous devez effectuer deux actions dans le portail Azure. Tout d’abord, vous devez publier votre application avec le proxy d’application. Ensuite, vous devez collecter certaines informations sur l’application que vous pouvez utiliser pendant la procédure PingAccess.

#### <a name="publish-your-application"></a>Publication de votre application

Vous devrez tout d’abord publier votre application. Cette action implique :

- Ajout de votre application en local à Azure AD
- Affectation d’un utilisateur de test de l’application et en choisissant l’authentification unique basée sur l’en-tête
- Configuration des URL de redirection de l’application
- Octroi d’autorisations pour les utilisateurs et d’autres applications à utiliser votre application en local

Pour publier votre propre application en local :

1. Si vous n’avez pas dans la dernière section, vous connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/) en tant qu’application administrateur.
2. Sélectionnez **applications d’entreprise** > **nouvelle application** > **On-premises application**. Le **ajouter votre propre application en local** page s’affiche.

   ![Ajouter votre propre application locale](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. Remplissez les champs obligatoires avec des informations sur votre nouvelle application. Utilisez les instructions ci-dessous pour les paramètres.

   > [!NOTE]
   > Pour obtenir une description plus détaillée de cette étape, consultez [ajouter une application en local à Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL interne** : Normalement, vous indiquez l’URL qui vous permet page de connexion de l’application lorsque vous êtes sur le réseau d’entreprise. Pour ce scénario, le connecteur doit traiter le proxy PingAccess en tant que la première page de l’application. Utilisez le format suivant : `https://<host name of your PingAccess server>:<port>`. Le port par défaut est 3000, mais vous pouvez le configurer dans PingAccess.

      > [!WARNING]
      > Pour ce type de l’authentification unique, l’URL interne doit utiliser `https` et vous ne pouvez pas utiliser `http`.

   2. **Méthode de pré-authentification** : Choisissez **Azure Active Directory**.
   3. **Traduire l’URL dans les en-têtes** : Choisissez **Non**.

   > [!NOTE]
   > S’il s’agit de votre première application, utilisez le port 3000 pour démarrer et revenir afin de mettre à jour ce paramètre si vous modifiez votre configuration PingAccess. Pour les applications suivantes, le port sera doivent correspondre à l’écouteur que vous avez configuré dans PingAccess. Découvrez plus en détail les [écouteurs dans PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).
4. Sélectionnez **Ajouter**. La page Vue d’ensemble de la nouvelle application s’affiche.

Maintenant affecter un utilisateur pour le test de l’application et choisissez basée sur l’en-tête authentification unique :

1. Dans la barre latérale application, sélectionnez **utilisateurs et groupes** > **ajouter un utilisateur** > **utilisateurs et groupes (\<nombre > sélectionnés)** . Une liste des utilisateurs et groupes apparaît pour vous permettre de choisir à partir de.

   ![Utilisateurs et groupes](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. Sélectionnez un utilisateur pour le test de l’application, puis **sélectionnez**. Vérifiez que ce compte de test a accès à l’application locale.
3. Sélectionnez **Attribuer**.
4. Dans la barre latérale application, sélectionnez **Single sign-on** > **basée sur l’en-tête**.

   > [!TIP]
   > S’il s’agit votre première authentification unique basée sur un en-tête, vous devez installer PingAccess. Pour vous assurer que votre abonnement Azure est automatiquement associé à votre installation PingAccess, utilisez le lien de cette page d’authentification unique afin de télécharger PingAccess. Vous pouvez ouvrir le site de téléchargement maintenant ou revenir sur cette page ultérieurement.

   ![Authentification basée sur l’en-tête](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. Sélectionnez **Enregistrer**.

Vérifiez que votre URL est définie sur votre URL externe de redirection :

1. À partir de la **centre d’administration Azure Active Directory** barre latérale, sélectionnez **Azure Active Directory** > **inscriptions**. Une liste d’applications s’affiche.

   ![Inscriptions des applications](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. Sélectionnez votre application.
3. Sélectionnez le lien en regard **URI de redirection**, indiquant le nombre de redirection URI configuré pour le web et les clients publics. Le  **\<nom_application >-authentification** page s’affiche.
4. Vérifiez si l’URL externe que vous avez affecté à votre application précédemment se trouve dans le **URI de redirection** liste. Si elle n’est pas le cas, ajoutez l’URL externe à présent, à l’aide d’un type d’URI de redirection de **Web**, puis sélectionnez **enregistrer**.

Enfin, configurer votre application en local afin que les utilisateurs ont accès en lecture et d’autres applications ont un accès en lecture/écriture :

1. À partir de la **inscriptions** encadré pour votre application, sélectionnez **autorisations d’API** > **ajouter une autorisation**  >   **API Microsoft** > **Microsoft Graph**. Le **autorisations d’API demande** page **Microsoft Graph** s’affiche, qui contient les API pour Windows Azure Active Directory.

   ![Demander des autorisations d'API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. Sélectionnez **autorisations déléguées** > **utilisateur** > **User.Read**.
3. Sélectionnez **autorisations d’Application** > **Application** > **Application.ReadWrite.All**.
4. Sélectionnez **ajouter des autorisations**.
5. Dans le **autorisations d’API** page, sélectionnez **accorder le consentement de l’administrateur pour \<votre nom de répertoire >**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Collecter les informations pour la procédure PingAccess

Vous devez collecter ces trois éléments d’information (tous les GUID) pour configurer votre application avec PingAccess :

| Nom du champ d’Azure AD | Nom du champ de PingAccess | Format de données |
| --- | --- | --- |
| **ID d’application (client)** | **ID client** | GUID |
| **ID de répertoire (client)** | **émetteur** | GUID |
| `PingAccess key` | **Clé secrète client** | Chaîne aléatoire |

Pour collecter ces informations :

1. À partir de la **centre d’administration Azure Active Directory** barre latérale, sélectionnez **Azure Active Directory** > **inscriptions**. Une liste d’applications s’affiche.
2. Sélectionnez votre application. Le **inscriptions** page pour votre application s’affiche.

   ![Vue d’ensemble de l’inscription d’une application](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. À côté du **ID d’Application (client)** valeur, sélectionnez le **copier dans le Presse-papiers** icône, puis copiez et enregistrez-le. Vous spécifiez cette valeur ultérieurement en tant qu’ID de client. de PingAccess
4. Suivant le **ID de répertoire (locataire)** valeur, sélectionnez également **copier dans le Presse-papiers**, puis copiez et enregistrez-le. Vous spécifiez cette valeur ultérieurement en tant que l’émetteur de PingAccess.
5. À partir de la barre latérale de la **inscriptions** pour votre application, sélectionnez **certificats et clés secrètes** > **nouvelle clé secrète client**. Le **ajouter une clé secrète client** page s’affiche.

   ![Ajouter un secret client](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. Dans **Description**, type `PingAccess key`.
7. Sous **expiration**, choisissez comment définir la clé de PingAccess : **Dans 1 an**, **dans 2 ans**, ou **jamais**.
8. Sélectionnez **Ajouter**. Les PingAccess clé s’affiche dans la table de clés secrètes de client, avec un texte aléatoire de chaîne qui est automatiquement renseignée dans le **valeur** champ.
9. En regard de la clé PingAccess **valeur** champ, sélectionnez le **copier dans le Presse-papiers** icône, puis copiez et enregistrez-le. Vous spécifiez cette valeur ultérieurement en tant que clé secrète du client de PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Mettre à jour de GraphAPI pour envoyer des champs personnalisés (facultatif)

Pour obtenir la liste de jetons de sécurité Azure AD envoie pour l’authentification, consultez [les jetons d’ID plateforme Microsoft identity](../develop/id-tokens.md). Si vous avez besoin d’une revendication personnalisée qui envoie d’autres jetons, définissez le `acceptMappedClaims` champ d’application à `True`. Vous pouvez utiliser l’Explorateur graphique ou manifeste d’application du portail Azure AD pour effectuer cette modification.

Cet exemple utilise l’Explorateur Graph :

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

Cet exemple utilise le [portail Azure Active Directory](https://aad.portal.azure.com/) pour mettre à jour le `acceptMappedClaims` champ :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/) en tant qu’application administrateur.
2. Sélectionnez **Azure Active Directory** > **Inscriptions des applications**. Une liste d’applications s’affiche.
3. Sélectionnez votre application.
4. À partir de la barre latérale de la **inscriptions** page de votre application, sélectionnez **manifeste**. Le code JSON de manifeste pour l’inscription de votre application s’affiche.
5. Recherchez le `acceptMappedClaims` champ et remplacez la valeur par `True`.
6. Sélectionnez **Enregistrer**.

### <a name="use-a-custom-claim-optional"></a>Utiliser une revendication personnalisée (facultative)

Pour rendre votre application utilise une revendication personnalisée et inclure des champs supplémentaires, veillez à ce que vous avez également [créé une stratégie de mappage des revendications personnalisées et lui a attribué à l’application](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Pour utiliser une revendication personnalisée, vous devez également disposer d’une stratégie personnalisée définie et affectée à l’application. Cette stratégie doit inclure tous les attributs personnalisés nécessaires.
>
> Vous pouvez effectuer la définition de stratégie et l’assignation via PowerShell, Azure AD Graph Explorer ou Microsoft Graph. Si vous les effectuez dans PowerShell, vous devrez peut-être d’abord utiliser `New-AzureADPolicy` et puis l’affecter à l’application avec `Add-AzureADServicePrincipalPolicy`. Pour plus d’informations, consultez [affectation de stratégie de mappage de revendications](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-application"></a>Télécharger PingAccess et configurer votre application

Maintenant que vous avez terminé toutes les étapes de configuration d’Azure Active Directory, vous pouvez passer à la configuration de PingAccess.

Les étapes détaillées pour la partie de PingAccess de ce scénario continuent dans la documentation de Ping Identity. Suivez les instructions de [configuration de PingAccess pour Azure AD protéger les applications publiées à l’aide de Microsoft Azure AD Application Proxy](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) sur le site web de Ping Identity.

Ces étapes vous permettent d’installer PingAccess et configurer un compte PingAccess (si vous n’avez pas encore). Puis, pour créer une connexion Azure AD OpenID Connect (OIDC), vous mettez en œuvre un fournisseur de jetons avec la **ID de répertoire (locataire)** valeur que vous avez copiée à partir du portail Azure AD. Ensuite, pour créer une session web sur PingAccess, vous utilisez le **ID d’Application (client)** et `PingAccess key` valeurs. Enfin, vous pouvez configurer le mappage d’identité et créer un hôte virtuel, le site et l’application.

### <a name="test-your-application"></a>Tester votre application

Lorsque vous avez terminé toutes ces étapes, votre application doit être en cours d’exécution. Pour la tester, ouvrez un navigateur et accédez à l’URL externe que vous avez créé quand vous avez publié l’application dans Azure. Connectez-vous au compte de test que vous avez affecté à l’application.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer PingAccess pour Azure AD protéger les applications publiées à l’aide du Proxy d’Application Microsoft Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory](what-is-single-sign-on.md)
- [Résoudre les problèmes de proxy d’application et les messages d’erreur](application-proxy-troubleshoot.md)
