---
title: Vue d’ensemble de la sécurité d’Azure SQL Database | Microsoft Docs
description: Découvrez les aspects liés à la sécurité d’Azure SQL Database et de SQL Server, notamment les différences entre le cloud et SQL Server en local.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 584f30cc12aee722aed1079d5cefaee06d403cba
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867662"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Une vue d’ensemble des fonctionnalités de sécurité d’Azure SQL Database

Cet article décrit les principes de la sécurisation de la couche Données d’une application à l’aide de Microsoft Azure SQL Database. La stratégie de sécurité décrite suit une approche en couches de défense en profondeur comme illustré ci-après, et se déplace de l’extérieur vers :

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Sécurité du réseau

Microsoft Azure SQL Database fournit un service de base de données relationnelle pour les applications du cloud et de l'entreprise. Pour protéger les données client, les pare-feu empêchent tout accès réseau au serveur de base de données, sauf octroi d'accès explicite en fonction de l'adresse IP ou de l'origine du trafic de réseau virtuel Azure.

### <a name="ip-firewall-rules"></a>Règles de pare-feu IP

Les règles de pare-feu IP octroient l’accès à la base de données en fonction de l’adresse IP d’origine de chaque requête. Pour plus d’informations, consultez [Vue d’ensemble des règles de pare-feu d’Azure SQL Database et de SQL Data Warehouse](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Règles de pare-feu de réseau virtuel

Les [points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) étendent la connectivité de votre réseau virtuel sur la dorsale principale d’Azure et permettent à Azure SQL Database d’identifier le sous-réseau de réseau virtuel d’où provient le trafic. Pour permettre au trafic d'atteindre Azure SQL Database, utilisez les [balises de service](../virtual-network/security-overview.md) SQL et autorisez le trafic sortant via les groupes de sécurité réseau.

Les [règles de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) permettent à Azure SQL Database de n’accepter que les communications provenant de sous-réseaux sélectionnés à l’intérieur d’un réseau virtuel.

> [!NOTE]
> Le contrôle d’accès avec des règles de pare-feu ne s’applique *pas* à une **instance gérée**. Pour plus d’informations sur la configuration de la mise en réseau requise, consultez [Connexion à une instance gérée](sql-database-managed-instance-connect-app.md).

## <a name="access-management"></a>gestion de l’accès

> [!IMPORTANT]
> La gestion des bases de données et des serveurs de bases de données dans Azure est contrôlée par les attributions de rôle de votre compte d’utilisateur du portail. Pour en savoir plus à ce sujet, consultez [Contrôle d’accès en fonction du rôle dans le portail Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentification

L’authentification est le processus consistant à prouver que l’utilisateur est bien celui qu’il prétend être. Azure SQL Database prend en charge deux types d’authentification :

- **Authentification SQL** :

    L’authentification SQL Database fait référence à l’authentification d’un utilisateur qui se connecte à [Azure SQL Database](sql-database-technical-overview.md) à l'aide d'un nom d'utilisateur et d'un mot de passe. Lors de la création d'un serveur de base de données pour la base de données, une connexion « Administrateur de serveur » avec nom d’utilisateur et mot de passe doit être spécifiée. À l’aide de ces informations d’identification, un « administrateur de serveur » peut s'authentifier auprès de n’importe quelle base de données sur ce serveur en tant que propriétaire de la base de données. Ensuite, des connexions SQL et utilisateurs supplémentaires peuvent être créés par l'administrateur de serveur, ce qui permet aux utilisateurs de se connecter à l'aide d'un nom d'utilisateur et d'un mot de passe.

- **Authentification Azure Active Directory** :

    L’authentification Azure Active Directory est un mécanisme servant à se connecter aux services Azure [SQL Database](sql-database-technical-overview.md) et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) à l’aide d’identités dans Azure Active Directory (Azure AD). L’authentification Azure AD permet aux administrateurs de gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. Cela permet de réduire le stockage des mots de passe et d'activer des stratégies de rotation centralisée des mots de passe.

     Un administrateur de serveur appelé **administrateur Active Directory** doit être créé pour utiliser l’authentification Azure AD avec SQL Database. Pour plus d’informations, consultez [Connexion à SQL Database avec l’authentification Azure Active Directory](sql-database-aad-authentication.md). L’authentification Azure AD prend en charge les comptes managés et fédérés. Les comptes fédérés prennent en charge les utilisateurs et groupes Windows pour un domaine de client fédéré avec Azure AD.

    Les autres options d’authentification Azure AD disponibles correspondent aux connexions d'[authentification universelle Active Directory pour SQL Server Management Studio](sql-database-ssms-mfa-authentication.md), avec [authentification multifacteur](../active-directory/authentication/concept-mfa-howitworks.md) et [accès conditionnel](sql-database-conditional-access.md).

> [!IMPORTANT]
> La gestion des bases de données et des serveurs dans Azure est contrôlée par les attributions de rôle de votre compte d’utilisateur du portail. Pour en savoir plus à ce sujet, consultez [Contrôle d’accès en fonction du rôle dans le portail Azure](../role-based-access-control/overview.md). Le contrôle d’accès avec des règles de pare-feu ne s’applique *pas* à une **instance gérée**. Consultez l’article suivant sur la [connexion à une instance gérée](sql-database-managed-instance-connect-app.md) pour en savoir plus sur la configuration réseau nécessaire.

## <a name="authorization"></a>Authorization

L'autorisation fait référence aux autorisations accordées à un utilisateur au sein d’une base de données Azure SQL Database, et détermine ce que l’utilisateur est autorisé à faire. Les autorisations sont contrôlées en ajoutant des comptes d’utilisateur vers [rôles de base de données](/sql/relational-databases/security/authentication-access/database-level-roles) et assignation d’autorisations de niveau de base de données à ces rôles ou en lui accordant l’utilisateur certaine [autorisations au niveau de l’objet](/sql/relational-databases/security/permissions-database-engine). Pour plus d’informations, consultez [Connexions et utilisateurs](sql-database-manage-logins.md).

Comme meilleure pratique, créer des rôles personnalisés si nécessaire. Ajouter des utilisateurs au rôle avec les privilèges minimum requis pour effectuer leur fonction. N’affectez pas d’autorisations directement aux utilisateurs. Le compte d’administrateur de serveur est un membre du rôle db_owner intégrés, qui a des autorisations étendues et doit uniquement être accordé aux peu d’utilisateurs avec des droits d’administration. Pour les applications de base de données SQL Azure, utilisez le [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) pour spécifier le contexte d’exécution du module appelé ou utiliser [rôles d’Application](/sql/relational-databases/security/authentication-access/application-roles) avec des autorisations limitées. Cette pratique garantit que l’application qui se connecte à la base de données dispose des privilèges nécessaires à l’application. Suivant ces meilleures pratiques encourage également la séparation des tâches.

### <a name="row-level-security"></a>Sécurité au niveau des lignes

La sécurité au niveau des lignes permet aux clients de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). Sécurité de niveau ligne peuvent également servir à mettre en œuvre des concepts de sécurité basée sur l’étiquette personnalisée. Pour plus d’informations, consultez [Sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Protection contre les menaces

SQL Database sécurise les données client en fournissant des fonctionnalités d’audit et de détection des menaces.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>L’audit SQL dans les journaux Azure Monitor et Event Hubs

L’audit SQL Database suit les activités de base de données et permet d’assurer la conformité avec les normes de sécurité en enregistrant les événements de base de données dans un journal d’audit d’un compte de stockage Azure client. L’audit permet aux utilisateurs de surveiller les activités de base de données en cours, d’analyser et d’examiner l’historique des activités pour identifier les menaces potentielles ou les violations de sécurité et abus présumés. Pour plus d'informations, consultez [Prise en main de l’audit SQL Database](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Protection avancée contre les menaces

Advanced Threat Protection analyse vos journaux de SQL Server pour détecter un comportement inhabituel et les tentatives potentiellement dangereuses visant à accéder ou à exploiter les bases de données. Alertes sont créées pour les activités suspectes, telles que les attaques en force l’injection SQL, infiltration de données potentielle et brute ou des anomalies dans access utilisent des modèles pour intercepter les escalades de privilèges et les informations d’identification de violation. Les alertes sont affichés depuis la [Azure Security Center](https://azure.microsoft.com/services/security-center/), où les détails sur les activités suspectes sont fournis et recommandations pour d’autres recherches menées donnée, ainsi que des actions à atténuer la menace. Advanced Threat Protection peut être activée par serveur pour un coût supplémentaire. Pour plus d’informations, consultez [prise en main SQL de base de données Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Protection et chiffrement des informations

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS (Transport Layer Security - Chiffrement en transit)

SQL Database sécurise les données client en utilisant le protocole [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) pour chiffrer les données en mouvement.

SQL Server applique le chiffrement (SSL/TLS) à tout moment pour toutes les connexions. Cela garantit que toutes les données sont chiffrées « en transit » entre le client et le serveur, quel que soit le paramètre de **Encrypt** ou **TrustServerCertificate** dans la chaîne de connexion.

Comme meilleure pratique, stipulent que, dans la connexion de votre application, chaîne que vous spécifiez une connexion chiffrée et _**pas**_ approuver le certificat de serveur. Cela force le votre application afin de vérifier le certificat de serveur et empêcher ainsi à votre application d’être vulnérable pour les attaques de type intermédiaire.

Par exemple, lorsque vous utilisez le pilote ADO.NET, cela s’effectue par **Encrypt = True** et **TrustServerCertificate = False**. Si vous obtenez votre chaîne de connexion à partir du portail Azure, il aura les paramètres appropriés.

> [!IMPORTANT]
> Notez que certains pilotes non Microsoft ne peuvent pas utiliser TLS par défaut ou s’appuient sur une version antérieure de TLS (< 1.2) pour pouvoir fonctionner. Dans ce cas SQL Server vous permet de se connecter à votre base de données. Toutefois, nous vous recommandons d’évaluer les risques de sécurité de l’autorisation de ces pilotes et l’application se connecte à la base de données SQL, en particulier si vous stockez des données sensibles. 
>
> Pour plus d’informations sur TLS et de connectivité, consultez [considérations relatives à TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (chiffrement des données au repos)

[Transparent Data Encryption (TDE) pour Azure SQL Database](transparent-data-encryption-azure-sql.md) ajoute une couche de sécurité pour protéger les données au repos contre tout accès non autorisé ou hors connexion aux fichiers bruts ou sauvegardes. Parmi les scénarios courants, citons le vol de centre de données, l'élimination non sécurisée de matériel ou de supports tels que les lecteurs de disque et les bandes de sauvegarde. La fonctionnalité TDE chiffre l'intégralité de la base de données à l'aide d'un algorithme de chiffrement AES, sans que les développeurs d'applications aient besoin de modifier les applications existantes.

Dans Azure, toutes les bases de données SQL créées sont chiffrées par défaut, et la clé de chiffrement de ces bases de données est protégée par un certificat de serveur intégré.  La maintenance et la rotation des certificats sont gérées par le service, sans intervention de l'utilisateur. Les clients peuvent également gérer eux-mêmes les clés de chiffrement dans [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gestion des clés dans Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) prenant en charge la fonctionnalité  [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE), les clients peuvent assurer eux-mêmes la gestion et la rotation des clés à l'aide d' [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), le système de gestion des clés externes basé sur le cloud d'Azure. Si l’accès de la base de données au coffre de clés est révoqué, une base de données ne peut pas être déchiffrée et lue en mémoire. Azure Key Vault propose une plateforme de gestion centrale des clés, utilise des modules de sécurité matériels étroitement surveillés (HSM) et permet la séparation des responsabilités entre la gestion de clés et des données pour aider à répondre aux exigences de conformité en matière de sécurité.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (Chiffrement en cours d’utilisation)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) est une fonctionnalité conçue pour protéger les données sensibles stockées dans des colonnes de base de données spécifiques (numéros de cartes de crédit, de carte d'identité ou autres données _sensibles_). Les administrateurs de base de données ou autres utilisateurs disposant de privilèges autorisés peuvent accéder à la base de données afin d'y effectuer des tâches de gestion, sans devoir accéder aux données contenues dans les colonnes chiffrées. Les données sont systématiquement chiffrées, ce qui signifie qu'elles sont uniquement déchiffrées à des fins de traitement par les applications clientes ayant accès à la clé de chiffrement.  La clé de chiffrement n’est jamais exposée dans SQL et peut être stockée dans le [magasin de certificats Windows](sql-database-always-encrypted.md) ou dans [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>Masquage

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Masquage des données dynamiques

Le masquage des données dynamiques de base de données SQL limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage de données dynamiques détecte automatiquement les données potentiellement sensibles dans Azure SQL Database et fournit des recommandations pouvant donner lieu à une action permettant de masquer ces champs, avec un impact minimal sur la couche d’application. Il dissimule les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées. Pour en savoir plus, consultez [Masquage de données dynamiques dans une base de données SQL](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Masquage des données statiques

Le [Masquage des données statiques](/sql/relational-databases/security/static-data-masking) est un outil côté client disponible dans [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18.0 préversion 5 et ou ultérieure.  Le Masquage des données statiques permet aux utilisateurs de créer une copie d’une base de données dans laquelle les données de colonnes sélectionnées ont été définitivement masquées. Les fonctions de masquage disponibles sont notamment : masquage NULL, masquage de valeur unique, masquage aléatoire et aléatoire de groupe et masquage de composite de chaîne. La copie masquée de la base de données permet aux organisations de séparer les environnements de test et de production en partageant la copie masquée. Les données sensibles sont suffisamment protégées et toutes les autres caractéristiques de base de données sont préservées. Le masquage des bases de données est recommandé lorsqu'un accès de tiers aux bases de données est requis.

## <a name="security-management"></a>Gestion de la sécurité

### <a name="vulnerability-assessment"></a>Évaluation des vulnérabilités

[L’évaluation des vulnérabilités ](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et vous aide à corriger des vulnérabilités de base de données potentielles, avec pour objectif d’améliorer de manière proactive la sécurité globale des bases de données. L’évaluation des vulnérabilités (VA) fait partie de l’offre Advanced Data Security (ADS), qui est un package unifié de fonctionnalités de sécurité SQL avancées. L’évaluation des vulnérabilités peut être accessible et gérée via le portail SQL Advanced Data Security central.

### <a name="data-discovery--classification"></a>Découverte et classification des données

Découverte et classification des données (actuellement en préversion) offre des fonctionnalités avancées intégrées à Azure SQL Database pour la découverte, la classification, l’étiquetage et la protection des données sensibles dans vos bases de données. La découverte et la classification de vos données les plus sensibles (professionnelles/financières, soins de santé, données personnelles, etc.) peuvent jouer un rôle essentiel dans la protection des informations de l’organisation. Elles peuvent servir d’infrastructure pour :

- Divers scénarios de sécurité, comme la surveillance (audit) et la génération d’alertes en cas d’accès anormaux aux données sensibles.
- Contrôler l’accès et renforcer la sécurité des bases de données contenant des données sensibles.
- Aider à répondre aux normes de confidentialité des données et aux exigences de conformité aux normes.

Pour plus d’informations, consultez [Bien démarrer avec Découverte et classification des données](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformité

Non seulement Azure SQL Database propose les fonctions ci-dessus et des fonctionnalités permettant à votre application de répondre à différentes exigences en matière de sécurité, mais il participe également à des audits réguliers et est certifié conforme à de nombreuses normes actuelles. Pour plus d’informations, consultez le [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) où vous trouverez la liste actualisée des certifications de conformité de base de données SQL.

## <a name="next-steps"></a>Étapes suivantes

- Pour une description de l’utilisation des fonctionnalités de contrôle d’accès dans SQL Database, consultez [Azure SQL Database access control (Contrôle d’accès d’Azure SQL Database)](sql-database-control-access.md).
- Pour une présentation de l’audit de base de données, consultez [Audit de base de données SQL](sql-database-auditing.md).
- Pour une description de la détection des menaces, consultez [Détection de menaces pour les bases de données SQL](sql-database-threat-detection.md).
