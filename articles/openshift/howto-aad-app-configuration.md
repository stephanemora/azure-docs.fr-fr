---
title: Créer une inscription d’application Azure AD et l’utilisateur pour Azure Red Hat OpenShift | Microsoft Docs
description: Découvrez comment créer un principal de service, générer une URL de rappel client secret et l’authentification et créer un nouvel utilisateur Active Directory pour tester des applications sur votre cluster Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078519"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Créer une inscription d’application Azure AD et l’utilisateur pour Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift a besoin d’autorisations pour effectuer des tâches pour le compte de votre cluster. Si votre organisation n’a pas encore une inscription de l’application Azure Active Directory (Azure AD) à utiliser en tant que principal du service, suivez ces instructions pour créer un.

Cette rubrique se termine par des instructions pour créer un utilisateur Azure AD dont vous avez besoin pour accéder aux applications en cours d’exécution sur votre cluster Azure Red Hat OpenShift.

Si vous n’avez pas déjà créé un locataire Azure AD, suivez les instructions de [créer un locataire Azure AD pour Azure Red Hat OpenShift](howto-create-tenant.md) avant de continuer avec ces instructions.

## <a name="create-a-new-app-registration"></a>Créer une nouvelle inscription d’application

Une application qui souhaite utiliser les fonctionnalités d’Azure AD doit tout d’abord être inscrite dans un locataire Azure AD. Ce processus d’inscription implique donnant des détails d’Azure AD sur votre application, telles que l’URL où se trouve l’application, l’URL d’envoyer des réponses après avoir authentifié un utilisateur, l’URI qui identifie l’application et ainsi de suite.

1. Dans le [Azure portal](https://portal.azure.com), vérifiez que votre client s’affiche sous votre nom d’utilisateur dans le coin supérieur droit du portail :

    ![Capture d’écran du portail locataire répertoriés dans le coin supérieur droit][tenantcallout] si le client incorrect est affiché, cliquez sur votre nom d’utilisateur dans le coin supérieur droit, puis cliquez sur **changer de répertoire**et sélectionnez le client approprié à partir de la **toutes les Répertoires** liste.

2. Ouvrez le [panneau inscriptions d’application](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) et cliquez sur **nouvelle inscription d’application**.
3. Dans le **créer** volet, entrez un nom convivial pour votre objet d’application.
4. Vérifiez que **type d’Application** a la valeur *application Web/API*.
5. Créer un **Sign-on URL** au format suivant :

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . où `<cluster-name>` est le nom de votre cluster Azure Red Hat OpenShift prévu et `<azure-region>` est la [région Azure hébergeant votre cluster Azure Red Hat OpenShift](supported-resources.md#azure-regions). Par exemple, si votre nom de cluster doit être `contoso`, et vous allez créer dans le `eastus` région, le nom de domaine complet (FQDN) que vous devez entrer pour le **Sign-on URL** serait `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > Le nom du cluster doit être en minuscule, et l’URL de nom de domaine complet doit être unique.
    > Veillez à que choisir un nom suffisamment distinct pour votre cluster.

    Prenez note du nom de votre cluster et l’URL d’authentification, vous en aurez besoin plus tard à accéder aux applications en cours d’exécution sur votre cluster. Nous appellerons le nom du cluster en tant que `CLUSTER_NAME`et cette URL de connexion en tant que `FQDN` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.

6. Vérifiez votre **Sign-on URL** valeur valide avec une coche verte. (Appuyez sur la touche Tab pour le focus hors de la *Sign-on URL* champ et exécuter la vérification de validation.)
7. Cliquez sur le **créer** bouton permettant de créer l’objet d’application Azure AD.
8. Sur le **application inscrite** page qui s’affiche, notez le **ID d’Application**. Nous faisons référence à cette valeur en tant que `APPID` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.

    ![Capture d’écran de la zone de texte ID application][appidimage]
    
Pour plus d’informations sur les objets d’Application Azure, consultez [Application et principal du service dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Pour plus d’informations sur la création d’une nouvelle application Azure AD, consultez [inscrire une application avec le point de terminaison Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Créer une clé secrète client

Vous êtes maintenant prêt à générer une clé secrète client pour authentifier votre application à Azure Active Directory.

1. À partir de la **application inscrite** page, cliquez sur **paramètres** pour ouvrir les paramètres pour votre application inscrite.
2. Sur le **paramètres** volet qui s’affiche, cliquez sur **clés**.  Le **clés** volet s’affiche.
![Capture d’écran de la page Créer la clé dans le portail][createkeyimage]
3. Fournir une clé **Description**.
4. Définir une valeur pour **expiration**, par exemple *dans 2 ans*.
5. Cliquez sur **enregistrer** et la valeur de clé s’affiche.
6. Copiez la valeur de clé. Nous faisons référence à cette valeur en tant que `SECRET` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.

### <a name="create-a-reply-url"></a>Créer une URL de réponse

Azure AD utilise l’objet application *URL de réponse* pour spécifier le rappel à utiliser lors de l’autorisation de l’application. Dans le cas d’une API web ou une application web, l’URL de réponse est l’emplacement où Azure AD envoie la réponse d’authentification, y compris un jeton si l’authentification a réussi.

L’incompatibilité de plus petite (fin manquant de la barre oblique, une casse différente) entraîne l’échec de l’opération d’émission de jeton et vous ne pourrez pas vous connecter.

1. Revenez à la **paramètres** volet (vous pouvez cliquer sur **paramètres** dans la barre de navigation en haut du portail), puis cliquez sur **URL de réponse** sur la droite.  Le **URL de réponse** volet s’affiche.
2. La première URL de réponse dans la liste sera le `FQDN` valeur de l’étape 6 dans [créer une inscription d’application](#create-a-new-app-registration). Modifier et ajouter `/oauth2callback/Azure%20AD`.  Par exemple, votre URL de réponse doit maintenant ressembler à `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Cliquez sur **enregistrer** pour enregistrer l’URL de réponse.

## <a name="create-a-new-active-directory-user"></a>Créer un nouvel utilisateur Active Directory

Créer un nouvel utilisateur dans Active Directory à utiliser pour se connecter à l’application en cours d’exécution sur votre cluster Azure Red Hat OpenShift.

1. Accédez à la [utilisateurs - tous les utilisateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) panneau.
2. Cliquez sur **+ Nouvel utilisateur**. Le **utilisateur** volet s’affiche.
3. Entrez un **nom** que vous aimeriez pour cet utilisateur.
4. Créer un **nom d’utilisateur** basé sur le nom du client que vous avez créé avec `.onmicrosoft.com` ajouté à la fin. Par exemple : `yourUserName@yourTenantName.onmicrosoft.com`. Notez ce nom d’utilisateur. Vous en aurez besoin pour vous connecter à l’application sur votre cluster.
5. Cliquez sur **rôle d’annuaire** et sélectionnez **administrateur général** puis cliquez sur **Ok** en bas du volet.
6. Au milieu de la **utilisateur** volet, cliquez sur **afficher le mot de passe** et enregistrer le mot de passe temporaire. Une fois que vous êtes dans la première fois, vous devrez faire pour le réinitialiser.
7. En bas du volet, cliquez sur **créer** pour créer l’utilisateur.

## <a name="resources"></a>Ressources

* [Les applications et les objets principal du service dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Démarrage rapide : Inscrire une application avec le point de terminaison Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Étapes suivantes

Si vous avez rempli tous les [conditions préalables Azure Red Hat OpenShift](howto-setup-environment.md), vous êtes prêt à créer votre premier cluster !

Essayez le didacticiel :
> [!div class="nextstepaction"]
> [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)
