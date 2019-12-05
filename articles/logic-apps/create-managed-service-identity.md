---
title: Authentifier avec des identités gérées
description: Accéder aux ressources d’autres locataires Azure Active Directory informations d’identification ou secrets en utilisant une identité managée
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 49c925cfe61084d8fedfdf953d469db4bd2c10b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792677"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Authentifier l’accès aux ressources Azure avec des identités managées dans Azure Logic Apps

Pour accéder aux ressources des autres locataires d’Azure Active Directory (Azure AD) et authentifier votre identité sans vous connecter, votre application logique peut utiliser l’[identité managée](../active-directory/managed-identities-azure-resources/overview.md) attribuée par le système (anciennement appelée MSI ou Managed Service Identity), plutôt que des informations d’identification ou des secrets. Azure gère cette identité pour vous et vous aide à sécuriser vos informations d’identification, car vous n’êtes pas obligé de fournir ni de faire pivoter des secrets. Cet article montre comment configurer et utiliser l’identité managée attribuée par le système dans votre application logique. Actuellement, les identités managées ne fonctionnent qu’avec des [déclencheurs et actions intégrés spécifiques](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls), non avec des connecteurs ou connexions managés.

Pour plus d’informations, consultez les rubriques suivantes :

* [Déclencheurs et actions qui prennent en charge les identités managées](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Services Azure qui prennent en charge l’authentification Azure AD avec des identités managées](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Types d’authentification pris en charge sur des appels sortants](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limites d’identité managée pour les applications logiques](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure ou, si vous n’en avez pas, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). L’identité managée et la ressource Azure cible à laquelle vous souhaitez accéder doivent toutes deux utiliser le même abonnement Azure.

* [Autorisations d’administrateur Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) qui peuvent attribuer des rôles à des identités managées dans le même locataire Azure AD que la ressource cible. Pour accorder à une identité managée l’accès à une ressource Azure, vous devez ajouter un rôle pour cette identité sur la ressource cible.

* Ressource Azure cible à laquelle vous souhaitez accéder

* Application logique qui utilise des [déclencheurs et actions qui prennent en charge les identités managées](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Activer l’identité attribuée par le système

À la différences des identités attribuées par l’utilisateur, vous n’avez pas besoin de créer manuellement l’identité attribuée par le système. Pour configurer l’identité attribuée par le système de votre application logique, vous pouvez utiliser les options suivantes :

* [Portail Azure](#azure-portal-system-logic-app)
* [Modèles Microsoft Azure Resource Manager](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Interface de ligne de commande Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Activer une identité attribuée par le système sur le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité** > **Attribuée par le système**. Sous **État**, sélectionnez **Activé** > **Enregistrer** > **Oui**.

   ![Activer l’identité attribuée par le système](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Votre application logique peut désormais utiliser l’identité attribuée par le système qui est inscrite auprès d’Azure Active Directory et représentée par un ID d’objet.

   ![ID d’objet pour l’identité attribuée par le système](./media/create-managed-service-identity/object-id.png)

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **ID d’objet** | <*identity-resource-ID*> | Identificateur global unique (GUID) qui représente l’identité attribuée par le système pour votre application logique dans votre locataire Azure AD |
   ||||

1. À présent, suivez les [étapes qui donnent à l’identité l’accès aux ressources](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Activer l’identité attribuée par le système dans un modèle Resource Manager

Pour automatiser la création et le déploiement de ressources Azure telles que des applications logiques, vous pouvez utiliser des [modèles Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Pour activer l’identité managée attribuée par le système pour votre application logique dans le modèle, ajoutez l’objet `identity` et la propriété enfant `type` à la définition de ressource de l’application logique dans le modèle, par exemple :

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Quand Azure crée la définition de ressource de votre application logique, l’objet `identity` reçoit les propriétés supplémentaires suivantes :

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriété (JSON) | Valeur | Description |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Identificateur global unique (GUID) de l’objet du principal de service pour l’identité managée qui représente votre application logique dans le locataire Azure AD. Ce GUID apparaît parfois sous la forme d’un « ID d’objet » ou `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | Identificateur global unique (GUID) représentant le locataire Azure AD où l’application logique est maintenant membre. À l’intérieur du locataire Azure AD, le principal du service a le même nom que l’instance d’application logique. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Accorder à une identité l’accès aux ressources

Une fois que vous avez configuré une identité managée pour votre application logique, vous pouvez [accorder à cette identité l’accès à d’autres ressources Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Vous pouvez ensuite utiliser cette identité à des fins d’authentification.

1. Dans le [portail Azure](https://portal.azure.com), accédez à la ressource Azure à laquelle vous souhaitez que votre identité managée ait accès.

1. Dans le menu de la ressource, sélectionnez **Contrôle d’accès (IAM)**  > **Attributions de rôle** pour examiner les attributions de rôles actuelles pour cette ressource. Dans la barre d’outils, sélectionnez **Ajouter** > **Ajouter une attribution de rôle**.

   ![Sélectionner « Ajouter » > « Ajouter une attribution de rôle »](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Si l’option **Ajouter une attribution de rôle** est désactivée, vous n’avez probablement pas les autorisations appropriées. Pour plus d’informations sur les autorisations permettant de gérer des rôles pour des ressources, voir [Autorisations des rôles d’administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Sous **Ajouter une attribution de rôle**, sélectionnez un **rôle** qui donne à votre identité l’accès nécessaire à la ressource cible.

   Pour l’exemple de cette rubrique, votre identité doit avoir un [rôle pouvant accéder à l’objet blob dans un conteneur de Stockage Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights) :

   ![Sélectionner « Contributeur aux données Blob du stockage »](./media/create-managed-service-identity/assign-role.png)

1. Dans la zone **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service Azure AD**.

   ![Sélectionner l’accès pour un identité attribuée par le système](./media/create-managed-service-identity/assign-access-system.png)

1. Dans la zone **Sélectionner**, recherchez et sélectionnez votre application logique.

   ![Sélectionner une application logique pour l’identité attribuée par le système](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Quand vous avez terminé, sélectionnez **Enregistrer**.

   La liste d’attributions de rôle de la ressource cible affiche maintenant l’identité et le rôle managés sélectionnés.

   ![Identités et rôles managés ajoutés à la ressource cible](./media/create-managed-service-identity/added-roles-for-identities.png)

1. À présent, suivez les [étapes pour authentifier l’accès avec l’identité](#authenticate-access-with-identity) dans un déclencheur ou une action prenant en charge les identités managées.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Authentifier l’accès avec des identités managées

Une fois que vous avez [activé l’identité managée pour votre application logique](#azure-portal-system-logic-app) et [accordé à cette identité l’accès à la ressource ou entité cibles](#access-other-resources), vous pouvez utiliser cette identité dans des [déclencheurs et actions prenant en charge les identités managées](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Si vous avez une fonction Azure dans laquelle vous voulez utiliser l’identité attribuée par le système, commencez par [activer l’authentification pour les fonctions Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Ces étapes montrent comment utiliser l’identité managée avec un déclencheur ou une action via le portail Azure. Pour spécifier l’identité managée dans la définition JSON sous-jacente d’un déclencheur ou d’une action, voir [Authentification d’identité managée](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Si ce n’est encore fait, ajoutez [le déclencheur ou l’action prenant en charge les identités managées](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Par exemple, le déclencheur ou l’action HTTP peuvent utiliser l’identité attribuée par le système que vous avez activée pour votre application logique. En général, le déclencheur ou l’action HTTP utilisent ces propriétés pour spécifier la ressource ou l’entité auxquelles vous souhaitez accéder :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Méthode** | OUI | Méthode HTTP utilisée par l’opération que vous souhaitez exécuter |
   | **URI** | OUI | URL de point de terminaison pour accéder à la ressource ou entité Azure cibles. La syntaxe de l’URI comprend généralement l’[ID de ressource](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) pour la ressource ou le service Azure. |
   | **En-têtes** | Non | Toute valeur d’en-tête que vous devez ou souhaitez inclure dans la demande sortante, telle que le type de contenu |
   | **Requêtes** | Non | Tout paramètre de requête que vous devez ou souhaitez inclure dans la demande, tel que le paramètre pour une opération spécifique ou la version de l’API pour l’opération que vous souhaitez exécuter |
   | **Authentification** | OUI | Type d’authentification à utiliser pour authentifier l’accès à la ressource ou entité cibles |
   ||||

   À titre d’exemple, supposons que vous souhaitez exécuter l’[opération de capture instantanée d’objet blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) sur un blob dans le compte de Stockage Azure où vous avez précédemment configuré l’accès pour votre identité. Toutefois, le [connecteur de Stockage Blob Azure](https://docs.microsoft.com/connectors/azureblob/) ne propose pas cette opération actuellement. Au lieu de cela, vous pouvez l’exécuter à l’aide de l’[action HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou d’une autre [opération de l’API REST du service BLOB](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Pour accéder aux comptes de stockage Azure derrière des pare-feu en utilisant des requêtes HTTP et des identités managées, veillez à configurer également votre compte de stockage avec l’[exception qui autorise l’accès de services Microsoft approuvés](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Pour exécuter l’[opération de capture instantanée d’objet blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), l’action HTTP spécifie les propriétés suivantes :

   | Propriété | Obligatoire | Exemple de valeur | Description |
   |----------|----------|---------------|-------------|
   | **Méthode** | OUI | `PUT`| Méthode HTTP utilisée par l’opération de capture instantanée d’objet blob |
   | **URI** | OUI | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | ID de ressource d’un fichier de Stockage Blob Azure dans l’environnement global (public) Azure qui utilise cette syntaxe |
   | **En-têtes** | Oui, pour Stockage Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Valeurs d’en-tête `x-ms-blob-type` et `x-ms-version` requises pour les opérations de Stockage Azure. <p><p>**Important !** Dans le déclencheur HTTP sortant et les demandes d’action pour Stockage Azure, l’en-tête requiert la propriété `x-ms-version` et la version de l’API pour l’opération que vous souhaitez exécuter. <p>Pour plus d’informations, consultez les rubriques suivantes : <p><p>- [En-têtes de demande – Capture instantanée d’objet blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Contrôle de version pour les services Stockage Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Requêtes** | Oui, pour cette opération | `comp` = `snapshot` | Nom et valeur du paramètre de requête pour l’opération de capture instantanée d’objet blob. |
   | **Authentification** | OUI | `Managed Identity` | Type d’authentification à utiliser pour l’authentification de l’accès à l’objet blob Azure |
   |||||

   Voici l’exemple d’action HTTP qui affiche toutes ces valeurs de propriété :

   ![Ajouter une action HTTP pour accéder à une ressource Azure](./media/create-managed-service-identity/http-action-example.png)

   Pour plus d’informations sur toutes les opérations de l’API REST Azure disponibles, voir les [Informations de référence sur l’API REST](https://docs.microsoft.com/rest/api/azure/).

1. Dans la liste **Authentification**, sélectionnez **Identité managée**. Si la propriété [**Authentification** est prise en charge](logic-apps-securing-a-logic-app.md#add-authentication-outbound) mais masquée, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez **Authentification**.

   > [!NOTE]
   > Les déclencheurs et actions ne vous permettent pas tous de sélectionner un type d’authentification. Pour plus d’informations, voir [Ajouter l’authentification aux appels sortants](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Dans la propriété « Authentification », sélectionner « Identité managée »](./media/create-managed-service-identity/select-managed-identity.png)

1. Après que vous avez sélectionné **Identité managée**, la propriété **Audience** s’affiche pour certains déclencheurs et actions. Si la propriété **Audience** est prise en charge mais masquée, ouvrez la **Ajouter un nouveau paramètre**, puis sélectionnez **Audience**.

1. Veillez à définir la valeur **Audience** sur l’ID de ressource de la ressource ou du service cible. Autrement, par défaut, la propriété **Audience** utilise l’ID de ressource `https://management.azure.com/`, qui est l’ID de ressource pour Azure Resource Manager.

   > [!IMPORTANT]
   > Vérifiez que l’ID de ressource cible *correspond exactement* à la valeur qu’attend Azure Active Directory, y compris les barres obliques de fin obligatoires. Par exemple, l’ID de ressource pour tous les comptes de Stockage Blob Azure requiert une barre oblique finale. Toutefois, l’ID de ressource pour un compte de stockage spécifique ne requiert pas de barre oblique finale. Vérifiez les [ID de ressource des services Azure qui prennent en charge Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Cet exemple définit la propriété **Audience** sur `https://storage.azure.com/` afin que les jetons d’accès utilisés pour l’authentification soient valides pour tous les comptes de stockage. Toutefois, vous pouvez également spécifier l’URL du service racine, `https://fabrikamstorageaccount.blob.core.windows.net`, pour un compte de stockage spécifique.

   ![Spécifier l’ID de ressource cible dans la propriété « Audience »](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Pour plus d’informations sur l’autorisation de l’accès avec Azure AD pour Azure Storage, consultez les rubriques suivantes :

   * [Autoriser l’accès aux objets blob et aux files d’attente Azure avec Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autoriser l’accès au Stockage Azure avec Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Supprimer une identité attribuée par le système

Pour cesser d’utiliser l’identité attribuée par le système pour votre application logique, vous disposez des options suivantes :

* [Portail Azure](#azure-portal-disable)
* [Modèles Microsoft Azure Resource Manager](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Si vous supprimez votre application logique, Azure supprime automatiquement d’Azure AD l’identité managée.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Supprimer une identité attribuée par le système du Portail Azure

Sur le Portail Azure, supprimez l’identité attribuée par le système [de votre application logique](#disable-identity-logic-app), et l’accès de cette identité à partir [de votre ressources cible](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Supprimer une identité attribuée par le système d’une application logique

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Dans le menu de l’application logique, sous **Paramètres**, sélectionnez **Identité** > **Attribuée par le système**. Sous **État**, sélectionnez **Désactivé** > **Enregistrer** > **Oui**.

   ![Arrêter d’utiliser une identité attribuée par le système](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Supprimer l’accès d’une identité à des ressources

1. Sur le [portail Azure](https://portal.azure.com), accédez à la ressource Azure cible donc vous souhaitez supprimer l’accès à votre identité managée.

1. Dans le menu de la ressource cible, sélectionnez **Contrôle d’accès (IAM)** . Sous la barre d’outils, sélectionnez **Attributions de rôle**.

1. Dans la liste des rôles, sélectionnez les identités managées que vous souhaitez supprimer. Dans la barre d’outils, sélectionnez **Supprimer**.

   > [!TIP]
   > Si l’option **Supprimer** est désactivée, vous n’avez probablement pas les autorisations appropriées. Pour plus d’informations sur les autorisations permettant de gérer des rôles pour des ressources, voir [Autorisations des rôles d’administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

L’identité managée est maintenant supprimée et n’a plus accès à la ressource cible.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Désactiver une identité managée dans un modèle Azure Resource Manager

Si vous avez activé l’identité managée par le système de l’application logique à l’aide d’un modèle Azure Resource Manager, définissez la propriété enfant `type` de l’objet `identity` sur `None`. Cette action supprime également d’Azure AD l’ID du principal pour l’identité managée par le système.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser l’accès et les données dans Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
