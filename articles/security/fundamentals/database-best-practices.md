---
title: Meilleures pratiques de sécurité des bases de données - Microsoft Azure
description: Cet article fournit un ensemble de meilleures pratiques pour la sécurité des bases de données Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 316c3ef3c5bd16b52291029924d04fc159375bc8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943657"
---
# <a name="azure-database-security-best-practices"></a>Meilleures pratiques en matière de sécurité pour les bases de données Azure
Cet article décrit les meilleures pratiques pour la sécurité des bases de données.

Ces meilleures pratiques font l’objet d’un consensus et sont compatibles avec les capacités et fonctionnalités actuelles de la plateforme Azure. Les opinions et technologies évoluent au fil du temps ; cet article est régulièrement mis à jour de manière à tenir compte de ces changements.

## <a name="secure-databases"></a>Sécuriser les bases de données
La sécurité est une préoccupation majeure pour la gestion des bases de données, et elle a toujours été une priorité pour [Azure SQL Database](../../sql-database/index.yml). Vos bases de données peuvent être étroitement sécurisées pour permettre de répondre aux exigences réglementaires ou de sécurité, notamment HIPAA, ISO 27001/27002 et PCI DSS Level 1. Une liste à jour des certifications de conformité en matière de sécurité est disponible sur le [site du Centre de gestion de la confidentialité Microsoft](https://azure.microsoft.com/support/trust-center/services/). Vous pouvez également choisir de placer vos bases de données dans des centres de données Azure spécifiques, en fonction des exigences réglementaires.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Utiliser des règles de pare-feu pour restreindre l’accès aux bases de données
Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour assurer la sécurité des accès, SQL Database contrôle l’accès à l’aide des éléments suivants :

- Règles de pare-feu qui limitent la connectivité par adresse IP.
- Mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité.
- Mécanismes d’autorisation qui limitent les utilisateurs à certaines actions et données.

Le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.

La figure suivante montre où vous définissez un pare-feu de serveur dans SQL Database :

![Règles de pare-feu](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

Le service Azure SQL Database n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu du client autorise les communications TCP sortantes sur le port 1433. Bloquez les connexions entrantes sur le port TCP 1433 à l’aide de règles de pare-feu, si vous n’avez pas besoin de ces connexions pour d’autres applications.

Dans le cadre du processus de connexion, les connexions à partir de machines virtuelles Azure sont redirigées vers une adresse IP et un port qui sont propres à chaque rôle de travail. Le numéro du port est compris entre 11000 et 11999. Pour plus d’informations sur les ports TCP, consultez [Ports au-delà de 1433 pour ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Pour en savoir plus sur les règles de pare-feu dans la base de données SQL, voir [Règles de pare-feu de la base de données SQL](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Outre les règles d’adresses IP, le pare-feu gère également les règles de réseau virtuel. Les règles de réseau virtuel sont basées sur des points de terminaison de service de réseau virtuel. Les règles de réseau virtuel peuvent être préférables aux règles d’adresses IP dans certains cas. Pour en savoir plus, consultez [Points de terminaison de service de réseau virtuel et règles dans Azure SQL Database](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Activer l’authentification pour les bases de données
SQL Database prend en charge deux types d’authentification : l’authentification SQL Server et l’authentification Azure AD.

### <a name="sql-server-authentication"></a>*Authentification SQL Server*

Les avantages suivants sont inclus :

- Permet à SQL Database de prendre en charge des environnements avec des systèmes d’exploitation mixtes, dans lesquels tous les utilisateurs ne sont pas authentifiés par un domaine Windows.
- Permet à SQL Database de prendre en charge des applications anciennes et des applications fournies par des tiers qui exigent l’authentification SQL Server.
- Permet aux utilisateurs de se connecter à partir de domaines inconnus ou non approuvés. C’est le cas, par exemple, d’une application où les clients établis se connectent via des connexions SQL Server attribuées pour obtenir l’état de leurs commandes.
- Permet à SQL Database de prendre en charge les applications web où les utilisateurs créent leur propre identité.
- Permet aux développeurs de logiciels de distribuer leurs applications à l’aide d’une hiérarchie d’autorisations complexe basée sur des connexions SQL Server connues et prédéfinies.

> [!NOTE]
> L’authentification SQL Server ne peut pas utiliser le protocole de sécurité Kerberos.

Si vous utilisez l’authentification SQL Server, vous devez :

- Gérer les informations d’identification fortes vous-même.
- Protéger les informations d’identification dans la chaîne de connexion.
- (Éventuellement) Protéger les informations d’identification transmises via le réseau à partir du serveur web à la base de données. Pour plus d’informations, consultez [Procédure : Se connecter à SQL Server à l’aide de l’authentification SQL dans ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Authentification Azure Active Directory (AD)*
L’authentification Azure AD est un mécanisme de connexion aux services Azure SQL Database et [SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) à l’aide d’identités dans Azure AD. Avec l’authentification Azure AD, vous pouvez gérer les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs de la base de données et simplifie la gestion des autorisations.

> [!NOTE]
> Nous recommandons d’utiliser l’authentification Azure AD plutôt que l’authentification SQL Server.

Les avantages suivants sont inclus :

- Elle fournit une alternative à l’authentification SQL Server.
- Elle permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
- Elle permet une rotation du mot de passe dans un emplacement unique.
- Les clients peuvent gérer les autorisations de base de données à l’aide des groupes (Azure AD) externes.
- Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
- Elle utilise les utilisateurs de base de données autonome pour authentifier les identités au niveau de la base de données.
- Elle prend en charge l’authentification basée sur les jetons pour les applications qui se connectent à SQL Database.
- Elle prend en charge AD FS (fédération de domaine) ou l’authentification utilisateur natif/mot de passe pour une instance Azure Active Directory local sans synchronisation du domaine.
- Azure AD prend en charge les connexions à partir de SQL Server Management Studio qui utilisent l’authentification universelle Active Directory, laquelle inclut et notamment Multi-Factor Authentication. Multi-Factor Authentication fournit une authentification forte avec diverses options de vérification (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile). Pour plus d’informations, consultez [Prise en charge de SSMS pour Azure AD Multi-Factor Authentication avec SQL Database et SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure AD :

- Créer et renseigner Azure AD.
- Facultatif : Associer ou changer l’instance Active Directory actuellement associée à votre abonnement Azure.
- Créer un administrateur Azure Active Directory pour Azure SQL Database ou [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configurer vos ordinateurs clients.
- Créer des utilisateurs de base de données autonome dans votre base de données mappés sur les identités Azure AD.
- Se connecter à la base de données à l’aide des identités Azure AD.

Vous trouverez des informations détaillées dans [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database, de Managed Instance ou de SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Protéger vos données à l’aide du chiffrement et de la sécurité au niveau des lignes
Le [chiffrement transparent des données Azure SQL Database](../../sql-database/transparent-data-encryption-azure-sql.md) permet de protéger les données sur disque et protège contre tout accès non autorisé au matériel. Il assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux des transactions au repos, sans que cela nécessite de modifier l’application. Le chiffrement transparent des données chiffre le stockage d’une base de données entière, à l’aide d’une clé symétrique appelée clé de chiffrement de base de données.

Même quand l’ensemble du stockage est chiffré, il est important de chiffrer également la base de données. Il s’agit d’une implémentation de l’approche de défense en profondeur visant à protéger les données. Si vous utilisez Azure SQL Database et souhaitez protéger des données sensibles (comme des numéros de carte de crédit ou de sécurité sociale), vous pouvez chiffrer des bases de données avec le chiffrement AES 256 bits, conforme à la norme FIPS 140-2. Ce chiffrement répond aux exigences de nombreuses normes du secteur (HIPAA, PCI, etc.).

Les fichiers liés à [l’extension du pool de mémoires tampons (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) ne sont pas chiffrés quand vous chiffrez une base de données à l’aide du chiffrement transparent des données. Vous devez utiliser des outils de chiffrement au niveau du système de fichiers comme [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) ou le [système de fichiers EFS (Encrypting File System)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) pour les fichiers liés à l’extension du pool de mémoires tampons (BPE).

Étant donné qu’un utilisateur autorisé (un administrateur de sécurité ou un administrateur de base de données, par exemple) peut accéder aux données même si la base de données est chiffrée avec le chiffrement transparent des données (TDE), vous devez également suivre les recommandations suivantes :

- Activez l’authentification SQL Server au niveau de la base de données.
- Utilisez Azure AD Authentication avec des [rôles RBAC](/azure/role-based-access-control/overview).
- Vérifiez que les utilisateurs et les applications utilisent des comptes distincts pour l’authentification. De cette façon, vous pouvez limiter les autorisations accordées aux utilisateurs et aux applications, et réduire les risques d’activité malveillante.
- Implémentez la sécurité de niveau de la base de données en utilisant des rôles de base de données fixes (comme db_datareader ou db_datawriter). Vous pouvez aussi créer des rôles personnalisés pour votre application afin d’accorder des autorisations explicites à des objets de base de données sélectionnés.

Vous pouvez également utiliser les méthodes de sécurisation des données suivantes :

- [chiffrement au niveau des cellules](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) permet de chiffrer des colonnes spécifiques, voire des cellules de données, avec des clés de chiffrement différentes.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), qui permet aux clients de chiffrer les données sensibles dans les applications clientes et de ne jamais divulguer les clés de chiffrement au moteur de base de données (SQL Database ou SQL Server). Always Encrypted sépare ainsi les utilisateurs qui sont propriétaires des données (et peuvent les consulter) des utilisateurs qui gèrent les données (mais n’y ont pas accès).
- [Sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security), qui permet aux clients de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête. (Par exemple, son groupe d’appartenance et son contexte d’exécution.)

Les organisations qui n’utilisent pas le chiffrement au niveau de la base de données peuvent être plus vulnérables à des attaques qui compromettent les données se trouvant dans des bases de données SQL.

Vous pouvez en savoir plus sur le chiffrement transparent des données SQL Database en lisant l’article [Transparent Data Encryption avec Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Activer l’audit pour les bases de données
L’audit d’une instance du moteur de base de données SQL Server ou d’une base de données individuelle implique le suivi et la journalisation des événements. Pour SQL Server, vous pouvez créer des audits qui contiennent des spécifications pour les événements au niveau du serveur et des spécifications pour les événements au niveau de la base de données. Les événements audités peuvent être écrits dans les journaux d’événements ou les fichiers d’audit.

Il existe plusieurs niveaux d’audit pour SQL Server, en fonction des exigences normatives ou gouvernementales de votre installation. L’audit SQL Server fournit les outils et processus nécessaires pour activer, stocker et afficher les audits sur différents objets de serveur et de base de données.

[L’audit Azure SQL Database](/azure/sql-database/sql-database-auditing) suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure.

L’audit peut vous aider à rester en conformité avec les réglementations, à comprendre l’activité des bases de données et à détecter les écarts et les anomalies susceptibles d’indiquer des problèmes dans l’activité de l’entreprise ou des violations de la sécurité. L’audit facilite le respect de normes de conformité, mais il ne garantit pas cette conformité.

Pour en savoir plus sur l’audit de base de données et la façon de l’activer, consultez [Bien démarrer avec l’audit de bases de données SQL](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Activer la détection des menaces pour les bases de données
La protection contre les menaces va au-delà de la détection. La protection contre les menaces sur les bases de données inclut les opérations suivantes :

- Découverte et classification de vos données les plus sensibles afin que vous puissiez protéger vos données.
- Implémentation de configurations sécurisées sur votre base de données afin de pouvoir la protéger.
- Détection et traitement des menaces potentielles quand elles se produisent, afin que vous puissiez rapidement les traiter et les corriger.

**Bonne pratique** : Découvrir, classer et étiqueter les données sensibles dans vos bases de données.   
**Détail** : Classez les données dans votre base de données SQL en activant le service [Découverte et classification des données](/azure/sql-database/sql-database-data-discovery-and-classification) dans Azure SQL Database. Vous pouvez superviser l’accès à vos données sensibles dans le tableau de bord Azure ou télécharger des rapports.

**Bonne pratique** : Suivre les vulnérabilités de base de données afin de pouvoir améliorer la sécurité de votre base de données de façon proactive.   
**Détail** : Utilisez le service [Évaluation des vulnérabilités](/azure/sql-database/sql-vulnerability-assessment) d’Azure SQL Database, qui analyse les vulnérabilités de base de données potentielles. Le service s’appuie sur une base de connaissances de règles qui signale les vulnérabilités de sécurité et indique les écarts par rapport aux bonnes pratiques, notamment les erreurs de configuration, les autorisations excessives et les données sensibles non protégées.

Les règles sont basées sur les bonnes pratiques Microsoft et se concentrent sur les problèmes de sécurité qui présentent des risques majeurs pour votre base de données et ses données importantes. Elles couvrent les problèmes au niveau de la base de données et les problèmes de sécurité au niveau du serveur, comme les paramètres de pare-feu de serveur et les autorisations au niveau du serveur. Ces règles représentent également les nombreuses exigences à respecter, provenant d’organismes de réglementation.

**Bonne pratique** : Activer la détection des menaces.  
**Détail** :  Activez le service [Détection des menaces](/azure/sql-database/sql-database-threat-detection) d’Azure SQL Database pour obtenir des alertes de sécurité, ainsi que des suggestions pour examiner et corriger les menaces. Vous obtenez des alertes sur les activités de base de données suspectes, les vulnérabilités potentielles et les attaques par injection de code SQL, ainsi que des modèles de requêtes et d’accès anormaux à la base de données.

La [Protection avancée contre les menaces](/azure/sql-database/sql-advanced-threat-protection) est un package unifié de fonctionnalités de sécurité SQL avancées. Il inclut les services mentionnés précédemment : Détection des menaces, Découverte et classification des données et Évaluation des vulnérabilités. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement.

L’activation de ces fonctionnalités vous permet d’effectuer les opérations suivantes :

- Répondre aux normes de confidentialité des données et aux exigences de conformité réglementaire.
- Contrôler l’accès à vos bases de données et renforcer leur sécurité.
- Superviser un environnement de base de données dynamique où les changements sont difficiles à suivre.
- Détecter et traiter les menaces potentielles.

De plus, le service Détection des menaces intègre les alertes avec Azure Security Center pour une vue centralisée de l’état de sécurité de toutes vos ressources Azure.

## <a name="next-steps"></a>Étapes suivantes
Consultez l’article [Bonnes pratiques et tendances Azure relatives à la sécurité](best-practices-and-patterns.md) pour découvrir d’autres bonnes pratiques en matière de sécurité à appliquer dans le cadre de la conception, du déploiement et de la gestion de vos solutions cloud avec Azure.

Les ressources suivantes fournissent des informations générales sur la sécurité Azure et les services Microsoft associés :
* [Blog de l’équipe de sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : pour obtenir des informations à jour sur les dernières actualités sur la sécurité Azure
* [Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx) : emplacement où les vulnérabilités de sécurité Microsoft, dont les problèmes rencontrés avec Azure, peuvent être rapportées ou signalées par e-mail à l’adresse secure@microsoft.com
