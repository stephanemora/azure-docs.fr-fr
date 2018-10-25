---
title: Qu’est-ce qu’Azure Key Vault ? | Microsoft Docs
description: Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Azure Key Vault, les clients peuvent chiffrer les clés et secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers .PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: barclayn
ms.openlocfilehash: 56a1ebcfbb6dda9bc96aa241bd2b8d753022181a
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385843"
---
# <a name="what-is-azure-key-vault"></a>Qu’est-ce qu’Azure Key Vault ?

Azure Key Vault aide à résoudre les problèmes suivants
- **Gestion des secrets** : Azure Key Vault peut être utilisé pour stocker en toute sécurité les jetons, mots de passe, certificats, clés API et autres secrets, et pour en contrôler étroitement l’accès.
- **Gestion des clés** : Azure Key Vault peut également servir de solution de gestion de clés. Azure Key Vault simplifie la création et le contrôle des clés de chiffrement utilisées pour chiffrer vos données. 
- **Gestion des certificats** : Azure Key Vault est également un service qui vous permet de configurer, gérer et déployer facilement des certificats SSL/TLS publics et privés pour une utilisation avec Azure et vos ressources connectées internes. 
- **Stockage de secrets protégés par des modules de sécurité matériels** : les secrets et les clés peuvent être protégés soit par logiciel soit par des modules de sécurité matériels valides FIPS 140-2 de niveau 2

## <a name="basic-concepts"></a>Concepts de base

Azure Key Vault est un outil permettant de stocker les secrets et d’y accéder en toute sécurité. Un secret est un élément pour lequel vous voulez contrôler étroitement l’accès. Il peut s’agir de clés d’API, de mots de passe ou de certificats. Un **coffre** est un groupe logique de secrets. Pour effectuer des opérations avec un coffre de clés, vous devez vous authentifier auprès de celui-ci. 

Il existe 3 façons de s’authentifier auprès d’un coffre de clés :

1. **À l’aide d’[identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)**  (**méthode recommandée/bonne pratique**) : lorsque vous déployez une application sur une machine virtuelle dans Azure, vous pouvez assigner une identité à votre machine virtuelle qui a accès au coffre de clés. Vous pouvez également assigner une identité aux autres ressources Azure qui sont répertoriées [ici](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). L’avantage de cette approche est que l’application/le service ne gère pas la rotation du premier secret. Azure fait alterner automatiquement l’identité. 
2. **À l’aide d’un principal de service et d’un certificat :** la deuxième option consiste à utiliser un principal de service et un certificat associé qui a accès au coffre de clés. La rotation du certificat incombe alors au propriétaire de l’application ou au développeur et, par conséquent, cette option n’est pas recommandée.
3. **À l’aide d’un principal de service et d’un secret :** la troisième option (non recommandée) consiste à utiliser un principal de service et un secret pour s’authentifier auprès du coffre de clés.

Vous trouverez ici quelques mots-clés :
- **Locataire** : un locataire est l’organisation qui possède et gère une instance spécifique de services de cloud Microsoft. Il fait souvent référence à l’ensemble des services Azure et Office 365 pour une organisation.
- **Propriétaire du coffre** : un propriétaire du coffre peut créer un accès et un contrôle complets au coffre de clés. Le propriétaire du coffre peut également configurer l’audit pour consigner qui accède aux secrets et aux clés. Les administrateurs peuvent contrôler le cycle de vie de la clé. Ils peuvent déployer une nouvelle version de la clé, la sauvegarder, et effectuer les tâches associées.
- **Consommateur du coffre** : un consommateur du coffre peut effectuer des actions sur les ressources à l’intérieur du coffre de clés lorsque le propriétaire du coffre lui accorde l’accès de consommateur. Les actions disponibles varient selon les autorisations accordées.
- **Ressource** : une ressource est un élément gérable disponible via Azure. Les ressources telles que les machines virtuelles, les comptes de stockage, les applications web, les bases de données et les réseaux virtuels sont courantes, mais il en existe beaucoup d’autres.
- **groupe de ressources** : un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation.
- **Principal de service** : pour accéder aux ressources qui sont sécurisées par un locataire Azure AD, l’entité qui nécessite l’accès doit être représentée par un principal de sécurité. Ceci est valable aussi bien pour les utilisateurs (principal d’utilisateur) que pour les applications (principal de service). Le principal de sécurité définit la stratégie d’accès et les autorisations pour l’utilisateur ou l’application du locataire. Cela rend possibles les fonctionnalités de base, telles que l’authentification de l’application ou de l’utilisateur lors de la connexion, et l’autorisation lors de l’accès aux ressources.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Azure AD est le service Active Directory pour un locataire. Chaque répertoire contient au moins un domaine personnalisé. Un répertoire peut avoir plusieurs abonnements associés, mais qu’un seul locataire. 
- **ID de locataire Azure** : un ID de locataire est un moyen unique d’identification d’une instance Azure AD au sein d’un abonnement Azure.
- **Identités managées pour les ressources Azure** : Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier auprès de Key Vault pour les récupérer. L’utilisation d’une identité mangée simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement managée dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur Key Vault ou n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. Pour plus d’informations, consultez le schéma ci-dessous et lisez la section de présentation des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Schéma sur la manière d’utiliser les identités managées pour les travaux Azure](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Rôles de Key Vault

Utilisez le tableau suivant afin de mieux comprendre comment Key Vault peut permettre de répondre aux besoins des développeurs et des administrateurs de sécurité.

| Rôle | Définition du problème | Résolu par Azure Key Vault |
| --- | --- | --- |
| Développeur d’une application Azure |« Je souhaite écrire une application pour Azure qui utilise des clés de signature et de chiffrement, mais je veux que ces clés soient externes à mon application afin que la solution soit adaptée à une application répartie au niveau géographique. <br/><br/>Je souhaite protéger ces clés et secrets, sans avoir à écrire le code moi-même, et je veux qu’ils soient faciles à utiliser pour moi à partir de mes applications, avec des performances optimales. » |√ Les clés sont stockées dans un coffre et appelées par un URI, si nécessaire.<br/><br/> √ Les clés sont protégées par Azure, à l’aide d’algorithmes standard, de longueurs de clé et de modules de sécurité matériel.<br/><br/> √ Les clés sont traitées dans des modules de sécurité matériels situés dans les mêmes centres de données Azure que les applications. Cette méthode garantit une meilleure fiabilité et une latence plus faible que si les clés résidaient dans un emplacement séparé, par exemple localement. |
| Développeur de logiciels SaaS (Software as a service) |« Je ne veux pas prendre la responsabilité des clés et secrets de mes clients. <br/><br/>Je veux que les clients détiennent et gèrent leurs clés, pour pouvoir me concentrer sur ce que je fais le mieux, c’est-à-dire fournir les principales fonctionnalités du logiciel. » |√ Les clients peuvent importer leurs propres clés dans Azure et les gérer. Lorsqu’une application SaaS doit effectuer des opérations de chiffrement à l’aide des clés de leurs clients, Key Vault s’en charge à sa place. L’application ne voit pas les clés des clients. |
| Responsable de la sécurité |« Je veux être sûr que nos applications sont conformes à la norme FIPS 140-2HSM de niveau 2 pour la gestion sécurisée des clés. <br/><br/>Je souhaite m’assurer que mon organisation contrôle le cycle de vie d’une clé et peut surveiller son utilisation. <br/><br/>Et bien que nous utilisions plusieurs ressources et services Azure, je souhaite gérer les clés à partir d’un emplacement unique dans Azure. » |√ Les modules de sécurité matériels sont certifiés FIPS 140-2 de niveau 2.<br/><br/>√ Key Vault a été conçu de manière à ce que Microsoft ne puisse pas afficher ni extraire vos clés.<br/><br/>√ L’utilisation de la clé est consignée en temps quasi réel.<br/><br/>√ Le coffre fournit une interface unique, indépendamment du nombre de coffres dont vous disposez dans Azure, des régions qui sont prises en charge et des applications qui les utilisent. |

Toute personne disposant d’un abonnement Azure peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, il peut être implémenté et géré par l’administrateur d’une organisation qui gère les autres services Azure pour cette dernière. Par exemple, cet administrateur peut se connecter avec un abonnement Azure, créer un coffre pour l’organisation dans lequel stocker les clés et avoir la responsabilité de tâches opérationnelles, telles que :

* créer ou importer une clé ou un secret ;
* supprimer ou effacer une clé ou un secret ;
* Autoriser des utilisateurs ou des applications à accéder au coffre de clés, afin qu’ils puissent gérer ou utiliser les clés et les clés secrètes
* configurer l’utilisation de la clé (par exemple, signer ou chiffrer) ;
* surveiller l’utilisation de clés.

Cet administrateur peut ensuite fournir aux développeurs des URI à appeler à partir de leurs applications et fournir à leur administrateur de sécurité des informations de journalisation sur l’utilisation des clés. 

![Vue d’ensemble Azure Key Vault][1]

Les développeurs peuvent également gérer les clés directement à l’aide d’API. Pour plus d’informations, consultez le [guide du développeur de Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Étapes suivantes

Pour un didacticiel de prise en main d’un administrateur, consultez la page [Prise en main d’Azure Key Vault](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation du coffre de clés, consultez [journalisation d’Azure Key Vault](key-vault-logging.md).

Pour en savoir plus sur l’utilisation des clés et des secrets avec Azure Key Vault, consultez la page [À propos des clés, des secrets et des certificats](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
