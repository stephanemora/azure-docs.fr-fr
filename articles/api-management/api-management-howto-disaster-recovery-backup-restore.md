---
title: Implémenter une reprise d’activité à l’aide d’une sauvegarde et d’une restauration dans Gestion des API
titleSuffix: Azure API Management
description: Apprenez à utiliser la sauvegarde et la restauration pour effectuer une récupération d'urgence dans Gestion des API Azure.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 10/03/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b356d18c1a0c6a29d4fce142fc05e449f08f70d2
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419142"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Comment implémenter une récupération d'urgence à l'aide d'une sauvegarde de service et la récupérer dans Gestion des API Azure

En publiant et en gérant vos API via Gestion des API Azure, vous bénéficiez de fonctionnalités de tolérance de panne et d’infrastructure que vous n’avez plus à concevoir, implémenter ou gérer manuellement. La plateforme Azure permet de limiter une grande partie des risques de défaillance à moindres frais.

Pour effectuer une récupération à la suite de problèmes de disponibilité affectant la région où vous hébergez votre service Gestion des API, préparez-vous à reconstituer votre service dans une autre région à tout moment. Selon votre objectif de délai de récupération, vous souhaiterez peut-être conserver un service en attente dans une ou plusieurs régions. Vous pouvez aussi essayer de synchroniser leur configuration et leur contenu avec le service actif en fonction de l’objectif de délai de récupération. Les fonctionnalités de sauvegarde et de restauration de service fournissent les éléments de base nécessaires à l’implémentation d’une stratégie de récupération d’urgence.

Vous pouvez également recourir à des opérations de sauvegarde et restauration pour répliquer la configuration du service Gestion des API entre des environnements opérationnels (par exemple, développement et intermédiaire). Notez que les données de runtime telles que les informations utilisateur et les abonnements seront également copiées, ce qui n’est pas toujours souhaitable.

Ce guide montre comment automatiser les opérations de sauvegarde et de restauration et garantir l’authentification des requêtes de sauvegarde et de restauration par Azure Resource Manager.

> [!IMPORTANT]
> Une opération de restauration ne modifie pas la configuration du nom d’hôte du service cible. Nous vous recommandons d’utiliser les mêmes certificats TLS et nom d’hôte personnalisés pour les services actifs et en veille, afin que le trafic puisse être redirigé vers l’instance en veille via un simple changement de CNAME DNS une fois l’opération de restauration terminée.
>
> Une opération de sauvegarde ne capture pas les données de journal préagrégées qui sont utilisées dans les rapports affichés dans le panneau **Analytics** du portail Microsoft Azure.

> [!WARNING]
> Chaque sauvegarde expire au bout de 30 jours. Si vous essayez de restaurer une sauvegarde après l’expiration de la période de 30 jours, la restauration échoue avec un message `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Demandes d'authentification Azure Resource Manager

> [!IMPORTANT]
> L'API REST pour la sauvegarde et la restauration utilise Azure Resource Manager et dispose d'un autre mécanisme d'authentification que pour les API REST pour la gestion de vos entités de gestion des API. Les étapes de cette section décrivent comment authentifier les requêtes Azure Resource Manager. Pour plus d’informations, consultez [Demandes d'authentification Azure Resource Manager](/rest/api/azure).

Toutes les tâches que vous effectuez sur les ressources à l’aide d’Azure Resource Manager doivent être authentifiées avec Azure Active Directory en effectuant les étapes suivantes :

-   Ajoutez une application au locataire Azure Active Directory.
-   Définissez les autorisations pour l'application que vous avez ajoutée.
-   Obtenez le jeton d'authentification des demandes pour Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Créer une application Azure Active Directory

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. À l’aide de l’abonnement qui contient votre instance de service Gestion des API, accédez à [portail Azure - Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) pour enregistrer une application dans le Répertoire actif.
    > [!NOTE]
    > Si le répertoire par défaut Azure Active Directory n’est pas visible dans votre compte, contactez l’administrateur de l’abonnement Azure pour accorder les autorisations nécessaires à votre compte.
1. Sélectionnez **+ Nouvelle inscription**.
1. Sur la page **Inscrire une application**, définissez les valeurs comme suit :
    
    * Choisissez un nom explicite pour le champ **Nom**.
    * Définissez **Types de comptes pris en charge** sur **Comptes dans cet annuaire organisationnel**. 
    * Dans l’**URI de redirection**, entrez une URL d’espace réservé, telle que `https://resources`. Il s’agit d’un champ obligatoire, mais la valeur n’est pas utilisée ultérieurement. 
    * Sélectionnez **Inscription**.

### <a name="add-permissions"></a>Ajout d’autorisations

1. Une fois l’application créée, sélectionnez **Autorisations d’API** >  **+ Ajouter une autorisation**.
1. Sélectionnez **API Microsoft**.
1. Sélectionnez **Gestion du Service Azure**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Capture d’écran montrant comment des permissions d’application."::: 

1. Cliquez sur **Autorisations déléguées** à côté de l’application nouvellement ajoutée, puis cochez la case **Accéder à la Gestion du Service Azure en tant qu’utilisateurs de l’organisation (préversion)** .

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Capture d’écran montrant comment ajouter des permissions d’application déléguées.":::

1. Sélectionnez **Ajouter des autorisations**.

### <a name="configure-your-app"></a>Configuration de votre application

Avant d’appeler les API qui génèrent la sauvegarde et la restauration, vous devez obtenir un jeton. L’exemple suivant utilise le package NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pour récupérer le jeton.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Remplacez `{tenant id}`, `{application id}` et `{redirect uri}` en suivant les instructions suivantes :

1. Remplacez `{tenant id}` par l’ID de locataire de l’application Azure Active Directory que vous avez créée. Vous pouvez accéder à l’ID en cliquant sur **Inscriptions d’applications** -> **Points de terminaison**.

    ![Points de terminaison][api-management-endpoint]

2. Remplacez `{application id}` par la valeur que vous avez obtenue en accédant à la page **Paramètres**.
3. Remplacez `{redirect uri}` par la valeur provenant de l’onglet **URI de redirection** de votre application Azure Active Directory.

    Une fois que les valeurs sont spécifiées, l’exemple de code doit renvoyer un jeton similaire à l’exemple suivant :

    ![par jeton][api-management-arm-token]

    > [!NOTE]
    > Le jeton peut expirer après un certain temps. Réexécutez l’exemple de code pour générer un nouveau jeton.

## <a name="accessing-azure-storage"></a>Accès à Stockage Azure

Gestion des API utilise un compte Stockage Azure que vous spécifiez pour les opérations de sauvegarde et de restauration. Lorsque vous exécutez une opération de sauvegarde ou de restauration, vous devez configurer l’accès au compte de stockage. Gestion des API prend en charge deux mécanismes d’accès au stockage : une clé d’accès Stockage Azure (valeur par défaut) ou une identité managée Gestion des API.

### <a name="configure-storage-account-access-key"></a>Configurer la clé d’accès du compte de stockage

Pour connaître les étapes à suivre, consultez [Gérer les clés d’accès au compte de stockage](../storage/common/storage-account-keys-manage.md?tabs=azure-portal).

### <a name="configure-api-management-managed-identity"></a>Configurer l’identité managée Gestion des API

> [!NOTE]
> L’utilisation d’une identité managée Gestion des API pour les opérations de stockage pendant une sauvegarde ou une restauration requiert la version `2021-04-01-preview` ou une version ultérieure de l’API REST Gestion des API.

1. Activez une [identité managée affectée par le système ou par l’utilisateur pour Gestion des API](api-management-howto-use-managed-service-identity.md) dans votre instance Gestion des API.

    * Si vous activez une identité managée affectée par l’utilisateur, prenez note de l’**ID client** de l’identité.
    * Si vous devez sauvegarder et restaurer des instances Gestion des API différentes, activez une identité managée dans les instances source et cible.
1. Attribuez à l’identité le rôle **Contributeur aux données Blob du stockage**, délimité par le compte de stockage utilisé pour la sauvegarde et la restauration. Pour attribuer le rôle, utilisez le [portail Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md) ou d’autres outils Azure.

## <a name="calling-the-backup-and-restore-operations"></a>Appel d’opérations de sauvegarde et de restauration

Les API REST sont [Service Gestion des API - Sauvegarde](/rest/api/apimanagement/2020-12-01/api-management-service/backup) et [Service Gestion des API - Restauration](/rest/api/apimanagement/2020-12-01/api-management-service/restore).

> [!NOTE]
> Vous pouvez aussi effectuer les opérations de sauvegarde et de restauration avec les commandes PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) et [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement), respectivement.

Avant d’appeler les opérations de « sauvegarde et de restauration » décrites dans les sections suivantes, définissez l’en-tête de demande d’autorisation de votre appel REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Sauvegarde d’un service Gestion des API

Pour sauvegarder un service Gestion des API, envoyez la demande HTTP suivante :

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

où :

-   `subscriptionId` : ID de l’abonnement qui contient le service Gestion des API que vous tentez de sauvegarder
-   `resourceGroupName` : nom du groupe de ressources de votre service Gestion des API Azure
-   `serviceName` : Nom du service Gestion des API que vous sauvegardez, spécifié au moment de sa création
-   `api-version` : version valide de l’API REST, telle que `2020-12-01` ou `2021-04-01-preview`

Dans le corps de la demande, spécifiez le nom du compte de stockage cible, le nom du conteneur de blobs, le nom de la sauvegarde et le type d’accès au stockage. Si le conteneur de stockage n’existe pas, l’opération de sauvegarde le crée.

#### <a name="access-using-storage-access-key"></a>Accès à l’aide de la clé d’accès de stockage

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### <a name="access-using-managed-identity"></a>Accès à l’aide de l’identité managée

> [!NOTE]
> L’utilisation d’une identité managée Gestion des API pour les opérations de stockage pendant une sauvegarde ou une restauration requiert la version `2021-04-01-preview` ou une version ultérieure de l’API REST Gestion des API.

**Accès à l’aide d’une identité managée affectée par le système**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

**Accès à l’aide d’une identité managée affectée par l’utilisateur**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```


Définissez la valeur de l’en-tête de la demande `Content-Type` sur `application/json`.

La sauvegarde est une opération longue qui peut prendre plusieurs minutes. Si la demande a réussi et que le processus de sauvegarde a commencé, vous recevez le code d’état de réponse `202 Accepted` avec un en-tête `Location`. Envoyez des requêtes `GET` à l’URL dans l’en-tête `Location` pour connaître l’état de l’opération. Quand la sauvegarde est en cours, vous continuez à recevoir le code d’état `202 Accepted`. Un code de réponse `200 OK` indique que l’opération de sauvegarde a réussi.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Restauration d’un service Gestion des API

Pour restaurer un service Gestion des API à partir d’une sauvegarde créée précédemment, envoyez la demande HTTP suivante :

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

où :

-   `subscriptionId` : ID de l’abonnement qui contient le service Gestion des API dans lequel vous restaurez une sauvegarde
-   `resourceGroupName` : Nom du groupe de ressources qui contient le service Gestion des API Azure dans lequel vous restaurez une sauvegarde
-   `serviceName` : Nom du service Gestion des API en cours de restauration, spécifié au moment de sa création
-   `api-version` : version valide de l’API REST, telle que `2020-12-01` ou `2021-04-01-preview`

Dans le corps de la demande, spécifiez le nom du compte de stockage existant, le nom du conteneur de blobs, le nom de la sauvegarde et le type d’accès au stockage. 

#### <a name="access-using-storage-access-key"></a>Accès à l’aide de la clé d’accès de stockage

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### <a name="access-using-managed-identity"></a>Accès à l’aide de l’identité managée

> [!NOTE]
> L’utilisation d’une identité managée Gestion des API pour les opérations de stockage pendant une sauvegarde ou une restauration requiert la version `2021-04-01-preview` ou une version ultérieure de l’API REST Gestion des API.

**Accès à l’aide d’une identité managée affectée par le système**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

**Accès à l’aide d’une identité managée affectée par l’utilisateur**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```

Définissez la valeur de l’en-tête de la demande `Content-Type` sur `application/json`.

La restauration est une opération longue qui peut prendre jusqu’à 30 minutes, voire plus. Si la demande a réussi et que le processus de restauration a commencé, vous recevez le code d’état de réponse `202 Accepted` avec un en-tête `Location`. Envoyez des demandes « GET » à l’URL dans l’en-tête `Location` pour connaître l’état de l’opération. Quand la restauration est en cours, vous continuez à recevoir le code d’état `202 Accepted`. Un code de réponse `200 OK` indique que l’opération de restauration a réussi.

> [!IMPORTANT]
> Le **SKU** du service à restaurer **doit correspondre** à celui du service sauvegardé utilisé pour la restauration.
>
> Les **modifications** de configuration du service (par exemple, API, stratégies, apparence du portail des développeurs) pendant qu’une opération de restauration est en cours **peuvent être écrasées**.

## <a name="constraints-when-making-backup-or-restore-request"></a>Contraintes lors de la création d’une requête de sauvegarde ou de restauration

-   Pendant la sauvegarde, **évitez toutes les modifications de gestion dans le service**, comme mettre à niveau une référence SKU ou la passer à une version antérieure, changer un nom de domaine, etc.
-   La restauration d’une **sauvegarde n’est garantie que pendant 30 jours** à partir du moment de sa création.
-   Les **changements** de configuration du service (par exemple, les API, les stratégies et l’apparence du portail des développeurs) pendant une opération de sauvegarde **peuvent être exclus de la sauvegarde et être perdus**.

-   [Le partage des ressources Cross-Origin (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) ne doit **pas** être activé sur le service BLOB dans le compte de stockage Azure.
-   Le **SKU** du service à restaurer **doit correspondre** à celui du service sauvegardé utilisé pour la restauration.

## <a name="storage-networking-constraints"></a>Contraintes de mise en réseau du stockage

### <a name="access-using-storage-access-key"></a>Accès à l’aide de la clé d’accès de stockage

Si le **[pare-feu][azure-storage-ip-firewall] est activé** sur le compte de stockage et qu’une clé de stockage est utilisée pour l’accès, le client doit **autoriser** l’ensemble des [adresses IP du plan de contrôle de Gestion des API Azure][control-plane-ip-address] sur son compte de stockage à des fins de sauvegarde ou de restauration. Le compte de stockage peut se trouver dans n’importe quelle région Azure, à l’exception de celle où se trouve le service Gestion des API. Par exemple, si le service Gestion des API se trouve dans la région USA Ouest, le compte Stockage Azure peut être dans la région USA Ouest 2 et le client doit ouvrir l’adresse IP 13.64.39.16 du plan de contrôle (adresse IP du plan de contrôle de Gestion des API de la région USA Ouest) sur le pare-feu. Cela est dû au fait que les demandes adressées à Stockage Azure ne font pas l’objet d’une traduction d’adresse réseau source en IP publique à partir du calcul (plan de contrôle de Gestion des API Azure) dans la même région Azure. Les demandes de stockage interrégion feront l’objet d’une traduction d’adresse réseau source en IP publique.

### <a name="access-using-managed-identity"></a>Accès à l’aide de l’identité managée

Si une identité managée affectée par le système Gestion des API est utilisée pour accéder à un compte de stockage avec pare-feu, assurez-vous que le compte de stockage [accorde l’accès aux services Azure approuvés](../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-to-trusted-azure-services).

## <a name="what-is-not-backed-up"></a>Éléments non sauvegardés
-   Les **données d’utilisation** servant à la création des rapports analytiques **ne sont pas incluses** dans la sauvegarde. Utilisez l'[API REST de Gestion des API Azure][azure api management rest api] pour récupérer régulièrement les rapports d'analyse et les conserver en toute sécurité.
-   Certificats [TLS/SSL de domaine personnalisé](configure-custom-domain.md).
-   [Certificats d’autorité de certification personnalisé](api-management-howto-ca-certificates.md) qui incluent des certificats intermédiaires ou racines chargés par le client.
-   Paramètres d’intégration du [réseau virtuel](api-management-using-with-vnet.md).
-   Configuration d’une [identité managée](api-management-howto-use-managed-service-identity.md).
-   Configuration des [diagnostics Azure Monitor](api-management-howto-use-azure-monitor.md).
-   Paramètres [des protocoles et des chiffrements ](api-management-howto-manage-protocols-ciphers.md).
-   Contenu du [portail des développeurs](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management).

La fréquence à laquelle vous effectuez les sauvegardes du service affecte votre objectif de point de récupération. Pour la réduire, nous vous conseillons d’implémenter des sauvegardes régulières et d’effectuer des sauvegardes à la demande quand vous apportez des changements à votre service Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

Consultez les ressources connexes suivantes pour en savoir plus sur le processus de sauvegarde/restauration :

-   [Automating API Management Backup and Restore with Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps) (Automatiser la sauvegarde et la restauration avec Gestion des API avec Logic Apps)
- [Comment migrer une instance Gestion des API Azure d'une région vers une autre](api-management-howto-migrate.md)

Le niveau **Premium** de Gestion des API prend également en charge la [redondance de zone](zone-redundancy.md), qui offre résilience et haute disponibilité à une instance de service dans une région (emplacement) Azure spécifique.

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ip-addresses
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
