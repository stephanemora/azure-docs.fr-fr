---
title: Guide de sécurité du stockage Azure Data Lake Storage Gen2 | Microsoft Docs
description: Présente en détail les nombreuses méthodes de sécurisation de Stockage Azure, notamment RBAC et Storage Service Encryption.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/04/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: d2182942b8d1ce78fd4a72ff387c7a6a1cfead5a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976371"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Guide de sécurité Azure Data Lake Storage Gen2

La préversion d’Azure Data Lake Storage Gen2 est un ensemble de fonctionnalités basées sur les comptes Stockage Azure. Par conséquent, toutes les références de cet article concernent un compte Stockage Azure avec espace de noms hiérarchique activé (fonctionnalités de Data Lake Storage Gen2).

- Toutes les données écrites dans le stockage Azure sont automatiquement chiffrées à l’aide du [Storage Service Encryption (SSE)](storage-service-encryption.md). Pour plus d’informations, consultez [Annonce du chiffrement par défaut des objets blob, fichiers, tables et stockages de file d’attente Azure](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) et le contrôle d’accès en fonction du rôle (RBAC) sont pris en charge pour le stockage Azure pour les opérations de gestion des ressources et les opérations de données comme suit :
    - Vous pouvez attribuer des rôles RBAC limités au compte de stockage à des principaux de sécurité et utiliser Azure AD pour autoriser les opérations de gestion des ressources telles que la gestion des clés.
    - L’intégration Azure AD est prise en charge dans la préversion pour les opérations de données sur Stockage Azure. Vous pouvez attribuer des rôles RBAC limités à un abonnement, groupe de ressources, compte de stockage ou système de fichiers spécifique à un principal de sécurité ou une identité managée pour des ressources Azure. Pour plus d’informations, consultez [Authenticate access to Azure Storage using Azure Active Directory (Preview)](storage-auth-aad.md) (Authentifier l’accès à Azure Storage à l’aide d’Azure Active Directory (préversion)).
- Il est possible d’accorder un accès délégué aux objets de données d’Azure Storage en utilisant des [signatures d’accès partagé](../storage-dotnet-shared-access-signature-part-1.md).

Cet article fournit une vue d’ensemble sur chacune de ces fonctionnalités de sécurité, qui peuvent être utilisées avec le stockage Azure. Des liens vers des articles détaillés vous sont proposés pour vous permettre d’explorer de façon plus détaillée chaque fonctionnalité.

Voici les sujets qui sont abordés dans cet article :

* [Sécurité du plan de gestion](#management-plane-security) – Sécurisation de votre compte de stockage

  Le plan de gestion se compose des ressources utilisées pour gérer votre compte de stockage. Cette section couvre le modèle de déploiement Azure Resource Manager et la façon d’utiliser le contrôle d’accès en fonction du rôle (RBAC) pour contrôler l’accès à vos comptes de stockage. Elle couvre aussi la gestion des clés de compte de stockage et vous explique comment les regénérer.
* [Sécurité du plan de données](#data-plane-security) – Sécurisation de l’accès à vos données

  Dans cette section, nous verrons comment autoriser l’accès aux objets de données réels de votre compte de stockage (à savoir, les fichiers et répertoires) en utilisant des signatures d’accès partagé et des stratégies d’accès stockées. Nous évoquerons à la fois les signatures d’accès partagé (SAP) au niveau des services et les signatures d’accès partagé au niveau des comptes. Nous verrons aussi comment limiter l’accès à une adresse IP spécifique (ou à une plage d’adresses IP), comment limiter le protocole utilisé pour HTTPS et comment révoquer une signature d’accès partagé sans attendre son expiration.
* [Chiffrement en transit](#encryption-in-transit)

Cette section explique comment sécuriser les données pendant leur transfert vers ou hors d’un compte de stockage avec Data Lake Storage Gen2 activé. Nous aborderons l’utilisation recommandée du protocole HTTPS.

## <a name="management-plane-security"></a>Sécurité du plan de gestion

Le plan de gestion se compose des opérations qui affectent le compte de stockage proprement dit. Par exemple, vous pouvez créer ou supprimer un compte de stockage, obtenir la liste des comptes de stockage d’un abonnement, récupérer les clés de compte de stockage ou régénérer les clés de compte de stockage.

Ce guide est axé sur le modèle de déploiement Resource Manager, utilisé pour créer des comptes de stockage avec les fonctionnalités Data Lake Storage Gen2. Au lieu d’accorder l’accès à l’ensemble de l’abonnement, les comptes de stockage Resource Manager vous permettent de contrôler l’accès au plan de gestion à un niveau plus limité en utilisant le contrôle d’accès en fonction du rôle (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Comment sécuriser un compte de stockage en utilisant le contrôle d’accès en fonction du rôle (RBAC)

Pour commencer, expliquons ce qu’est RBAC et voyons comment l’utiliser. À chaque abonnement Azure correspond un annuaire Azure Active Directory. Les utilisateurs, les groupes et les applications de cet annuaire peuvent être autorisés à gérer les ressources de l’abonnement Azure qui reposent sur le modèle de déploiement Gestionnaire de ressources. Ce type de sécurité porte le nom de contrôle d’accès en fonction du rôle (RBAC). Pour gérer cet accès, utilisez le **Contrôle d’accès (IAM)** dans le portail Azure.

Avec le modèle Gestionnaire de ressources, vous devez placer le compte de stockage dans un groupe de ressources et contrôler l’accès au plan de gestion de ce compte de stockage spécifique à l’aide d’Azure Active Directory. Par exemple, vous pouvez permettre à certains utilisateurs d’accéder aux clés de compte de stockage, pendant que d’autres pourront voir les informations relatives au compte de stockage, mais pas accéder aux clés de compte de stockage.

#### <a name="granting-access"></a>Octroi de l’accès

L’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans l’étendue appropriée. Pour octroyer l’accès à l’ensemble de l’abonnement, vous devez attribuer un rôle au niveau de l’abonnement. Vous pouvez accorder l’accès à toutes les ressources d’un groupe de ressources en octroyant des autorisations au groupe de ressources proprement dit. Vous pouvez aussi attribuer des rôles spécifiques à certaines ressources, telles que des comptes de stockage.

Voici les principaux points à prendre en compte pour accéder aux opérations de gestion d’un compte de stockage Azure à l’aide de RBAC :

* Pour permettre à une personne d’accéder aux objets de données du compte de stockage, vous pouvez lui accorder l’autorisation de lire les clés de compte de stockage, ce qui lui permettra d’utiliser ces clés pour accéder aux fichiers et répertoires.
* Il est possible d’attribuer des rôles à un compte d’utilisateur spécifique, à un groupe d’utilisateurs ou à une application déterminée.
* À chaque rôle correspond une liste d’actions et de non-actions. Par exemple, le rôle de contributeur de machine virtuelle dispose d’une action nommée « listKeys » qui autorise la lecture des clés de compte de stockage. Le contributeur dispose de « non-actions », telles que la mise à jour de l’accès des utilisateurs présents dans Active Directory.
* Les rôles relatifs au stockage sont (entre autres) les suivants :

  * Propriétaire : il peut tout gérer, y compris l’accès.
  * Contributeur : il a les mêmes prérogatives que le propriétaire, sauf qu’il ne peut pas attribuer l’accès. Une personne associée à ce rôle peut afficher et régénérer les clés de compte de stockage. Les clés de compte de stockage lui permettent d’accéder aux objets de données.
  * Lecteur : il peut afficher les informations relatives au compte de stockage, à l’exception des secrets. Par exemple, si vous attribuez un rôle avec des autorisations de lecture sur le compte de stockage, l’utilisateur qui en bénéficie peut afficher les propriétés du compte de stockage, mais il ne peut y apporter aucune modification ni afficher les clés de compte de stockage.
  * Contributeur de compte de stockage : il peut gérer le compte de stockage, à savoir lire les groupes de ressources et les ressources de l’abonnement, créer et gérer des déploiements de groupes de ressources d’abonnement. Il peut également accéder aux clés de compte de stockage, ce qui sous-entend qu’il peut accéder au plan de données.
  * Administrateur de l’accès utilisateur :  il peut gérer l’accès utilisateur au compte de stockage. Par exemple, il peut accorder un accès en lecture à un utilisateur spécifique.
  * Contributeur de machine virtuelle : il peut gérer les machines virtuelles, mais pas le compte de stockage auquel elles sont connectées. Ce rôle permet de dresser la liste des clés de compte de stockage, ce qui signifie que l’utilisateur qui en bénéficie peut mettre à jour le plan de données.

    Pour pouvoir créer une machine virtuelle, l’utilisateur doit pouvoir créer le fichier VHD correspondant dans un compte de stockage. Pour ce faire, il doit pouvoir récupérer la clé de compte de stockage et la passer à l’API chargée de créer la machine virtuelle. Autrement dit, il doit disposer de cette autorisation pour pouvoir dresser la liste des clés de compte de stockage.
* Cette possibilité de définir des rôles personnalisés est une spécificité qui vous permet de composer un ensemble d’actions à partir d’une liste d’actions applicables aux ressources Azure.
* Pour pouvoir affecter un rôle à l’utilisateur, celui-ci doit au préalable avoir été défini dans Azure Active Directory.
* Vous pouvez créer un rapport indiquant qui a accordé/révoqué tel type d’accès à telle personne sur telle étendue à l’aide de PowerShell ou de l’interface de ligne de commande Azure.

#### <a name="resources"></a>Ressources

* [Contrôle d’accès en fonction du rôle Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Cet article décrit le contrôle d’accès en fonction du rôle d’Active Directory Azure et en explique le fonctionnement.
* [Contrôle d’accès en fonction du rôle (RBAC) : rôles intégrés](../../role-based-access-control/built-in-roles.md)

  Cet article explique en détail tous les rôles intégrés disponibles dans RBAC.
* [Présentation du déploiement de Resource Manager et du déploiement classique](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>Gestion des clés de compte de stockage

Les clés de compte de stockage sont des chaînes de 512 bits créées par Azure qui, combinées avec le nom de compte de stockage, permettent d’accéder aux objets de données présents dans le compte de stockage, notamment les blobs, les entités d’une table, les messages de file d’attente et les fichiers se trouvant sur un partage de fichiers Azure. Le contrôle d’accès aux clés de compte de stockage permet de contrôler l’accès au plan de données de ce compte de stockage.

Chaque compte de stockage a deux clés appelées « Clé 1 » et « Clé 2 » dans le [portail Azure](http://portal.azure.com/) et dans les applets de commande PowerShell. Il est possible de les régénérer manuellement au moyen de diverses méthodes, notamment avec le [Portail Azure](https://portal.azure.com/), PowerShell, l’interface de ligne de commande Azure ou par programmation en utilisant la bibliothèque cliente de stockage .NET ou l’API REST des services de Stockage Azure.

La décision de régénérer les clés de compte de stockage peut être motivée par différents facteurs.

* Vous pouvez souhaiter les régénérer à intervalles réguliers pour des raisons de sécurité.
* De même, vous régénérerez vos clés de compte de stockage si un individu a réussi à s’introduire dans une application et a pu récupérer la clé qui était codée en dur ou enregistrée dans un fichier de configuration. En pareil cas, il aura en effet un accès complet à votre compte de stockage.
* Autre cas où vos clés devront être régénérées : si votre équipe utilise une application Storage Explorer qui conserve la clé de compte de stockage et que l’un des membres de l’équipe est sur le départ. L’application continuera de fonctionner, et cette personne sera en mesure d’accéder à votre compte de stockage après être parti. Il s’agit de fait du premier motif de création de signatures d’accès partagé au niveau des comptes, qui se substituent au stockage des clés d’accès dans un fichier de configuration.

#### <a name="key-regeneration-plan"></a>Plan de régénération de clé

Vous ne pouvez pas vous permettre de régénérer la clé que vous utilisez sans aucune planification. Vous risqueriez en effet de couper tous les accès au compte de stockage, ce qui provoquerait une interruption majeure. C’est la raison pour laquelle il existe deux clés. Nous vous recommandons de régénérer une seule clé à la fois.

Avant de régénérer vos clés, veillez à dresser une liste qui recense toutes les applications qui dépendent du compte de stockage, ainsi que tous les autres services que vous utilisez dans Azure, le cas échéant. Par exemple, si vous utilisez Azure Media Services et que ce produit dépend de votre compte de stockage, vous devez resynchroniser les clés d’accès avec ce service après avoir régénéré la clé. Si vous utilisez des applications telles que Storage Explorer, vous devrez aussi fournir les nouvelles clés à ces applications. Si vous disposez de machines virtuelles dont les fichiers VHD sont stockés dans le compte de stockage, elles ne seront pas affectées par la régénération des clés de compte de stockage.

Vous pouvez régénérer vos clés dans le Portail Azure. Une fois les clés régénérées, leur synchronisation au niveau des services de stockage peut prendre jusqu’à 10 minutes.

Quand vous êtes prêt, voici la procédure générale à suivre pour modifier votre clé. Dans ce cas, vous êtes censé utiliser actuellement la clé 1 et vous apprêter à tout modifier pour utiliser la clé 2.

1. Régénérez la clé 2 de façon à la sécuriser. Vous pouvez faire cette opération dans le Portail Azure.
2. Dans toutes les applications où est stockée la clé de stockage, remplacez la valeur de clé de stockage par la nouvelle valeur de la clé 2. Testez et publiez l’application.
3. Dès lors que l’ensemble des applications et des services fonctionnent correctement, régénérez la clé 1. Cela garantit que toute personne à laquelle vous n’avez pas accordé expressément la nouvelle clé n’aura plus accès au compte de stockage.

Si vous utilisez actuellement la clé 2, vous pouvez utiliser le même processus, mais en inversant les noms des clés.

Vous pouvez effectuer la migration sur deux jours, en modifiant chaque application pour utiliser la nouvelle clé et en la publiant. Quand vous avez traité l’ensemble des services et applications, vous devez revenir en arrière et régénérer l’ancienne clé pour qu’elle ne fonctionne plus.

Une autre option consiste à placer la clé de compte de stockage dans [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) en tant que clé secrète et à faire en sorte que vos applications la récupèrent à cet endroit. Ensuite, quand vous régénérez la clé et mettez à jour Azure Key Vault, vous n’avez pas à redéployer les applications, car elles choisissent automatiquement la nouvelle clé dans Azure Key Vault. Vous pouvez faire en sorte que l’application lise la clé chaque fois que vous en avez besoin, ou vous pouvez la mettre en cache en mémoire et, en cas d’échec lors de son utilisation, vous pouvez de nouveau la récupérer dans Azure Key Vault.

L’utilisation d’Azure Key Vault ajoute également un autre niveau de sécurité pour les clés de stockage. Si vous utilisez cette méthode, la clé de stockage n’est jamais codée en dur dans un fichier de configuration, ce qui rend impossible l’accès aux clés par une personne sans autorisation spécifique.

Un autre avantage de l’utilisation d’Azure Key Vault est que vous pouvez également contrôler l’accès à vos clés à l’aide d’Azure Active Directory. Cela signifie que vous pouvez accorder l’accès aux quelques applications qui doivent récupérer les clés dans Azure Key Vault, tout en sachant que d’autres applications ne seront pas en mesure d’accéder aux clés si l’autorisation ne leur est pas expressément accordée.

> [!NOTE]
> Microsoft recommande d’utiliser uniquement l’une des clés dans toutes vos applications en même temps. Si vous utilisez parfois la clé 1 et parfois la clé 2, vous ne pouvez effectuer aucune rotation de vos clés sans qu’une application ne perde l’accès.

#### <a name="resources"></a>Ressources

* [Gérer les paramètres de compte de stockage dans le portail Azure](storage-account-manage.md)
* [Informations de référence sur l’API REST du fournisseur de ressources Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Sécurité du plan de données
La sécurité du plan de données fait référence aux méthodes permettant de sécuriser les objets de données stockés dans Stockage Azure. Nous avons vu des méthodes qui permettent de chiffrer les données et la sécurité pendant le transit des données, mais comment faire pour contrôler l’accès aux objets ?

Trois options vous permettent d’autoriser l’accès aux objets de données dans le stockage Azure, notamment :

- Utilisation d’Azure AD pour autoriser l’accès aux systèmes de fichiers et files d’attente (préversion). Azure AD offre des avantages par rapport à d’autres approches d’autorisation, notamment la suppression de la nécessité de stocker des secret dans votre code. Pour plus d’informations, consultez [Authenticate access to Azure Storage using Azure Active Directory (Preview)](storage-auth-aad.md) (Authentifier l’accès à Azure Storage à l’aide d’Azure Active Directory (préversion)). 
- Utilisation de vos clés de compte de stockage pour autoriser l’accès via la clé partagée. L’autorisation via la clé partagée nécessite de stocker vos clés de compte de stockage dans votre application. Lorsque cela est possible, Microsoft recommande donc d’utiliser Azure AD. Pour les applications de production, ou pour autoriser l’accès aux tables et fichiers Azure, continuez à utiliser la clé partagée lorsque l’intégration Azure AD est en préversion.
- Utilisation de signatures d’accès partagé pour accorder des autorisations contrôlées sur des objets de données spécifiques pour une durée spécifique.

En plus de limiter l’accès par le biais de l’autorisation, vous pouvez également utiliser [Pare-feu et réseaux virtuels](storage-network-security.md) pour limiter l’accès au compte de stockage en fonction des règles de réseau.  Cette approche vous permet de refuser l’accès au trafic Internet public et d’accorder l’accès uniquement à certains réseaux virtuels Azure ou certaines plages d’adresses IP Internet publiques.

### <a name="storage-account-keys"></a>Clés de compte de stockage

Les clés de compte de stockage sont des chaînes de 512 bits créées par Azure qui, combinées avec le nom de compte, permettent d’accéder aux objets de données stockés dans le compte de stockage.

Par exemple, vous pouvez lire des fichiers. La plupart de ces actions peuvent être effectuées avec le Portail Azure ou à l’aide de l’une des nombreuses applications de l’explorateur de stockage. Vous pouvez également écrire du code afin d’utiliser l’API REST pour effectuer ces opérations.

Comme discuté dans la section sur la [Sécurité du plan de gestion](#management-plane-security), l’accès aux clés de stockage pour un compte de stockage avec le modèle Azure Resource Manager peut être contrôlé par le biais du contrôle d’accès en fonction du rôle (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Comment déléguer l’accès aux objets dans votre compte à l’aide de signatures d’accès partagé et stratégies d’accès stockées

Une signature d’accès partagé (SAP) est une chaîne contenant un jeton de sécurité qui peut être associé à un URI et vous permet de déléguer l’accès aux objets de stockage et de spécifier des restrictions telles que les autorisations et la plage des dates/heures d’accès.

Vous pouvez accorder l’accès à des fichiers ou des répertoires.

Vous pouvez également donner à un client un jeton SAP qu’il peut utiliser pour charger des images sur un système de fichiers dans le Stockage Blob, et donner à une application web l’autorisation de lire ces images. Dans les deux cas, les problèmes sont séparés : chaque application peut recevoir uniquement l’accès dont elle a besoin pour effectuer sa tâche. Ceci est possible avec l’utilisation des signatures d’accès partagé.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Pourquoi utiliser des signatures d’accès partagé

Pourquoi utiliser une signature d’accès partagé au lieu de donner simplement votre clé de compte de stockage, ce qui est tellement plus facile ? La distribution de votre clé de compte de stockage revient à partager les clés de votre royaume de stockage. L’accès accordé est complet. Une personne peut utiliser vos clés et charger l’intégralité de son audiothèque sur votre compte de stockage. Elle peut également remplacer vos fichiers par des versions infectées par des virus, ou voler vos données. Le fait d’accorder un accès illimité à votre compte de stockage est une décision que vous ne devez pas prendre à la légère.

Avec les signatures d’accès partagé, vous pouvez donner à un client uniquement les autorisations requises pour une durée limitée. Par exemple, si un utilisateur charge un fichier sur votre compte, vous pouvez lui accorder un accès en écriture juste le temps nécessaire de charger le fichier (en fonction de la taille du fichier, bien sûr). Si vous changez ensuite d’avis, vous pouvez révoquer cet accès.

En outre, vous pouvez spécifier que les demandes effectuées à l’aide d’une signature d’accès partagé doivent être limitées à une certaine adresse IP ou plage d’adresses IP externe à Azure. Vous pouvez également exiger que les demandes soient effectuées à l’aide d’un protocole spécifique (HTTPS ou HTTP/HTTPS). Cela signifie que, si vous souhaitez uniquement autoriser le trafic HTTPS, vous pouvez affecter la valeur HTTPS uniquement au protocole requis et le trafic HTTP sera bloqué.

#### <a name="definition-of-a-shared-access-signature"></a>Définition d’une signature d’accès partagé

Une signature d’accès partagé est un ensemble de paramètres de requête ajoutés à l’URL pointant vers la ressource

qui fournit des informations sur l’accès autorisé et la durée pendant laquelle l’accès est autorisé. Voici un exemple ; cet URI fournit un accès en lecture à un objet blob pendant cinq minutes. Les paramètres de requête SAP doivent être encodés au format URL, par exemple %3A pour le signe deux-points (:) ou %20 pour un espace.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Autorisation de la signature d’accès partagé par le service de Stockage Azure

Quand le service de stockage reçoit la demande, il accepte les paramètres de requête d’entrée et crée une signature en utilisant la même méthode que le programme appelant. Il compare ensuite les deux signatures. Si elles correspondent, le service de stockage peut vérifier que la version du service de stockage est valide, que la date et l’heure sont dans la fenêtre spécifiée, que l’accès demandé correspond à la demande effectuée, etc.

Par exemple, avec notre URL ci-dessus, si l’URL pointe vers un fichier au lieu d’un objet blob, cette demande échoue, car elle spécifie que la signature d’accès partagé concerne un objet blob. Si la commande REST appelée doit mettre à jour un objet blob, elle échoue car la signature d’accès partagé spécifie que seul l’accès en lecture est autorisé.

#### <a name="types-of-shared-access-signatures"></a>Types de signatures d’accès partagé

* Une signature d’accès partagé de niveau service permet d’accéder à des ressources spécifiques dans un compte de stockage. Il pourrait par exemple s’agir de récupérer une liste de fichiers dans un système de fichiers ou de télécharger un fichier.
* Une signature d’accès partagé de niveau compte permet d’accéder à tout ce pour quoi une signature d’accès partagé de niveau service peut être utilisée. En outre, elle peut proposer des options pour des ressources qui ne sont pas autorisées avec une signature d’accès partagé de niveau service, telles que la capacité de créer des systèmes de fichiers.

#### <a name="creating-a-sas-uri"></a>Création d’un URI SAS

1. Vous pouvez créer un URI à la demande, en définissant chaque fois l’ensemble des paramètres de requête.

   Cette approche est flexible, mais si vous avez un ensemble logique de paramètres qui sont chaque fois similaires, il est plus judicieux d’utiliser une stratégie d’accès stockée.
2. Vous pouvez créer une stratégie d’accès stockée pour un système de fichiers entier, un partage de fichiers, une table ou une file d’attente. Vous pouvez ensuite l’utiliser comme base pour les URI SAP que vous créez. Les autorisations basées sur les stratégies d’accès stockées peuvent être facilement révoquées. Jusqu’à cinq stratégies peuvent être définies sur chaque système de fichiers, file d’attente, table ou partage de fichiers.

   Par exemple, si un grand nombre de personnes doivent lire les objets blob dans un système de fichiers spécifique, vous pouvez créer une stratégie d’accès stockée qui indique « accorder l’accès en lecture » et tous les autres paramètres qui seront chaque fois les mêmes. Vous pouvez ensuite créer un URI SAP en utilisant les paramètres de la stratégie d’accès stockée et en spécifiant la date/l’heure d’expiration. L’avantage de cette opération est que vous n’avez pas à spécifier chaque fois tous les paramètres de requête.

#### <a name="revocation"></a>Révocation

Supposons que votre signature d’accès partagé a été compromise ou que vous voulez la modifier en raison des exigences de sécurité de l’entreprise ou de conformité aux réglementations. Comment révoquer l’accès à une ressource à l’aide de cette signature ? Cela dépend de la façon dont vous avez créé l’URI SAP.

Si vous utilisez des URI appropriés, vous avez trois possibilités. Vous pouvez émettre des jetons SAP avec des stratégies de délai d’expiration court et attendre que la signature d’accès partagé expire. Vous pouvez renommer ou supprimer la ressource (en supposant que le jeton a été étendu à un objet unique). Vous pouvez modifier les clés de compte de stockage. Cette dernière option peut avoir un impact important, en fonction du nombre de services qui utilisent ce compte de stockage, et n’est probablement pas souhaitable sans une certaine planification.

Si vous utilisez une signature d’accès partagé dérivée d’une stratégie d’accès stockée, vous pouvez supprimer l’accès en révoquant la stratégie d’accès stockée : vous pouvez simplement la modifier de sorte qu’elle ait déjà expiré, ou la supprimer complètement. Cette opération prend effet immédiatement et invalide toutes les signatures d’accès partagé créées à l’aide de cette stratégie d’accès stockée. La mise à jour ou la suppression de la stratégie d’accès stockée peut avoir un impact sur les personnes qui accèdent à ce système de fichiers, ce partage de fichiers, cette table ou cette file d’attente spécifique par le biais d’une signature d’accès partagé, mais si les clients sont écrits de façon à demander une nouvelle signature quand l’ancienne n’est plus valide, cela fonctionne correctement.

Comme l’utilisation d’une signature d’accès partagé dérivée d’une stratégie d’accès stockée vous permet de révoquer immédiatement cette signature d’accès partagé, il est recommandé de toujours utiliser des stratégies d’accès stockées quand cela est possible.

#### <a name="resources"></a>Ressources

Pour plus d’informations sur l’utilisation des signatures d’accès partagé et des stratégies d’accès stockées, ainsi que pour obtenir des exemples, consultez les articles suivants :

* Ce sont les articles de référence.

  * [Exemples d’associations de sécurité de service](https://msdn.microsoft.com/library/dn140256.aspx)

    Cet article fournit des exemples d’utilisation d’une signature d’accès partagé de niveau service avec des objets blob, des messages de file d’attente, des plages de tables et des fichiers.
  * [Construction d’un service SAP](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Construction d’un compte SAP](https://msdn.microsoft.com/library/mt584140.aspx)
* Il s’agit de didacticiels pour l’utilisation de la bibliothèque cliente .NET pour créer des signatures d’accès partagé et des stratégies d’accès stockées.

  * [Utilisation des signatures d’accès partagé (SAP)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Signatures d’accès partagé, partie 2 : créer et utiliser une signature d’accès partagé avec Stockage Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Cet article contient une description du modèle SAP, des exemples de signatures d’accès partagé et des recommandations pour une utilisation optimale de ces signatures. La révocation de l’autorisation accordée est également abordée.

* Authentification

  * [Authentification pour les services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Didacticiel de prise en main des signatures d’accès partagé

  * [SAS Getting Started Tutorial (Didacticiel de prise en main des signatures d’accès partagé)](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Chiffrement en transit

### <a name="transport-level-encryption--using-https"></a>Chiffrement au niveau du transport – Utilisation de HTTPS

Une autre étape à suivre pour garantir la sécurité de vos données Azure Storage consiste à chiffrer les données entre le client et Azure Storage. La première recommandation est de toujours utiliser le protocole [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , qui garantit une communication sécurisée via l’Internet public.

Pour disposer d’un canal de communication sécurisé, vous devez toujours utiliser HTTPS lors de l’appel des API REST ou de l’accès aux objets dans le stockage. De plus, les **signatures d’accès partagé**, qui peuvent être utilisées pour déléguer l’accès aux objets de stockage Azure, incluent une option pour spécifier que seul le protocole HTTPS est autorisé avec les signatures d’accès partagé. Cette option garantit que le protocole approprié est utilisé par tous ceux qui envoient des liens avec des jetons SAP.

Vous pouvez appliquer l’utilisation du protocole HTTPS lorsque vous appelez les API REST pour accéder aux objets dans les comptes de stockage en activant l’option [Transfert sécurisé requis](../storage-require-secure-transfer.md) pour le compte de stockage. Les connexions utilisant le protocole HTTP seront refusées une fois cette option activée.

## <a name="encryption-at-rest"></a>Chiffrement au repos

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE est activé pour tous les comptes de stockage et ne peut pas être désactivé. SSE chiffre automatiquement vos données lors de leur écriture dans le stockage Azure. Lorsque vous lisez des données depuis le stockage Azure, elles sont déchiffrées par le stockage Azure avant d’être retournées. SSE vous permet de sécuriser vos données sans avoir à modifier le code existant ni à ajouter du code dans les applications.

Vous pouvez utiliser des clés gérées par Microsoft ou vos propres clés personnalisées. Microsoft génère les clés gérées, puis gère leur stockage sécurisé ainsi que leur rotation régulière, conformément à la politique interne de Microsoft en la matière. Pour en savoir plus sur l’utilisation de clés personnalisées, consultez [Storage Service Encryption avec des clés gérées par le client dans Azure Key Vault](storage-service-encryption-customer-managed-keys.md).