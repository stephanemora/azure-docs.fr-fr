---
title: Rôles intégrés pour les ressources Azure | Microsoft Docs
description: Décrit les rôles intégrés pour le contrôle d’accès en fonction du rôle (RBAC) et des ressources Azure. Répertorie les actions, notActions, dataActions et notDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 63b1adc7b25b732cda147c5c1d11cc37e7b39248
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562018"
---
# <a name="built-in-roles-for-azure-resources"></a>Rôles intégrés pour les ressources Azure

Le [contrôle d’accès en fonction du rôle (RBAC)](overview.md) a plusieurs rôles intégrés pour les ressources Azure que vous pouvez affecter aux utilisateurs, groupes, principaux de service et identités managées. Les attributions de rôles vous permettent de contrôler l’accès aux ressources Azure. Si les rôles intégrés ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres [rôles personnalisés pour les ressources Azure](custom-roles.md).

Cet article répertorie les rôles intégrés pour les ressources Azure, qui sont en constante évolution. Pour obtenir les derniers rôles, utilisez la commande [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az role definition list](/cli/azure/role/definition#az-role-definition-list). Si vous recherchez des rôles d’administrateur pour Azure Active Directory, consultez [Autorisations de rôles d’administrateur dans Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descriptions des rôles intégrés

Le tableau ci-après fournit une brève description de chaque rôle intégré. Cliquez sur le nom d’un rôle pour voir la liste de `Actions`, `NotActions`, `DataActions` et `NotDataActions` concernant ce rôle. Pour plus d’informations sur la signification de ces actions et la manière dont elles s'appliquent en termes de gestion et de données, consultez [Comprendre les définitions de rôle relatives aux ressources Azure](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Rôle intégré | Description | Id |
> | --- | --- | --- |
> | [Propriétaire](#owner) | Permet de tout gérer, notamment l’accès aux ressources. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Contributeur](#contributor) | Permet de tout gérer, à l’exception de l’octroi de l’accès aux ressources. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Lecteur](#reader) | Vous permet de tout afficher, mais sans apporter de modifications. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [AcrDelete](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | signataire d’image ACR | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | tirer (pull) acr | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | envoyer (push) acr | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | lecteur de données de quarantaine ACR | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | écriture de données de quarantaine ACR | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Contributeur du service Gestion des API](#api-management-service-contributor) | Peut gérer le service et les API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rôle d’opérateur du service Gestion des API](#api-management-service-operator-role) | Peut gérer le service, mais pas les API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Rôle de lecteur du service Gestion des API](#api-management-service-reader-role) | Accès en lecture seule au service et aux API | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Propriétaire des données App Configuration](#app-configuration-data-owner) | Permet l’accès total aux données App Configuration. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Lecteur des données App Configuration](#app-configuration-data-reader) | Permet de lire les données App Configuration. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Contributeur de composants Application Insights](#application-insights-component-contributor) | Gérer les composants Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Débogueur de capture instantanée d’Application Insights](#application-insights-snapshot-debugger) | Autorise l’utilisateur à consulter et à télécharger les instantanés de débogage collectés à l’aide du débogueur de capture instantanée Application Insights. Ces autorisations ne sont pas incluses dans les rôles [Propriétaire](#owner) et [Contributeur](#contributor). | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Opérateur de travaux Automation](#automation-job-operator) | Permet de créer et de gérer des travaux avec des runbooks Automation. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Opérateur Automation](#automation-operator) | Les opérateurs d’Automation sont en mesure de démarrer, d’arrêter, de suspendre et de reprendre des travaux | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Opérateur de runbook Automation](#automation-runbook-operator) | Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Contributeur Avere](#avere-contributor) | Peut créer et gérer un cluster Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Opérateur Avere](#avere-operator) | Utilisé par le cluster Avere vFXT pour gérer le cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Intégration de machine connectée à Azure](#azure-connected-machine-onboarding) | Peut intégrer des machines connectées à Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrateur des ressources de la machine connectée à Azure](#azure-connected-machine-resource-administrator) | Peut lire, écrire, supprimer et réintégrer des machines connectées à Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Propriétaire de données Azure Event Hubs](#azure-event-hubs-data-owner) | Permet un accès complet aux ressources Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Récepteur de données Azure Event Hubs](#azure-event-hubs-data-receiver) | Permet d’obtenir un accès en réception aux ressources Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Expéditeur de données Azure Event Hubs](#azure-event-hubs-data-sender) | Permet d’obtenir un accès en envoi aux ressources Azure Event Hubs. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Rôle d’administrateur de cluster Azure Kubernetes Service](#azure-kubernetes-service-cluster-admin-role) | Répertorie les actions relatives aux informations d’identification de l’administrateur du cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Rôle d’utilisateur de cluster Azure Kubernetes Service](#azure-kubernetes-service-cluster-user-role) | Répertorie les actions relatives aux informations d’identification de l’utilisateur du cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Lecteur de données Azure Maps (préversion)](#azure-maps-data-reader-preview) | Octroie un accès pour lire les données liées au mappage à partir d’un compte Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Contributeur Azure Sentinel](#azure-sentinel-contributor) | Contributeur Azure Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Lecteur Azure Sentinel](#azure-sentinel-reader) | Lecteur Azure Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Répondeur Azure Sentinel](#azure-sentinel-responder) | Répondeur Azure Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Propriétaire de données Azure Service Bus](#azure-service-bus-data-owner) | Permet un accès total aux ressources Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Récepteur de données Azure Service Bus](#azure-service-bus-data-receiver) | Permet d’obtenir un accès en réception aux ressources Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Expéditeur de données Azure Service Bus](#azure-service-bus-data-sender) | Permet d’obtenir un accès en envoi aux ressources Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Propriétaire de l’inscription Azure Stack](#azure-stack-registration-owner) | Permet de gérer les inscriptions Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Contributeur de sauvegarde](#backup-contributor) | Permet de gérer le service de sauvegarde, mais pas de créer des coffres, ni d’accorder l’accès à d’autres personnes | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Opérateur de sauvegarde](#backup-operator) | Permet de gérer des services de sauvegarde, à l’exception de la suppression de la sauvegarde, de la création de coffres et de l’octroi d’autorisations d’accès à d’autres personnes | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Lecteur de sauvegarde](#backup-reader) | Peut afficher des services de sauvegarde, mais pas apporter des modifications | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Lecteur de facturation](#billing-reader) | Autorise l’accès en lecture aux données de facturation | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Contributeur BizTalk](#biztalk-contributor) | Permet de gérer des services BizTalk, mais pas d’y accéder. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Accès au nœud du membre blockchain (préversion)](#blockchain-member-node-access-preview) | Permet d’accéder aux nœuds du membre blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | [Contributeur blueprint](#blueprint-contributor) | Peut gérer les définitions blueprint, mais ne peut pas les affecter. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Opérateur blueprint](#blueprint-operator) | Peut affecter des blueprints publiés existants, mais ne peut pas en créer de nouveaux. REMARQUE : Cela fonctionne uniquement si l’affectation est effectuée avec une identité managée affectée par l’utilisateur. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Contributeur de point de terminaison CDN](#cdn-endpoint-contributor) | Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Lecteur de point de terminaison CDN](#cdn-endpoint-reader) | Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Contributeur de profil CDN](#cdn-profile-contributor) | Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Lecteur de profil CDN](#cdn-profile-reader) | Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Contributeur de réseau classique](#classic-network-contributor) | Permet de gérer des réseaux classiques, mais pas d’y accéder. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Contributeur de compte de stockage classique](#classic-storage-account-contributor) | Permet de gérer des comptes de stockage classiques, mais pas d’y accéder. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Rôle de service d’opérateur de clé de compte de stockage classique](#classic-storage-account-key-operator-service-role) | Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Contributeur de machine virtuelle classique](#classic-virtual-machine-contributor) | Permet de gérer des machines virtuelles classiques, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Contributeur Cognitive Services](#cognitive-services-contributor) | Vous permet de créer, lire, mettre à jour, supprimer et gérer les clés de Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Lecteur de données Cognitive Services (préversion)](#cognitive-services-data-reader-preview) | Permet de lire des données Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Utilisateur Cognitive Services](#cognitive-services-user) | Vous permet de lire et de répertorier les clés de Cognitive Services. | a97b65f3-24C7-4388-baec-2e87135dc908 |
> | [Rôle de lecteur de compte Cosmos DB](#cosmos-db-account-reader-role) | Lire les données de comptes Azure Cosmos DB. Consultez [Contributeur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Opérateur Cosmos DB](#cosmos-db-operator) | Permet de gérer des comptes Azure Cosmos DB, mais pas d’accéder aux données qu’ils contiennent. Empêche d’accéder aux clés de compte et aux chaînes de connexion. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Peut envoyer une requête de restauration d’une base de données Cosmos DB ou d’un conteneur pour un compte | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Contributeur Cost Management](#cost-management-contributor) | Peut afficher les coûts et gérer la configuration des coûts (par exemple, budgets, exportations) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Lecteur Cost Management](#cost-management-reader) | Peut afficher les données et la configuration des coûts (par exemple, budgets, exportations) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Contributeur Data Box](#data-box-contributor) | Permet de gérer toutes les opérations sous le service Data Box à l’exception de l’octroi d’accès à d’autres personnes. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Lecteur Data Box](#data-box-reader) | Permet de gérer le service Data Box, mais ne permet pas de créer une commande, de modifier les détails d’une commande ou d’octroyer l’accès à d’autres personnes. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Contributeurs de fabrique de données](#data-factory-contributor) | Créer et gérer des fabriques de données, ainsi que les ressources enfants qu’elles contiennent. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Développeur Data Lake Analytics](#data-lake-analytics-developer) | Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Videur de données](#data-purger) | Peut vider les données d’analytique | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Utilisateur de DevTest Labs](#devtest-labs-user) | Permet de connecter, de démarrer, de redémarrer et d’arrêter vos machines virtuelles dans votre Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Contributeur de Zone DNS](#dns-zone-contributor) | Permet de gérer des zones DNS et des jeux d’enregistrements dans Azure DNS, mais pas de contrôler qui y a accès. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Contributeur de compte DocumentDB](#documentdb-account-contributor) | Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Contributeur EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Vous permet de gérer les opérations d’abonnement aux événements EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Lecteur EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Vous permet de lire les abonnements aux événements EventGrid. | 2414bbcf-6497-4FAF-8c65-045460748405 |
> | [Opérateur de cluster HDInsight](#hdinsight-cluster-operator) | Permet de lire et de modifier des configurations de cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Contributeur HDInsight Domain Services](#hdinsight-domain-services-contributor) | Peut lire, créer, modifier et supprimer les opérations Domain Services nécessaires pour le pack Sécurité Entreprise HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Contributeur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Permet de gérer des comptes Intelligent Systems, mais pas d’y accéder. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Contributeur Key Vault](#key-vault-contributor) | Permet de gérer des coffres de clés, mais pas d’y accéder. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Créateur Lab](#lab-creator) | Permet de créer, de gérer et de supprimer des labs gérés dans vos comptes Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | [Contributeur Log Analytics](#log-analytics-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de supervision inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux d’activité du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lecteur Log Analytics](#log-analytics-reader) | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Contributeur d’application logique](#logic-app-contributor) | Permet de gérer des applications logiques, mais pas d’en modifier l’accès. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Opérateur d’application logique](#logic-app-operator) | Permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ou de les mettre à jour. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | [Rôle Contributeur d'application managée](#managed-application-contributor-role) | Permet de créer des ressources d’application managées. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rôle opérateur d’application managée](#managed-application-operator-role) | Permet de lire les ressources d’application managée et d’effectuer des actions sur ces ressources. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lecteur Applications managées](#managed-applications-reader) | Vous permet de lire les ressources dans une application managée et de demander un accès JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Contributeur d’identités gérées](#managed-identity-contributor) | Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur. | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Opérateur d’identités gérées](#managed-identity-operator) | Peut lire et assigner une identité attribuée à l’utilisateur. | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Suppression du rôle d’attribution d’inscription de services managé](#managed-services-registration-assignment-delete-role) | La suppression du rôle d’attribution d’inscription de services managés permet aux utilisateurs du client gérant de supprimer l’attribution d’inscription assignée à leur locataire. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Contributeur du groupe d’administration](#management-group-contributor) | Rôle de collaborateur du groupe d’administration | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lecteur du groupe d’administration](#management-group-reader) | Rôle de lecteur du groupe d’administration | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Contributeur de surveillance](#monitoring-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Publication des métriques de surveillance](#monitoring-metrics-publisher) | Permet de publier les métriques relatives aux ressources Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Lecteur de surveillance](#monitoring-reader) | Peut lire toutes les données de supervision (métriques, journaux d’activité, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Contributeur de réseau](#network-contributor) | Permet de gérer des réseaux, mais pas d’y accéder. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Contributeur de compte NewRelic APM](#new-relic-apm-account-contributor) | Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights Data Writer (préversion)](#policy-insights-data-writer-preview) | Permet de lire les stratégies de ressources et d’écrire les événements de stratégie de composant de ressource. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Lecteur et accès aux données](#reader-and-data-access) | Permet d’afficher tous les éléments, mais pas de supprimer ou de créer un compte de stockage ou une ressource contenue. En outre, autorise l’accès en lecture/écriture à toutes les données contenues dans un compte de stockage via l’accès aux clés de compte de stockage. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Contributeur Cache Redis](#redis-cache-contributor) | Permet de gérer des caches Redis, mais pas d’y accéder. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Contributeur de stratégie de ressource](#resource-policy-contributor) | Utilisateurs dotés de droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire des ressources ou la hiérarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Contributeur des collections de travaux du planificateur](#scheduler-job-collections-contributor) | Permet de gérer des collections de tâches du planificateur, mais pas d’y accéder. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Contributeur du service de recherche](#search-service-contributor) | Permet de gérer des services de recherche, mais pas d’y accéder. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Administrateur de la sécurité](#security-admin) | peut afficher les stratégies de sécurité, les états de sécurité, les alertes et les recommandations, modifier les stratégies de sécurité et ignorer les alertes et les recommandations. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Contributeur d'évaluation de la sécurité](#security-assessment-contributor) | Vous permet d’envoyer (push) les évaluations à Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestionnaire de sécurité (hérité)](#security-manager-legacy) | Il s’agit d’un rôle hérité. Utilisez plutôt l’administrateur de sécurité. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Lecteur de sécurité](#security-reader) | Peut afficher les recommandations et les alertes, afficher les stratégies de sécurité, afficher les états de la sécurité, mais ne peut pas apporter de modifications. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Contributeur Site Recovery](#site-recovery-contributor) | Permet de gérer le service Site Recovery sauf la création de coffre et l’attribution de rôle | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Opérateur Site Recovery](#site-recovery-operator) | Permet de basculer et de restaurer mais pas d’effectuer d’autres opérations de gestion de Site Recovery | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Lecteur Site Recovery](#site-recovery-reader) | Permet d’afficher l’état de Site Recovery mais pas d’effectuer d’autres opérations de gestion | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Contributeur de compte Spatial Anchors](#spatial-anchors-account-contributor) | Permet de gérer des ancres spatiales dans votre compte, mais pas de les supprimer | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Propriétaire de compte Spatial Anchors](#spatial-anchors-account-owner) | Permet de gérer des ancres spatiales dans votre compte, y compris de les supprimer | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Lecteur de compte Spatial Anchors](#spatial-anchors-account-reader) | Permet de localiser et de lire les propriétés d’ancres spatiales dans votre compte | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [Contributeur de base de données SQL](#sql-db-contributor) | Permet de gérer des bases de données SQL, mais pas d’y accéder. Vous ne pouvez pas non plus gérer leurs stratégies de sécurité ni leurs serveurs SQL parents. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Contributeur de SQL Managed Instance](#sql-managed-instance-contributor) | Permet de gérer des instances SQL Managed Instance et la configuration réseau requise, mais pas d’accorder l’accès à d’autres personnes. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestionnaire de sécurité SQL](#sql-security-manager) | Permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d’y accéder. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Contributeur SQL Server](#sql-server-contributor) | Permet de gérer des serveurs et bases de données SQL, mais pas d’y accéder, ni de gérer leurs stratégies de sécurité. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Contributeur de compte de stockage](#storage-account-contributor) | Permet la gestion des comptes de stockage. Fournit l’accès à la clé de compte, qui peut être utilisée pour accéder aux données par le biais de l’autorisation de clé partagée. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Rôle de service d’opérateur de clé de compte de stockage](#storage-account-key-operator-service-role) | Permet de répertorier et de régénérer les clés d’accès au compte de stockage. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Contributeur aux données Blob du stockage](#storage-blob-data-contributor) | Lire, écrire et supprimer des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Propriétaire des données Blob du stockage](#storage-blob-data-owner) | Fournit un accès total aux conteneurs d’objets blob et aux données du Stockage Azure, notamment l’attribution du contrôle d’accès POSIX. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lecteur des données blob du stockage](#storage-blob-data-reader) | Lire et répertorier des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Délégation du Stockage Blob](#storage-blob-delegator) | Obtenez une clé de délégation d’utilisateur qui peut être utilisée pour créer une signature d’accès partagé pour un conteneur ou un objet blob signé avec les informations d’identification Azure AD. Pour en savoir plus, consultez [Créer une SAP de délégation d’utilisateur](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Contributeur de partage SMB de données de fichier de stockage](#storage-file-data-smb-share-contributor) | Permet l'accès en lecture, en écriture et en suppression aux fichiers/répertoires des partages de fichiers Azure. Ce rôle n'a pas d'équivalent intégré sur les serveurs de fichiers Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Contributeur élevé de partage SMB de données de fichier de stockage](#storage-file-data-smb-share-elevated-contributor) | Permet la lecture, l'écriture, la suppression et la modification des listes de contrôle d'accès sur les fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en modification sur les serveurs de fichiers Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Lecteur de partage SMB de données de fichier de stockage](#storage-file-data-smb-share-reader) | Permet l'accès en lecture aux fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en lecture sur les serveurs de fichiers Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Contributeur aux données en file d’attente du stockage](#storage-queue-data-contributor) | Lire, écrire et supprimer des files d'attente et messages en file d'attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processeur de messages de données en file d’attente du stockage](#storage-queue-data-message-processor) | Récupérer et supprimer un message, ou en afficher un aperçu à partir d’une file d’attente Stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Expéditeur de messages de données en file d’attente du stockage](#storage-queue-data-message-sender) | Ajoutez des messages à une file d’attente de stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Lecteur des données en file d’attente du stockage](#storage-queue-data-reader) | Lire et répertorier des files d’attente et messages en file d’attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Contributeur de demande de support](#support-request-contributor) | Permet de créer et de gérer des demandes de support | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Contributeur Traffic Manager](#traffic-manager-contributor) | Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | [Administrateur de l'accès utilisateur](#user-access-administrator) | Vous permet de gérer l'accès utilisateur aux ressources Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | [Connexion de l’administrateur aux machines virtuelles](#virtual-machine-administrator-login) | Afficher les machines virtuelles dans le portail et se connecter en tant qu’administrateur | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Contributeur de machine virtuelle](#virtual-machine-contributor) | Permet de gérer des machines virtuelles, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Connexion de l’utilisateur aux machines virtuelles](#virtual-machine-user-login) | Affichez les machines virtuelles dans le portail et connectez-vous en tant qu’utilisateur normal. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | [Contributeur de plan web](#web-plan-contributor) | Permet de gérer des plans web pour des sites web, mais pas d’y accéder. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Contributeur de site web](#website-contributor) | Permet de gérer des sites web (pas des plans web), mais pas d’y accéder. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | [Contributeur de classeur](#workbook-contributor) | Peut enregistrer les classeurs partagés. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Lecteur de classeur](#workbook-reader) | Peut lire les classeurs. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |


## <a name="owner"></a>Propriétaire
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de tout gérer, notamment l’accès aux ressources. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Actions** |  |
> | * | Créer et gérer les ressources de tous les types |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="contributor"></a>Contributeur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de tout gérer, à l’exception de l’octroi de l’accès aux ressources. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Actions** |  |
> | * | Créer et gérer les ressources de tous les types |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Supprimer des rôles, des affectations de stratégie, des définitions de stratégie et des définitions d’ensemble de stratégies |
> | Microsoft.Authorization/*/Write | Créer des rôles, des attributions de rôle, des affectations de stratégie, des définitions de stratégie et des définitions d’ensemble de stratégies |
> | Microsoft.Authorization/elevateAccess/Action | Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client |
> | Microsoft.Blueprint/blueprintAssignments/write | Créer ou mettre à jour toutes les affectations de blueprint |
> | Microsoft.Blueprint/blueprintAssignments/delete | Supprimer toutes les affectations de blueprint |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="reader"></a>Lecteur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de tout afficher, mais sans apporter de modifications. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | acr delete |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Supprimer l’artefact dans un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | signataire d’image ACR |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Envoie ou tire des métadonnées d’approbation du contenu pour un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | tirer (pull) acr |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Tire (pull) ou obtient des images à partir d’un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | envoyer (push) acr |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Tire (pull) ou obtient des images à partir d’un registre de conteneurs. |
> | Microsoft.ContainerRegistry/registries/push/write | Envoie (push) ou écrit des images dans un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | lecteur de données de quarantaine ACR |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantine/read | Tire (pull) ou obtient des images en quarantaine à partir du registre de conteneurs |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | écriture de données de quarantaine ACR |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Actions** |  |
> | Microsoft.ContainerRegistry/registries/quarantine/read | Tire (pull) ou obtient des images en quarantaine à partir du registre de conteneurs |
> | Microsoft.ContainerRegistry/registries/quarantine/write | Écrit ou modifie l’état des images en quarantaine |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="api-management-service-contributor"></a>Contributeur du service de gestion des API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer le service et les API |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/* | Créer et gérer le service Gestion des API |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="api-management-service-operator-role"></a>Rôle d’opérateur du service Gestion des API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer le service, mais pas les API |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Lire les instances du service Gestion des API |
> | Microsoft.ApiManagement/service/backup/action | Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | Microsoft.ApiManagement/service/delete | Supprimer l’instance du service Gestion des API |
> | Microsoft.ApiManagement/service/managedeployments/action | Modifier une référence/unité, ajouter/supprimer des déploiements régionaux du service Gestion des API |
> | Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | Microsoft.ApiManagement/service/restore/action | Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | Microsoft.ApiManagement/service/updatecertificate/action | Télécharger le certificat SSL pour un service Gestion des API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API |
> | Microsoft.ApiManagement/service/write | Créer ou mettre à jour une instance du service Gestion des API |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtenir les clés associées à un utilisateur |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="api-management-service-reader-role"></a>Rôle de lecteur du service Gestion des API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Accès en lecture seule au service et aux API |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Actions** |  |
> | Microsoft.ApiManagement/service/*/read | Lire les instances du service Gestion des API |
> | Microsoft.ApiManagement/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obtenir les clés associées à un utilisateur |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="app-configuration-data-owner"></a>Propriétaire des données App Configuration
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l’accès total aux données App Configuration. |
> | **Id** | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | Microsoft.AppConfiguration/configurationStores/*/write |  |
> | Microsoft.AppConfiguration/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="app-configuration-data-reader"></a>Lecteur des données App Configuration
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire les données App Configuration. |
> | **Id** | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="application-insights-component-contributor"></a>Contributeur de composants Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Gérer les composants Application Insights |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte classiques |
> | Microsoft.Insights/metricAlerts/* | Créer et gérer de nouvelles règles d’alerte |
> | Microsoft.Insights/components/* | Créer et gérer les composants Insights |
> | Microsoft.Insights/webtests/* | Créer et gérer les tests web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="application-insights-snapshot-debugger"></a>Débogueur de capture instantanée d’Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Autorise l’utilisateur à consulter et à télécharger les instantanés de débogage collectés à l’aide du débogueur de capture instantanée Application Insights. Ces autorisations ne sont pas incluses dans les rôles [Propriétaire](#owner) et [Contributeur](#contributor). |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="automation-job-operator"></a>Opérateur de travaux Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer et de gérer des travaux avec des runbooks Automation. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtient le résultat d’un travail |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="automation-operator"></a>Opérateur Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Les opérateurs d’Automation sont en mesure de démarrer, d’arrêter, de suspendre et de reprendre des travaux |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | Microsoft.Automation/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obtient une planification du travail Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Crée une planification du travail Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtient l’espace de travail lié au compte Automation. |
> | Microsoft.Automation/automationAccounts/read | Obtient un compte Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/read | Obtient une ressource de planification Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/write | Crée ou met à jour une ressource de planification Azure Automation |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obtient le résultat d’un travail |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="automation-runbook-operator"></a>Opérateur de runbook Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="avere-contributor"></a>Contributeur Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut créer et gérer un cluster Avere vFXT. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/virtualNetworks/subnets/read | Obtient une définition de sous-réseau de réseau virtuel. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | Microsoft.Network/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Obtient les ressources du groupe de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retourner le résultat de la suppression d’un objet blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retourner le résultat de l’écriture d’un objet blob |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="avere-operator"></a>Opérateur Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Utilisé par le cluster Avere vFXT pour gérer le cluster |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Actions** |  |
> | Microsoft.Compute/virtualMachines/read | Obtenir les propriétés d’une machine virtuelle |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Network/networkInterfaces/write | Crée une interface réseau ou met à jour une interface réseau existante.  |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/virtualNetworks/subnets/read | Obtient une définition de sous-réseau de réseau virtuel. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | Microsoft.Network/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retourne le résultat de la suppression d’un conteneur |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourne la liste des conteneurs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourne le résultat du conteneur put blob |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retourner le résultat de la suppression d’un objet blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retourner le résultat de l’écriture d’un objet blob |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-connected-machine-onboarding"></a>Intégration de machine connectée à Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut intégrer des machines connectées à Azure. |
> | **Id** | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | **Actions** |  |
> | Microsoft.HybridCompute/machines/read | Lit les données des machines Azure Arc |
> | Microsoft.HybridCompute/machines/write | Écrit toutes les machines Azure Arc |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Obtient l’attribution de la configuration des invités. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-connected-machine-resource-administrator"></a>Administrateur des ressources de la machine connectée à Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire, écrire, supprimer et réintégrer des machines connectées à Azure. |
> | **Id** | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | **Actions** |  |
> | Microsoft.HybridCompute/machines/read | Lit les données des machines Azure Arc |
> | Microsoft.HybridCompute/machines/write | Écrit toutes les machines Azure Arc |
> | Microsoft.HybridCompute/machines/delete | Supprime toutes les machines Azure Arc |
> | Microsoft.HybridCompute/machines/reconnect/action | Reconnecte toutes les machines Azure Arc |
> | Microsoft.HybridCompute/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-event-hubs-data-owner"></a>Propriétaire de données Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet un accès complet aux ressources Azure Event Hubs. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Actions** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-event-hubs-data-receiver"></a>Récepteur de données Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’obtenir un accès en réception aux ressources Azure Event Hubs. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Actions** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-event-hubs-data-sender"></a>Expéditeur de données Azure Event Hubs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’obtenir un accès en envoi aux ressources Azure Event Hubs. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Actions** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Rôle d’administrateur de cluster Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Répertorie les actions relatives aux informations d’identification de l’administrateur du cluster. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Obtient un profil d’accès au cluster géré en fonction du nom de rôle à l’aide des informations d’identification de la liste |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Rôle d’utilisateur de cluster Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Répertorie les actions relatives aux informations d’identification de l’utilisateur du cluster. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Actions** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-maps-data-reader-preview"></a>Lecteur de données Azure Maps (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Octroie un accès pour lire les données liées au mappage à partir d’un compte Azure Maps. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Maps/accounts/data/read | Accorde à un compte Maps l’accès en lecture aux données. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-sentinel-contributor"></a>Contributeur Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Contributeur Azure Sentinel |
> | **Id** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Actions** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Microsoft.OperationalInsights/workspaces/read | Obtient un espace de travail existant. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Obtenir la solution OMS existante. |
> | Microsoft.OperationalInsights/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | Microsoft.Insights/workbooks/* |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-sentinel-reader"></a>Lecteur Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lecteur Azure Sentinel |
> | **Id** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Actions** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Microsoft.OperationalInsights/workspaces/read | Obtient un espace de travail existant. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtient une requête de recherche enregistrée. |
> | Microsoft.OperationsManagement/solutions/read | Obtenir la solution OMS existante. |
> | Microsoft.OperationalInsights/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | Microsoft.Insights/workbooks/read | Lire un classeur |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-sentinel-responder"></a>Répondeur Azure Sentinel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Répondeur Azure Sentinel |
> | **Id** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Actions** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Microsoft.OperationalInsights/workspaces/read | Obtient un espace de travail existant. |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Obtient une requête de recherche enregistrée. |
> | Microsoft.OperationsManagement/solutions/read | Obtenir la solution OMS existante. |
> | Microsoft.OperationalInsights/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | Microsoft.OperationalInsights/workspaces/query/*/read |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | Microsoft.Insights/workbooks/read | Lire un classeur |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-service-bus-data-owner"></a>Propriétaire de données Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet un accès total aux ressources Azure Service Bus. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Actions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-service-bus-data-receiver"></a>Récepteur de données Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’obtenir un accès en réception aux ressources Azure Service Bus. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Actions** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-service-bus-data-sender"></a>Expéditeur de données Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’obtenir un accès en envoi aux ressources Azure Service Bus. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Actions** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="azure-stack-registration-owner"></a>Propriétaire de l’inscription Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer les inscriptions Azure Stack. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Actions** |  |
> | Microsoft.AzureStack/registrations/products/*/action |  |
> | Microsoft.AzureStack/registrations/products/read | Obtient les propriétés d’un produit de la place de marché Azure Stack. |
> | Microsoft.AzureStack/registrations/read | Obtient les propriétés d’une inscription Azure Stack. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="backup-contributor"></a>Contributeur de sauvegarde
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer le service de sauvegarde, mais pas de créer des coffres, ni d’accorder l’accès à d’autres personnes |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gérer les résultats des opérations de gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Créer et gérer des conteneurs de sauvegarde dans les structures de sauvegarde du coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Actualise la liste de conteneurs. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Créer et gérer des stratégies de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Créer et gérer les éléments sauvegardés |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Créer et gérer les conteneurs contenant les éléments de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Créer et gérer des certificats associés à la sauvegarde dans le coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
> | Microsoft.RecoveryServices/Vaults/usages/* | Créer et gérer l’utilisation du coffre Recovery Services |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Valider l’opération sur l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obtient tous les conteneurs protégeables |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Valide des fonctionnalités |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | Microsoft.RecoveryServices/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="backup-operator"></a>Opérateur de sauvegarde
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des services de sauvegarde, à l’exception de la suppression de la sauvegarde, de la création de coffres et de l’octroi d’autorisations d’accès à d’autres personnes |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Effectue la sauvegarde d’un élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Approvisionner la récupération d’éléments instantanée pour l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurer les points de récupération des éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Révoquer la récupération d’éléments instantanée pour l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Actualise la liste de conteneurs. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | L’opération d’inscription d’un conteneur de service peut être utilisée pour inscrire un conteneur avec Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Valider l’opération sur l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obtenir l’état de l’opération de stratégie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Crée un conteneur inscrit |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Recherche les charges de travail dans un conteneur |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Créer une intention de protection de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obtient tous les conteneurs protégeables |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obtient tous les éléments figurant dans un conteneur |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Valide des fonctionnalités |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | Microsoft.RecoveryServices/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="backup-reader"></a>Lecteur de sauvegarde
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher des services de sauvegarde, mais pas apporter des modifications |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Renvoie le résultat de l’opération de travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Renvoie tous les objets de travail. |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Travaux d’exportation |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Renvoie le résultat de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Renvoie la configuration de stockage pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Renvoie la configuration pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obtenir l’état de l’opération de stratégie. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Créer une intention de protection de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obtient tous les éléments figurant dans un conteneur |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | Microsoft.RecoveryServices/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="billing-reader"></a>Lecteur de facturation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Autorise l’accès en lecture aux données de facturation |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Billing/*/read | Lire les informations de facturation |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="biztalk-contributor"></a>Contributeur BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des services BizTalk, mais pas d’y accéder. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.BizTalkServices/BizTalk/* | Créer et gérer BizTalk Services |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="blockchain-member-node-access-preview"></a>Accès au nœud du membre blockchain (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’accéder aux nœuds du membre blockchain |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Actions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Crée ou répertorie un ou plusieurs nœuds de transaction existants du membre blockchain. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Connecte à un nœud de transaction d’un membre blockchain. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="blueprint-contributor"></a>Contributeur blueprint
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer les définitions blueprint, mais ne peut pas les affecter. |
> | **Id** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Blueprint/blueprints/* | Créer et gérer des définitions de blueprint ou des artefacts de blueprint. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="blueprint-operator"></a>Opérateur blueprint
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut affecter des blueprints publiés existants, mais ne peut pas en créer de nouveaux. REMARQUE : Cela fonctionne uniquement si l’affectation est effectuée avec une identité managée affectée par l’utilisateur. |
> | **Id** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Blueprint/blueprintAssignments/* | Créer et gérer des affectations de blueprint |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cdn-endpoint-contributor"></a>Contributeur de point de terminaison CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cdn-endpoint-reader"></a>Lecteur de point de terminaison CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cdn-profile-contributor"></a>Contributeur de profil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cdn-profile-reader"></a>Lecteur de profil CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="classic-network-contributor"></a>Contributeur de réseau classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des réseaux classiques, mais pas d’y accéder. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.ClassicNetwork/* | Créer et gérer des réseaux classiques |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="classic-storage-account-contributor"></a>Contributeur de compte de stockage classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des comptes de stockage classiques, mais pas d’y accéder. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.ClassicStorage/storageAccounts/* | Créer et gérer les comptes de stockage |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Actions** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Régénère les clés d’accès existantes du compte de stockage. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="classic-virtual-machine-contributor"></a>Contributeur de machine virtuelle classique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des machines virtuelles classiques, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.ClassicCompute/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
> | Microsoft.ClassicCompute/virtualMachines/* | Créer et gérer les machines virtuelles |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Lier une adresse IP réservée |
> | Microsoft.ClassicNetwork/reservedIps/read | Obtient les adresses IP réservées |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Joint le réseau virtuel. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obtenez le réseau virtuel. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Retourne le disque du compte de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Retourne l’image du compte de stockage. (Déconseillé. Utilisez « Microsoft.ClassicStorage/storageAccounts/vmImages ») |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cognitive-services-contributor"></a>Contributeur Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de créer, lire, mettre à jour, supprimer et gérer les clés de Cognitive Services. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Afficher les fonctionnalités d’un abonnement |
> | Microsoft.Features/providers/features/read | Afficher les fonctionnalités d’un abonnement pour un fournisseur de ressources donné |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | Microsoft.Insights/logDefinitions/read | Lire les définitions de journal |
> | Microsoft.Insights/metricdefinitions/read | Lire les définitions des mesures |
> | Microsoft.Insights/metrics/read | Lire des mesures |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Microsoft.Resources/subscriptions/read | Obtient la liste des abonnements. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cognitive-services-data-reader-preview"></a>Lecteur de données Cognitive Services (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire des données Cognitive Services. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cognitive-services-user"></a>Utilisateur Cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de lire et de répertorier les clés de Cognitive Services. |
> | **Id** | a97b65f3-24C7-4388-baec-2e87135dc908 |
> | **Actions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Afficher la liste des clés |
> | Microsoft.Insights/alertRules/read | Lire une alerte de métrique classique |
> | Microsoft.Insights/diagnosticSettings/read | Lire un paramètre de diagnostic de ressource |
> | Microsoft.Insights/logDefinitions/read | Lire les définitions de journal |
> | Microsoft.Insights/metricdefinitions/read | Lire les définitions des mesures |
> | Microsoft.Insights/metrics/read | Lire des mesures |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Microsoft.Resources/subscriptions/read | Obtient la liste des abonnements. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cosmos-db-account-reader-role"></a>Rôle de lecteur de compte Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lire les données de comptes Azure Cosmos DB. Consultez [Contributeur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les attributions de rôle, lire les autorisations accordées à chaque utilisateur |
> | Microsoft.DocumentDB/*/read | Lire n’importe quelle collection |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Lire les clés en lecture seule du compte de base de données. |
> | Microsoft.Insights/MetricDefinitions/read | Lire les définitions des mesures |
> | Microsoft.Insights/Metrics/read | Lire des mesures |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cosmos-db-operator"></a>Opérateur Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des comptes Azure Cosmos DB, mais pas d’accéder aux données qu’ils contiennent. Empêche d’accéder aux clés de compte et aux chaînes de connexion. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Actions** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut envoyer une requête de restauration d’une base de données Cosmos DB ou d’un conteneur pour un compte |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Actions** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Soumettre une demande pour configurer la sauvegarde |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Soumettre une demande de restauration |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cost-management-contributor"></a>Contributeur Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher les coûts et gérer la configuration des coûts (par exemple, budgets, exportations) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Actions** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Obtient la liste des abonnements. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Advisor/configurations/read | Obtenir des configurations |
> | Microsoft.Advisor/recommendations/read | Lit les recommandations |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="cost-management-reader"></a>Lecteur Cost Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher les données et la configuration des coûts (par exemple, budgets, exportations) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Actions** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read |  |
> | Microsoft.Resources/subscriptions/read | Obtient la liste des abonnements. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Advisor/configurations/read | Obtenir des configurations |
> | Microsoft.Advisor/recommendations/read | Lit les recommandations |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="data-box-contributor"></a>Contributeur Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer toutes les opérations sous le service Data Box à l’exception de l’octroi d’accès à d’autres personnes. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="data-box-reader"></a>Lecteur Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer le service Data Box, mais ne permet pas de créer une commande, de modifier les détails d’une commande ou d’octroyer l’accès à d’autres personnes. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Répertorie les informations d’identification non chiffrées liées à la commande |
> | Microsoft.Databox/locations/availableSkus/action | Retourner la liste des références (SKU) disponibles |
> | Microsoft.Databox/locations/validateInputs/action | Cette méthode effectue tous les types de validations. |
> | Microsoft.Databox/locations/regionConfiguration/action | Cette méthode retourne les configurations pour la région. |
> | Microsoft.Databox/locations/validateAddress/action | Valider l'adresse de livraison et fournir d'autres adresses s’il en est |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="data-factory-contributor"></a>Contributeurs de fabrique de données
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Créer et gérer des fabriques de données, ainsi que les ressources enfants qu’elles contiennent. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.DataFactory/dataFactories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
> | Microsoft.DataFactory/factories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.EventGrid/eventSubscriptions/write | Créer ou mettre à jour un abonnement à un événement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="data-lake-analytics-developer"></a>Développeur Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Supprime un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Accorde des autorisations pour annuler des travaux soumis par d’autres utilisateurs. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Crée ou met à jour un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Crée ou met à jour un compte Data Lake Store lié d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Dissocie un compte Data Lake Store d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Crée ou met à jour un compte de stockage lié d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Dissocie un compte de stockage d’un compte Data Lake Analytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Créer ou mettre à jour une règle de pare-feu. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Supprimer une règle de pare-feu. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Crée ou met à jour une stratégie de calcul. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Supprime une stratégie de calcul. |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="data-purger"></a>Videur de données
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut vider les données d’analytique |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Actions** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Vider des données d’Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Supprime les données spécifiées de l’espace de travail |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="devtest-labs-user"></a>Utilisateur de DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de connecter, de démarrer, de redémarrer et d’arrêter vos machines virtuelles dans votre Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Compute/availabilitySets/read | Obtenir les propriétés d’un groupe à haute disponibilité |
> | Microsoft.Compute/virtualMachines/*/read | Lire les propriétés d’une machine virtuelle (tailles de machine virtuelle, état de l’exécution, extensions de machine virtuelle, etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Mettre hors tension la machine virtuelle et libérer les ressources de calcul |
> | Microsoft.Compute/virtualMachines/read | Obtenir les propriétés d’une machine virtuelle |
> | Microsoft.Compute/virtualMachines/restart/action | Redémarrer la machine virtuelle |
> | Microsoft.Compute/virtualMachines/start/action | Démarrer la machine virtuelle |
> | Microsoft.DevTestLab/*/read | Lire les propriétés d’un laboratoire |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Créer des machines virtuelles dans un laboratoire. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Vérifiez que l’utilisateur actuel dispose d’un profil valide dans le labo. |
> | Microsoft.DevTestLab/labs/formulas/delete | Supprimer des formules. |
> | Microsoft.DevTestLab/labs/formulas/read | Lire des formules. |
> | Microsoft.DevTestLab/labs/formulas/write | Ajouter ou modifier des formules. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Évaluer la stratégie de laboratoire. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Prendre possession d’une machine virtuelle existante. |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Répertorie les planifications de démarrage/arrêt applicables, le cas échéant. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Obtenir une chaîne qui représente le contenu du fichier RDP pour la machine virtuelle |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. Impossible à alerter. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joint une règle nat de trafic entrant d’équilibrage de charge. Impossible à alerter. |
> | Microsoft.Network/networkInterfaces/*/read | Lire les propriétés d’une interface réseau (par exemple, tous les équilibreurs de charge dont l’interface réseau fait partie) |
> | Microsoft.Network/networkInterfaces/join/action | Joint une machine virtuelle à une interface réseau. Impossible à alerter. |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Network/networkInterfaces/write | Crée une interface réseau ou met à jour une interface réseau existante.  |
> | Microsoft.Network/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
> | Microsoft.Network/publicIPAddresses/join/action | Joint une adresse IP publique. Impossible à alerter. |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Resources/deployments/read | Obtient ou répertorie les déploiements. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="dns-zone-contributor"></a>Contributeur de Zone DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des zones DNS et des jeux d’enregistrements dans Azure DNS, mais pas de contrôler qui y a accès. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/dnsZones/* | Créer et gérer des enregistrements et zones DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="documentdb-account-contributor"></a>Contributeur de compte DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.DocumentDb/databaseAccounts/* | Créer et gérer des comptes Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Contributeur EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de gérer les opérations d’abonnement aux événements EventGrid. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lister les abonnements à des événements globaux par type de rubrique |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Lister les abonnements à des événements régionaux |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lister des abonnements à des événements régionaux par type de rubrique |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Lecteur EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de lire les abonnements aux événements EventGrid. |
> | **Id** | 2414bbcf-6497-4FAF-8c65-045460748405 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.EventGrid/eventSubscriptions/read | Lit un abonnement à un événement |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Lister les abonnements à des événements globaux par type de rubrique |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Lister les abonnements à des événements régionaux |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Lister des abonnements à des événements régionaux par type de rubrique |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="hdinsight-cluster-operator"></a>Opérateur de cluster HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire et de modifier des configurations de cluster HDInsight. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Actions** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | Obtenir les paramètres de passerelle pour un HDInsight Cluster |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Mettre à jour les paramètres de passerelle pour un HDInsight Cluster |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="hdinsight-domain-services-contributor"></a>Contributeur HDInsight Domain Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire, créer, modifier et supprimer les opérations Domain Services nécessaires pour le pack Sécurité Entreprise HDInsight |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Actions** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="intelligent-systems-account-contributor"></a>Contributeur de compte Intelligent Systems
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des comptes Intelligent Systems, mais pas d’y accéder. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes Intelligent Systems |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="key-vault-contributor"></a>Contributeur Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des coffres de clés, mais pas d’y accéder. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Vider un coffre Key Vault supprimé de manière réversible |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="lab-creator"></a>Créateur Lab
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer, de gérer et de supprimer des labs gérés dans vos comptes Azure Lab. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Crée un lab dans un compte lab. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Obtenir des informations de disponibilité régionale pour chaque catégorie de taille configurée sous un compte Lab |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | Permet d'obtenir le prix et la disponibilité de combinaisons de tailles, de zones géographiques et de systèmes d'exploitation pour le compte Lab. |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action | Permet d'obtenir les informations principales sur les restrictions et l'utilisation de cet abonnement |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="log-analytics-contributor"></a>Contributeur Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de supervision inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux d’activité du stockage Azure, la création et la configuration de comptes Automation, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="log-analytics-reader"></a>Lecteur Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="logic-app-contributor"></a>Contributeur d’application logique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des applications logiques, mais pas d’en modifier l’accès. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | Microsoft.Insights/logdefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
> | Microsoft.Insights/metricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
> | Microsoft.Logic/* | Gère les ressources Logic Apps. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/connectionGateways/* | Crée et gère une passerelle de connexion. |
> | Microsoft.Web/connections/* | Crée et gère une connexion. |
> | Microsoft.Web/customApis/* | Crée et gère une API personnalisée. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | Microsoft.Web/sites/functions/listSecrets/action | Liste les secrets de fonction. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="logic-app-operator"></a>Opérateur d’application logique
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ou de les mettre à jour. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/*/read | Lit les règles d’alerte d’Insights. |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Obtient les paramètres de diagnostic de Logic Apps. |
> | Microsoft.Insights/metricDefinitions/*/read | Obtient les mesures disponibles pour Logic Apps. |
> | Microsoft.Logic/*/read | Lit les ressources Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Désactive le workflow. |
> | Microsoft.Logic/workflows/enable/action | Active le workflow. |
> | Microsoft.Logic/workflows/validate/action | Valide le workflow. |
> | Microsoft.Resources/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/connectionGateways/*/read | Lit les passerelles de connexion. |
> | Microsoft.Web/connections/*/read | Lit les connexions. |
> | Microsoft.Web/customApis/*/read | Lit l’API personnalisée. |
> | Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="managed-application-contributor-role"></a>Rôle Contributeur d'application managée
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer des ressources d’application managées. |
> | **Id** | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Solutions/applications/* |  |
> | Microsoft.Solutions/register/action | Inscrit à Solutions. |
> | Microsoft.Resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="managed-application-operator-role"></a>Rôle opérateur d’application managée
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire les ressources d’application managée et d’effectuer des actions sur ces ressources. |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Solutions/applications/read | Récupère une liste d’applications. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="managed-applications-reader"></a>Lecteur Applications managées
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de lire les ressources dans une application managée et de demander un accès JIT. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="managed-identity-contributor"></a>Contributeur d’identités gérées
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur. |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Obtient l’identité assignée d’un utilisateur existant |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Crée une identité assignée d’utilisateur ou met à jour les balises associées à l’identité assignée d’un utilisateur existant |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Supprime l’identité assignée d’un utilisateur existant |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="managed-identity-operator"></a>Opérateur d’identités gérées
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire et assigner une identité attribuée à l’utilisateur. |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Actions** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="managed-services-registration-assignment-delete-role"></a>Suppression du rôle d’attribution d’inscription de services managé
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | La suppression du rôle d’attribution d’inscription de services managés permet aux utilisateurs du client gérant de supprimer l’attribution d’inscription assignée à leur locataire. |
> | **Id** | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | **Actions** |  |
> | Microsoft.ManagedServices/registrationAssignments/read | Récupère la liste des affectations d'inscription des services managés. |
> | Microsoft.ManagedServices/registrationAssignments/delete | Supprime l'affectation d'inscription des services managés. |
> | Microsoft.ManagedServices/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="management-group-contributor"></a>Collaborateur du groupe d’administration
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Rôle de collaborateur du groupe d’administration |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/delete | Supprime un groupe d’administration. |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Dissocie un abonnement du groupe d’administration. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associe un abonnement existant au groupe d’administration. |
> | Microsoft.Management/managementGroups/write | Crée ou met à jour un groupe d’administration. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="management-group-reader"></a>Lecteur du groupe d’administration
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Rôle de lecteur du groupe d’administration |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Actions** |  |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="monitoring-contributor"></a>Contributeur d’analyse
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Règles d’alerte en lecture/écriture/suppression. |
> | Microsoft.Insights/components/* | Lire/écrire/supprimer des composants Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Paramètres de diagnostic en lecture/écriture/suppression. |
> | Microsoft.Insights/eventtypes/* | Événements du journal d’activité, (événements de gestion) dans un abonnement. Cette autorisation est applicable pour l’accès par programme et portail dans le journal d’activité. |
> | Microsoft.Insights/LogDefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
> | Microsoft.Insights/Metrics/* | Lire des mesures pour une ressource. |
> | Microsoft.Insights/Register/Action | Inscrire le fournisseur Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Lire/écrire/supprimer des tests web Application Insights. |
> | Microsoft.Insights/workbooks/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Lire/écrire/supprimer des packs de solution Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Lire/écrire/supprimer des recherches enregistrées Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Lire/écrire/supprimer les configurations des insights de stockage Log Analytics. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="monitoring-metrics-publisher"></a>Publication des métriques de surveillance
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de publier les métriques relatives aux ressources Azure |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Actions** |  |
> | Microsoft.Insights/Register/Action | Inscrire le fournisseur Microsoft Insights |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Écrit des métriques |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="monitoring-reader"></a>Lecteur d’analyse
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire toutes les données de supervision (métriques, journaux d’activité, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.OperationalInsights/workspaces/search/action | Exécute une requête de recherche. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="network-contributor"></a>Contributeur de réseau
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des réseaux, mais pas d’y accéder. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/* | Créer et gérer des réseaux |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="new-relic-apm-account-contributor"></a>Contributeur de compte NewRelic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (préversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de lire les stratégies de ressources et d’écrire les événements de stratégie de composant de ressource. |
> | **Id** | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | **Actions** |  |
> | Microsoft.Authorization/policyassignments/read | Obtenez des informations sur une affectation de stratégie. |
> | Microsoft.Authorization/policydefinitions/read | Obtenez des informations sur une définition de stratégie. |
> | Microsoft.Authorization/policysetdefinitions/read | Obtient des informations sur une définition d’ensemble de stratégies |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Vérifie l’état de conformité d’un composant donné par rapport aux stratégies de données. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Journalise les événements de stratégie de composant de ressource. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="reader-and-data-access"></a>Lecteur et accès aux données
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’afficher tous les éléments, mais pas de supprimer ou de créer un compte de stockage ou une ressource contenue. En outre, autorise l’accès en lecture/écriture à toutes les données contenues dans un compte de stockage via l’accès aux clés de compte de stockage. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Retourne le jeton SAS du compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="redis-cache-contributor"></a>Contributeur Cache Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des caches Redis, mais pas d’y accéder. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Cache/redis/* | Créer et gérer les caches Redis |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="resource-policy-contributor"></a>Contributeur de la stratégie de ressource
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Utilisateurs dotés de droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire des ressources ou la hiérarchie. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Authorization/policyassignments/* | Créer et gérer des attributions de stratégies |
> | Microsoft.Authorization/policydefinitions/* | Créer et gérer des définitions de stratégies |
> | Microsoft.Authorization/policysetdefinitions/* | Créer et gérer des ensembles de stratégies |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="scheduler-job-collections-contributor"></a>Contributeur des collections de travaux du planificateur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des collections de tâches du planificateur, mais pas d’y accéder. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Scheduler/jobcollections/* | Créer et gérer des collections de travaux |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="search-service-contributor"></a>Contributeur du service de recherche
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des services de recherche, mais pas d’y accéder. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Search/searchServices/* | Créer et gérer les services de recherche |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="security-admin"></a>Administrateur de la sécurité
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | peut afficher les stratégies de sécurité, les états de sécurité, les alertes et les recommandations, modifier les stratégies de sécurité et ignorer les alertes et les recommandations. |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Authorization/policyAssignments/* | Créer et gérer des attributions de stratégies |
> | Microsoft.Authorization/policyDefinitions/* | Créer et gérer des définitions de stratégies |
> | Microsoft.Authorization/policySetDefinitions/* | Créer et gérer des ensembles de stratégies |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | Microsoft.operationalInsights/workspaces/*/read | Afficher les données Log Analytics |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="security-assessment-contributor"></a>Contributeur d'évaluation de la sécurité
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet d’envoyer (push) les évaluations à Security Center |
> | **Id** | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | **Actions** |  |
> | Microsoft.Security/assessments/write | Créer ou mettre à jour des évaluations de la sécurité de votre abonnement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="security-manager-legacy"></a>Gestionnaire de sécurité (hérité)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Il s’agit d’un rôle hérité. Utilisez plutôt l’administrateur de sécurité. |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.ClassicCompute/*/read | Lire les informations de configuration relatives aux machines virtuelles classiques |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Écrire la configuration des machines virtuelles classiques |
> | Microsoft.ClassicNetwork/*/read | Lire les informations de configuration relatives au réseau classique |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Security/* | Créer et gérer des stratégies et des composants de sécurité |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="security-reader"></a>Lecteur de sécurité
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut afficher les recommandations et les alertes, afficher les stratégies de sécurité, afficher les états de la sécurité, mais ne peut pas apporter de modifications. |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.operationalInsights/workspaces/*/read | Afficher les données Log Analytics |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Security/*/read | Lire des stratégies et des composants de sécurité |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Management/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="site-recovery-contributor"></a>Contributeur Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer le service Site Recovery sauf la création de coffre et l’attribution de rôle |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Créer ou mettre à jour les paramètres d’alerte de réplication |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Créer et gérer des structures de réplication |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Créer et gérer des stratégies de réplication |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Créer et gérer des plans de récupération |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Créer et gérer la configuration de stockage du coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | Lit tout état de l’opération de réplication du coffre |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="site-recovery-operator"></a>Opérateur Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de basculer et de restaurer mais pas d’effectuer d’autres opérations de gestion de Site Recovery |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Vérifie la cohérence de la structure. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire des structures. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Réassocier une passerelle. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renouveler le certificat pour Fabric |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages réseau. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Appliquer un point de récupération. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Validation du basculement. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Basculement planifié. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Réparer la réplication. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Reprotéger l’élément protégé. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Basculer un conteneur de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Nettoyage de basculement test. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Basculement |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mettre à jour le service Mobilité. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Actualiser un fournisseur. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire des serveurs vCenter. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire des stratégies. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan de récupération de validation de basculement. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan de récupération de basculement planifié. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Reprotéger le plan de récupération. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan de récupération de basculement test. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan de récupération de nettoyage de basculement test. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan de récupération de basculement. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="site-recovery-reader"></a>Lecteur Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet d’afficher l’état de Site Recovery mais pas d’effectuer d’autres opérations de gestion |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Lire des événements. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Lire des structures. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages réseau. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Lire des serveurs vCenter. |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Lire des travaux. |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Lire des stratégies. |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="spatial-anchors-account-contributor"></a>Contributeur de compte Spatial Anchors
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des ancres spatiales dans votre compte, mais pas de les supprimer |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Créer des ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Mettre à jour les propriétés d’ancres spatiales |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="spatial-anchors-account-owner"></a>Propriétaire de compte Spatial Anchors
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des ancres spatiales dans votre compte, y compris de les supprimer |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Créer des ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Supprimer des ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Mettre à jour les propriétés d’ancres spatiales |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="spatial-anchors-account-reader"></a>Lecteur de compte Spatial Anchors
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de localiser et de lire les propriétés d’ancres spatiales dans votre compte |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="sql-db-contributor"></a>Contributeur de base de données SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des bases de données SQL, mais pas d’y accéder. Vous ne pouvez pas non plus gérer leurs stratégies de sécurité ni leurs serveurs SQL parents. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer les règles d’alerte |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Créer et gérer les bases de données SQL |
> | Microsoft.Sql/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Insights/metrics/read | Lire des mesures |
> | Microsoft.Insights/metricDefinitions/read | Lire les définitions des mesures |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Modifier les stratégies d'audit |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Modifier les paramètres d'audit |
> | Microsoft.Sql/servers/databases/auditRecords/read | Récupère les enregistrements d’audit d’objet blob de base de données. |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Modifier les stratégies de connexion |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Modifier les stratégies de masquage des données |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Modifier les stratégies d'alerte de sécurité |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Modifier les mesures de sécurité |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="sql-managed-instance-contributor"></a>Contributeur d’Instance managée SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des instances SQL Managed Instance et la configuration réseau requise, mais pas d’accorder l’accès à d’autres personnes. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Actions** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/metrics/read | Lire des mesures |
> | Microsoft.Insights/metricDefinitions/read | Lire les définitions des mesures |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="sql-security-manager"></a>Gestionnaire de sécurité SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d’y accéder. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture Microsoft |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Créer et gérer les stratégies d’audit de serveur SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Créer et gérer les paramètres d’audit de serveur SQL |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Récupère les détails de la stratégie étendue d’audit des objets blob de serveur configurée sur un serveur spécifié |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Créer et gérer les stratégies d’audit de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Créer et gérer les paramètres d’audit de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Lire les enregistrements d’audit |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Créer et gérer les stratégies de connexion de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Créer et gérer les stratégies de masquage de données de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Récupère les détails de la stratégie étendue d’audit d’objets blob configurée dans une base de données spécifique |
> | Microsoft.Sql/servers/databases/read | Retourner la liste des bases de données ou obtenir les propriétés pour la base de données spécifiée. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Obtenir un schéma de base de données. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obtenir une colonne de base de données. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Obtenir un tableau de base de données. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Créer et gérer les mesures de sécurité de base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de serveur SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="sql-server-contributor"></a>Contributeur SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des serveurs et bases de données SQL, mais pas d’y accéder, ni de gérer leurs stratégies de sécurité. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Créer et gérer les serveurs SQL |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Insights/metrics/read | Lire des mesures |
> | Microsoft.Insights/metricDefinitions/read | Lire les définitions des mesures |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Modifier les stratégies d'audit d'un serveur SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Modifier les paramètres d'audit d'un serveur SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Modifier les stratégies d'audit d'une base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Modifier les paramètres d'audit d'une base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Lire les enregistrements d’audit |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Modifier les stratégies de connexion d'une base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Modifier les stratégies de masquage de données d'une base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Modifier les stratégies d'alerte de sécurité d'une base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Modifier les mesures de sécurité d'une base de données de serveur SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Modifier les stratégies d'alerte de sécurité du serveur SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-account-contributor"></a>Contributeur de compte de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet la gestion des comptes de stockage. Fournit l’accès à la clé de compte, qui peut être utilisée pour accéder aux données par le biais de l’autorisation de clé partagée. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture totale |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/diagnosticSettings/* | Gérer les paramètres de diagnostic |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Storage/storageAccounts/* | Créer et gérer les comptes de stockage |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de répertorier et de régénérer les clés d’accès au compte de stockage. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Régénère les clés d’accès au compte de stockage spécifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-blob-data-contributor"></a>Contributeur aux données Blob du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lire, écrire et supprimer des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Supprimer un conteneur. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourner un conteneur ou une liste de conteneurs. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modifier les métadonnées ou les propriétés d’un conteneur. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Supprimer un objet blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourner un objet blob ou une liste d'objets blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Écrire dans un objet blob. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-blob-data-owner"></a>Propriétaire des données Blob du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Fournit un accès total aux conteneurs d’objets blob et aux données du Stockage Azure, notamment l’attribution du contrôle d’accès POSIX. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Toutes les autorisations sur les conteneurs. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Toutes les autorisations sur les objets blob. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-blob-data-reader"></a>Lecteur des données blob du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lire et répertorier des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourner un conteneur ou une liste de conteneurs. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourner un objet blob ou une liste d'objets blob. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-blob-delegator"></a>Délégation du Stockage Blob
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Obtenez une clé de délégation d’utilisateur qui peut être utilisée pour créer une signature d’accès partagé pour un conteneur ou un objet blob signé avec les informations d’identification Azure AD. Pour en savoir plus, consultez [Créer une SAP de délégation d’utilisateur](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Contributeur de partage SMB de données de fichier de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l'accès en lecture, en écriture et en suppression aux fichiers/répertoires des partages de fichiers Azure. Ce rôle n'a pas d'équivalent intégré sur les serveurs de fichiers Windows. |
> | **Id** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourne un fichier/dossier ou une liste de fichiers/dossiers. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retourne le résultat de l’écriture d’un fichier ou de la création d’un dossier. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourne le résultat de la suppression d’un fichier/dossier. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Contributeur élevé de partage SMB de données de fichier de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet la lecture, l'écriture, la suppression et la modification des listes de contrôle d'accès sur les fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en modification sur les serveurs de fichiers Windows. |
> | **Id** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourne un fichier/dossier ou une liste de fichiers/dossiers. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retourne le résultat de l’écriture d’un fichier ou de la création d’un dossier. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourne le résultat de la suppression d’un fichier/dossier. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retourne le résultat de la modification de l’autorisation sur un fichier/dossier. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-file-data-smb-share-reader"></a>Lecteur de partage SMB de données de fichier de stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet l'accès en lecture aux fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en lecture sur les serveurs de fichiers Windows. |
> | **Id** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourne un fichier/dossier ou une liste de fichiers/dossiers. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-queue-data-contributor"></a>Contributeur aux données en file d’attente du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lire, écrire et supprimer des files d'attente et messages en file d'attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Supprimer une file d’attente. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retourner une file d’attente ou une liste de files d’attente. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modifier les métadonnées ou propriétés en file d’attente. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Supprimer un ou plusieurs messages à partir d’une file d’attente. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Récupérer un ou plusieurs messages à partir d’une file d’attente, ou en afficher un aperçu. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Ajouter un message à une file d'attente. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-queue-data-message-processor"></a>Processeur de messages de données en file d’attente du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Récupérer et supprimer un message, ou en afficher un aperçu à partir d’une file d’attente Stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Afficher l’aperçu d’un message. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Récupérer et supprimer un message. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-queue-data-message-sender"></a>Expéditeur de messages de données en file d’attente du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Ajoutez des messages à une file d’attente de stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Actions** |  |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Ajouter un message à une file d'attente. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="storage-queue-data-reader"></a>Lecteur des données en file d’attente du stockage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Lire et répertorier des files d’attente et messages en file d’attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Actions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retourne une file d’attente ou une liste de files d’attente. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Récupérer un ou plusieurs messages à partir d’une file d’attente, ou en afficher un aperçu. |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="support-request-contributor"></a>Contributeur de demande de support
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de créer et de gérer des demandes de support |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="traffic-manager-contributor"></a>Contributeur Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="user-access-administrator"></a>Administrateur de l'accès utilisateur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Vous permet de gérer l'accès utilisateur aux ressources Azure. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Actions** |  |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | Microsoft.Authorization/* | Gérer les autorisations |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="virtual-machine-administrator-login"></a>Connexion de l’administrateur aux machines virtuelles
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Afficher les machines virtuelles dans le portail et se connecter en tant qu’administrateur |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="virtual-machine-contributor"></a>Contributeur de machine virtuelle
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des machines virtuelles, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Compute/availabilitySets/* | Créer et gérer des groupes à haute disponibilité de calcul |
> | Microsoft.Compute/locations/* | Créer et gérer des emplacements de calcul |
> | Microsoft.Compute/virtualMachines/* | Créer et gérer les machines virtuelles |
> | Microsoft.Compute/virtualMachineScaleSets/* | Créez et gérez des jeux de mise à l’échelle des machines virtuelles |
> | Microsoft.Compute/disks/write | Créer ou mettre à jour un disque |
> | Microsoft.Compute/disks/read | Obtenir les propriétés d’un disque |
> | Microsoft.Compute/disks/delete | Supprimer le disque |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joint un pool d’adresses principales de passerelle d’application. Impossible à alerter. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. Impossible à alerter. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Joint un pool NAT entrant d’équilibrage de charge. Impossible à alerter. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Joint une règle nat de trafic entrant d’équilibrage de charge. Impossible à alerter. |
> | Microsoft.Network/loadBalancers/probes/join/action | Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde. Impossible à alerter. |
> | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Microsoft.Network/locations/* | Créer et gérer des emplacements réseau |
> | Microsoft.Network/networkInterfaces/* | Créer et gérer des interfaces réseau |
> | Microsoft.Network/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | Microsoft.Network/networkSecurityGroups/read | Obtient une définition de groupe de sécurité réseau. |
> | Microsoft.Network/publicIPAddresses/join/action | Joint une adresse IP publique. Impossible à alerter. |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Crée une stratégie de protection. |
> | Microsoft.RecoveryServices/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | Microsoft.RecoveryServices/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | Microsoft.Storage/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="virtual-machine-user-login"></a>Connexion de l’utilisateur aux machines virtuelles
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Affichez les machines virtuelles dans le portail et connectez-vous en tant qu’utilisateur normal. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Actions** |  |
> | Microsoft.Network/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | Microsoft.Network/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | Microsoft.Network/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | Microsoft.Network/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="web-plan-contributor"></a>Contributeur de plan web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des plans web pour des sites web, mais pas d’y accéder. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/serverFarms/* | Créer et gérer des batteries de serveurs |
> | Microsoft.Web/hostingEnvironments/Join/Action | Joint un environnement App Service Environment |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="website-contributor"></a>Contributeur de site web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Permet de gérer des sites web (pas des plans web), mais pas d’y accéder. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Actions** |  |
> | Microsoft.Authorization/*/read | Autorisation de lecture |
> | Microsoft.Insights/alertRules/* | Créer et gérer des règles d’alerte Insights |
> | Microsoft.Insights/components/* | Créer et gérer les composants Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | Microsoft.Resources/deployments/* | Créer et gérer les déploiements de groupes de ressources |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.Support/* | Créer et gérer les tickets de support |
> | Microsoft.Web/certificates/* | Créer et gérer les certificats de site web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Récupère les noms de sites affectés à un nom d’hôte. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | Microsoft.Web/sites/* | Créer et gérer des sites web (la création de sites nécessite également des autorisations d’écriture pour le plan App Service associé) |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="workbook-contributor"></a>Contributeur de classeur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut enregistrer les classeurs partagés. |
> | **Id** | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | **Actions** |  |
> | Microsoft.Insights/workbooks/write | Créer ou mettre à jour un classeur |
> | Microsoft.Insights/workbooks/delete | Supprimer un classeur |
> | Microsoft.Insights/workbooks/read | Lire un classeur |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="workbook-reader"></a>Lecteur de classeur
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Description** | Peut lire les classeurs. |
> | **Id** | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Actions** |  |
> | microsoft.insights/workbooks/read | Lire un classeur |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

## <a name="next-steps"></a>Étapes suivantes

- [Fournisseur de ressources correspondant au service](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Rôles intégrés pour les ressources Azure](custom-roles.md)
- [Autorisations dans Azure Security Center](../security-center/security-center-permissions.md)
