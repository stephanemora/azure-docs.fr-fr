---
title: Présentation de la sécurité
titleSuffix: Azure Cognitive Search
description: Recherche cognitive Azure est compatible avec SOC 2, HIPAA et les autres certifications. Connexion et chiffrement des données, authentification ainsi qu’identité et accès via des ID de sécurité utilisateur et de groupe dans les expressions de filtres.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: cc02890cb5293e48a8065b63f4f9c799c5dda7f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85081033"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Sécurité dans Recherche cognitive Azure - Vue d’ensemble

Cet article décrit les principales fonctionnalités de sécurité de Recherche cognitive Azure qui peuvent protéger le contenu et les opérations. 

+ Au niveau de la couche de stockage, le chiffrement au repos est effectué au niveau de la plateforme, mais Recherche cognitive offre également une option de « chiffrement double » pour les clients qui souhaitent bénéficier de la double protection des clés gérées par l’utilisateur et de celles gérées par Microsoft.

+ La sécurité du trafic entrant protège le point de terminaison du service Recherche à des niveaux de sécurité plus élevés : depuis des clés API sur la demande à des règles de trafic entrant dans le pare-feu et à des points de terminaison privés qui protègent intégralement votre service de l’Internet public.

+ La sécurité du trafic sortant s’applique aux indexeurs qui extraient du contenu de sources externes. Pour les demandes sortantes, configurez une identité managée pour effectuer une recherche dans un service approuvé lors de l’accès aux données dans Stockage Azure, Azure SQL, Cosmos DB ou d’autres sources de données Azure. Une identité managée est un substitut pour les informations d’identification ou les clés d’accès sur la connexion. La sécurité du trafic sortant n’est pas abordée dans cet article. Pour plus d’informations sur cette fonctionnalité, consultez [Se connecter à une source de données en utilisant une identité managée](search-howto-managed-identities-data-sources.md).

Regardez cette vidéo rapide pour obtenir une vue d’ensemble de l’architecture de la sécurité et de chaque catégorie de fonctionnalités.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="encrypted-transmissions-and-storage"></a>Transmissions et stockage chiffrés

Le chiffrement est omniprésent dans Recherche cognitive Azure : il commence aux connexions et aux transmissions, et s’étend au contenu stocké sur disque. Pour les services de recherche sur l’Internet public, Recherche cognitive Azure écoute sur le port HTTPS 443. Toutes les connexions client-à-service utilisent le chiffrement TLS 1.2. Les versions antérieures (1.0 et 1.1) ne sont pas prises en charge.

### <a name="data-encryption-at-rest"></a>Chiffrement des données au repos

Recherche cognitive Azure stocke les définitions d’index et le contenu, les définitions de sources de données, les définitions d’indexeurs, les définitions d’ensembles de compétences et les mappages de synonymes.

Sur la couche de stockage, les données sont chiffrées sur disque avec des clés gérées par Microsoft. Vous ne pouvez pas activer ou désactiver le chiffrement, ni visualiser les paramètres de chiffrement dans le portail ou programmatiquement. Le chiffrement est entièrement internalisé, sans aucun impact mesurable sur la durée d’exécution de l’indexation ou la taille de l’index. Il se produit automatiquement lors de toutes les indexations, y compris lors des mises à jour incrémentielles d’un index qui n’est pas entièrement chiffré (créé avant janvier 2018).

En interne, le chiffrement est basé sur le [chiffrement du service de stockage Azure](../storage/common/storage-service-encryption.md), à l’aide du [chiffrement AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 bits.

> [!NOTE]
> Le chiffrement au repos a été annoncé le 24 janvier 2018 et s’applique à tous les niveaux de service, y compris au niveau gratuit, dans toutes les régions. Pour un chiffrement complet, les index créés avant cette date doivent être supprimés et recréés afin que le chiffrement soit effectué. Dans le cas contraire, seules les nouvelles données ajoutées après le 24 janvier sont chiffrées.

### <a name="customer-managed-key-cmk-encryption"></a>Chiffrement avec une clé gérée par le client

Les clients qui veulent une protection supplémentaire du stockage peuvent chiffrer les données et les objets avant leur stockage et leur chiffrement sur le disque. Cette approche est basée sur une clé appartenant à l’utilisateur, gérée et stockée via Azure Key Vault, indépendamment de Microsoft. Le chiffrement du contenu avant son chiffrement sur disque est appelé « double chiffrement ». Actuellement, vous pouvez double-chiffrer de façon sélective les index et les mappages de synonymes. Pour plus d’informations, consultez [Clés de chiffrement gérées par le client dans Recherche cognitive Azure](search-security-manage-encryption-keys.md).

> [!NOTE]
> Le chiffrement CMK est en disponibilité générale pour les services de recherche créés après janvier 2019. Il n’est pas pris en charge sur les services gratuits (partagés). 
>
>L’activation de cette fonctionnalité a pour effet d’augmenter la taille de l’index et dégrader les performances des requêtes. Sur la base des observations effectuées à ce jour, vous pouvez vous attendre à une augmentation de 30 à 60 % des temps de requête, même si les performances réelles varient en fonction de la définition d’index et des types de requêtes. En raison de cet incidence sur les performances, nous vous recommandons de n’activer cette fonctionnalité que sur les index qui en ont réellement besoin.

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Sécurité du trafic entrant et protection des points de terminaison

Les fonctionnalités de sécurité du trafic entrant protègent le point de terminaison du service de recherche via des niveaux croissants de sécurité et de complexité. Premièrement, toutes les demandes nécessitent une clé API pour l’accès authentifié. Deuxièmement, vous avez la possibilité de définir des règles de pare-feu qui limitent l’accès à des adresses IP spécifiques. Pour une protection avancée, une troisième option consiste à activer Azure Private Link pour protéger votre point de terminaison de service de tout le trafic Internet.

### <a name="public-access-using-api-keys"></a>Accès public avec des clés API

Par défaut, un service de recherche est accessible via le cloud public, en utilisant une authentification basée sur des clés pour l’administration ou l’accès aux requêtes au point de terminaison du service de recherche. Une clé API est une chaîne composée de nombres et de lettres générée de manière aléatoire. Le type de clé (admin ou requête) détermine le niveau d’accès. La soumission d’une clé valide est considérée comme la preuve que la requête provient d’une entité approuvée. 

Il existe deux niveaux d’accès à votre service de recherche, qui sont activés par les deux clés API suivantes :

+ Clé d’administration (autorise l’accès en lecture-écriture pour les opérations [créer - lire - mettre à jour - supprimer](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) sur le service de recherche)

+ Clé de requête (autorise l’accès en lecture seule à la collection documents d’un index)

Des *clés d’administration* sont créées une fois le service approvisionné. Bien qu’il existe deux clés d’administration, désignées comme *principale* et *secondaire*, celles-ci sont en fait interchangeables. Chaque service dispose de deux clés Admin que vous pouvez interchanger sans perdre l’accès à votre service. Vous pouvez [régénérer un clé d’administration](search-security-api-keys.md#regenerate-admin-keys) périodiquement conformément aux meilleures pratiques de sécurité Azure, mais ne pouvez pas en ajouter au nombre total de clés d’administration. Il y a, au maximum, deux clés d’administration par service de recherche.

Des *clés de requête* sont créées en fonction des besoins pour les applications clientes qui émettent des requêtes. Vous pouvez créer, au maximum, 50 clés de ce type. Dans le code d’application, vous spécifiez l’URL de recherche et une clé d’API de requête pour autoriser l’accès en lecture seule à la collection de documents d’un index spécifique. Ensemble, le point de terminaison, une clé API pour un accès en lecture seule et un index cible définissent le niveau de portée et d’accès de la connexion à partir de votre application cliente.

L’authentification est requise à chaque requête, chaque requête étant composée d’une clé obligatoire, d’une opération et d’un objet. Quand ils sont chaînés, les deux niveaux d’autorisation (complet ou en lecture seule) et le contexte (par exemple, une opération de requête sur un index) sont suffisants pour fournir une sécurité couvrant l’ensemble des opérations de service. Pour plus d’informations sur les clés, consultez [Créer et gérer des clés de l’api](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Accès restreint à des adresses IP

Pour contrôler davantage l’accès à votre service de recherche, vous pouvez créer des règles de pare-feu de trafic entrant qui autorisent l’accès à une adresse IP spécifique ou à une plage d’adresses IP. Toutes les connexions clientes doivent être effectuées via une adresse IP autorisée, sans quoi la connexion est refusée.

Vous pouvez utiliser le portail pour [configurer l’accès du trafic entrant](service-configure-firewall.md). 

Vous pouvez aussi utiliser les API REST de gestion. L’API version 13-03-2020 avec le paramètre [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) vous permet de restreindre l’accès à votre service en identifiant les adresses IP, individuellement ou dans une plage, autorisées à accéder à votre service de recherche. 

### <a name="private-endpoint-no-internet-traffic"></a>Point de terminaison privé (pas de trafic Internet)

Un [point de terminaison privé](../private-link/private-endpoint-overview.md) pour Recherche cognitive Azure permet à un client d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) d’accéder de façon sécurisée aux données d’un index de recherche grâce à une [liaison privée](../private-link/private-link-overview.md). 

Le points de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour les connexions à votre service de recherche. Le trafic entre le client et le service Search traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public. Un réseau virtuel permet une communication sécurisée entre des ressources, avec votre réseau local ainsi qu’avec Internet. 

Bien que cette solution soit la plus sécurisée, l’utilisation de services supplémentaires représente un coût supplémentaire : veillez donc à avoir une compréhension claire des avantages avant de la mettre en place. Pour plus d’informations sur les coûts, consultez la [page Tarification](https://azure.microsoft.com/pricing/details/private-link/). Pour plus d’informations sur la façon dont ces composants fonctionnent ensemble, regardez la vidéo en haut de cet article. L’option du point de terminaison privé est présentée à partir de 5:48 dans la vidéo. Pour obtenir des instructions sur la configuration du point de terminaison, consultez [Créer un point de terminaison privé pour Recherche cognitive Azure](service-create-private-endpoint.md).

## <a name="index-access"></a>Accès aux index

Dans Recherche cognitive Azure, les index individuels ne sont pas des objets sécurisables. Au lieu de cela, l’accès aux index est déterminé au niveau de la couche du service (accès en lecture ou en écriture au service) et du contexte d’une opération.

Pour l’accès de l’utilisateur final, vous pouvez structurer les demandes de requête pour établir la connexion à l’aide d’une clé de requête, qui configure toutes les demandes en mode de lecture seule et qui inclut l’index spécifique utilisé par votre application. Dans une demande de requête, il est impossible de joindre des index ou d’accéder simultanément à plusieurs index. Ainsi, toutes les demandes ciblent un index unique par définition. Par conséquent, la structure de la demande de requête proprement dite (une clé plus un index unique cible) définit la limite de sécurité.

Il n’existe aucune différence entre l’accès administrateur et l’accès développeur aux index : tous deux doivent disposer d’un accès en écriture pour pouvoir créer, supprimer et mettre à jour des objets gérés par le service. Toute personne disposant d’une clé d’administration pour votre service peut lire, modifier ou supprimer un index de ce service. En ce qui concerne la protection contre la suppression accidentelle ou malveillante d’index, votre contrôle de code source en interne pour les ressources de code est la solution appropriée pour annuler des suppressions ou des modifications d’index indésirables. Recherche cognitive Azure dispose d’un système de basculement dans le cluster pour garantir sa disponibilité, mais il ne stocke pas et n’exécute pas le code propriétaire que vous avez utilisé pour créer ou charger des index.

Pour les solutions d’architecture mutualisée qui nécessitent des limites de sécurité au niveau des index, ces solutions incluent généralement un niveau intermédiaire, que les clients utilisent pour gérer l’isolation des index. Pour plus d’informations sur les cas d’usage d’architecture mutualisée, consultez [Modèles de conception pour les applications SaaS mutualisées et Recherche cognitive Azure](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Accès utilisateur

La façon dont un utilisateur accède à un index et à d’autres objets est déterminée par le type de clé API sur la demande. La plupart des développeurs créent et affectent des [*clés de requête*](search-security-api-keys.md) pour les demandes de recherche du côté client. Une clé de requête accorde un accès en lecture au contenu pouvant faire l’objet d’une recherche dans l’index.

Si vous avez besoin d’un contrôle précis par utilisateur sur les résultats de la recherche, vous pouvez créer des filtres de sécurité sur vos requêtes, en retournant des documents associés à une identité de sécurité donnée. Au lieu des rôles prédéfinis et des attributions de rôles, le contrôle d’accès basé sur l’identité est implémenté en tant que *filtre* qui limite les résultats de recherche de documents et de contenu en fonction des identités. Le tableau suivant décrit les deux approches permettant de filtrer les résultats de recherche de contenu non autorisé.

| Approche | Description |
|----------|-------------|
|[Filtrage de sécurité basé sur les filtres d’identité](search-security-trimming-for-azure-search.md)  | Cet article décrit le workflow de base pour l’implémentation du contrôle d’accès basé sur l’identité de l’utilisateur. Il décrit l’ajout d’identificateurs de sécurité à un index, puis le filtrage relatif à ce champ qui permet d’omettre les résultats de contenu non autorisé. |
|[Filtrage de sécurité basé sur les identités Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Cet article développe l’article précédent, en indiquant les étapes à suivre pour récupérer des identités d’Azure Active Directory (AAD), l’un des [services gratuits](https://azure.microsoft.com/free/) de la plateforme cloud Azure. |

## <a name="administrative-rights"></a>Droits d’administration

[RBAC (Contrôle d’accès en fonction du rôle)](../role-based-access-control/overview.md) est un système d’autorisation basé sur [Azure Resource Manager](../azure-resource-manager/management/overview.md) pour le provisionnement de ressources Azure. Dans Recherche cognitive Azure , Resource Manager est utilisé pour créer ou supprimer le service, gérer les clés API et mettre à l’échelle le service. Ainsi, les attributions de rôles RBAC déterminent qui peut effectuer ces tâches, qu’elles utilisent le [portail](search-manage.md), [PowerShell](search-manage-powershell.md) ou les [API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

En revanche, les droits d’administrateur sur le contenu hébergé sur le service, comme la possibilité de créer ou de supprimer un index, sont conférés via des clés API, comme décrit dans la [section précédente](#index-access).

> [!TIP]
> En utilisant des mécanismes à l’échelle d’Azure, vous pouvez verrouiller un abonnement ou une ressource pour empêcher la suppression accidentelle ou non autorisée de votre service de recherche par les utilisateurs disposant de droits d’administration. Pour plus d’informations, consultez [Verrouiller les ressources pour en empêcher la suppression](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certifications et conformité

Recherche cognitive Azure a été certifié conforme à plusieurs standards mondiaux, régionaux et spécifiques à des secteurs pour le cloud public et Azure Government. Pour obtenir la liste complète, téléchargez le livre blanc [**Microsoft Azure Compliance Offerings**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) depuis la page des rapports d’audit officiels.

## <a name="see-also"></a>Voir aussi

+ [Concepts de base de la sécurité Azure](../security/fundamentals/index.yml)
+ [Sécurité Azure](https://azure.microsoft.com/overview/security)
+ [Centre de sécurité Azure](../security-center/index.yml)