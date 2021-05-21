---
title: Contrôler l’accès à IoT Hub à l’aide d’Azure Active Directory | Microsoft Docs
description: 'Guide du développeur : Comment contrôler l’accès à IoT Hub pour les applications principales à l’aide d’Azure AD et d’Azure RBAC.'
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 196afc38c24254c4628173180205a858d1085eeb
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489698"
---
# <a name="control-access-to-iot-hub-using-azure-active-directory"></a>Contrôler l’accès à IoT Hub à l’aide d’Azure Active Directory

Azure IoT Hub prend en charge l’utilisation d’Azure Active Directory (AAD) pour authentifier les demandes à ses API de service, telles que la création de l’identité de l’appareil ou l’appel d’une méthode directe. En outre, IoT Hub prend en charge l’autorisation des mêmes API de service avec le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Ensemble, vous pouvez accorder des autorisations pour accéder aux API de service d’IoT Hub pour un principal de sécurité AAD, qui peut être un utilisateur, un groupe ou un principal de service d’application.

L’authentification de l’accès avec Azure AD et le contrôle des autorisations avec Azure RBAC offrent une sécurité et une facilité d’utilisation supérieures à celles des [jetons de sécurité](iot-hub-dev-guide-sas.md). Pour limiter les failles de sécurité potentielles inhérentes aux jetons de sécurité, Microsoft recommande d’utiliser Azure AD avec IoT Hub chaque fois que cela est possible.

> [!NOTE]
> L’authentification avec Azure AD n’est pas prise en charge pour les *API d’appareil* d’IoT Hub (comme les messages appareil-à-cloud et les propriétés de mise à jour signalées). Utilisez des [clés symétriques](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) ou [X.509](iot-hub-x509ca-overview.md) pour authentifier les appareils auprès d’IoT Hub.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Lorsqu’un principal de sécurité Azure AD demande à accéder à une API de service IoT Hub, l’identité du principal est d’abord *authentifiée*. Cette étape nécessite que la demande contienne un jeton d’accès OAuth 2.0 au moment de l’exécution. Le nom de ressource pour la demande du jeton est `https://iothubs.azure.net`. Si l’application s’exécute dans une ressource Azure comme une machine virtuelle Azure, une application de fonction Azure ou une application App Service, elle peut être représentée comme une [identité gérée](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md). 

Une fois le principal Azure AD authentifié, la deuxième étape est l’*autorisation*. Dans cette étape, IoT Hub effectue des vérifications auprès du service d’attribution de rôle d’Azure AD pour connaître les autorisations dont dispose le principal. Si les autorisations du principal correspondent à la ressource ou à l’API demandée, IoT Hub autorise la demande. Ainsi, cette étape exige qu’un ou plusieurs rôles Azure soient attribués au principal de sécurité. IoT Hub fournit des rôles intégrés qui ont des groupes d’autorisations communs.

## <a name="manage-access-to-iot-hub-using-azure-rbac-role-assignment"></a>Gérer l’accès à IoT Hub à l’aide de l’attribution de rôle Azure RBAC

Avec Azure AD et RBAC, IoT Hub nécessite que le principal qui demande l’API dispose du niveau d’autorisation approprié pour l’autorisation. Pour octroyer l’autorisation au principal, donnez à ce principal une attribution de rôle. 

- Si le principal est un utilisateur, un groupe ou un principal du service d’application, suivez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
- Si le principal est une identité gérée, suivez [Attribuer à une identité managée un accès à une ressource à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

Pour garantir des privilèges minimum, attribuez toujours le rôle approprié au niveau de l’[étendue de ressource](#resource-scope) le plus faible possible, ce qui est probablement l’étendue IoT Hub.

IoT Hub fournit les rôles intégrés Azure suivants pour autoriser l’accès à l’API de service IoT Hub à l’aide d’Azure AD et de RBAC :

| Role | Description | 
| ---- | ----------- | 
| [Contributeur de données IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | Permet un accès complet à aux opérations du plan de données IoT Hub. |
| [Lecteur de données IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | Permet un accès en lecture complet aux propriétés du plan de données IoT Hub. |
| [Contributeur du Registre IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | Permet un accès complet au Registre de l’appareil IoT Hub. |
| [IoT Hub Twin Contributor](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | Autorise l’accès en lecture et en écriture à tous les appareils et jumeaux de module IoT Hub. |

Vous pouvez également définir des rôles personnalisés à utiliser avec IoT Hub en combinant les [autorisations](#permissions-for-iot-hub-service-apis) dont vous avez besoin. Pour plus d’informations, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/custom-roles.md).

### <a name="resource-scope"></a>Étendue des ressources

Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès à IoT Hub, en commençant par la plus petite étendue :

- **L’instance IoT Hub.** Dans cette étendue, une attribution de rôle s’applique à l’instance IoT Hub. Il n’y a pas d’étendue plus étroite qu’une instance IoT Hub individuelle. L’attribution de rôle à des étendues plus petites, telles que l’identité de l’appareil individuel ou la section de jumeau, n’est pas prise en charge.
- **Le groupe de ressources.** Pour cette étendue, l’attribution de rôle s’applique à toutes les instances IoT Hub dans le groupe de ressources.
- **L’abonnement.** Pour cette étendue, l’attribution de rôle s’applique à toutes les instances IoT Hub dans tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à tous les conteneurs ou à toutes les files d’attente dans tous les comptes de stockage de tous les groupes de ressources de tous les abonnements dans le groupe d’administration.

## <a name="permissions-for-iot-hub-service-apis"></a>Autorisations pour les API de service IoT Hub

Les tableaux suivants décrivent les autorisations disponibles pour les opérations d’API de service IoT Hub. Pour permettre à un client d’appeler une opération particulière, vérifiez que le rôle RBAC attribué au client offre des autorisations suffisantes pour cette opération.

| Action RBAC | Description |
|-|-|
| Microsoft.Devices/IotHubs/devices/read | Lit une identité d’appareil ou de module |
| Microsoft.Devices/IotHubs/devices/write  | Crée ou met à jour une identité d’appareil ou de module  |
| Microsoft.Devices/IotHubs/devices/delete | Supprime une identité d’appareil ou de module |
| Microsoft.Devices/IotHubs/twins/read | Lit un jumeau d’appareil ou de module |
| Microsoft.Devices/IotHubs/twins/write | Écrit un jumeau d’appareil ou de module |
| Microsoft.Devices/IotHubs/jobs/read | Obtient une liste des tâches |
| Microsoft.Devices/IotHubs/jobs/write | Crée ou met à jour tous les travaux |
| Microsoft.Devices/IotHubs/jobs/delete | Supprime tous les travaux |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action | Envoie un message cloud-à-appareil à n’importe quel appareil  |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action | Reçoit, termine ou abandonne la notification de commentaire sur les messages cloud-à-appareil |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action | Supprime toutes les commandes en attente pour un appareil  |
| Microsoft.Devices/IotHubs/directMethods/invoke/action | Appelle une méthode directe sur un appareil |
| Microsoft.Devices/IotHubs/fileUpload/notifications/action  | Reçoit, termine ou abandonne les notifications de téléchargement de fichier |
| Microsoft.Devices/IotHubs/statistics/read | Lit les statistiques des services et des appareils |
| Microsoft.Devices/IotHubs/configurations/read | Lit les configurations de gestion des appareils |
| Microsoft.Devices/IotHubs/configurations/write | Crée ou met à jour des configurations de gestion des appareils |
| Microsoft.Devices/IotHubs/configurations/delete | Supprime une configuration de gestion des appareils |
| Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action  | Applique le contenu de configuration à un périphérique |
| Microsoft.Devices/IotHubs/configurations/testQueries/action | Valide la condition cible et les requêtes de métriques personnalisées pour une configuration |

> [!TIP]
> - L’opération de [Mise à jour en bloc du Registre](/rest/api/iothub/service/bulkregistry/updateregistry) *requiert à la fois* `Microsoft.Devices/IotHubs/devices/write` *et* `Microsoft.Devices/IotHubs/devices/delete`.
> - L’opération de [Requête de jumeau](/rest/api/iothub/service/query/gettwins) requiert `Microsoft.Devices/IotHubs/twins/read`.
> - [Obtenir le jumeau numérique](/rest/api/iothub/service/digitaltwin/getdigitaltwin) requiert `Microsoft.Devices/IotHubs/twins/read` tandis que [Mettre à jour le jumeau numérique](/rest/api/iothub/service/digitaltwin/updatedigitaltwin) requiert `Microsoft.Devices/IotHubs/twins/write`
> - Les commandes [Appeler le composant](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) et [Appeler le niveau racine](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand) requièrent `Microsoft.Devices/IotHubs/directMethods/invoke/action`.

## <a name="azure-ad-access-from-azure-portal"></a>Accès Azure AD à partir du portail Azure

Lorsque vous essayez d’accéder à IoT Hub, le portail Azure vérifie d’abord si un rôle Azure vous a été attribué avec **Microsoft.Devices/iotHubs/listkeys/action**. Dans ce cas, le portail Azure utilise les clés des stratégies d’accès partagé pour accéder à IoT Hub. Sinon, le portail Azure tente d’accéder aux données à l’aide de votre compte Azure AD. 

Pour accéder à IoT Hub à partir du portail Azure à l’aide de votre compte Azure AD, vous avez besoin d’autorisations pour accéder aux ressources de données IoT Hub (comme les appareils et les jumeaux), et vous avez également besoin d’autorisations pour accéder à la ressource IoT Hub dans le portail Azure. Les rôles intégrés fournis par IoT Hub accordent l’accès à des ressources telles que des appareils et des jumeaux, mais ils n’accordent pas l’accès à la ressource IoT Hub. Ainsi, l’accès au portail nécessite également l’attribution d’un rôle Azure Resource Manager (ARM), comme [Lecteur](../role-based-access-control/built-in-roles.md#reader). Le rôle Lecteur est un bon choix, car il s’agit du rôle le plus restreint qui vous permet de naviguer dans le portail, et il n’inclut pas l’autorisation **Microsoft.Devices/iotHubs/listkeys/action** (qui donne accès à toutes les ressources de données IoT Hub par le biais de stratégies d’accès partagé). 

Pour vous assurer qu’un compte n’a pas d’accès en dehors des autorisations qui lui sont attribuées, n’incluez *pas* l’autorisation **Microsoft.Devices/iotHubs/listkeys/action** lors de la création d’un rôle personnalisé. Par exemple, pour créer un rôle personnalisé qui peut lire les identités des appareils, mais ne peut pas créer ou supprimer des appareils, créez un rôle personnalisé qui :
- Contient l’action de données **Microsoft.Devices/IotHubs/devices/read**
- N’a pas l’action de données **Microsoft.Devices/IotHubs/devices/write**
- N’a pas l’action de données **Microsoft.Devices/IotHubs/devices/delete**
- N’a pas l’action **Microsoft.Devices/iotHubs/listkeys/action**

Ensuite, assurez-vous que le compte n’a pas d’autres rôles qui disposent de l’autorisation **Microsoft.Devices/iotHubs/listkeys/action**, comme [Propriétaire](../role-based-access-control/built-in-roles.md#owner) ou [Contributeur](../role-based-access-control/built-in-roles.md#contributor). Pour permettre au compte d’accéder aux ressources et de naviguer dans le portail, attribuez le rôle [Lecteur](../role-based-access-control/built-in-roles.md#reader).

## <a name="built-in-event-hub-compatible-endpoint-doesnt-support-azure-ad-authentication"></a>Le point de terminaison compatible avec Event Hub intégré ne prend pas en charge l’authentification Azure AD

Le [point de terminaison intégré](iot-hub-devguide-messages-read-builtin.md) ne prend pas en charge l’intégration Azure AD. Il n’est pas possible d’y accéder à l’aide d’un principal de sécurité ou d’une identité managée. Pour accéder au point de terminaison intégré, utilisez la méthode de la chaîne de connexion (clé d’accès partagé) comme avant.

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

- [.NET Microsoft.Azure.Devices SDK sample](https://aka.ms/iothubaadcsharpsample)
- [Exemple du kit SDK Java](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les avantages de l’utilisation d’Azure AD dans votre application, consultez [Intégration à Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md).
- Pour plus d’informations sur la demande de jetons d’accès à partir d’Azure AD pour les utilisateurs et les principaux de service, consultez [Scénarios d’authentification pour Azure AD](../active-directory/develop/authentication-vs-authorization.md).