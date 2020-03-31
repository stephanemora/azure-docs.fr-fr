---
title: Vue d’ensemble de la sécurité des bases de données Azure | Microsoft Docs
description: Cet article fournit une vue d’ensemble des fonctionnalités de sécurité des bases de données Azure.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906055"
---
# <a name="azure-database-security-overview"></a>Vue d’ensemble de la sécurité des bases de données Azure

La sécurité est une préoccupation majeure pour la gestion des bases de données, et elle a toujours été une priorité pour Azure SQL Database. Azure SQL Database prend en charge la sécurité de connexion avec des règles de pare-feu et un chiffrement de connexion. Cette solution prend en charge l’authentification avec nom d’utilisateur et mot de passe, ainsi que l’authentification Azure Active Directory (Azure AD) qui utilise des identités gérées par Azure Active Directory. L’autorisation utilise le contrôle d’accès en fonction du rôle.

Azure SQL Database prend en charge le chiffrement en chiffrant et déchiffrant en temps réel les bases de données, les sauvegardes associées et les fichiers journaux des transactions au repos sans que cela nécessite de modifications de l’application.

Microsoft offre des méthodes supplémentaires pour chiffrer des données d’entreprise :

-   Le chiffrement au niveau des cellules permet de chiffrer certaines colonnes, voire des cellules de données ayant différentes clés de chiffrement.
-   S’il vous faut un module de sécurité matériel ou si vous devez centraliser la gestion des clés de chiffrement, consultez l’article de blog relatif à l’utilisation d’Azure Key Vault avec SQL Server dans une machine virtuelle Azure.
-   Always Encrypted (actuellement en préversion) rend le chiffrement transparent aux applications. Il permet également aux clients de chiffrer les données sensibles dans les applications clientes, sans partager les clés de chiffrement avec SQL Database.

L’audit Azure SQL Database permet aux entreprises d’enregistrer les événements dans un journal d’audit dans Stockage Azure. Cette fonction s’intègre également dans Microsoft Power BI, afin de faciliter la création d’analyses et de rapports approfondis.

Les bases de données Azure SQL peuvent être étroitement sécurisées conformément à la plupart des exigences réglementaires ou de sécurité, dont HIPAA, ISO 27001/27002 et PCI DSS Niveau 1. Une liste actualisée des certifications de conformité de sécurité est disponible sur le [site du Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/services/).

Cet article présente les principes fondamentaux de la sécurisation des bases de données Microsoft Azure SQL pour les données structurées, tabulaires et relationnelles. Plus spécifiquement, cet article vous offre un aperçu sur les ressources dédiées à la protection des données, au contrôle d’accès et à la surveillance proactive.

## <a name="protection-of-data"></a>Protection des données

Azure SQL Database contribue à sécuriser vos données par le chiffrement :

- Des données en mouvement via le protocole [TLS (Transport Layer Security)](https://support.microsoft.com/kb/3135244).
- Des données au repos via le [chiffrement transparent des données](https://go.microsoft.com/fwlink/?LinkId=526242).
- Des données en cours d’utilisation via [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Vous pouvez également utiliser les méthodes de chiffrement des données suivantes :

-   [chiffrement au niveau des cellules](https://msdn.microsoft.com/library/ms179331.aspx) permet de chiffrer des colonnes spécifiques, voire des cellules de données, avec des clés de chiffrement différentes.
-   [Azure Key Vault avec SQL Server dans une machine virtuelle Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), s’il vous faut un module de sécurité matériel ou si vous devez centraliser la gestion de votre hiérarchie de clés de chiffrement.

### <a name="encryption-in-motion"></a>Chiffrement en mouvement

Un problème courant pour toutes les applications client/serveur est le besoin de confidentialité lorsque des données se déplacent sur des réseaux publics et privés. Si les données en transit sur un réseau ne sont pas chiffrées, il existe un risque qu’elles soient capturées et volées par des utilisateurs non autorisés. Lorsque vous utilisez des services de base de données, assurez-vous que les données sont chiffrées entre le client de base de données et le serveur. Vérifiez également que les données sont chiffrées entre les serveurs de base de données qui communiquent entre eux et avec les applications intermédiaires.

Lorsque vous administrez un réseau, la sécurisation des données échangées entre les applications via un réseau non approuvé est un problème. Vous pouvez utiliser le protocole [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) pour authentifier les serveurs et les clients, puis pour chiffrer les messages entre les parties authentifiées.

Dans le processus d’authentification, un client TLS/SSL envoie un message à un serveur TLS/SSL. Le serveur répond avec les informations nécessaires pour s’authentifier auprès du serveur. Le client et le serveur effectuent un échange supplémentaire de clés de session, puis la boîte de dialogue d’authentification se ferme. Une fois l’authentification terminée, les communications SSL sécurisées peuvent commencer entre le serveur et le client à l’aide de clés de chiffrement symétriques définies pendant le processus d’authentification.

Toutes les connexions à Azure SQL Database requièrent un chiffrement (par TLS/SSL) à tout moment, lorsque les données sont « en transit » depuis ou vers la base de données. Azure SQL Database utilise TLS/SSL pour authentifier les serveurs et les clients, puis pour chiffrer les messages entre les parties authentifiées. 

Dans la chaîne de connexion de votre application, vous devez spécifier des paramètres pour chiffrer la connexion et non pour approuver le certificat du serveur. (L’opération est automatique si vous copiez votre chaîne de connexion à partir du portail Azure.) Sinon, la connexion ne vérifie pas l’identité du serveur et sera vulnérable aux attaques de type « man in the middle ». Pour le pilote ADO.NET, par exemple, ces paramètres de chaîne de connexion sont `Encrypt=True` et `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Chiffrement au repos

Vous pouvez prendre plusieurs précautions pour sécuriser la base de données. Par exemple, concevoir un système sécurisé, chiffrer les ressources confidentielles et créer un pare-feu autour des serveurs de base de données. Toutefois, en cas de vol des supports physiques (par exemple, les lecteurs ou les bandes de sauvegarde), une personne malveillante ne peut que restaurer ou attacher la base de données et consulter les données.

Une solution consiste à chiffrer les données sensibles dans la base de données et à protéger les clés utilisées pour chiffrer les données avec un certificat. Cette solution empêche toute personne ne possédant pas les clés d’utiliser les données, mais ce type de protection doit être planifié.

Pour résoudre ce problème, SQL Server et SQL Database prennent en charge la fonctionnalité [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Celle-ci chiffre les fichiers de données SQL Server et SQL Database, au cours d’une phase appelée chiffrement des données au repos.

Ce chiffrement transparent des données vous protège contre le risque d’activités malveillantes. Il assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux des transactions au repos, sans que cela nécessite de modifier l’application.  

Le chiffrement transparent des données chiffre le stockage d’une base de données entière, à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Dans SQL Database, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur SQL Database.

Si une base de données est dans une relation de géoredondance, elle est protégée par une clé différente sur chaque serveur. Si deux bases de données sont connectées au même serveur, elles partagent le même certificat intégré. Microsoft fait automatiquement alterner ces certificats au moins tous les 90 jours. 

Pour plus d’informations, consultez [Chiffrement transparent des données (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Chiffrement en cours d’utilisation (client)

La plupart des violations de données impliquent le vol de données critiques telles que des numéros de carte de crédit ou des informations d’identification personnelle. Les bases de données peuvent contenir des trésors d’informations sensibles. Elles peuvent contenir des données personnelles de clients (comme des numéros d’identification nationaux), des informations confidentielles sur la concurrence et des éléments de propriété intellectuelle. Les pertes ou vols de données, en particulier de données client, peuvent entraîner une dégradation de l’image de marque, une perte de compétitivité, des amendes lourdes, voire des actions en justice.

![Fonctionnalité Always Encrypted illustrée sous la forme d’une serrure et d’une clé](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) est une fonctionnalité conçue pour protéger les données sensibles stockées dans les bases de données Azure SQL Database ou SQL Server. Elle permet aux clients de chiffrer les données sensibles dans les applications clientes et de ne jamais divulguer les clés de chiffrement au moteur de base de données (SQL Database ou SQL Server).

Always Encrypted sépare les utilisateurs qui sont propriétaires des données (et peuvent les consulter) des utilisateurs qui gèrent les données (mais n’y ont pas accès). Elle veille à ce que les administrateurs de base de données locales, les opérateurs de base de données cloud ou d’autres utilisateurs disposant de privilèges élevés mais non autorisés, ne puissent pas accéder aux données chiffrées.

Par ailleurs, Always Encrypted rend le chiffrement transparent pour les applications. Un pilote prenant en charge Always Encrypted est installé sur l’ordinateur client, pour chiffrer et déchiffrer automatiquement les données sensibles dans l’application cliente. Le pilote chiffre les données dans les colonnes sensibles avant de les transmettre au moteur de base de données. Le pilote réécrit automatiquement les requêtes afin de préserver la sémantique de l’application. De même, le pilote déchiffre de manière transparente les données stockées dans les colonnes de base de données chiffrées contenues dans les résultats de requête.

## <a name="access-control"></a>Contrôle d’accès

Pour assurer la sécurité, SQL Database contrôle l’accès à l’aide des éléments suivants :

- Règles de pare-feu qui limitent la connectivité par adresse IP.
- Mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité.
- Mécanismes d’autorisation qui limitent les utilisateurs à certaines actions et données.

### <a name="database-access"></a>Accès à la base de données

La protection des données commence avec le contrôle de l’accès à celles-ci. Le centre de données qui héberge vos données gère l’accès physique. Vous pouvez configurer un pare-feu pour gérer la sécurité au niveau de la couche réseau. Vous pouvez également contrôler l’accès en configurant des connexions pour l’authentification, et définir des autorisations pour les rôles serveur et base de données.

#### <a name="firewall-and-firewall-rules"></a>Pare-feu et règles de pare-feu

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande. Pour plus d’informations, consultez [Vue d’ensemble des règles de pare-feu Azure SQL Database](/azure/sql-database/sql-database-firewall-configure).

Le service Azure SQL Database n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu du client autorise les communications TCP sortantes sur le port 1433. Si les connexions entrantes ne sont pas nécessaires aux autres applications, bloquez-les sur le port TCP 1433.

#### <a name="authentication"></a>Authentification

Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. Une base de données SQL prend en charge deux types d’authentification :

-   **Authentification SQL Server** : Un seul compte de connexion est créé lorsqu’une instance SQL logique est créée, appelé compte abonné à la base de données SQL Azure. Ce compte se connecte en utilisant l’[authentification SQL Server](/azure/sql-database/sql-database-security-overview) (nom d’utilisateur et mot de passe). Ce compte est un administrateur sur l’instance de serveur logique et sur toutes les bases de données utilisateur associées à cette instance. Les autorisations du compte abonné ne peuvent pas être restreintes. Un seul de ces comptes peut exister.
-   **Authentification Azure Active Directory** : [L’authentification Azure AD](/azure/sql-database/sql-database-aad-authentication) est un mécanisme de connexion aux services Azure SQL Database et Azure SQL Data Warehouse à l’aide d’identités dans Azure AD. Vous pouvez l’utiliser pour centraliser la gestion des identités des utilisateurs de base de données.

![Authentification Azure AD avec SQL Database](./media/database-security-overview/azure-database-fig2.png)

 Les avantages de l’authentification Azure AD sont les suivants :
  - Elle fournit une alternative à l’authentification SQL Server.
  - Elle contribue à maîtriser la prolifération des identités d’utilisateur sur les serveurs de base de données et permet une rotation centralisée des mots de passe.
  - Vous pouvez gérer les autorisations de base de données à l’aide de groupes (Azure AD) externes.
  - Elle peut éliminer le stockage des mots de passe en activant l’authentification Windows intégrée et les autres formes d’authentification prises en charge par Azure AD.

#### <a name="authorization"></a>Autorisation

L’[autorisation](/azure/sql-database/sql-database-manage-logins) désigne ce que vous pouvez faire dans une base de données Azure SQL. Elle est contrôlée par les [rôles attribués](https://msdn.microsoft.com/library/ms189121) à votre compte d’utilisateur et les [autorisations définies au niveau des objets](https://msdn.microsoft.com/library/ms191291.aspx). L’autorisation est le processus de détermination des ressources sécurisables auxquelles un principal peut accéder, ainsi que des opérations autorisées pour ces ressources.

### <a name="application-access"></a>Accès aux applications

#### <a name="dynamic-data-masking"></a>Masquage dynamique des données

Un membre d’un centre d’appels peut identifier un appelant par quelques chiffres de son numéro de sécurité sociale ou de carte de crédit. Mais ces éléments de données ne doivent pas être entièrement accessibles à l’employé du centre d’appels.

Vous pouvez définir une règle de masquage qui ne révèle que les quatre derniers chiffres du numéro de sécurité sociale ou de la carte de crédit dans les résultats d’une requête.

![Masquage d’un numéro envoyé entre une base de données SQL et des applications métiers](./media/database-security-overview/azure-database-fig3.png)

Vous pouvez également définir un masque de données approprié pour protéger les informations d’identification personnelle. Un développeur peut ensuite interroger les environnements de production à des fins de dépannage, sans enfreindre les réglementations de conformité.

Le [masquage des données dynamiques de SQL Database](/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage des données dynamiques est pris en charge pour la version V12 d’Azure SQL Database.

Le [masquage dynamique des données](/sql/relational-databases/security/dynamic-data-masking) contribue à empêcher tout accès non autorisé aux données sensibles en vous permettant d’indiquer la quantité de données sensibles à révéler avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête sur des champs de base de données désignés (les données dans la base de données ne sont pas modifiées).

> [!Note]
> Le masquage des données dynamiques peut être configuré par l’administrateur de base de données Azure, l’administrateur de serveur ou le responsable de la sécurité.

#### <a name="row-level-security"></a>Sécurité au niveau des lignes

Une autre exigence de sécurité courante pour les bases de données mutualisées est la [Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131.aspx). Vous pouvez utiliser cette fonctionnalité pour contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête. (Par exemple, son groupe d’appartenance et son contexte d’exécution.)

![Sécurité au niveau des lignes permettant à un utilisateur d’accéder aux lignes d’une table via une application cliente](./media/database-security-overview/azure-database-fig4.png)

La logique de la restriction d'accès est située dans la couche de base de données plutôt que loin des données d'une autre couche Application. Le système de base de données applique les restrictions d'accès chaque fois que cet accès aux données est tenté à partir d'une couche quelconque. Cela rend votre système de sécurité plus fiable et robuste en réduisant sa surface d’exposition.

La sécurité au niveau des lignes met en œuvre le contrôle d’accès en fonction d’un prédicat. Elle opère une évaluation flexible et centralisée, qui peut prendre en compte des métadonnées ou tout autre critère que l’administrateur juge approprié. Le prédicat est utilisé en tant que critère pour déterminer si, en fonction des attributs de l’utilisateur, celui-ci dispose ou non de l’accès approprié aux données. Vous pouvez implémenter un contrôle d’accès en fonction d’une étiquette, en utilisant le contrôle d’accès en fonction d’un prédicat.

## <a name="proactive-monitoring"></a>Surveillance proactive

SQL Database sécurise vos données, avec ses fonctionnalités d’*audit* et de *détection des menaces*.

### <a name="auditing"></a>Audit

L’[audit Azure SQL Database](/azure/sql-database/sql-database-auditing-get-started) augmente votre capacité à analyser les événements et modifications qui se produisent au sein de la base de données. Par exemple, les mises à jour et les requêtes sur les données.

L’audit SQL Database suit les événements de base de données et les consigne dans un journal d’audit dans votre compte de stockage Azure. L’audit peut vous aider à vous conformer à la réglementation, à comprendre l’activité de la base de données, et à découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité. L’audit permet et facilite le respect de normes de conformité, mais il ne garantit pas cette conformité.

Vous pouvez utiliser l’audit SQL Database pour :

- **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
- **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d’activités et d’événements.
- **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Il existe deux méthodes d’audit :

-   **Audit d’objets blob** : Les journaux d’activité sont écrits dans le Stockage Blob Azure. Cette méthode d’audit est récente. Elle est plus performante, prend en charge l’audit avec une granularité plus élevée au niveau des objets et est plus économique.
-   **Audit de table** : Les journaux d’activité sont écrits dans le Stockage Table Azure.

### <a name="threat-detection"></a>Détection de menaces

[Advanced Threat Protection pour Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) détecte des activités suspectes indiquant des menaces de sécurité potentielles. Vous pouvez utiliser la détection des menaces pour répondre aux événements suspects se produisant dans la base de données, tels que des injections de code SQL. Cette méthode génère des alertes et permet d’utiliser l’audit Azure SQL Database pour analyser les événements suspects.

![Détection des menaces pour SQL Database et une application web, avec un attaquant externe et un utilisateur interne malveillant](./media/database-security-overview/azure-database-fig5.jpg)

Protection avancée contre les menaces SQL fournit un ensemble de fonctionnalités de sécurité SQL avancées, dont Découverte et classification des données, Évaluation des vulnérabilités et Détection de menaces. 

- [Découverte et classification des données](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Évaluation des vulnérabilités](/azure/sql-database/sql-vulnerability-assessment)  
- [Détection de menaces](/azure/sql-database/sql-database-threat-detection)

[Azure ATP pour Azure Database pour PostgreSQL](/azure/postgresql/concepts-data-access-and-security-threat-protection) fournit une nouvelle couche de sécurité qui vous permet de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales de bases de données. Les utilisateurs reçoivent une alerte en cas d’activités de base de données suspectes, de vulnérabilités potentielles et de modèles d’accès et de requêtes anormaux à la base de données. La protection avancée contre les menaces d’Azure Database pour PostgreSQL intègre des alertes avec Azure Security Center. Les types d’alerte sont les suivants :

- Accès à partir d’un emplacement inhabituel
- Accès à partir d’un centre de données Azure inhabituel 
- Accès à partir d’une entité de sécurité inconnue 
- Accès à partir d’une application potentiellement dangereuse 
- Attaque par force brute d’une base de données Azure pour obtenir les identifiants PostgreSQL 

[Azure ATP pour Azure Database pour MySQL](/azure/mysql/concepts-data-access-and-security-threat-protection) offre une protection similaire à PostgreSQL Advanced Protection.  

## <a name="centralized-security-management"></a>Gestion centralisée de la sécurité

[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) vous aide à vous empêcher, détecter et répondre aux menaces. Il fournit des fonctions intégrées de surveillance de la sécurité et de gestion des stratégies sur vos abonnements Azure. Il permet de détecter les menaces qui pourraient passer inaperçues et fonctionne avec un vaste écosystème de solutions de sécurité.

[Security Center](../../security-center/security-center-alerts-data-services.md) vous aide à protéger les données dans SQL Database en fournissant une visibilité de la sécurité de l’ensemble de vos serveurs et bases de données. Avec Security Center, vous pouvez :

- définir des stratégies pour l’audit et le chiffrement de SQL Database ;
- surveiller la sécurité des ressources SQL Database dans tous vos abonnements ;
- identifier et corriger rapidement les problèmes de sécurité ;
- intégrer les alertes de la [détection de menaces Azure SQL Database](/azure/sql-database/sql-database-threat-detection).

Le Centre de sécurité prend en charge l’accès en fonction du rôle.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) détecte et classe automatiquement les données potentiellement sensibles, fournit un mécanisme permettant d’étiqueter de manière permanente les données sensibles avec des attributs de classification, et elle fournit un tableau de bord détaillé montrant l’état de classification de la base de données.  

En outre, il calcule la sensibilité du jeu de résultats des requêtes SQL, pour permettre un audit explicite des requêtes extrayant des données sensibles, et protéger les données. Pour plus d’informations sur la protection des informations SQL, consultez Découverte et classification des données Azure SQL Database.

Vous pouvez configurer les [stratégies de protection des informations SQL](/azure/security-center/security-center-info-protection-policy) dans Azure Security Center.

## <a name="azure-marketplace"></a>Place de marché Azure

La Place de marché Azure est un marché en ligne d'applications et de services, qui permet aux startups et aux éditeurs de logiciels indépendants de proposer leurs solutions aux clients Azure dans le monde entier.
La Place de marché Microsoft Azure rassemble en une seule plateforme les écosystèmes partenaires de Microsoft Azure afin de mieux répondre aux besoins des clients et partenaires. Vous pouvez [effectuer une recherche](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) pour afficher les produits de sécurité de base de données disponibles dans Place de marché Microsoft Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser votre base de données Azure SQL](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center et service Azure SQL Database](/azure/security-center/security-center-sql-database)
- [Détection de menaces pour les bases de données SQL](/azure/sql-database/sql-database-threat-detection)
- [Améliorer les performances des bases de données SQL](/azure/sql-database/sql-database-performance-tutorial)
