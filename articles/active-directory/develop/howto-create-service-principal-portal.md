---
title: Créer une application et un principal de service Azure AD à l’aide du portail
titleSuffix: Microsoft identity platform
description: Créez une application et un principal de service Azure Active Directory pour gérer l’accès aux ressources avec un contrôle d’accès en fonction du rôle dans Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249c313f8f6195371985f3dad2e707638e52d0df
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967114"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Activation Utiliser le portail pour créer une application et un principal du service Azure AD pouvant accéder aux ressources

Cet article explique comment créer une application et un principal du service Azure Active Directory (Azure AD) qui peuvent être utilisés avec le contrôle d’accès basé sur les rôles. Si vous utilisez du code qui doit accéder aux ressources ou les modifier, vous pouvez créer une identité pour l’application. Cette identité est connue en tant que principal de service. Vous pouvez alors assigner les autorisations nécessaires au principal de service. Cet article explique comment utiliser le portail pour créer le principal de service. Elle se concentre sur une application à locataire unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à locataire unique sont généralement utilisées pour les applications métier exécutées au sein de votre organisation.

> [!IMPORTANT]
> Au lieu de créer un principal du service, envisagez d’utiliser des identités managées pour les ressources Azure en ce qui concerne l’identité de votre application. Si votre code s’exécute sur un service qui prend en charge les identités managées et accède aux ressources qui prennent en charge l’authentification Azure AD, les identités managées correspondent bien à vos besoins. Pour en savoir plus sur les identités gérées pour les ressources Azure, y compris les services qui les prennent actuellement en charge, consultez la rubrique [Que sont les identités gérées pour les ressources Azure ?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Créer une application Azure Active Directory

Passons directement à la création de l’identité. Si vous rencontrez un problème, vérifiez que votre compte a les [autorisations nécessaires](#required-permissions) pour créer l’identité.

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
1. Sélectionnez **Azure Active Directory**.
1. Sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Nouvelle inscription**.
1. Nommez l’application. Sélectionnez un type de compte pris en charge, qui détermine qui peut utiliser l’application. Sous **URI de redirection**, sélectionnez **Web** pour le type d’application que vous souhaitez créer. Saisissez l’URI vers lequel le jeton d’accès est envoyé. Vous ne pouvez pas créer d’informations d’identification pour une [application native](../manage-apps/application-proxy-configure-native-client-application.md). Vous ne pouvez pas utiliser ce type pour une application automatisée. Après avoir défini les valeurs, sélectionnez **S’inscrire**.

   ![Saisissez un nom pour votre application](./media/howto-create-service-principal-portal/create-app.png)

Vous avez créé votre application Azure AD et le principal de service.

## <a name="assign-the-application-to-a-role"></a>Affecter l’application à un rôle

Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Déterminez quel rôle fournit les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](../../role-based-access-control/built-in-roles.md).

Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient).

1. Dans le portail Azure, sélectionnez le niveau d’étendue auquel vous souhaitez affecter l’application. Par exemple, pour affecter un rôle à l’étendue de l’abonnement, recherchez et sélectionnez **Abonnements**, ou sélectionnez **Abonnements** dans la **page d’accueil**.

   ![Par exemple, affecter un rôle à l’étendue d’abonnement](./media/howto-create-service-principal-portal/select-subscription.png)

1. Sélectionnez l’abonnement auquel l’application doit être affectée.

   ![Sélectionner l’abonnement pour l’assignation](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Si vous ne voyez pas l’abonnement recherché, sélectionnez le **filtre des abonnements généraux**. Assurez-vous que l’abonnement souhaité est sélectionné dans le portail.

1. Sélectionnez **Contrôle d’accès (IAM)** .
1. Sélectionnez **Ajouter une attribution de rôle**.
1. Sélectionnez le rôle que vous souhaitez affecter à l’application. Par exemple, pour autoriser l’application à exécuter des actions telles que **redémarrer**, **démarrer** et **arrêter** des instances, sélectionnez le rôle **Contributeur**.  En savoir plus sur les [rôles disponibles](../../role-based-access-control/built-in-roles.md). Par défaut, les applications Azure AD ne figurent pas dans les options disponibles. Pour trouver votre application, recherchez-la par son nom et sélectionnez-la.

   ![Sélectionnez le rôle à assigner à l’application](./media/howto-create-service-principal-portal/select-role.png)

1. Sélectionnez **Enregistrer** pour finaliser l’attribution du rôle. Votre application apparaît dans la liste des utilisateurs affectés à un rôle pour cette étendue.

Votre principal de service est créé. Vous pouvez commencer à l’utiliser pour exécuter vos scripts ou applications. La section suivante montre comment obtenir les valeurs nécessaires pour vous connecter par programmation.

## <a name="get-values-for-signing-in"></a>Obtenir les valeurs pour la connexion

Quand vous vous connectez par programmation, vous devez passer l’ID du locataire avec votre requête d’authentification. Vous avez également besoin de l’ID de votre application et d’une clé d’authentification. Pour obtenir ces valeurs, procédez comme suit :

1. Sélectionnez **Azure Active Directory**.
1. Dans **Inscriptions d’applications** dans Azure AD, sélectionnez votre application.
1. Copiez l’ID du répertoire (locataire) et stockez-le dans votre code d’application.

    ![Copier l’ID du répertoire (locataire) et le stocker dans votre code d’application](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copiez l’**ID d’application** et stockez-le dans votre code d’application.

   ![Copier l’ID d’application (client)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificats et secrets
Les applications démon peuvent utiliser deux formes d’informations d’identification pour s’authentifier auprès d’Azure AD : les certificats et les secrets d’application.  Nous vous recommandons d’utiliser un certificat, mais vous pouvez également créer un nouveau secret d’application.

### <a name="upload-a-certificate"></a>Téléchargement d'un certificat

Vous pouvez utiliser un certificat existant si vous en avez un.  Vous pouvez également utiliser un certificat auto-signé à des fins de test. Ouvrez PowerShell et exécutez [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) avec les paramètres suivants pour créer un certificat auto-signé dans le magasin de certificats utilisateur sur votre ordinateur : 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exportez ce certificat dans un fichier à l’aide du composant logiciel enfichable MMC [Gérer un certificat utilisateur](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) accessible depuis le Panneau de configuration Windows.

Pour charger le certificat :

1. Cliquez sur **Certificats et secrets**.
1. Sélectionnez **Charger un certificat**, puis sélectionnez le certificat (un certificat existant ou le certificat auto-signé que vous avez exporté).

    ![Sélectionnez Charger un certificat, puis sélectionnez celui que vous souhaitez ajouter](./media/howto-create-service-principal-portal/upload-cert.png)

1. Sélectionnez **Ajouter**.

Une fois le certificat inscrit avec votre application dans le portail d’inscription des applications, vous devez activer le code de l’application cliente pour utiliser le certificat.

### <a name="create-a-new-application-secret"></a>Créer un secret d’application

Si vous choisissez de ne pas utiliser un certificat, vous pouvez créer un nouveau secret d’application.

1. Cliquez sur **Certificats et secrets**.
1. Sélectionnez **Clés secrètes client -> Nouvelle clé secrète client**.
1. Fournissez une description et la durée du secret. Quand vous avez terminé, sélectionnez **Ajouter**.

   Une fois la clé secrète client enregistrée, la valeur de la clé secrète client s’affiche. Copiez cette valeur car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la valeur de la clé avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

   ![Copiez la valeur du secret, car vous ne pourrez pas la récupérer plus tard.](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Configurer des stratégies d’accès sur les ressources
Gardez à l’esprit que vous devrez peut-être configurer des autorisations supplémentaires sur les ressources auxquelles votre application doit accéder. Par exemple, vous devez également [mettre à jour les stratégies d’accès d’un coffre de clés](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) pour permettre à votre application d’accéder aux clés, secrets ou certificats.  

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre coffre de clés et sélectionnez **Accès partagé**.  
1. Sélectionnez **Ajouter une stratégie d’accès**, puis sélectionnez les autorisations de clé, de secret et de certificat que vous souhaitez accorder à votre application.  Sélectionnez le principal de service que vous avez créé précédemment.
1. Sélectionnez **Ajouter** pour ajouter la stratégie d’accès, puis **Enregistrer** pour valider vos modifications.
    ![Ajouter une stratégie d’accès](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Autorisations requises

Vous devez avoir les autorisations nécessaires pour inscrire une application auprès de votre locataire Azure AD et assigner un rôle à l’application dans votre abonnement Azure.

### <a name="check-azure-ad-permissions"></a>Vérifier les autorisations dans Azure AD

1. Sélectionnez **Azure Active Directory**.
1. Notez votre rôle. Si vous avez le rôle **Utilisateur**, vous devez vous assurer que les utilisateurs non-administrateurs peuvent inscrire des applications.

   ![Trouvez votre rôle. Si vous êtes un utilisateur, vérifiez que les utilisateurs non-administrateurs peuvent inscrire des applications.](./media/howto-create-service-principal-portal/view-user-info.png)

1. Dans le volet gauche, sélectionnez **Paramètres utilisateur**.
1. Vérifiez le paramètre **Inscriptions d’applications**. Cette valeur peut uniquement être définie par un administrateur. Si la valeur est **Oui**, tous les utilisateurs dans le locataire Azure AD peuvent inscrire une application.

Si le paramètre d’inscriptions d’applications est défini sur **Non**, seuls les utilisateurs ayant un rôle d’administrateur peuvent inscrire ces types d’applications. Reportez-vous aux [rôles disponibles](../users-groups-roles/directory-assign-admin-roles.md#available-roles) et aux [autorisations de rôle](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) pour en savoir plus sur les rôles administrateur disponibles et les autorisations spécifiques dans Azure AD qui sont attribuées à chaque rôle. Si le rôle Utilisateur est assigné à votre compte mais que le paramètre d’inscription d’applications est limité aux utilisateurs administrateurs, demandez à votre administrateur de vous assigner l’un des rôles Administrateur pouvant créer et gérer tous les aspects des inscriptions d’application ou autoriser les utilisateurs à inscrire des applications.

### <a name="check-azure-subscription-permissions"></a>Vérifier les autorisations d’abonnement Azure

Dans votre abonnement Azure, votre compte doit disposer d’un accès `Microsoft.Authorization/*/Write` pour affecter un rôle à une application AD. Cette action est accordée par le biais du rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../../role-based-access-control/built-in-roles.md#user-access-administrator). Si le rôle **Collaborateur** est affecté à votre compte, vous ne disposez pas de l’autorisation appropriée. Vous recevez un message d’erreur lorsque vous tentez d’attribuer le principal de service à un rôle.

Pour vérifier vos autorisations d’abonnement :

1. Sélectionnez votre compte dans le coin supérieur droit, puis **... -> Mes autorisations**.

   ![Sélectionnez votre compte et vos autorisations utilisateur.](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Dans la liste déroulante, sélectionnez l’abonnement où vous souhaitez créer le principal de service. Ensuite, sélectionnez **Cliquer ici pour afficher les détails d’accès complet pour cet abonnement**.

   ![Sélectionnez l’abonnement où vous souhaitez créer le principal de service.](./media/howto-create-service-principal-portal/view-details.png)

1. Sélectionnez **Attributions de rôles** pour afficher les rôles qui vous sont affectés et déterminez si vous disposez des autorisations appropriées pour affecter un rôle à une application AD. Si ce n’est pas le cas, demandez à votre administrateur d’abonnement de vous ajouter un rôle Administrateur de l’accès utilisateur. Dans l’image suivante, le rôle Propriétaire est assigné à l’utilisateur, ce qui signifie que l’utilisateur dispose des autorisations appropriées.

   ![Cet exemple montre que l’utilisateur est affecté au rôle Propriétaire](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la spécification de stratégies de sécurité, consultez la rubrique [Contrôle d’accès en fonction du rôle](../../role-based-access-control/role-assignments-portal.md).  
* Pour une liste des actions disponibles qui peuvent être autorisées ou refusées aux utilisateurs, consultez [Opérations du fournisseur de ressources Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
