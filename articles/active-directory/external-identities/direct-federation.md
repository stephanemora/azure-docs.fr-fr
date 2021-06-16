---
title: Fédération avec un fournisseur d’identité (IdP) SAML/WS-Fed pour B2B - Azure AD
description: Fédérer directement avec un fournisseur d’identité SAML ou WS-Fed de sorte que les invités puissent se connecter à vos applications Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1be64850cba80602c13e30c2545470767963251
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963562"
---
# <a name="federation-with-samlws-fed-identity-providers-for-guest-users-preview"></a>Fédération avec des fournisseurs d’identité SAML/WS-Fed pour les utilisateurs invités (préversion)

> [!NOTE]
>- *La fédération directe* dans Azure Active Directory est désormais appelée *Fédération de fournisseur d’identité (IdP) SAML/WS-Fed*.
>- La fédération IdP SAML/WS-Fed est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article décrit comment configurer la fédération directe avec n’importe quelle organisation dont le fournisseur d’identité (IdP) prend en charge le protocole SAML 2.0 ou WS-Fed. Lorsque vous configurez la fédération avec un fournisseur d’identité partenaire, les nouveaux utilisateurs invités de ce domaine peuvent utiliser leur propre compte professionnel géré par le fournisseur d’identité pour se connecter à votre locataire Azure AD et commencer à travailler avec vous. Il n’est pas nécessaire pour l’utilisateur invité de créer un compte Azure AD séparé.

> [!IMPORTANT]
> - Nous avons supprimé la limitation qui exigeait que le domaine de l’URL d’authentification corresponde au domaine cible ou qu’il provienne d’un fournisseur d’identité autorisé. Pour plus d'informations, consultez [Étape 1 : Déterminer si le partenaire doit mettre à jour ses enregistrements texte DNS](#step-1-determine-if-the-partner-needs-to-update-their-dns-text-records).
>-  Nous recommandons maintenant que le partenaire définisse le public du fournisseur d'identité basé sur SAML ou WS-Fed sur un public avec locataire. Reportez-vous aux sections relatives aux attributs et revendications requis pour [SAML 2.0](#required-saml-20-attributes-and-claims) et [WS-Fed](#required-ws-fed-attributes-and-claims) ci-dessous.

## <a name="when-is-a-guest-user-authenticated-with-samlws-fed-idp-federation"></a>Quand un utilisateur invité est-il authentifié avec la fédération IdP SAML/WS-Fed ?

Une fois la fédération configurée avec le fournisseur d’identité SAML/WS-Fed de l’organisation, tous les nouveaux utilisateurs invités que vous invitez sont authentifiés à l’aide de ce fournisseur d’identité SAML/WS-Fed. Il est important de noter que configurer la fédération ne change pas la méthode d’authentification pour les utilisateurs invités qui ont déjà utilisé une invitation de votre part. Voici quelques exemples :

 - Si les utilisateurs invités ont déjà utilisé des invitations provenant de vous, et que vous avez par la suite configuré la fédération avec le fournisseur SAML/WS-Fed leur organisation, ces utilisateurs invités continueront à utiliser la même méthode d’authentification qu’ils ont utilisée avant que vous ne configuriez la fédération.
 - Si vous configurez la fédération avec le fournisseur d’identité SAML/WS-Fed d’une organisation et invitez des utilisateurs invités et qu’ensuite l’organisation partenaire passe à Azure AD, les utilisateurs invités qui ont déjà utilisé des invitations continueront d’utiliser le fournisseur d’identité SAML/WS-Fed fédéré, tant que la stratégie existe dans votre locataire.
 - Si vous supprimez la fédération avec un fournisseur d’identité SAML/WS-Fed d’une organisation, tous les utilisateurs invités qui utilisent actuellement l’IdP SAML/WS-Fed ne pourront pas se connecter.

Dans l’un de ces scénarios, vous pouvez mettre à jour la méthode d’authentification d’un utilisateur invité en [réinitialisant l’état d’acceptation](reset-redemption-status.md).

La fédération SAML/WS-Fed est liée aux espaces de noms domaine, par exemple contoso.com et fabrikam.com. Lorsque vous établissez une fédération avec AD FS ou un fournisseur d’identité tiers, les organisations associent un ou plusieurs espaces de noms domaine à ces fournisseurs d’identité.

## <a name="end-user-experience"></a>Expérience de l’utilisateur final 

Avec la fédération de fournisseur d’identité SAML/WS-Fed, les utilisateurs invités se connectent à votre client Azure AD à l’aide de leur propre compte professionnel. Lorsqu’ils accèdent à des ressources partagées et qu’ils sont invités à se connecter, les utilisateurs sont redirigés vers leur fournisseur d’identité. Après s’être connectés, ils sont renvoyés vers Azure AD pour accéder aux ressources. Leurs jetons d’actualisation sont valides pendant 12 heures, ce qui est la [durée par défaut de transfert d’un jeton d’actualisation](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties) dans Azure AD. Si le fournisseur d’identité fédéré dispose de l’authentification unique, l’utilisateur en profitera et ne verra aucune invite de connexion après son authentification initiale.

## <a name="sign-in-endpoints"></a>Points de terminaison de connexion

Les utilisateurs invités de la fédération de fournisseur d’identité SAML/WS-Fed peuvent désormais se connecter à vos applications multilocataires ou à vos applications principales Microsoft à l’aide d’un [point de terminaison commun](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (en d’autres termes, une URL d’application générale qui n’inclut pas le contexte de votre locataire). Durant le processus de connexion, l’utilisateur invité choisit **Options de connexion**, puis sélectionne **Sign in to an organization** (Se connecter à une organisation). L’utilisateur tape ensuite le nom de votre organisation et poursuit le processus de connexion à l’aide de ses propres informations d’identification.

Les utilisateurs invités de la fédération de fournisseur d’identité SAML/WS-Fed peuvent également se servir des points de terminaison d’application qui incluent les informations de votre locataire, par exemple :

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Vous pouvez également fournir aux utilisateurs invités un lien direct vers une application ou une ressource en incluant les informations de votre locataire, par exemple `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`.

## <a name="limitations"></a>Limites

### <a name="dns-verified-domains-in-azure-ad"></a>Domaines vérifiés par DNS dans Azure AD
Le domaine avec lequel vous voulez vous fédérer ne doit ***pas*** être vérifié par le système DNS dans Azure AD. Vous êtes autorisé à configurer la fédération avec des locataires Azure AD non managés (vérifiés par e-mail ou « viraux »), car ils ne sont pas vérifiés par le système DNS.

### <a name="signing-certificate-renewal"></a>Renouvellement du certificat de signature
Si vous spécifiez l’URL de métadonnées dans les paramètres du fournisseur d’identité, Azure AD renouvelle automatiquement le certificat de signature à son expiration. Toutefois, si le certificat pivote avant le délai d’expiration pour une raison quelconque, ou si vous ne fournissez pas une URL de métadonnées, Azure AD ne pourra pas le renouveler. Dans ce cas, vous devez mettre à jour le certificat de signature manuellement.

### <a name="limit-on-federation-relationships"></a>Limite des relations de fédération
Pour le moment, le nombre de relations de fédération maximum pris en charge est limité à 1 000. Cette limite concerne à la fois les [fédérations internes et les fédérations de fournisseur d’identité SAML/WS-Fed](/powershell/module/msonline/set-msoldomainfederationsettings).

### <a name="limit-on-multiple-domains"></a>Limite des domaines multiples
Nous ne prenons pas en charge actuellement la fédération de fournisseur d’identité SAML/WS-Fed avec plusieurs domaines du même locataire.

## <a name="frequently-asked-questions"></a>Forum aux questions
### <a name="can-i-set-up-samlws-fed-idp-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Puis-je configurer la fédération de fournisseur d’identité SAML/WS-Fed avec un domaine pour lequel un client non géré (vérifié par e-mail) existe ? 
Oui. Si le domaine n’a pas été vérifié et que le locataire n’a pas subi de [prise en charge administrateur](../enterprise-users/domains-admin-takeover.md), vous pouvez configurer la fédération avec de domaine. Les clients non gérés, ou vérifiés par e-mail, sont créés lorsqu’un utilisateur a utilisé une invitation B2B ou effectue une inscription libre-service pour Azure AD à l’aide d’un domaine qui n’existe pas actuellement. Vous pouvez configurer la fédération avec ces domaines. Si vous essayez de configurer la fédération avec un domaine vérifié par DNS, dans le portail Azure ou via PowerShell, vous rencontrerez une erreur.
### <a name="if-samlws-fed-idp-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Si la fédération de fournisseur d’identité SAML/WS-Fed et l’authentification par code secret à usage unique par e-mail sont activées, quelle méthode est prioritaire ?
Lorsque la fédération de fournisseur d’identité SAML/WS-Fed est établie avec une organisation partenaire, elle est prioritaire sur l’authentification par code secret à usage unique par e-mail pour les nouveaux utilisateurs invités de cette organisation. Si un utilisateur invité a utilisé une invitation à l’aide de l’authentification par code secret à usage unique avant que vous n’ayez configuré la fédération de fournisseur d’identité SAML/WS-Fed, il continue d’utiliser l’authentification par code secret à usage unique.
### <a name="does-samlws-fed-idp-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>La fédération de fournisseur d’identité SAML/WS-Fed répond-elle aux problèmes de connexion, étant une location partiellement synchronisée ?
Non, la fonctionnalité de connexion par [code secret à usage unique par e-mail](one-time-passcode.md) doit être utilisée dans ce cas. Une « location partiellement synchronisée » fait référence à un locataire Azure AD partenaire où les identités utilisateur locales ne sont pas entièrement synchronisées avec le cloud. Un invité dont l’identité n’existe pas encore dans le cloud mais qui essaie d’utiliser votre invitation B2B ne pourra pas se connecter. La fonctionnalité de code secret à usage unique permettrait à cet invité de se connecter. La fonctionnalité de fédération de fournisseur d’identité SAML/WS-Fed répond aux scénarios où l’invité a son propre compte professionnel géré par le fournisseur d’identité, mais que l’organisation n’a aucune présence sur Azure AD.
### <a name="once-samlws-fed-idp-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Une fois que la fédération de fournisseur d’identité SAML/WS-Fed est configurée avec une organisation, faut-il envoyer une invitation individuelle à chaque invité pour qu’il l’utilise ?
La configuration de la fédération de fournisseur d’identité SAML/WS-Fed ne change pas la méthode d’authentification pour les utilisateurs invités qui ont déjà utilisé une invitation de votre part. Vous pouvez mettre à jour la méthode d’authentification d’un utilisateur invité en [réinitialisant l’état d’acceptation](reset-redemption-status.md).

## <a name="step-1-determine-if-the-partner-needs-to-update-their-dns-text-records"></a>Étape 1 : Déterminer si le partenaire doit mettre à jour ses enregistrements texte DNS

En fonction de son fournisseur d’identité, le partenaire devra peut-être mettre à jour ses enregistrements DNS pour activer la fédération avec vous. Procédez comme suit pour déterminer si des mises à jour des enregistrements DNS sont nécessaires.

1. Si le fournisseur d’identité du partenaire correspond à l’un des fournisseurs d’identité autorisés suivants, aucune modification des enregistrements DNS n’est nécessaire (cette liste est sujette à modification) :

     - accounts.google.com
     - pingidentity.com
     - login.pingone.com
     - okta.com
     - oktapreview.com
     - okta-emea.com
     - my.salesforce.com
     - federation.exostar.com
     - federation.exostartest.com
     - idaptive.app
     - idaptive.qa

2. Si le fournisseur d'identité ne fait pas partie des fournisseurs autorisés répertoriés à l'étape précédente, vérifiez l'URL d'authentification du fournisseur d'identité du partenaire pour déterminer si le domaine correspond au domaine cible ou à un hôte du domaine cible. En d’autres termes, lors de la configuration de la fédération pour `fabrikam.com` :

     - Si l'URL d'authentification est `https://fabrikam.com` ou `https://sts.fabrikam.com/adfs` (hôte du même domaine), aucune modification des enregistrements DNS n'est nécessaire.
     - Si l'URL d'authentification est `https://fabrikamconglomerate.com/adfs`,   ou  `https://fabrikam.com.uk/adfs`, le domaine ne correspond pas au domaine fabrikam.com. Le partenaire doit donc ajouter à sa configuration DNS un enregistrement texte pour l'URL d'authentification ; passez à l'étape suivante.

3. Si des modifications des enregistrements DNS sont nécessaires en fonction de l'étape précédente, demandez au partenaire d'ajouter un enregistrement TXT aux enregistrements DNS de son domaine, comme dans l'exemple suivant :

   `fabrikam.com.  IN   TXT   DirectFedAuthUrl=https://fabrikamconglomerate.com/adfs`

## <a name="step-2-configure-the-partner-organizations-idp"></a>Étape 2 : Configurer le fournisseur d’identité de l’organisation partenaire

Votre organisation partenaire doit ensuite configurer son fournisseur d’identité avec les revendications requises et les approbations de partie de confiance.

> [!NOTE]
> Pour illustrer comment configurer un fournisseur d’identité SAML/WS-Fed pour la fédération, nous allons utiliser les services de fédération Active Directory (AD FS) comme exemple. Consultez l’article [Configurer la fédération de fournisseur d’identité SAML/WS-Fed avec AD FS](direct-federation-adfs.md), qui donne des exemples montrant comment configurer AD FS en tant que fournisseur d’identité SAML 2.0 ou WS-Fed en vue de la fédération.

### <a name="saml-20-configuration"></a>Configuration SAML 2.0

Azure AD B2B peut être configuré pour la fédération avec les fournisseurs d’identité qui utilisent le protocole SAML avec certaines exigences spécifiques indiquées ci-dessous. Pour plus d’informations sur la configuration d’une approbation entre votre fournisseur d’identité SAML et Azure AD, consultez [Utiliser un fournisseur d’identité SAML 2.0 pour l’authentification unique](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Le domaine cible pour la fédération de fournisseur d’identité SAML/WS-Fed ne doit pas être vérifié par DNS sur Azure AD. Consultez la section [Limitations](#limitations) pour plus d’informations.

#### <a name="required-saml-20-attributes-and-claims"></a>Attributs SAML 2.0 et revendications requises
Les tableaux suivants présentent la configuration requise pour les attributs spécifiques et les revendications qui doivent être configurés au niveau du fournisseur d’identité tiers. Pour configurer la fédération, les attributs suivants doivent être reçus dans la réponse SAML 2.0 à partir du fournisseur d’identité. Ces attributs peuvent être configurés en liant le fichier XML du service d’émission de jeton de sécurité en ligne ou en les entrant manuellement.

Attributs requis pour la réponse SAML 2.0 du fournisseur d’identité :

|Attribut  |Valeur  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Public visé     |`https://login.microsoftonline.com/<tenant ID>/` (Public avec locataire recommandé.) Remplacez `<tenant ID>` par l’ID du locataire Azure AD avec lequel vous configurez la fédération.<br><br>`urn:federation:MicrosoftOnline` (Cette valeur sera déconseillée.)          |
|Émetteur     |L’URI de l’émetteur du fournisseur d’identité partenaire, par exemple `http://www.example.com/exk10l6w90DHM0yi...`         |


Revendications requises pour le jeton SAML 2.0 émis par le fournisseur d’identité :

|Attribut  |Valeur  |
|---------|---------|
|Format NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configuration WS-Fed

Azure AD B2B peut être configuré pour la fédération avec les fournisseurs d’identité qui utilisent le protocole WS-Fed avec certaines exigences spécifiques indiquées ci-dessous. Actuellement, les deux fournisseurs WS-Fed testés pour assurer la compatibilité avec Azure AD incluent AD FS et Shibboleth. Pour plus d’informations sur l’établissement d’une partie de confiance entre un fournisseur compatible WS-Fed avec Azure AD, consultez le document « STS Integration Paper using WS Protocols » (Intégration » STS à l’aide des protocoles WS) disponible dans [Compatibilité des fournisseurs d’identité Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Le domaine cible pour la fédération ne doit pas être vérifié par DNS sur Azure AD. Consultez la section [Limitations](#limitations) pour plus d’informations.

#### <a name="required-ws-fed-attributes-and-claims"></a>Attributs WS-Fed et revendications requises

Les tableaux suivants présentent la configuration requise pour les attributs spécifiques et les revendications qui doivent être configurés au niveau du fournisseur d’identité SAML/WS-Fed tiers. Pour configurer la fédération, les attributs suivants doivent être reçus dans le message WS-Fed à partir du fournisseur d’identité. Ces attributs peuvent être configurés en liant le fichier XML du service d’émission de jeton de sécurité en ligne ou en les entrant manuellement.

Attributs requis dans le message WS-Fed du fournisseur d’identité :
 
|Attribut  |Valeur  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Public visé     |`https://login.microsoftonline.com/<tenant ID>/` (Public avec locataire recommandé.) Remplacez `<tenant ID>` par l’ID du locataire Azure AD avec lequel vous configurez la fédération.<br><br>`urn:federation:MicrosoftOnline` (Cette valeur sera déconseillée.)          |
|Émetteur     |L’URI de l’émetteur du fournisseur d’identité partenaire, par exemple `http://www.example.com/exk10l6w90DHM0yi...`         |

Revendications requises pour le jeton WS-Fed émis par le fournisseur d’identité :

|Attribut  |Valeur  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-3-configure-samlws-fed-idp-federation-in-azure-ad"></a>Étape 3 : Configurer la fédération d’IdP SAML/WS-Fed dans Azure AD

Ensuite, vous allez configurer la fédération avec le fournisseur d’identité configuré à l’étape 1 dans Azure AD. Vous pouvez utiliser le portail Azure AD ou PowerShell. La stratégie de la fédération prend effet au bout de 5 à 10 minutes. Pendant ce temps, n’essayez pas d’utiliser une invitation pour le domaine de la fédération. Les attributs suivants sont requis :

- URI de l’émetteur du fournisseur d’identité du partenaire
- Point de terminaison de l’authentification passive du fournisseur d’identité du partenaire (https uniquement est pris en charge)
- Certificat

### <a name="to-configure-federation-in-the-azure-ad-portal"></a>Pour configurer la fédération dans le portail Azure AD

1. Accédez au [portail Azure](https://portal.azure.com/). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Identités externes** > **Tous les fournisseurs d’identité**.
3. Sélectionnez **Nouvel IdP SAML/WS-Fed**.

    ![Capture d’écran montrant le bouton pour ajouter un nouveau fournisseur d’identité SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Sur la page **Nouveau fournisseur d’identité SAML/WS-Fed**, sous **Protocole de fournisseur d’identité**, sélectionnez **SAML** ou **WS-FED**.

    ![Capture d’écran montrant le bouton d’analyse sur la page Fournisseur d’identité SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Entrez le nom de domaine de votre organisation partenaire, qui sera le nom de domaine cible pour la fédération
6. Vous pouvez télécharger un fichier de métadonnées pour renseigner les informations des métadonnées. Si vous choisissez d’entrer les métadonnées manuellement, entrez les informations suivantes :
   - Nom de domaine du fournisseur d’identité du partenaire
   - ID d’entité du fournisseur d’identité du partenaire
   - Point de terminaison du demandeur passif du fournisseur d’identité du partenaire
   - Certificat
   > [!NOTE]
   > L’URL des métadonnées est facultative, mais fortement recommandée. Si vous fournissez l’URL des métadonnées, Azure AD peut renouveler automatiquement le certificat de signature lorsqu’il arrive à expiration. Si le certificat pivote avant le délai d’expiration pour une raison quelconque, ou si vous ne fournissez pas une URL de métadonnées, Azure AD ne pourra pas le renouveler. Dans ce cas, vous devez mettre à jour le certificat de signature manuellement.

7. Sélectionnez **Enregistrer**. 

### <a name="to-configure-samlws-fed-idp-federation-in-azure-ad-using-powershell"></a>Pour configurer la fédération de fournisseur d’identité SAML/WS-Fed dans Azure AD via PowerShell

1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). Si vous avez besoin d'une procédure détaillée, le Guide de démarrage rapide comprend des instructions : [Module PowerShell](b2b-quickstart-invite-powershell.md#prerequisites).
2. Exécutez la commande suivante :

   ```powershell
   Connect-AzureAD
   ```

3. À l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.
4. Exécutez les commandes suivantes, en remplaçant les valeurs à partir du fichier de métadonnées de fédération. Pour le serveur AD FS et Okta, le fichier de la fédération est federationmetadata.xml, par exemple : `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-4-test-samlws-fed-idp-federation-in-azure-ad"></a>Étape 4 : Tester la fédération d’IdP SAML/WS-Fed dans Azure AD
Testez maintenant la configuration de votre fédération en invitant un utilisateur invité B2B. Pour plus d’informations, consultez [Ajouter des utilisateurs Azure Active Directory B2B Collaboration dans le Portail Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-samlws-fed-idp-federation-relationship"></a>Comment modifier une relation de fédération d’IdP SAML/WS-Fed ?

1. Accédez au [portail Azure](https://portal.azure.com/). Sélectionnez **Azure Active Directory** dans le volet de gauche. 
2. Sélectionnez **Identités externes**.
3. Sélectionnez **Tous les fournisseurs d’identité**
4. Sous **Fournisseurs d’identité SAML/WS-Fed**, sélectionnez le fournisseur.
5. Dans le volet des informations du fournisseur identité, mettez à jour les valeurs.
6. Sélectionnez **Enregistrer**.


## <a name="how-do-i-remove-federation"></a>Comment supprimer une fédération ?

Vous pouvez supprimer la configuration de votre fédération. Si vous le faites, les utilisateurs invités par fédération qui ont déjà utilisé leur invitation ne pourront plus se connecter. Toutefois, vous pouvez autoriser de nouveau l’accès à vos ressources en [réinitialisant l’état d’acceptation](reset-redemption-status.md). Pour supprimer une fédération avec IdP dans le portail Azure :

1. Accédez au [portail Azure](https://portal.azure.com/). Sélectionnez **Azure Active Directory** dans le volet de gauche.
2. Sélectionnez **Identités externes**.
3. Sélectionnez **Tous les fournisseurs d’identité**.
4. Sélectionnez le fournisseur d’identité, puis **Supprimer**.
5. Sélectionnez **Oui** pour confirmer la suppression. 

Pour supprimer une fédération avec un fournisseur d’identité via PowerShell :

1. Installez la dernière version d’Azure AD PowerShell pour le module Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Exécutez la commande suivante :

   ```powershell
   Connect-AzureAD
   ```

3. À l’invite de connexion, connectez-vous avec le compte d’administrateur général géré.
4. Entrez la commande suivante :

   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur l’[expérience d’acceptation d’invitation](redemption-experience.md) lorsque des utilisateurs externes se connectent avec différents fournisseurs d’identité.
