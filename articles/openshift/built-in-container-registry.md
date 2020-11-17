---
title: Configurer le registre de conteneurs intégré pour Azure Red Hat OpenShift 4
description: Configurer le registre de conteneurs intégré pour Azure Red Hat OpenShift 4
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413490"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configurer le registre de conteneurs intégré pour Azure Red Hat OpenShift 4

Cet article explique comment configurer le registre d’images conteneurs intégré pour un cluster Azure Red Hat OpenShift (ARO) 4. Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer Azure AD
> * Configurer OpenID Connect
> * Accéder au registre d’images conteneurs intégré

## <a name="prerequisites"></a>Prérequis

* Créez un cluster en suivant les étapes décrites dans [Créer un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster). Veillez à créer le cluster avec l’argument `--pull-secret` pour la commande `az aro create`.  Cela est nécessaire si vous souhaitez configurer Azure AD pour la connexion, comme le requiert cet article.
* Connectez-vous au cluster en suivant les étapes décrites dans [Se connecter à un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Veillez à suivre les étapes décrites dans la section « Installer l’interface CLI OpenShift », car nous allons utiliser la commande `oc` plus loin dans cet article.
   * Prenez note de l’URL de la console du cluster, qui ressemble à `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`. Les valeurs de `<random>` et `<region>` seront utilisées plus loin dans cet article.
   * Notez les informations d’identification de `kubeadmin`. Vous les utiliserez plus loin dans cet article.

## <a name="set-up-azure-active-directory"></a>Configurer Azure Active Directory

Azure Active Directory (Azure AD) implémente OpenID Connect (OIDC). OIDC vous permet d’utiliser Azure AD pour vous connecter au cluster ARO. Suivez les étapes ci-dessous pour configurer Azure AD.

1. Configurez un locataire Azure AD en suivant les étapes décrites dans [Démarrage rapide : Configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant). Votre compte Azure dispose peut-être déjà d’un locataire. Dans ce cas, si vous disposez de privilèges suffisants dans ce locataire pour suivre la procédure, vous pouvez vous abstenir d’en créer un. Notez votre **ID de locataire**. Vous l’utiliserez ultérieurement.
2. Créez au moins un utilisateur Azure AD en suivant les étapes décrites dans [Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory). Vous pouvez utiliser ces comptes ou vos propres comptes pour tester l’application. Prenez note des adresses e-mail et des mots de passe de ces comptes pour la connexion.
3. Créez une inscription d’application dans votre locataire Azure AD en suivant les étapes décrites dans [Démarrage rapide : Inscrire une application avec la plateforme des identités Microsoft](/azure/active-directory/develop/quickstart-register-app). 
   1. Souvenez-vous de la remarque dans les prérequis concernant les valeurs de `<random>` et `<region>`. Utilisez ces valeurs pour créer un URI selon la formule suivante :

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Lorsque vous atteignez l’étape traitant du champ **URI de redirection**, entrez l’URI de l’étape précédente.
   1. Sélectionnez **Inscription**.
   1. Sur la page **Vue d’ensemble** pour l’application, notez la valeur affichée pour **ID d’application (client)** , comme illustré ici.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Page Vue d’ensemble d’application Azure AD.":::

4. Créez une clé secrète client. 
   1. Dans l’inscription d’application nouvellement créée, sélectionnez **Certificats et secrets** dans le volet de navigation de gauche.
   1. Sélectionnez **Nouveau secret client**.
   1. Ajoutez **une description**, puis sélectionnez **Ajouter**.
   1. Réservez la valeur de **clé secrète du client** générée. Cette valeur sera utilisée ultérieurement dans l’article.

### <a name="add-openid-connect-identity-provider"></a>Ajouter un fournisseur d’identité OpenID Connect

ARO fournit un registre de conteneurs intégré.  Pour y accéder, configurez un fournisseur d’identité (IDP) à l’aide d’Azure AD OIDC en procédant comme suit.

1. Connectez-vous à la console web OpenShift à partir de votre navigateur en tant que `kubeadmin`.  Il s’agit de la même procédure que celle utilisée lors de l’exécution des étapes du [Tutoriel : Se connecter à un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
1. Dans le volet de navigation de gauche, sélectionnez **Administration** > **Paramètres de cluster**.
1. Au milieu de la page, sélectionnez **Configuration globale**.
1. Recherchez l’entrée **OAuth** dans la colonne **Ressource de configuration** du tableau, puis sélectionnez-la.
1. Sous **Fournisseurs d’identité**, sélectionnez **Ajouter**, puis choisissez **OpenID Connect**.
1. Pour **Nom**, définissez **OpenID**.  Il se peut que cette valeur soit déjà renseignée.
1. Définissez les valeurs de l’étape précédente pour **ID client** et **Clé secrète client**.
1. Suivez cette étape pour rechercher la valeur d’**URL de l’émetteur**.
   1. Remplacez **\<tenant-id>** par l’ID enregistré dans le cadre de la section **Configurer Azure Active Directory** dans l’URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration`.
   1. Ouvrez l’URL dans le même navigateur que celui que vous avez utilisé pour interagir avec le portail Azure.
   1. Copiez la valeur de la propriété **issuer** dans le corps du fichier JSON retourné, et collez-la dans la zone de texte intitulée **URL de l’émetteur**.  La valeur de la propriété **issuer** ressemble à `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0`.
1. Faites défiler vers le bas de la page, puis sélectionnez **Ajouter**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect dans OpenShift.":::
1. Déconnectez-vous de la console web OpenShift en sélectionnant le bouton **kube:admin** en haut à droite de la fenêtre du navigateur et en choisissant **Se déconnecter**.

### <a name="access-the-built-in-container-image-registry"></a>Accéder au registre d’images conteneurs intégré

Les instructions suivantes permettent d’accéder au registre intégré.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Définir l’utilisateur Azure AD en tant qu’administrateur

1. Connectez-vous à la console web OpenShift à partir de votre navigateur en utilisant les informations d’identification d’un utilisateur Azure AD.

   1. Utilisez une fonctionnalité de fenêtre de navigateur InPrivate, Incognito ou autre fonctionnalité équivalente pour vous connecter à la console.
   1. La fenêtre présentera un aspect différent après l’activation d’OIDC.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Fenêtre de connexion OpenID Connect activée.":::
   1. Sélectionnez **openid**

   > [!NOTE]
   > Notez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter ici. Ces nom d’utilisateur et mot de passe fonctionneront pour l’administrateur en lien avec d’autres actions décrites dans cet article et dans d’autres.
1. Connectez-vous avec l’interface CLI OpenShift en procédant comme suit.  Pour la discussion, ce processus est appelé `oc login`.
   1. Dans l’angle supérieur droit de la console web, développez le menu contextuel de l’utilisateur connecté, puis sélectionnez **Copier la commande de connexion**.
   1. Connectez-vous à une nouvelle fenêtre d’onglets avec le même utilisateur si nécessaire.
   1. Sélectionnez **Afficher le jeton**.
   1. Copiez la valeur indiquée sous **Connectez-vous avec ce jeton** dans le presse-papiers, puis exécutez le jeton dans un interpréteur de commandes, comme illustré ici.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Exécutez `oc whoami` dans la console et notez la sortie en tant que **\<aad-user>** .  Vous utiliserez cette valeur plus loin dans cet article.
1. Déconnectez-vous de la console web OpenShift. Sélectionnez le bouton en haut à droite de la fenêtre du navigateur, intitulé **\<aad-user>** , puis choisissez **Se déconnecter**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Accorder à l’utilisateur Azure AD les rôles nécessaires pour l’interaction avec le registre

1. Connectez-vous à la console web OpenShift à partir de votre navigateur en utilisant les informations d’identification de `kubeadmin`.
1. Connectez-vous à l’interface CLI OpenShift avec le jeton pour `kubeadmin` en suivant les étapes décrites pour `oc login` ci-dessus, mais faites-le après vous être connecté à la console web avec `kubeadmin`.
1. Exécutez les commandes suivantes pour activer l’accès au registre intégré pour l’utilisateur **aad-user**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   La sortie doit être semblable à ce qui suit.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   La sortie doit être semblable à ce qui suit.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   La sortie doit être semblable à ce qui suit.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   La sortie doit être semblable à ce qui suit.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Obtenir l’URL du registre de conteneurs

Utilisez la commande `oc get route` ci-dessous pour récupérer l’URL du registre de conteneurs.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Notez la sortie de la console pour **URL du registre de conteneurs**. Elle sera utilisée comme nom de registre complet dans le cadre de ce guide et des guides suivants.

## <a name="next-steps"></a>Étapes suivantes

Utilisez le registre d’images conteneurs intégré en déployant une application sur OpenShift.  Pour les applications Java, suivez le guide pratique, [Déployer une application Java avec Open Liberty/WebSphere Liberty sur un cluster Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).
