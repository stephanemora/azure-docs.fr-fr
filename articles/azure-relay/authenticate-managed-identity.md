---
title: S'authentifier avec des identités managées pour les ressources Azure Relay (préversion)
description: Cet article explique comment utiliser des identités managées pour accéder aux ressources Azure Relay.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: fa82f41056f3a5a8617bab572b8b4f312861419f
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654183"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-relay-resources-preview"></a>Authentifier une identité managée auprès d'Azure Active Directory pour accéder aux ressources Azure Relay (préversion)
La fonctionnalité [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) vous permet de créer une identité sécurisée associée au déploiement sous lequel s’exécute le code de votre application. Vous pouvez ensuite associer cette identité à des rôles de contrôle d’accès qui accordent des autorisations personnalisées pour l’accès aux ressources Azure nécessaires à votre application.

Avec les identités managées, la plateforme Azure gère cette identité d’exécution. Il n'est pas nécessaire de stocker et de protéger des clés d'accès dans le code ou la configuration de votre application, que ce soit pour l'identité elle-même ou pour les ressources auxquelles vous devez accéder. Une application cliente Relay exécutée dans une application Azure App Service ou dans une machine virtuelle prenant en charge les identités managées pour les ressources Azure n'a pas besoin de gérer des clés et des règles SAS, ou d'autres jetons d'accès. L'application cliente a uniquement besoin de l'adresse du point de terminaison de l'espace de noms Relay. Lorsque l'application se connecte, Relay lie le contexte de l'entité managée au client dans le cadre d'une opération illustrée plus loin dans cet article. Une fois associé à une identité managée, votre client Relay peut effectuer toutes les opérations autorisées. L'autorisation est accordée en associant une identité managée à des rôles Relay.

[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="enable-managed-identity"></a>Activer une identité managée
Tout d'abord, activez l'identité managée pour la ressource Azure qui doit accéder aux entités Azure Relay (connexions hybrides ou relais WCF). Par exemple, si votre application cliente Relay s'exécute sur une machine virtuelle Azure, activez l'identité managée correspondante en suivant les instructions de l'article [Configurer une identité managée pour une machine virtuelle Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). Une fois que vous avez activé ce paramètre, une nouvelle identité de service managée est créée dans Azure Active Directory (Azure AD).

Pour obtenir la liste des services qui prennent en charge les identités managées, consultez [Services qui prennent en charge les identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="assign-an-azure-relay-role-to-the-managed-identity"></a>Attribuer un rôle Azure Relay à l'identité managée
Après avoir activé l'identité managée, attribuez-lui un rôle Azure Relay (Propriétaire Azure Relay, Écouteur Azure Relay ou Expéditeur Azure Relay) dans l'étendue appropriée. Lorsque le rôle Azure est attribué à une identité managée, celle-ci est autorisée à accéder aux entités Relay dans l'étendue appropriée.

La section suivante utilise une application simple qui s'exécute sous une identité managée sur une instance de machine virtuelle Azure et accède aux ressources Relay.

## <a name="sample-app-on-vm-accessing-relay-entities"></a>Exemple d'application sur une machine virtuelle qui accède à des entités Relay

1. Téléchargez l'[exemple d'application console Connexions hybrides](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol) sur votre ordinateur à partir de GitHub.
1. [Créez une machine virtuelle Azure](../virtual-machines/windows/quick-create-portal.md). Pour cet exemple, utilisez une image Windows 10. 
1. Activez l'identité affectée par le système ou par un utilisateur pour la machine virtuelle Azure. Pour plus d'informations, consultez [Activer l'identité pour une machine virtuelle](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). 
1. Attribuez un des rôles Relay à l'identité de service managée dans l'étendue souhaitée (entité Relay, espace de noms Relay, groupe de ressources, abonnement). Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
1. Générez l'application console sur votre ordinateur local en suivant les instructions du [document LISEZ-MOI](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample). 
1. Copiez le fichier exécutable du dossier \<your local path\>\RoleBasedAccessControl\bin\Debug vers la machine virtuelle. Vous pouvez utiliser RDP pour vous connecter à votre machine virtuelle Azure. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](../virtual-machines/windows/connect-logon.md).
1. Exécutez RoleBasedAccessControl.exe sur la machine virtuelle Azure en suivant les instructions du [document LISEZ-MOI](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample). 

    > [!NOTE]
    > Suivez les étapes décrites ci-dessus afin d'exécuter l'[application console pour WCF Relays](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl).

#### <a name="highlighted-code-from-the-sample"></a>Code en surbrillance de l'exemple
Voici le code de l'exemple qui montre comment utiliser l'authentification Azure AD pour se connecter au service Azure Relay.  

1. Créez un objet [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) à l'aide de la méthode `TokenProvider.CreateManagedIdentityTokenProvider`. 
    
    - Vous utilisez une **identité managée affectée par le système :**
        ```csharp
        TokenProvider.CreateManagedIdentityTokenProvider();
        ```
    - Si vous utilisez une **identité managée affectée par l'utilisateur**, procurez-vous l'**ID client** de l'identité affectée par l'utilisateur sur la page **Identité managée** du portail Azure. Pour plus d'informations, consultez [Répertorier les identités managées affectées par l'utilisateur](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azp#list-user-assigned-managed-identities).
        ```csharp
        var managedCredential = new ManagedIdentityCredential(clientId);
        tokenProvider = TokenProvider.CreateManagedIdentityTokenProvider(managedCredential);    
        ```
1. Créez un objet [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) ou [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) en lui transmettant l'URI de la connexion hybride et le fournisseur de jetons que vous avez créé à l'étape précédente.

    **Écouteur :**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **Expéditeur :**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Relay, consultez les rubriques suivantes.
- [Qu’est-ce que Relay ?](relay-what-is-it.md)
- [Bien démarrer avec les WebSockets de connexions hybrides Azure Relay](relay-hybrid-connections-dotnet-get-started.md)
- [Bien démarrer avec les requêtes HTTP de connexions hybrides Azure Relay](relay-hybrid-connections-http-requests-dotnet-get-started.md)



