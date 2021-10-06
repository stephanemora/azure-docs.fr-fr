---
title: Contrôler l’accès à IoT Hub avec Azure Active Directory
description: Guide du développeur. Guide pratique pour contrôler l’accès à IoT Hub pour les applications back-end à l’aide d’Azure AD et d’Azure RBAC.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a8387a5732ab51add02495b03236b42f9cd4e671
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609438"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>Contrôler l’accès à IoT Hub avec Azure Active Directory

Vous pouvez utiliser Azure AD (Azure Active Directory) pour authentifier les requêtes aux API du service Azure IoT Hub, telles que la création d’identité de l’appareil et l’appel d’une méthode directe. Vous pouvez également utiliser le contrôle d’accès en fonction du rôle (RBAC) Azure pour autoriser ces mêmes API de service. En combinant ces technologies, vous pouvez accorder des autorisations d’accès aux API de service IoT Hub à un principal de sécurité Azure AD. Le principal de sécurité peut être un utilisateur, un groupe ou un principal du service d’application.

L’authentification de l’accès à l’aide d’Azure AD et le contrôle des autorisations avec Azure RBAC offrent une sécurité et une simplicité d’utilisation améliorées par rapport aux [jetons de sécurité](iot-hub-dev-guide-sas.md). Pour limiter les problèmes de sécurité potentiels inhérents aux jetons de sécurité, nous vous recommandons d’[utiliser Azure AD avec votre hub IoT dans la mesure du possible](#azure-ad-access-and-shared-access-policies). 

> [!NOTE]
> L’authentification avec Azure AD n’est pas prise en charge pour les *API d’appareil* IoT Hub (comme les messages appareil-à-cloud et les propriétés de mise à jour signalées). Utilisez des [clés symétriques](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) ou [X.509](iot-hub-x509ca-overview.md) pour authentifier les appareils auprès d’IoT Hub.

## <a name="authentication-and-authorization"></a>Authentification et autorisation

Lorsqu’un principal de sécurité Azure AD demande à accéder à une API de service IoT Hub, l’identité du principal est d’abord *authentifiée*. Pour l’authentification, la demande doit contenir un jeton d’accès OAuth 2.0 au moment de l’exécution. Le nom de ressource pour la demande du jeton est `https://iothubs.azure.net`. Si l’application s’exécute dans une ressource Azure comme une machine virtuelle Azure, une application Azure Functions ou une application Azure App Service, elle peut être représentée en tant qu’[identité managée](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md). 

Une fois le principal Azure AD authentifié, l’étape suivante est l’*autorisation*. Lors de cette étape, IoT Hub utilise le service d’attribution de rôle d’Azure AD pour déterminer les autorisations dont dispose le principal. Si les autorisations du principal correspondent à la ressource ou à l’API demandée, IoT Hub autorise la demande. Ainsi, cette étape exige qu’un ou plusieurs rôles Azure soient attribués au principal de sécurité. IoT Hub fournit des rôles intégrés qui ont des groupes d’autorisations communs.

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>Gérer l’accès à IoT Hub à l’aide de l’attribution de rôle Azure RBAC

Avec Azure AD et RBAC, IoT Hub nécessite que le principal qui demande l’API dispose du niveau d’autorisation approprié pour l’autorisation. Pour octroyer l’autorisation au principal, donnez-lui une attribution de rôle. 

- Si le principal est un utilisateur, un groupe ou un principal du service d’application, suivez les instructions fournies dans [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).
- Si le principal est une identité managée, suivez les instructions fournies dans [Attribuer à une identité managée un accès à une ressource à l’aide du portail Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

Pour garantir des privilèges minimum, attribuez toujours le rôle approprié au niveau de l’[étendue de ressource](#resource-scope) le plus faible possible, à savoir probablement l’étendue IoT Hub.

IoT Hub fournit les rôles intégrés Azure suivants pour autoriser l’accès à l’API de service IoT Hub à l’aide d’Azure AD et de RBAC :

| Rôle | Description | 
| ---- | ----------- | 
| [Contributeur de données IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | Accorde un accès complet aux opérations du plan de données IoT Hub. |
| [Lecteur de données IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | Accorde un accès complet en lecture aux propriétés du plan de données IoT Hub. |
| [Contributeur du Registre IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | Accorde un accès complet au Registre de l’appareil IoT Hub. |
| [IoT Hub Twin Contributor](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | Accorde l’accès en lecture et en écriture à tous les appareils et jumeaux de module IoT Hub. |

Vous pouvez également définir des rôles personnalisés à utiliser avec IoT Hub en combinant les [autorisations](#permissions-for-iot-hub-service-apis) dont vous avez besoin. Pour plus d’informations, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../role-based-access-control/custom-roles.md).

### <a name="resource-scope"></a>Étendue des ressources

Avant d’attribuer un rôle RBAC Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Il est toujours préférable d’accorder la plus petite étendue possible. Les rôles RBAC Azure définis au niveau d’une étendue plus large sont hérités par les ressources qui sont sous eux.

Cette liste décrit les niveaux auxquels vous pouvez étendre l’accès à IoT Hub, en commençant par la plus petite étendue :

- **L’instance IoT Hub.** Dans cette étendue, une attribution de rôle s’applique au hub IoT. Il n’y a pas d’étendue plus étroite qu’une instance IoT Hub individuelle. L’attribution de rôle à des étendues plus petites, telles que l’identité de l’appareil individuel ou la section de jumeau, n’est pas prise en charge.
- **Le groupe de ressources.** Dans cette étendue, une attribution de rôle s’applique à tous les hubs IoT dans le groupe de ressources.
- **L’abonnement.** Dans cette étendue, une attribution de rôle s’applique à tous les hubs IoT dans tous les groupes de ressources de l’abonnement.
- **Un groupe d’administration.** Dans cette étendue, une attribution de rôle s’applique à tous les hubs IoT dans tous les groupes de ressources de tous les abonnements dans le groupe d’administration.

## <a name="permissions-for-iot-hub-service-apis"></a>Autorisations pour les API de service IoT Hub

Le tableau suivant décrit les autorisations disponibles pour les opérations d’API de service IoT Hub. Pour permettre à un client d’appeler une opération particulière, vérifiez que le rôle RBAC attribué au client offre des autorisations suffisantes pour l’opération.

| Action RBAC | Description |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | Lit une identité d’appareil ou de module. |
| `Microsoft.Devices/IotHubs/devices/write`  | Crée ou met à jour une identité d’appareil ou de module.  |
| `Microsoft.Devices/IotHubs/devices/delete` | Supprime une identité d’appareil ou de module. |
| `Microsoft.Devices/IotHubs/twins/read` | Lit un jumeau d’appareil ou de module. |
| `Microsoft.Devices/IotHubs/twins/write` | Écrit un jumeau d’appareil ou de module. |
| `Microsoft.Devices/IotHubs/jobs/read` | Obtient une liste de travaux. |
| `Microsoft.Devices/IotHubs/jobs/write` | Crée ou met à jour un travail. |
| `Microsoft.Devices/IotHubs/jobs/delete` | Supprime un travail. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | Envoie un message cloud-à-appareil à n’importe quel appareil.  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | Reçoit, termine ou abandonne une notification de commentaire de message cloud-à-appareil. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | Supprime toutes les commandes en attente pour un appareil.  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | Appelle une méthode directe sur un appareil ou un module. |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | Reçoit, termine ou abandonne les notifications de chargement de fichier. |
| `Microsoft.Devices/IotHubs/statistics/read` | Lit les statistiques des services et des appareils. |
| `Microsoft.Devices/IotHubs/configurations/read` | Lit des configurations de gestion des appareils. |
| `Microsoft.Devices/IotHubs/configurations/write` | Crée ou met à jour des configurations de gestion des appareils. |
| `Microsoft.Devices/IotHubs/configurations/delete` | Supprime une configuration de gestion des appareils. |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | Applique le contenu de configuration à un appareil de périphérie. |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | Valide la condition cible et les requêtes de métriques personnalisées pour une configuration. |

> [!TIP]
> - L’opération de [Mise à jour en bloc du Registre](/rest/api/iothub/service/bulkregistry/updateregistry) requiert à la fois `Microsoft.Devices/IotHubs/devices/write` et `Microsoft.Devices/IotHubs/devices/delete`.
> - L’opération de [Requête de jumeau](/rest/api/iothub/service/query/gettwins) requiert `Microsoft.Devices/IotHubs/twins/read`.
> - [Obtenir le jumeau numérique](/rest/api/iothub/service/digitaltwin/getdigitaltwin) requiert `Microsoft.Devices/IotHubs/twins/read`. [Mettre à jour le jumeau numérique](/rest/api/iothub/service/digitaltwin/updatedigitaltwin) requiert `Microsoft.Devices/IotHubs/twins/write`.
> - Les commandes [Appeler le composant](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) et [Appeler le niveau racine](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand) requièrent `Microsoft.Devices/IotHubs/directMethods/invoke/action`.

> [!NOTE]
> Pour obtenir des données d’IoT Hub à l’aide d’Azure AD, [configurez le routage vers un hub d’événements distinct](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint). Pour accéder au [point de terminaison compatible Event Hubs intégré](iot-hub-devguide-messages-read-builtin.md), utilisez la méthode de la chaîne de connexion (clé d’accès partagé) comme auparavant. 

## <a name="azure-ad-access-and-shared-access-policies"></a>Stratégies d’accès et d’accès partagé Azure AD

Par défaut, IoT Hub prend en charge l’accès à l’API de service par le biais d’Azure AD et des [stratégies d’accès partagé et des jetons de sécurité](iot-hub-dev-guide-sas.md). Pour limiter les failles de sécurité potentielles inhérentes aux jetons de sécurité, désactivez l’accès avec des stratégies d’accès partagé : 

1. Vérifiez que les clients et les utilisateurs de votre service disposent de droits d’[accès suffisants](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment) à votre hub IoT. Suivez le [principe des privilèges minimum](../security/fundamentals/identity-management-best-practices.md).
1. Accédez à votre IoT Hub dans le [Portail Azure](https://portal.azure.com).
1. Dans le volet gauche, sélectionnez **Stratégies d’accès partagé**.
1. Sous **Connecter à l’aide de stratégies d’accès partagé**, sélectionnez **Refuser**.
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="Capture d’écran montrant comment désactiver les stratégies d’accès partagé IoT Hub.":::
1. Lisez l’avertissement, puis sélectionnez **Enregistrer**.

Vos API de service IoT Hub sont désormais accessibles uniquement par le biais d’Azure AD et de RBAC.

## <a name="azure-ad-access-from-the-azure-portal"></a>Accès à Azure AD à partir du portail Azure

Lorsque vous essayez d’accéder à IoT Hub, le portail Azure vérifie d’abord si un rôle Azure vous a été attribué avec `Microsoft.Devices/iotHubs/listkeys/action`. Si c’est le cas, le portail Azure utilise les clés des stratégies d’accès partagé pour accéder à IoT Hub. Sinon, il tente d’accéder aux données à l’aide de votre compte Azure AD. 

Pour accéder à IoT Hub à partir du portail Azure à l’aide de votre compte Azure AD, vous avez besoin d’autorisations pour accéder aux ressources de données IoT Hub (comme les appareils et les jumeaux). Vous avez également besoin d’autorisations pour accéder à la ressource IoT Hub dans le portail Azure. Les rôles intégrés fournis par IoT Hub accordent l’accès à des ressources telles que des appareils et des jumeaux, mais ils n’accordent pas l’accès à la ressource IoT Hub. Par conséquent, l’accès au portail nécessite également l’attribution d’un rôle Azure Resource Manager, comme [Lecteur](../role-based-access-control/built-in-roles.md#reader). Le rôle Lecteur est un bon choix, car il s’agit du rôle le plus restreint qui vous permet de naviguer dans le portail. Il n’inclut pas l’autorisation `Microsoft.Devices/iotHubs/listkeys/action` (qui donne accès à toutes les ressources de données IoT Hub par le biais de stratégies d’accès partagé). 

Pour vous assurer qu’un compte n’a pas accès en dehors des autorisations qui lui sont attribuées, n’incluez pas l’autorisation `Microsoft.Devices/iotHubs/listkeys/action` lorsque vous créez un rôle personnalisé. Par exemple, pour créer un rôle personnalisé qui peut lire les identités des appareils mais ne peut pas créer ou supprimer d’appareil, créez un rôle personnalisé qui :
- A l’action de données `Microsoft.Devices/IotHubs/devices/read`.
- N’a pas l’action de données `Microsoft.Devices/IotHubs/devices/write`.
- N’a pas l’action de données `Microsoft.Devices/IotHubs/devices/delete`.
- N’a pas l’action `Microsoft.Devices/iotHubs/listkeys/action`.

Ensuite, vérifiez que le compte n’a pas d’autres rôles qui disposent de l’autorisation `Microsoft.Devices/iotHubs/listkeys/action`, comme [Propriétaire](../role-based-access-control/built-in-roles.md#owner) ou [Contributeur](../role-based-access-control/built-in-roles.md#contributor). Pour permettre au compte d’accéder aux ressources et de naviguer dans le portail, attribuez le rôle [Lecteur](../role-based-access-control/built-in-roles.md#reader).

## <a name="azure-iot-extension-for-azure-cli"></a>Extension Azure IoT pour Azure CLI

La plupart des commandes sur IoT Hub prennent en charge l’authentification Azure AD. Vous pouvez contrôler le type d’authentification utilisé pour exécuter des commandes à l’aide du paramètre `--auth-type`, qui accepte des valeurs `key` ou `login`. La valeur par défaut est `key`.

- Lorsque `--auth-type` a la valeur `key`, comme précédemment, l’interface CLI découvre automatiquement une stratégie appropriée lors de l’interaction avec IoT Hub.

- Lorsque `--auth-type` a la valeur `login`, un jeton d’accès du principal connecté à Azure CLI est utilisé pour l’opération.

Pour plus d’informations, consultez la [page de publication de l’extension Azure IoT pour Azure CLI](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12).

## <a name="sdk-samples"></a>Exemples du Kit de développement logiciel (SDK)

- [.NET Microsoft.Azure.Devices SDK sample](https://aka.ms/iothubaadcsharpsample)
- [Exemple du kit SDK Java](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les avantages de l’utilisation d’Azure AD dans votre application, consultez [Intégration à Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md).
- Pour plus d’informations sur la demande de jetons d’accès à partir d’Azure AD pour les utilisateurs et les principaux de service, consultez [Scénarios d’authentification pour Azure AD](../active-directory/develop/authentication-vs-authorization.md).
