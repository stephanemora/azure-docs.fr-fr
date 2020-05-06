---
title: Sécurité et confidentialité des données
titleSuffix: Azure Cognitive Search
description: Recherche cognitive Azure est compatible avec SOC 2, HIPAA et les autres certifications. Connexion et chiffrement des données, authentification ainsi qu’identité et accès via des ID de sécurité utilisateur et de groupe dans les expressions de filtres.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/25/2020
ms.openlocfilehash: 68355ac4238aba3deaa951881bc164fe9dc08e28
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183430"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Sécurité et confidentialité des données dans Recherche cognitive Azure

Des fonctions complètes de sécurité et des contrôles d’accès sont intégrés à Recherche cognitive Azure pour s’assurer que le contenu privé reste inchangé. Cet article énumère les fonctionnalités de sécurité et la conformité aux normes intégrées à Recherche cognitive Azure.

L’architecture de sécurité de Recherche cognitive Azure inclut la sécurité physique, les transmissions chiffrées, le stockage chiffré et la conformité aux normes à l’échelle de la plateforme. Sur le plan opérationnel, Recherche cognitive Azure accepte uniquement les demandes authentifiées. Le cas échéant, vous pouvez ajouter des contrôles d’accès par utilisateur au contenu par le biais de filtres de sécurité. Cet article traite de la sécurité au niveau de chaque couche, en se centrant principalement sur la façon dont les données et les opérations sont sécurisées dans Recherche cognitive Azure.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformité aux normes : ISO 27001, SOC 2, HIPAA

Recherche cognitive Azure est certifiée pour les normes suivantes, comme [annoncé en juin 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/) :

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Conformité à la norme SOC 2 Type 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Pour accéder au rapport complet sur la conformité d’[Azure et d’Azure Government à SOC 2 de type II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [HIPAA (Health Insurance Portability and Accountability Act)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS Level 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

La conformité aux normes s’applique aux fonctionnalités généralement disponibles. Les fonctionnalités en préversion sont certifiées lors de leur transition vers la disponibilité générale, et ne doivent pas être utilisées dans des solutions soumises à des exigences strictes en termes de normes. La certification de la conformité est documentée dans [Overview of Microsoft Azure compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) (Présentation de la conformité Microsoft Azure) et [Trust Center](https://www.microsoft.com/en-us/trustcenter) (Centre de confidentialité). 

## <a name="encrypted-transmission-and-storage"></a>Stockage et transmission chiffrés

Le chiffrement s’étend à tout le pipeline d’indexation : des connexions aux données indexées stockées dans Recherche cognitive Azure, en passant par la transmission.

| Calque de sécurité | Description |
|----------------|-------------|
| Chiffrement en transit <br>(HTTPS/TLS) | Recherche cognitive Azure écoute le port HTTPS 443. Sur la plateforme, les connexions aux services Azure sont chiffrées. <br/><br/>Toutes les interactions client-service de Recherche cognitive Azure utilisent le chiffrement TLS 1.2. Les versions antérieures (1.0 et 1.1) ne sont pas prises en charge.|
| Chiffrement au repos <br>Clés gérées par Microsoft | Le chiffrement est entièrement internalisé dans le processus d’indexation, sans aucun impact mesurable sur la durée d’exécution de l’indexation ou la taille de l’index. Il se produit automatiquement lors de toutes les indexations, y compris lors des mises à jour incrémentielles d’un index qui n’est pas entièrement chiffré (créé avant janvier 2018).<br><br>En interne, le chiffrement est basé sur le [chiffrement du service de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits.<br><br> Le chiffrement est interne à Recherche cognitive Azure, tandis que les certificats et les clés de chiffrement sont gérés en interne par Microsoft et appliqués universellement. Vous ne pouvez pas activer ou désactiver le chiffrement, gérer ou substituer vos propres clés, ni afficher les paramètres de chiffrement dans le portail ou par programme.<br><br>Le chiffrement au repos a été annoncé le 24 janvier 2018 et s’applique à tous les niveaux de service, y compris au niveau gratuit, dans toutes les régions. Pour un chiffrement complet, les index créés avant cette date doivent être supprimés et recréés afin que le chiffrement soit effectué. Dans le cas contraire, seules les nouvelles données ajoutées après le 24 janvier sont chiffrées.|
| Chiffrement au repos <br>Clés gérées par le client | Le chiffrement avec des clés gérées par le client est désormais mis à la disposition générale pour les services de recherche créés à partir de janvier 2019. Il n’est pas pris en charge sur les services gratuits (partagés).<br><br>Les index et les cartes de synonymes Recherche cognitive Azure peuvent désormais être chiffrés au repos à l’aide de clés gérées par le client dans Azure Key Vault. Pour en savoir plus, consultez [Gérer les clés de chiffrement dans Recherche cognitive Azure](search-security-manage-encryption-keys.md).<br><br>Cette fonctionnalité ne remplace pas la fonctionnalité de chiffrement au repos par défaut, mais la complète.<br><br>L’activation de cette fonctionnalité a pour effet d’augmenter la taille de l’index et dégrader les performances des requêtes. Sur la base des observations effectuées à ce jour, vous pouvez vous attendre à une augmentation de 30 à 60 % des temps de requête, même si les performances réelles varient en fonction de la définition d’index et des types de requêtes. En raison de cet incidence sur les performances, nous vous recommandons de n’activer cette fonctionnalité que sur les index qui en ont réellement besoin.

## <a name="azure-wide-user-access-controls"></a>Contrôles d’accès utilisateur à l’échelle d’Azure

Plusieurs mécanismes de sécurité sont disponibles dans Azure et, de ce fait, automatiquement disponibles pour les ressources Recherche cognitive Azure que vous créez.

+ [Verrous au niveau des abonnements ou des ressources pour empêcher la suppression](../azure-resource-manager/management/lock-resources.md)
+ [Contrôle d'accès en fonction du rôle (RBAC) pour contrôler l’accès aux informations et aux opérations d’administration](../role-based-access-control/overview.md)

Tous les services Azure prennent en charge les contrôles d’accès en fonction du rôle (RBAC) pour permettre une définition des niveaux d’accès cohérente à travers tous les services. Par exemple, l’affichage de données sensibles, comme la clé d’administration, est réservé aux rôles Propriétaire et Collaborateur. En revanche, l’affichage de l’état du service est disponible pour les membres de tous les rôles. RBAC fournit des rôles Propriétaire, Collaborateur et Lecteur. Par défaut, tous les administrateurs de service sont propriétaires.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Accès au point de terminaison

### <a name="public-access"></a>Accès public

Le service Recherche cognitive Azure hérite des fonctions de sécurité de la plateforme Azure et fournit sa propre authentification basée sur une clé. Une clé API est une chaîne composée de nombres et de lettres générée de manière aléatoire. Le type de clé (admin ou requête) détermine le niveau d’accès. La soumission d’une clé valide est considérée comme la preuve que la requête provient d’une entité approuvée. 

Il existe deux niveaux d’accès à votre service de recherche, activés par deux types de clés :

* Accès administrateur (valable pour toute opération de lecture/écriture par rapport au service)
* Accès de requête (valable pour les opérations en lecture seule, telles que les requêtes, portant sur la collection de documents d’un index)

Des *clés d’administration* sont créées une fois le service approvisionné. Bien qu’il existe deux clés d’administration, désignées comme *principale* et *secondaire*, celles-ci sont en fait interchangeables. Chaque service dispose de deux clés Admin que vous pouvez interchanger sans perdre l’accès à votre service. Vous pouvez [régénérer un clé d’administration](search-security-api-keys.md#regenerate-admin-keys) périodiquement conformément aux meilleures pratiques de sécurité Azure, mais ne pouvez pas en ajouter au nombre total de clés d’administration. Il y a, au maximum, deux clés d’administration par service de recherche.

Des *clés de requête* sont créées en fonction des besoins pour les applications clientes qui émettent des requêtes. Vous pouvez créer, au maximum, 50 clés de ce type. Dans le code d’application, vous spécifiez l’URL de recherche et une clé d’API de requête pour autoriser l’accès en lecture seule à la collection de documents d’un index spécifique. Ensemble, le point de terminaison, une clé API pour un accès en lecture seule et un index cible définissent le niveau de portée et d’accès de la connexion à partir de votre application cliente.

L’authentification est requise à chaque requête, chaque requête étant composée d’une clé obligatoire, d’une opération et d’un objet. Quand ils sont chaînés, les deux niveaux d’autorisation (complet ou en lecture seule) et le contexte (par exemple, une opération de requête sur un index) sont suffisants pour fournir une sécurité couvrant l’ensemble des opérations de service. Pour plus d’informations sur les clés, consultez [Créer et gérer des clés de l’api](search-security-api-keys.md).

### <a name="restricted-access"></a>ACCÈS RESTREINT

Si vous disposez d’un service public et souhaitez restreindre son utilisation, vous pouvez utiliser la règle de restriction d’adresse IP dans la version de l’API REST de gestion : 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). IpRule vous permet de restreindre l’accès à votre service en identifiant les adresses IP, individuellement ou dans une plage, auxquelles vous souhaitez autoriser votre service de recherche à accéder. 

### <a name="private-access"></a>Accès privé

Les [points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)de Recherche cognitive Azure permettent à un client d’un réseau virtuel d’accéder en toute sécurité aux données d’un index de recherche grâce à une [liaison privée](https://docs.microsoft.com/azure/private-link/private-link-overview). Ils utilisent une adresse IP de l’espace d’adressage du réseau virtuel pour votre service de recherche. Le trafic entre le client et le service Search traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public.

Le [réseau virtuel Azure (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) permet une communication sécurisée entre ressources, avec votre réseau local, ainsi qu’avec Internet. 

## <a name="index-access"></a>Accès aux index

Dans Recherche cognitive Azure, les index individuels ne sont pas des objets sécurisables. En effet, l’accès aux index est déterminé au niveau de la couche de service (accès en lecture ou en écriture) et du contexte d’une opération.

Pour l’accès de l’utilisateur final, vous pouvez structurer les demandes de requête pour établir la connexion à l’aide d’une clé de requête, qui configure toutes les demandes en mode de lecture seule et qui inclut l’index spécifique utilisé par votre application. Dans une demande de requête, il est impossible de joindre des index ou d’accéder simultanément à plusieurs index. Ainsi, toutes les demandes ciblent un index unique par définition. Par conséquent, la structure de la demande de requête proprement dite (une clé plus un index unique cible) définit la limite de sécurité.

Il n’existe aucune différence entre l’accès administrateur et l’accès développeur aux index : tous deux doivent disposer d’un accès en écriture pour pouvoir créer, supprimer et mettre à jour des objets gérés par le service. Toute personne disposant d’une clé d’administration pour votre service peut lire, modifier ou supprimer un index de ce service. En ce qui concerne la protection contre la suppression accidentelle ou malveillante d’index, votre contrôle de code source en interne pour les ressources de code est la solution appropriée pour annuler des suppressions ou des modifications d’index indésirables. Recherche cognitive Azure dispose d’un système de basculement dans le cluster pour garantir sa disponibilité, mais il ne stocke pas et n’exécute pas le code propriétaire que vous avez utilisé pour créer ou charger des index.

Pour les solutions d’architecture mutualisée qui nécessitent des limites de sécurité au niveau des index, ces solutions incluent généralement un niveau intermédiaire, que les clients utilisent pour gérer l’isolation des index. Pour plus d’informations sur les cas d’usage d’architecture mutualisée, consultez [Modèles de conception pour les applications SaaS mutualisées et Recherche cognitive Azure](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Authentification

### <a name="admin-access"></a>Accès administrateur

L’[accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) détermine si vous avez accès aux contrôles sur le service et son contenu. Si vous êtes propriétaire ou contributeur d’un service de Recherche cognitive Azure, vous pouvez utiliser le portail ou le module **Az.Search** PowerShell pour créer, mettre à jour ou supprimer des objets sur le service. Vous pouvez également utiliser l’[API REST de gestion de Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="user-access"></a>Accès utilisateur

Par défaut, l’accès utilisateur à un index est déterminé par la clé d’accès sur la demande de requête. La plupart des développeurs créent et affectent des [*clés de requête*](search-security-api-keys.md) pour les demandes de recherche du côté client. Une clé de requête accorde un accès en lecture à l’ensemble du contenu dans l’index.

Si vous avez besoin d’un contrôle granulaire, par utilisateur, sur le contenu, vous pouvez créer des filtres de sécurité sur vos requêtes, en renvoyant des documents associés à une identité de sécurité donnée. Au lieu des rôles prédéfinis et des attributions de rôles, le contrôle d’accès basé sur l’identité est implémenté en tant que *filtre* qui limite les résultats de recherche de documents et de contenu en fonction des identités. Le tableau suivant décrit les deux approches permettant de filtrer les résultats de recherche de contenu non autorisé.

| Approche | Description |
|----------|-------------|
|[Filtrage de sécurité basé sur les filtres d’identité](search-security-trimming-for-azure-search.md)  | Cet article décrit le workflow de base pour l’implémentation du contrôle d’accès basé sur l’identité de l’utilisateur. Il décrit l’ajout d’identificateurs de sécurité à un index, puis le filtrage relatif à ce champ qui permet d’omettre les résultats de contenu non autorisé. |
|[Filtrage de sécurité basé sur les identités Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Cet article développe l’article précédent, en indiquant les étapes à suivre pour récupérer des identités d’Azure Active Directory (AAD), l’un des [services gratuits](https://azure.microsoft.com/free/) de la plateforme cloud Azure. |

## <a name="table-permissioned-operations"></a>Tableau : opérations autorisées

Le tableau suivant récapitule les opérations autorisées dans Recherche cognitive Azure, en indiquant la clé qui déverrouille l’accès à une opération particulière.

| Opération | Autorisations |
|-----------|-------------------------|
| Créer un service | Détenteur de l'abonnement Azure|
| Mettre à l’échelle un service | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource  |
| Supprimer un service | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource |
| Créer, modifier et supprimer des objets du service : <br>Index et composants (y compris les définitions de l’analyseur, les profils de score et les options CORS), indexeurs, sources de données, synonymes et générateurs de suggestions. | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource  |
| Interroger un index | Clé d’administration ou clé de requête (RBAC non applicable) |
| Interroger des informations système, telles que l’obtention de statistiques, de comptes et de listes d’objets. | Clé d’administration, RBAC sur la ressource (Propriétaire, Collaborateur ou Lecteur) |
| Gérer les clés d’administration | Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource |
| Gérer les clés de requête |  Clé d’administration, Propriétaire ou Collaborateur RBAC sur la ressource  |

## <a name="physical-security"></a>Sécurité physique

Les centres de données Microsoft fournissent une sécurité physique de pointe leader du secteur et sont conformes à un ensemble complet de normes et réglementations. Pour plus d’informations, consultez la page sur les [centres de données globaux](https://www.microsoft.com/cloud-platform/global-datacenters) ou regardez une courte vidéo sur la sécurité des centres de données.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Voir aussi

+ [Bien démarrer avec .NET (décrit l’utilisation d’une clé d’administration pour créer un index)](search-create-index-dotnet.md)
+ [Bien démarrer avec REST (décrit l’utilisation d’une clé d’administration pour créer un index)](search-create-index-rest-api.md)
+ [Contrôle d’accès basé sur l’identité à l’aide des filtres Recherche cognitive Azure](search-security-trimming-for-azure-search.md)
+ [Contrôle d’accès à Active Directory basé sur l’identité à l’aide des filtres Recherche cognitive Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtres dans la Recherche cognitive Azure](search-filters.md)