---
title: Tutoriel pour configurer Azure Active Directory B2C avec Nok Nok
titleSuffix: Azure AD B2C
description: Tutoriel sur la configuration de Nok Nok avec Azure Active Directory B2C pour activer l’authentification FIDO2 sans mot de passe
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 26842d40034527e5b7b785cfc94e4f7a37e36e04
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587211"
---
# <a name="tutorial-configure-nok-nok-with-azure-active-directory-b2c-to-enable-passwordless-fido2-authentication"></a>Tutoriel : Configuration de Nok Nok avec Azure Active Directory B2C pour activer l’authentification FIDO2 sans mot de passe

Dans cet exemple de tutoriel, découvrez comment intégrer la suite d’authentification Nok Nok S3 à votre locataire Azure Active Directory (AD) B2C. [Nok Nok](https://noknok.com/) active l’authentification multifactorielle certifiée FIDO, comme FIDO UAF, FIDO U2F, WebAuthn et FIDO2 pour les applications mobiles et web. Les clients Nok Nok peuvent améliorer leur sécurité tout en équilibrant l’expérience utilisateur.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](tutorial-create-tenant.md) lié à votre abonnement Azure.

- Obtenez un [locataire d’essai](https://noknok.com/products/strong-authentication-service/) gratuit Nok Nok.

## <a name="scenario-description"></a>Description du scénario

Pour activer l’authentification FIDO sans mot de passe pour vos utilisateurs, activez Nok Nok comme fournisseur d’identité pour votre locataire Azure AD B2C. L’intégration Nok Nok inclut les composants suivants :

- **Azure AD B2C** : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur.

- **Applications web et mobiles** : vos applications web ou mobiles que vous choisissez de protéger avec Nok Nok et Azure AD B2C.

- **Le SDK de l’application Nok Nok ou l’application Nok Nok** - Les applications utilisées pour authentifier les applications compatibles Azure AD B2C. Ces applications sont disponibles sur l’[Apple App Store](https://apps.apple.com/us/app/nok-nok-passport/id1050437340) et [Google Play](https://play.google.com/store/apps/details?id=com.noknok.android.passport2&hl=en&gl=US).

Le diagramme d’architecture suivant illustre l’implémentation. Nok Nok joue le rôle de fournisseur d’identité pour Azure AD B2C à l’aide d’Open ID Connecter (OIDC) pour activer l’authentification par mot de passe.

![Image montrant le diagramme d’architecture de Nok Nok et d’Azure AD B2C](./media/partner-nok-nok/nok-nok-architecture-diagram.png)

| Étape | Description |
|:------|:-----------|
| 1. | L’utilisateur accède à une page de connexion. L’utilisateur choisit de se connecter ou de s’inscrire, puis entre son nom d’utilisateur |
| 2. | Azure AD B2C redirige l’utilisateur vers le fournisseur d’authentification Nok Nok OIDC. |
| 3a. | Pour les authentifications basées sur mobile, la fonction Nok Nok affiche un code QR ou envoie une requête de notification push à l’appareil mobile de l’utilisateur final. |
| 3b. | Dans le cas d’une connexion basée sur un ordinateur de bureau ou un PC, Nok Nok redirige l’utilisateur final vers la page de connexion de l’application web pour lancer une invite d’authentification sans mot de passe. |
|4a. | L’utilisateur scanne le code QR sur son smartphone à l’aide du kit de développement logiciel (SDK) de l’application Nok Nok ou de l’application de Nok Nok Passport.|
| 4b. | L’utilisateur fournit un nom d’utilisateur en tant qu’entrée dans la page de connexion de l’application web et sélectionne Suivant. |
| 5a. | L’utilisateur est invité à s’authentifier sur smartphone. <BR> L’utilisateur effectue une authentification sans mot de passe à l’aide de la méthode préférée de l’utilisateur, comme la biométrie, le code PIN de l’appareil ou tout autre authentificateur itinérant.|
| 5b. | L’utilisateur est invité à s’authentifier sur l’application web. <BR> L’utilisateur effectue une authentification sans mot de passe à l’aide de la méthode préférée de l’utilisateur, comme la biométrie, le code PIN de l’appareil ou tout autre authentificateur itinérant. |
| 6. | Le serveur Nok Nok valide l’assertion FIDO et, lors de la validation, envoie la réponse d’authentification OIDC à Azure AD B2C.|
| 7. | En fonction de la réponse, l’utilisateur se voit accorder ou refuser l’accès. |

## <a name="onboard-with-nok-nok"></a>Intégration avec Nok Nok

Remplissez le [formulaire cloud Nok Nok](https://noknok.com/contact/) pour créer votre propre locataire Nok Nok. Une fois que vous envoyez le formulaire, vous recevez un e-mail expliquant comment accéder à votre locataire. L’e-mail inclut également l’accès aux guides Nok Nok. Suivez les instructions fournies dans le guide d’intégration de Nok Nok pour terminer la configuration OIDC de votre locataire cloud Nok Nok.

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Ajouter un fournisseur d’identité

Pour ajouter un fournisseur d’identité, procédez comme suit :

1. Connectez-vous au **[portail Azure](https://portal.azure.com/#home)** en tant qu’administrateur général de votre locataire Azure AD B2C.
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
1. Accédez à **Tableau de bord** > **Azure Active Directory B2C** >  **Fournisseurs d’identité**.
1. Sélectionnez **Fournisseurs d’identité**.
1. Sélectionnez **Ajouter**.

### <a name="configure-an-identity-provider"></a>Configurer un fournisseur d’identité 

Pour configurer un fournisseur d’identité, procédez comme suit :

1. Sélectionnez **Type de fournisseur d’identité** > **OpenID Connect (préversion)** .
1. Remplissez le formulaire pour configurer le fournisseur d’identité :

   |Propriété | Valeur |
   |:-----| :-----------|
   | Name   | Fournisseur d’authentification Nok Nok |
   | URL de métadonnées | Insérez l’URI de l’application hébergée d’authentification de Nok Nok, suivi du chemin d’accès spécifique, tel que https://demo.noknok.com/mytenant/oidc/.well-known/openid-configuration. |
   | Clé secrète client | Utilisez la clé secrète client fournie par la plateforme Nok Nok.|
   | ID client | Utilisez l’ID client fourni par la plateforme Nok Nok.|
   | Étendue | E-mail du profil OpenID |
   | Type de réponse | code |
   | Mode de réponse | form_post|

1. Sélectionnez **OK**.

1. Sélectionnez **Mapper les revendications de ce fournisseur d’identité**.

1. Remplissez le formulaire pour mapper le fournisseur d’identité :

   |Propriété | Valeur |
   |:-----| :-----------|
   | UserID    | De l’abonnement |
   | Nom complet | De l’abonnement |
   | Mode de réponse | De l’abonnement |

1. Sélectionnez **Enregistrer** pour terminer la configuration de votre nouveau fournisseur d’identité OIDC.

### <a name="create-a-user-flow-policy"></a>Créer une stratégie de flux utilisateur

Vous devriez maintenant voir Nok Nok répertorié comme nouveau fournisseur d’identité OIDC parmi vos fournisseurs d’identité B2C.

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.

2. Sélectionnez un **Nouveau** flux d’utilisateur.

3. Sélectionnez successivement **Inscription et connexion**, une **version**, puis **Créer**.

4. Sous **Nom**, spécifiez un nom pour votre stratégie.

5. Dans la section Fournisseurs d’identité, sélectionnez le fournisseur d’identité Nok Nok que vous venez de créer.

6. Configurez les paramètres de votre flux d’utilisateur. Insérez un nom et sélectionnez le fournisseur d’identité que vous avez créé. Vous pouvez également ajouter une adresse e-mail. Dans ce cas, Azure ne redirige pas la procédure de connexion directement vers Nok Nok. Au lieu de cela, il affiche un écran dans lequel l’utilisateur peut choisir l’option à utiliser.

7. Laissez le champ **Authentification multifacteur** tel quel.

8. Sélectionnez **Appliquer les stratégies d’accès conditionnel**.

9. Sous **Attributs utilisateur et revendications de jetons**, dans l’option Collecter l’attribut, sélectionnez **Adresse e-mail**. Vous pouvez ajouter tous les attributs qu’Azure AD peut collecter concernant l’utilisateur en plus des revendications qu’Azure AD B2C peut retourner à l’application cliente.

10. Sélectionnez **Create** (Créer).

11. Après une création réussie, sélectionnez votre nouveau **flux d’utilisateur**.

12. Dans le volet gauche, sélectionnez **Revendications d’application**. Sous les options, activez la case à cocher **E-mail**, puis sélectionnez **Enregistrer**.

## <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Ouvrez le locataire Azure AD B2C locataire, puis, sous Stratégies, sélectionnez Identity Experience Framework.

2. Sélectionnez votre SignUpSignIn créé précédemment.

3. Sélectionnez Exécuter le flux d’utilisateurs, puis sélectionnez les paramètres :

   a. Application : sélectionnez l’application inscrite (JWT dans l’exemple).

   b. URL de réponse : sélectionnez l’URL de redirection.

   c. Sélectionnez Exécuter le flux utilisateur.

4. Suivez le processus d’inscription et créez un compte

5. Nok Nok est appelé pendant le flux, après la création de l’attribut utilisateur. Si le flux est incomplet, vérifiez que l’utilisateur n’est pas enregistré dans le répertoire.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
