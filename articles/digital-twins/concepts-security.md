---
title: Sécurité pour les solutions Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Comprendre les meilleures pratiques de sécurité avec Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730505"
---
# <a name="secure-azure-digital-twins"></a>Sécuriser Azure Digital Twins

Pour assurer la sécurité, Azure Digital Twins permet un contrôle d’accès précis sur des données, ressources et actions spécifiques de votre déploiement. En effet, il utilise une gestion granulaire des rôles et une stratégie de gestion des autorisations appelée **contrôle d’accès en fonction du rôle Azure (Azure RBAC)** . Pour plus d’informations sur les principaux généraux d’Azure RBAC, voir [ici](../role-based-access-control/overview.md).

Azure Digital Twins prend également en charge le chiffrement des données au repos.

## <a name="roles-and-permissions-with-azure-rbac"></a>Rôles et autorisations avec le contrôle d’accès en fonction du rôle (RBAC) Azure

Azure RBAC est fourni à Azure Digital Twins via l’intégration à [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Vous pouvez utiliser Azure RBAC pour accorder des autorisations à un *principal de sécurité*, qui peut être un utilisateur, un groupe ou un principal de service d’application. Le principal de sécurité est authentifié par Azure AD et reçoit un jeton OAuth 2.0 en retour. Ce jeton peut être utilisé pour autoriser une demande d’accès à une instance Azure Digital Twins.

### <a name="authentication-and-authorization"></a>Authentification et autorisation

Avec Azure AD, l’accès est un processus en deux étapes. Quand un principal de sécurité (un utilisateur, un groupe ou une application) tente d’accéder à une entité Azure Digital Twins, la requête doit être *authentifiée* et *autorisée*. 

1. Pour commencer, l’identité du principal de sécurité est *authentifiée*, et un jeton OAuth 2.0 est renvoyé.
2. Ensuite, ce jeton est transmis dans une requête adressée au service Azure Digital Twins pour *autoriser* l’accès à la ressource spécifiée.

L’étape d’authentification nécessite que toute requête d’application contienne un jeton d’accès OAuth 2.0 au moment de l’exécution. Si une application s’exécute à partir d’une entité Azure telle qu’une application [Azure Functions](../azure-functions/functions-overview.md), elle peut utiliser une **identité managée** pour accéder aux ressources. Vous trouverez plus d'informations sur les identités managées dans la section suivante.

L’étape d’autorisation exige qu’un rôle Azure soit attribué au principal de sécurité. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations dont disposera le principal. Azure Digital Twins fournit des rôles Azure qui englobent des ensembles d’autorisations pour les ressources Azure Digital Twins. Ces rôles sont décrits plus loin dans cet article.

Pour en savoir plus sur les rôles et les attributions de rôles pris en charge dans Azure, consultez [*Comprendre les différents rôles*](../role-based-access-control/rbac-and-directory-admin-roles.md) dans la documentation RBAC Azure.

#### <a name="authentication-with-managed-identities"></a>Authentification avec des identités managées

La fonctionnalité [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) vous permet de créer une identité sécurisée associée au déploiement où s’exécute le code de votre application. Vous pouvez ensuite associer cette identité à des rôles de contrôle d’accès pour accorder des autorisations personnalisées pour l’accès aux ressources Azure nécessaires à votre application.

Avec les identités managées, la plateforme Azure gère cette identité d’exécution. Vous n’avez pas besoin de stocker et de protéger des clés d’accès dans le code ou la configuration de votre application, que ce soit pour l’identité elle-même ou pour les ressources auxquelles vous devez accéder. Une application cliente Azure Digital Twins en cours d’exécution à l’intérieur d’une application Azure App Service n’a pas besoin de gérer des clés et des règles SAS ou d’autres jetons d’accès. L’application cliente a uniquement besoin de l’adresse de point de terminaison de l’espace de noms Azure Digital Twins. Lorsque l’application se connecte, Azure Digital Twins lie le contexte de l’entité managée au client. Une fois associé à une identité managée, votre client Azure Digital Twins peut effectuer toutes les opérations autorisées. L’autorisation sera ensuite accordée en associant une entité gérée à un rôle Azure dans Azure Digital Twins (décrit ci-dessous).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorisation : Rôles Azure pour Azure Digital Twins

Azure fournit **deux rôles intégrés Azure** pour autoriser l’accès à aux [API de plan de données](how-to-use-apis-sdks.md#overview-data-plane-apis) d’Azure Digital Twins. Vous pouvez faire référence aux rôles par nom ou par ID :

| Rôle intégré | Description | id | 
| --- | --- | --- |
| Propriétaire des données Azure Digital Twins | Octroie un accès total aux ressources Azure Digital Twins | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Lecteur des données Azure Digital Twins | Octroie un accès en lecture seule aux ressources Azure Digital Twins | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Vous pouvez attribuer des rôles de deux manières :
* Via le volet de contrôle d’accès (IAM) pour Azure Digital Twins dans le portail Azure (consultez [*Attribuer des rôles Azure à l’aide du portail Azure*](../role-based-access-control/role-assignments-portal.md))
* Via les commandes CLI pour ajouter ou supprimer un rôle

Pour plus d’informations sur la procédure à suivre, testez le [*tutoriel Azure Digital Twins : Connecter une solution de bout en bout*](tutorial-end-to-end.md).

Pour plus d’informations sur la définition des rôles intégrés, consultez [*Comprendre les définitions de rôles*](../role-based-access-control/role-definitions.md) dans la documentation RBAC Azure. Pour plus d’informations sur la création de rôles personnalisés Azure, consultez [*Rôles personnalisés Azure*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatisation des rôles

Lorsque vous faites référence à des rôles dans des scénarios automatisés, il est recommandé d’y faire référence par leurs **ID** plutôt que par leurs noms. Les noms peuvent changer d’une version à l’autre, mais pas les ID, ce qui en fait une référence plus stable pour l’automatisation.

> [!TIP]
> Si vous affectez des rôles à l’aide d’une applet de commande, comme `New-AzRoleAssignment` ([référence](/powershell/module/az.resources/new-azroleassignment)), vous pouvez utiliser le paramètre `-RoleDefinitionId` au lieu de `-RoleDefinitionName` pour passer un ID à la place d’un nom pour le rôle.

### <a name="permission-scopes"></a>Étendues d’autorisation

Avant d’attribuer un rôle Azure à un principal de sécurité, déterminez l’étendue de l’accès dont doit disposer le principal de sécurité. Selon les bonnes pratiques, il est préférable d’accorder la plus petite étendue possible.

La liste suivante décrit les niveaux auxquels vous pouvez étendre l’accès aux ressources Azure Digital Twins.
* Modèles : Les actions de cette ressource dictent le contrôle de [modèles](concepts-models.md) chargés dans Azure Digital Twins.
* Interroger le graphique de jumeaux numériques : Les actions de cette ressource déterminent la capacité à exécuter des [opérations de requête](concepts-query-language.md) sur des jumeaux numériques dans le graphique Azure Digital Twins.
* Jumeau numérique : les actions de cette ressource permettent de contrôler les opérations CRUD sur les [jumeaux numériques](concepts-twins-graph.md) dans le graphique des jumeaux numériques.
* Relation de jumeaux numériques : les actions de cette ressource définissent le contrôle des opérations CRUD sur les opérations relatives aux [relations](concepts-twins-graph.md) entre des jumeaux numériques dans le graphique des jumeaux.
* Itinéraire des événements : les actions de cette ressource déterminent les autorisations de [routage d’événements](concepts-route-events.md) d’Azure Digital Twins vers un service de point de terminaison comme [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)ou [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Résolution des problèmes d'autorisations

Si un utilisateur tente d’effectuer une action qui n’est pas autorisée par son rôle, il peut recevoir un message d’erreur `403 (Forbidden)` de la demande de service. Si vous souhaitez en savoir plus, également sur les étapes à suivre pour le dépannage, consultez [*Résolution des problèmes : échec de la requête Azure Digital Twins avec l’état : 403 (Interdit)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Identité managée pour accéder à d’autres ressources (préversion)

La configuration d’une [identité managée](../active-directory/fundamentals/active-directory-whatis.md) **Azure Active Directory (Azure AD)** pour une instance Azure Digital Twins permet à l’instance d’accéder facilement à d’autres ressources protégées par Azure AD, par exemple [Azure Key Vault](../key-vault/general/overview.md). Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Pour plus d’informations sur les identités managées dans Azure AD, consultez  [*Identités managées pour les ressources Azure*](../active-directory/managed-identities-azure-resources/overview.md). 

Azure prend en charge deux types d’identités managées : celles affectées par le système et celles affectées par l’utilisateur. À l’heure actuelle, Azure Digital Twins prend uniquement en charge les **identités affectées par le système**. 

Vous pouvez utiliser une identité managée affectée par le système pour votre instance Azure Digital pour l’authentification auprès d’[un point de terminaison personnalisé](concepts-route-events.md#create-an-endpoint). Azure Digital Twins prend en charge l’authentification basée sur l’identité affectée par le système aux points de terminaison pour les destinations [Event Hub](../event-hubs/event-hubs-about.md) et  [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) , et à un point de terminaison [Conteneur Stockage Azure](../storage/blobs/storage-blobs-introduction.md)  pour les [événements de lettres mortes](concepts-route-events.md#dead-letter-events). Les points de terminaison [Event Grid](../event-grid/overview.md)  ne sont actuellement pas pris en charge pour les identités managées.

Pour obtenir des instructions sur l’activation d’une identité managée par le système pour Azure Digital Twins afin de l’utiliser pour acheminer des événements, consultez le [*Guide pratique : Activer une identité managée pour les événements de routage (préversion)*](./how-to-enable-managed-identities-portal.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Accès réseau privé avec Azure Private Link (préversion)

[Azure Private Link](../private-link/private-link-overview.md) est un service qui vous permet d’accéder aux ressources Azure (par exemple [Azure Event Hubs](../event-hubs/event-hubs-about.md), [Stockage Azure](../storage/common/storage-introduction.md) et [Azure Cosmos DB](../cosmos-db/introduction.md)) ainsi qu’aux services de partenaire ou de client hébergés par Azure via un point de terminaison privé dans votre [réseau virtuel (VNet) Azure](../virtual-network/virtual-networks-overview.md). 

De même, vous pouvez utiliser des points de terminaison privés pour votre instance Azure Digital Twin afin de permettre aux clients situés dans votre réseau virtuel d’accéder de façon sécurisée à l’instance via une liaison privée. 

Le point de terminaison privé utilise une adresse IP de l’espace d’adressage de votre réseau virtuel Azure. Le trafic entre un client de votre réseau privé et l’instance Azure Digital Twins traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine son exposition à l’Internet public. Voici une représentation visuelle de ce système :

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Diagramme montrant un réseau pour une société PowerGrid, constitué d’un réseau virtuel protégé sans accès à Internet/à un cloud public, se connectant via une liaison privée vers une instance Azure Digital Twins appelée CityOfTwins.":::

La configuration d’un point de terminaison privé pour votre instance Azure Digital Twins vous permet de sécuriser cette instance et d’éliminer l’exposition publique, tout en évitant l’exfiltration de données à partir de votre réseau virtuel.

Pour obtenir des instructions sur la configuration d’une liaison privée pour Azure Digital Twins, consultez le [*Guide pratique : Activer l’accès privé avec Private Link (préversion)*](./how-to-enable-private-link-portal.md).

### <a name="design-considerations"></a>Remarques relatives à la conception 

Lorsque vous utilisez une liaison privée pour Azure Digital Twins, voici quelques facteurs à prendre en compte :
* **Prix** : Pour plus d’informations sur les tarifs, consultez  [Tarification Azure Private Link](https://azure.microsoft.com/pricing/details/private-link). 
* **Disponibilité régionale** : Pour Azure Digital Twins, cette fonctionnalité est disponible dans toutes les régions Azure où Azure Digital Twins est disponible. 
* **Nombre maximal de points de terminaison privés par instance Azure Digital Twins** : 10

Pour plus d’informations sur les limites d’une liaison privée, consultez la  [documentation Azure Private Link : Limitations](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Balises de service

Une **étiquette de service** représente un groupe de préfixes d’adresses IP d’un service Azure donné. Microsoft gère les préfixes d’adresses englobés par l’étiquette de service et met à jour automatiquement l’étiquette de service quand les adresses changent, ce qui réduit la complexité des mises à jour fréquentes relatives aux règles de sécurité réseau. Pour plus d’informations sur les étiquettes de service, consultez  [*Étiquettes de réseau virtuel*](../virtual-network/service-tags-overview.md). 

Vous pouvez utiliser des étiquettes de service pour définir des contrôles d’accès réseau sur des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md#security-rules) ou [Pare-feu Azure](../firewall/service-tags.md), en utilisant des étiquettes de service à la place d’adresses IP spécifiques lorsque vous créez des règles de sécurité. En spécifiant le nom de l’étiquette de service (en l’occurrence,  **AzureDigitalTwins**) dans le champ *source* ou de *destination* approprié d’une règle, vous pouvez autoriser ou refuser le trafic pour le service correspondant. 

Vous trouverez ci-dessous les détails de l’étiquette de service **AzureDigitalTwins**.

| Tag | Objectif | Peut-elle utiliser le trafic entrant ou sortant ? | Peut-elle être étendue à une zone régionale ? | Peut-elle être utilisée avec le Pare-feu Azure ? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Remarque : Cette étiquette ou les adresses IP couvertes par cette étiquette peuvent être utilisées pour restreindre l’accès aux points de terminaison configurés pour des [routes d’événements](concepts-route-events.md). | Trafic entrant | Non | Oui |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Utilisation d’étiquettes de service pour accéder à des points de terminaison de route d’événement 

Voici les étapes à suivre pour accéder à des points de terminaison de [route d’événement](concepts-route-events.md) à l’aide d’étiquettes de service avec Azure Digital Twins.

1. Tout d’abord, téléchargez cette référence de fichier JSON montrant des plages d’adresses IP Azure et des étiquettes de service : [*Plages d’IP Azure et étiquettes de services*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Recherchez les plages d’IP « AzureDigitalTwins » dans le fichier JSON.  

3. Consultez la documentation de la ressource externe connectée au point de terminaison (par exemple [Event Grid](../event-grid/overview.md), [Event Hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) ou [Stockage Azure](../storage/blobs/storage-blobs-overview.md) pour [les événements de lettres mortes](concepts-route-events.md#dead-letter-events)) afin de découvrir comment définir des filtres IP pour cette ressource.

4. Définissez des filtres IP sur la ou les ressources externes en utilisant les plages d’IP de l’*Étape 2*.  

5. Mettez régulièrement à jour les plages d’IP en fonction des besoins. Les plages peuvent changer au fil du temps. Il convient donc de les vérifier régulièrement et de les actualiser si nécessaire. La fréquence de ces mises à jour peut varier, mais il est judicieux de les vérifier une fois par semaine.

## <a name="encryption-of-data-at-rest"></a>Chiffrement des données au repos

Azure Digital Twins assure le chiffrement des données au repos et en transit à mesure qu'elles sont écrites dans nos centres de données, et les déchiffre pour vous lorsque vous y accédez. Ce chiffrement s'effectue à l'aide d'une clé de chiffrement gérée par Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)

Azure Digital Twins ne prend pas actuellement en charge **le partage des ressources cross-origin (CORS)** . Par conséquent, si vous appelez une API REST à partir d’une application de navigateur, d’une interface de [Gestion des API (APIM)](../api-management/api-management-key-concepts.md) ou d’un connecteur [Power Apps](/powerapps/powerapps-overview), un message d’erreur de stratégie peut s’afficher.

Pour résoudre cette erreur, vous pouvez effectuer l’une des actions suivantes :
* Supprimez l’en-tête CORS `Access-Control-Allow-Origin` du message. Cet en-tête indique si la réponse peut être partagée. 
* Vous pouvez également créer un proxy CORS et envoyer la requête de l’API REST Azure Digital Twins à travers celui-ci. 

## <a name="next-steps"></a>Étapes suivantes

* Consultez ces concepts en action dans [*Guide pratique : Configurer une instance et l’authentification*](how-to-set-up-instance-portal.md).

* Voyez comment interagir avec ces concepts à partir du code d’application cliente dans [*Guide pratique : Écrire le code d’authentification d’une application*](how-to-authenticate-client.md).

* Découvrez plus d’informations sur [Azure RBAC](../role-based-access-control/overview.md).