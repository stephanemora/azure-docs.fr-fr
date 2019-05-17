---
title: Intégration d’Azure Active Directory pour Azure Red Hat OpenShift | Microsoft Docs
description: Découvrez comment créer un groupe de sécurité Azure AD et un utilisateur pour tester des applications sur votre cluster Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: f6b87748c33c1afd047ae25dfb7df3670a73e7c8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779676"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Intégration d’Azure Active Directory pour Azure Red Hat OpenShift

Si vous n’avez pas déjà créé un locataire Azure Active Directory (Azure AD), suivez les instructions de [créer un locataire Azure AD pour Azure Red Hat OpenShift](howto-create-tenant.md) avant de continuer avec ces instructions.

Microsoft Azure Red Hat OpenShift a besoin d’autorisations pour effectuer des tâches pour le compte de votre cluster. Si votre organisation n’a pas encore un utilisateur Azure AD, le groupe de sécurité Azure AD ou une inscription de l’application Azure AD à utiliser en tant que principal du service, suivez ces instructions pour les créer.

## <a name="create-a-new-azure-active-directory-user"></a>Création d’un nouvel utilisateur Azure Active Directory

Dans le [Azure portal](https://portal.azure.com), vérifiez que votre client s’affiche sous votre nom d’utilisateur dans le coin supérieur droit du portail :

![Capture d’écran du portail locataire répertoriés dans le coin supérieur droit](./media/howto-create-tenant/tenant-callout.png) si le client incorrect est affiché, cliquez sur votre nom d’utilisateur dans le coin supérieur droit, puis cliquez sur **changer de répertoire**et sélectionnez le client approprié à partir de la **toutes les Répertoires** liste.

Créer un nouvel utilisateur d’administrateur général Azure Active Directory pour vous connecter à votre cluster Azure Red Hat OpenShift.

1. Accédez à la [-les utilisateurs pour tous les utilisateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) panneau.
2. Cliquez sur **+ nouvel utilisateur** pour ouvrir le **utilisateur** volet.
3. Entrez un **nom** pour cet utilisateur.
4. Créer un **nom d’utilisateur** basé sur le nom du client que vous avez créé, avec `.onmicrosoft.com` ajouté à la fin. Par exemple : `yourUserName@yourTenantName.onmicrosoft.com`. Notez ce nom d’utilisateur. Vous en aurez besoin pour vous connecter à votre cluster.
5. Cliquez sur **rôle d’annuaire** pour ouvrir le volet de rôle de répertoire, puis sélectionnez **administrateur général** puis cliquez sur **Ok** en bas du volet.
6. Dans le **utilisateur** volet, cliquez sur **afficher le mot de passe** et enregistrer le mot de passe temporaire. Une fois que vous êtes dans la première fois, vous devrez faire pour le réinitialiser.
7. En bas du volet, cliquez sur **créer** pour créer l’utilisateur.

## <a name="create-an-azure-ad-security-group"></a>Créer un groupe de sécurité Azure AD

Pour accorder l’accès d’administrateur de cluster, les appartenances dans un groupe de sécurité Azure AD sont synchronisées dans l’OpenShift groupe « osa-client-admins ». Si non spécifié, aucun accès administrateur de cluster ne sera accordée.

1. Ouvrez le [groupes Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) panneau.
2. Cliquez sur **+ nouveau groupe**
3. Fournir un nom de groupe et une description.
4. Définissez **type de groupe** à **sécurité**.
5. Définissez **type d’appartenance** à **Assigned**.

    Ajoutez l’utilisateur Azure AD que vous avez créé à l’étape précédente pour ce groupe de sécurité.

6. Cliquez sur **membres** pour ouvrir le **sélectionner les membres** volet.
7. Dans la liste des membres, sélectionnez l’utilisateur Azure AD que vous avez créé ci-dessus.
8. En bas du portail, cliquez sur **sélectionnez** , puis **créer** pour créer le groupe de sécurité.

    Notez la valeur d’ID de groupe

9. Lorsque le groupe est créé, vous le verrez dans la liste de tous les groupes. Cliquez sur le nouveau groupe.
10. Dans la page qui s’affiche, notez le **ID d’objet**. Nous faisons référence à cette valeur en tant que `GROUPID` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.

## <a name="create-an-azure-ad-app-registration"></a>Créer une inscription de l’application Azure AD

Vous pouvez créer automatiquement un client de l’inscription d’application Azure Active Directory (Azure AD) dans le cadre de la création du cluster en omettant le `--aad-client-app-id` indicateur pour le `az openshift create` commande. Ce didacticiel vous montre comment créer l’inscription d’application Azure AD par souci d’exhaustivité.

Si votre organisation n’a pas encore une inscription de l’application Azure Active Directory (Azure AD) à utiliser en tant que service principal, suivez ces instructions pour créer un.

1. Ouvrez le [panneau inscriptions d’application](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) et cliquez sur **+ nouvelle inscription**.
2. Dans le **inscrire une application** volet, entrez un nom pour l’inscription de votre application.
3. Vérifiez que sous **pris en charge les types de comptes** qui **comptes dans ce répertoire d’organisation uniquement** est sélectionné. Il s’agit de l’option la plus sûre.
4. Nous allons ajouter un URI de redirection ultérieurement une fois que nous savons que l’URI du cluster. Cliquez sur le **inscrire** bouton permettant de créer l’inscription de l’application Azure AD.
5. Dans la page qui s’affiche, notez le **ID d’Application (client)**. Nous faisons référence à cette valeur en tant que `APPID` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.

![Capture d’écran de la page de l’objet d’application](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Créer une clé secrète client

Générer une clé secrète client pour authentifier votre application à Azure Active Directory.

1. Dans le **gérer** section de la page des enregistrements de l’application, cliquez sur **certificats et clés secrètes**.
2. Sur le **certificats et clés secrètes** volet, cliquez sur **+ nouvelle clé secrète client**.  Le **ajouter une clé secrète client** volet s’affiche.
3. Fournir un **Description**.
4. Définissez **expiration** à la durée que vous préférez, par exemple **dans 2 ans**.
5. Cliquez sur **ajouter** et la valeur de clé s’affiche dans le **clés secrètes de Client** section de la page.
6. Copiez la valeur de clé. Nous faisons référence à cette valeur en tant que `SECRET` dans le [créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) didacticiel.
 
![Capture d’écran du volet certificats et clés secrètes](./media/howto-create-tenant/create-key.png)
 
Pour plus d’informations sur les objets d’Application Azure, consultez [Application et principal du service dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Pour plus d’informations sur la création d’une nouvelle application Azure AD, consultez [inscrire une application avec le point de terminaison Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="resources"></a>Ressources

* [Les applications et les objets principal du service dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Démarrage rapide : Inscrire une application avec le point de terminaison Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

## <a name="next-steps"></a>Étapes suivantes

Si vous avez rempli tous les [conditions préalables Azure Red Hat OpenShift](howto-setup-environment.md), vous êtes prêt à créer votre premier cluster !

Essayez le didacticiel :
> [!div class="nextstepaction"]
> [Créer un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)