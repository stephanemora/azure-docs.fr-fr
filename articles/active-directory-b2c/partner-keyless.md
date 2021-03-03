---
title: Tutoriel de configuration de Keyless avec Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer Keyless avec Azure Active Directory B2C pour une authentification sans mot de passe
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b817cfc347ee79ff7c9cbb4124e3f2b7e4d2b7ee
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644253"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Keyless avec Azure Active Directory B2C

Cet exemple de tutoriel vous aide à configurer Azure Active Directory (AD) B2C avec [Keyless](https://keyless.io/). Avec Azure AD B2C en tant que fournisseur d’identité, vous pouvez intégrer Keyless avec toute application cliente pour fournir une véritable authentification sans mot de passe à vos utilisateurs.

La solution **Keyless Zero-Knowledge Biometric (ZKB™)** de Keyless fournit une authentification multifacteur sans mot de passe qui élimine la fraude, le hameçonnage et la réutilisation d’informations d’identification, tout en améliorant l’expérience des clients et en protégeant leur confidentialité.

## <a name="pre-requisites"></a>Conditions préalables

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md). Le locataire doit être lié à votre abonnement Azure.

- En tant que client cloud Keyless, obtenez un [compte d’essai](https://keyless.io/go) gratuit.

- L’application Keyless Authenticator installée sur l’appareil de votre utilisateur.

## <a name="scenario-description"></a>Description du scénario

L’intégration de Keyless inclut les composants suivants :

- Azure AD B2C : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur. Également appelé fournisseur d’identité.

- Applications web et mobiles : applications web ou mobiles que vous choisissez de protéger avec Keyless et Azure AD B2C.

- Application mobile Keyless : l’application mobile Keyless sera utilisée pour l’authentification auprès des applications activées pour Azure AD B2C.

Le diagramme d’architecture suivant illustre l’implémentation.

![Image montrant le diagramme d’architecture Keyless](./media/partner-keyless/keyless-architecture-diagram.png)

|Étape | Description |
|:-----| :-----------|
| 1. | L’utilisateur accède à une page de connexion. L’utilisateur choisit de se connecter ou de s’inscrire, puis entre son nom d’utilisateur.
| 2. | L’application envoie à Azure AD B2C les attributs utilisateur afin de vérifier l’identité.
| 3. | Azure AD B2C collecte les attributs utilisateur et envoie ceux-ci à Keyless afin d’authentifier l’utilisateur via l’application mobile Keyless.
| 4. | Keyless envoie une notification Push à l’appareil mobile de l’utilisateur inscrit pour effectuer une authentification préservant la confidentialité sous la forme d’une analyse biométrique faciale.
| 5. | Quand l’utilisateur réagi à la notification Push, il est autorisé ou non à accéder à l’application cliente en fonction des résultats de la vérification.

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Ajouter un fournisseur d’identité

Pour ajouter un fournisseur d’identité, procédez comme suit :

1. Connectez-vous au **[portail Azure](https://portal.azure.com/#home)** en tant qu’administrateur général de votre locataire Azure AD B2C.

2. Veillez à utiliser l’annuaire contenant votre locataire Azure AD B2C en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire contenant votre locataire.

3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.

4. Accédez à **Tableau de bord** > **Azure Active Directory B2C** >  **Fournisseurs d’identité**.

5. Sélectionnez **Fournisseurs d’identité**.

6. Sélectionnez **Ajouter**.

### <a name="configure-an-identity-provider"></a>Configurer un fournisseur d’identité

Pour configurer un fournisseur d'identité, procédez comme suit :

1. Sélectionnez **Type de fournisseur d’identité** > **OpenID Connect (préversion)** .
2. Remplissez le formulaire pour configurer le fournisseur d’identité :

   |Propriété | Value |
   |:-----| :-----------|
   | Nom   | Keyless |
   | URL de métadonnées | Insérez l’URI de l’application hébergée d’authentification de Keyless, suivi du chemin d’accès spécifique, tel que https://keyless.auth/.well-known/openid-configuration. |
   | Clé secrète client | Secret associé à l’instance d’authentification de Keyless, différent de celui configuré précédemment. Insérez une chaîne complexe de votre choix. Ce secret sera utilisé ultérieurement dans la configuration du conteneur Keyless.|
   | ID client | ID du client. Cet ID sera utilisé ultérieurement dans la configuration du conteneur Keyless.|
   | Étendue | openid |
   | Type de réponse | id_token |
   | Mode de réponse | form_post|

3. Sélectionnez **OK**.

4. Sélectionnez **Mapper les revendications de ce fournisseur d’identité**.

5. Remplissez le formulaire pour mapper le fournisseur d’identité :

   |Propriété | Valeur |
   |:-----| :-----------|
   | UserID    | De l’abonnement |
   | Nom complet | De l’abonnement |
   | Mode de réponse | De l’abonnement |

6. Sélectionnez **Enregistrer** pour achever la configuration de votre nouveau fournisseur d’identité Open ID Connect (OIDC).

### <a name="create-a-user-flow-policy"></a>Créer une stratégie de flux utilisateur

Vous devriez maintenant voir Keyless répertorié comme nouveau fournisseur d’identité OIDC parmi vos fournisseurs d’identité B2C.

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs** sous **Stratégies**.

2. Sélectionnez un **Nouveau** flux d’utilisateur.

3. Sélectionnez successivement **Inscription et connexion**, une **version**, puis **Créer**.

4. Sous **Nom**, spécifiez un nom pour votre stratégie.

5. Dans la section Fournisseurs d’identité, sélectionnez le fournisseur d’identité Keyless que vous venez de créer.

6. Configurez les paramètres de votre flux d’utilisateur. Insérez un nom et sélectionnez le fournisseur d’identité que vous avez créé. Vous pouvez également ajouter une adresse e-mail. Dans ce cas, Azure ne redirige pas la procédure de connexion directement vers Keyless. Au lieu de cela, il affiche un écran dans lequel l’utilisateur peut choisir l’option à utiliser.

7. Laissez le champ **Authentification multifacteur** tel quel.

8. Sélectionnez **Appliquer les stratégies d’accès conditionnel**.

9. Sous **Attributs utilisateur et revendications de jetons**, dans l’option Collecter l’attribut, sélectionnez **Adresse e-mail**. Vous pouvez ajouter tous les attributs qu’Azure Active Directory peut collecter concernant l’utilisateur en plus des revendications qu’Azure AD B2C peut retourner à l’application cliente.

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

5. Keyless est appelé pendant le flux, après la création de l’attribut utilisateur. Si le flux est incomplet, vérifiez que l’utilisateur n’est pas enregistré dans le répertoire.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./custom-policy-get-started.md?tabs=applications)