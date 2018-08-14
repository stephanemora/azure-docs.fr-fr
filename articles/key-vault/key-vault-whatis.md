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
ms.topic: get-started-article
ms.date: 08/02/2018
ms.author: barclayn
ms.openlocfilehash: 08331a399044eba17060d15f24af1863df38caf5
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480251"
---
# <a name="what-is-azure-key-vault"></a>Qu’est-ce qu’Azure Key Vault ?

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Key Vault, vous pouvez chiffrer les clés et les secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM. Dans ce cas, Microsoft traite vos clés dans des modules de sécurité matériels validés selon la norme « FIPS 140-2 Level 2 » (matériel et microprogramme).  

Key Vault rationalise le processus de gestion de clés et vous permet de garder le contrôle des clés qui accèdent à vos données et les chiffrent. Les développeurs peuvent créer des clés pour le développement et le test en quelques minutes, puis les migrer en toute transparence en clés de production. Les administrateurs de sécurité peuvent accorder (et annuler) les autorisations sur les clés, si nécessaire.

## <a name="basic-concepts"></a>Concepts de base

Azure Key Vault est un outil permettant de stocker les secrets et d’y accéder en toute sécurité. Un secret est un élément pour lequel vous voulez contrôler étroitement l’accès. Il peut s’agir de clés d’API, de mots de passe ou de certificats.
Ci-dessous, vous trouverez quelques mots-clés :
- **Locataire** : un locataire est l’organisation qui possède et gère une instance spécifique de services de cloud Microsoft. Il fait souvent référence à l’ensemble des services Azure et Office 365 pour une organisation.
- **Propriétaire du coffre** : il peut créer un accès et un contrôle complets au coffre de clés. Le propriétaire du coffre peut également configurer l’audit pour consigner qui accèdent aux secrets et aux clés. Les administrateurs peuvent contrôler le cycle de vie de la clé. Ils peuvent déployer une nouvelle version de la clé, la sauvegarder, etc.
- **Ressource** : un élément gérable disponible via Azure. Les ressources telles que les machines virtuelles, les comptes de stockage, les applications web, les bases de données et les réseaux virtuels sont courantes, mais il en existe beaucoup d’autres.
- **Groupe de ressources** : un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. Voir Groupes de ressources.
- **Consommateur du coffre** : il peut effectuer des actions sur les ressources à l’intérieur du coffre de clés lorsque le propriétaire du coffre lui accorde l’accès. L’accès dépend des autorisations octroyées.
- **[Azure Active Directory](../active-directory/active-directory-whatis.md)**  est le service Azure AD pour un locataire donné. Chaque répertoire contient au moins un domaine personnalisé. Un répertoire peut avoir plusieurs abonnements associés, mais qu’un seul locataire. 
- **ID de locataire Azure** : il s’agit d’un moyen unique d’identification d’Azure Active Directory au sein d’un abonnement Azure. 
- **Managed Service Identity** : Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. Managed Service Identity (MSI) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement managée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur Key Vault ou n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. Pour en savoir plus sur MSI, cliquez [ici](../active-directory/managed-service-identity/overview.md).

    ![Graphe MSI](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Rôles de Key Vault

Utilisez le tableau suivant afin de mieux comprendre comment Key Vault peut permettre de répondre aux besoins des développeurs et des administrateurs de sécurité.

| Rôle | Définition du problème | Résolu par Azure Key Vault |
| --- | --- | --- |
| Développeur d’une application Azure |« Je souhaite écrire une application pour Azure qui utilise des clés de signature et de chiffrement, mais je veux que ces clés soient externes à mon application afin que la solution soit adaptée à une application répartie au niveau géographique. <br/><br/>Je souhaite également protéger ces clés et secrets, sans avoir à écrire le code moi-même, et je veux qu’ils soient faciles à utiliser pour moi à partir de mes applications, avec des performances optimales. » |√ Les clés sont stockées dans un coffre et appelées par un URI, si nécessaire.<br/><br/> √ Les clés sont protégées par Azure, à l’aide d’algorithmes standard, de longueurs de clé et de modules de sécurité matériel (HSM).<br/><br/> √ Les clés sont traitées dans des modules de sécurité matériels situés dans les mêmes centres de données Azure que les applications. Cela garantit une meilleure fiabilité et une latence plus faible que si les clés résidaient dans un emplacement séparé, par exemple localement. |
| Développeur de logiciels en tant que service (SaaS) |« Je ne veux pas prendre la responsabilité des clés et secrets de mes clients. <br/><br/>Je souhaite que les clients possèdent et gèrent leurs clés pour pouvoir me concentrer sur ce que je fais le mieux, à savoir fournir les principales fonctionnalités du logiciel ». |√ Les clients peuvent importer leurs propres clés dans Azure et les gérer. Lorsqu’une application SaaS doit effectuer des opérations de chiffrement à l’aide des clés de leurs clients, Key Vault s’en charge à sa place. L’application ne voit pas les clés des clients. |
| Responsable de la sécurité |« Je veux être sûr que nos applications sont conformes à la norme FIPS 140-2HSM de niveau 2 pour la gestion sécurisée des clés. <br/><br/>Je souhaite m’assurer que mon organisation contrôle le cycle de vie d’une clé et peut surveiller son utilisation. <br/><br/>Et bien que nous utilisions plusieurs ressources et services Azure, je souhaite gérer les clés à partir d’un emplacement unique dans Azure. » |√ Les modules de sécurité matériels sont certifiés FIPS 140-2 de niveau 2.<br/><br/>√ Key Vault a été conçu de manière à ce que Microsoft ne puisse pas afficher ni extraire vos clés.<br/><br/>√ Journalisation de l’utilisation des clés, quasiment en temps réel.<br/><br/>√ Le coffre fournit une interface unique, indépendamment du nombre de coffres dont vous disposez dans Azure, des régions qui sont prises en charge et des applications qui les utilisent. |

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

Pour en savoir plus sur l’utilisation des clés et des secrets avec Azure Key Vault, consultez la page [About Keys, Secrets, and Certificates](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx) (À propos des clés, des secrets et des certificats).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
