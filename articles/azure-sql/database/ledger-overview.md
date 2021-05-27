---
title: Vue d’ensemble du registre Azure SQL Database
description: Vue d’ensemble du registre Azure SQL Database
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 36d7c5d6b97b54392be8c85d1b6a6088eda5f983
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386307"
---
# <a name="azure-sql-database-ledger"></a>Registre Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en **préversion publique**.

L’instauration d’une confiance dans l’intégrité des données stockées dans des systèmes de base de données est depuis longtemps un problème pour toutes les organisations qui gèrent des données financières, médicales ou autres données sensibles. La fonctionnalité de registre d’[Azure SQL Database](sql-database-paas-overview.md) fournit des fonctionnalités de preuve de fraude dans votre base de données, qui permettent d’attester par chiffrement, auprès d’autres parties, telles que des auditeurs ou des partenaires commerciaux, que vos données n’ont pas été falsifiées.

Le registre vous aide à protéger les données contre les utilisateurs malveillants ou dotés de privilèges élevés, tels que des administrateurs de base de données ou des administrateurs système et cloud. Tout comme dans un registre traditionnel, les données historiques sont conservées de telle sorte que, si une ligne est mise à jour dans la base de données, sa valeur précédente est conservée et protégée dans une table d’historique. Le registre tient une chronique de toutes les modifications apportées à la base de données au fil du temps. Le registre et les données historiques sont gérés de manière transparente, offrant ainsi une protection sans aucune modification de l’application. Les données historiques sont conservées sous forme relationnelle pour permettre la prise en charge de requêtes SQL à des fins d’audit, d’investigation et autres. Le registre fournit des garanties d’intégrité des données chiffrées tout en maintenant la puissance, la flexibilité et les performances d’Azure SQL Database.

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="architecture de table de registre":::

## <a name="use-case-for-azure-sql-database-ledger"></a>Cas d’usage du registre Azure SQL Database 

### <a name="streamlining-audits"></a>Simplification des audits

Toute valeur du système de production est basée sur la possibilité d’approuver les données que le système consomme et génère. Si les données de votre base de données ont été falsifiées par un utilisateur malveillant, les processus d’entreprise qui dépendent de ces données risquent de produire des résultats désastreux. Le maintien de la confiance dans vos données requiert une combinaison de contrôles de sécurité appropriés pour réduire les attaques potentielles, de pratiques de sauvegarde et de restauration, ainsi que de procédures de récupération d’urgence rigoureuses. La mise en place effective de ces pratiques fait souvent l’objet d’audits par des tiers externes. Les processus d’audit sont des activités extrêmement laborieuses. Un audit nécessite l’inspection sur site des pratiques mises en œuvre. Elle inclut notamment l’examen des journaux d’audit, ainsi que l’inspection des contrôles d’authentification et d’accès. Si ces processus manuels permettent de révéler certaines lacunes de sécurité, ils ne permettent pas d’apporter de preuve irréfutable que les données n’ont pas été indûment modifiées. Le registre apporte une preuve chiffrée de l’intégrité des données aux auditeurs. Cela permet, non seulement, de simplifier le processus d’audit, mais aussi, de garantir l’intégrité des données du système.

### <a name="multi-party-business-processes"></a>Processus métier multipartites

Les systèmes dans lesquels plusieurs organisations ont un processus métier dont l’état doit être partagé entre elles, par exemple, des systèmes de gestion de la chaîne logistique, rencontrent des difficultés en lien avec le partage et l’approbation réciproques des données. De nombreuses organisations se tournent vers des blockchains traditionnelles, telles Ethereum ou Hyperledger Fabric, pour transformer numériquement leurs processus métier multipartites. Une blockchain est une solution idéale pour des réseaux multipartites où la confiance entre les parties prenantes est faible. Pourtant, bon nombre de ces réseaux sont fondamentalement des solutions centralisées où la confiance est importante, mais où une infrastructure entièrement décentralisée constitue une solution lourde. Pour ces réseaux, la solution peut venir d’un registre dans lequel les participants peuvent vérifier l’intégrité des données, au lieu de la complexité et des implications en termes de performances associées à l’obtention d’un consensus dans un réseau basé sur une blockchain.

### <a name="trusted-off-chain-storage-for-blockchain"></a>Stockage off-chain de confiance pour blockchain

Quand un réseau basé sur une blockchain est nécessaire pour un processus métier multipartite, la possibilité d’interroger les données sur la blockchain sans sacrifier les performances est un véritable défi. Les modèles classiques pour résoudre ce problème impliquent une réplication des données de la blockchain dans un magasin hors chaîne (off-chain), tel qu’une base de données. Cependant, une fois les données répliquées dans la base de données à partir de la blockchain, la garantie d’intégrité des données qu’offre la blockchain est perdue. Un registre apporte l’intégrité des données nécessaire pour le stockage off-chain de réseaux basés sur une blockchain, garantissant ainsi une confiance totale dans les données de l’ensemble du système.

## <a name="how-it-works"></a>Fonctionnement

Chaque transaction que la base de données reçoit est hachée par chiffrement (SHA-256). La fonction de hachage utilise en entrée la valeur de la transaction (y compris les hachages des lignes contenues dans celle-ci), ainsi que le hachage de la transaction précédente. La fonction lie toutes les transactions par chiffrement, comme le ferait une blockchain. Les hachages chiffrés ([synthèses de base de données](#database-digests)), qui représentent l’état de la base de données, sont générés et stockés périodiquement en dehors d’Azure SQL Database, dans un emplacement de stockage inviolable. Un tel emplacement de stockage pourrait être, par exemple, des [blobs immuables du service Stockage Azure](../../storage/blobs/storage-blob-immutable-storage.md) ou un [Registre confidentiel Azure](/azure/confidential-ledger/). Les synthèses de base de données sont utilisées ultérieurement pour vérifier l’intégrité de la base de données en comparant la valeur du hachage dans les synthèses aux hachages calculés dans la base de données. 

La fonctionnalité de registre est introduite dans les tables d’Azure SQL Database sous deux formes :

- [**Tables de registre pouvant être mises à jour**](ledger-updatable-ledger-tables.md), qui vous permettent de mettre à jour et de supprimer des lignes dans vos tables.
- [**Tables de registre pour ajout uniquement**](ledger-append-only-ledger-tables.md), qui n’autorisent que des insertions dans vos tables.

Tant les **tables de registre pouvant être mises à jour** que les **tables de registre pour ajout uniquement** fournissent des fonctionnalités de preuve de fraude et d’investigation numérique. Il est important de comprendre quelles transactions soumises par quels utilisateurs ont entraîné des modifications dans la base de données, au cas où il s’avérerait nécessaire de corriger d’éventuelles falsifications, ou de prouver à des tiers que les transactions soumises au système l’ont été par des utilisateurs autorisés. La fonctionnalité de registre permet aux utilisateurs, à leurs partenaires ou aux auditeurs d’analyser toutes les opérations historiques, ainsi que de détecter des falsifications potentielles. Chaque opération de ligne est accompagnée de l’ID de la transaction correspondante, ce qui permet aux utilisateurs de récupérer plus d’informations sur le moment où la transaction a été effectuée et l’identité de son auteur, ainsi que de la mettre en corrélation avec d’autres opérations effectuées par cette transaction.

Les tables de registre présentent certaines limitations dont il faut avoir conscience. Pour plus d’informations sur les limitations des tables de registre, consultez [Limitations relatives au registre Azure SQL Database](ledger-limits.md).

### <a name="ledger-database"></a>Base de données de registre

Une base de données de registre est une base de données dans laquelle toutes les données utilisateur sont infalsifiables et stockées dans des tables de registre. Une base de données de registre ne peut contenir que des tables de registre créées par défaut en tant que tables de registre pouvant être mises à jour. Les bases de données de registre fournissent une solution facile à utiliser pour les applications qui exigent que l’intégrité de toutes les données soit protégée. 

### <a name="updatable-ledger-tables"></a>Tables de registre pouvant être mises à jour

Des [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md) sont idéales pour des modèles d’application qui doivent opérer des mises à jour et des suppressions dans des tables de votre base de données, telles que des applications de système d’enregistrement. Cela signifie que les modèles de données existants pour votre application n’ont pas besoin d’être modifiés pour activer la fonctionnalité de registre.  

Des [tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md) suivent l’historique des modifications apportées à toutes les lignes de votre base de données lorsque des transactions entraînent des mises à jour ou des suppressions. Une table de registre pouvant être mise à jour est une table faisant l’objet d’un suivi des versions par le système, qui contient une référence à une autre table présentant un schéma en miroir. Le système utilise cette table pour stocker automatiquement la version précédente d’une ligne chaque fois qu’une ligne de la table de registre est mise à jour ou supprimée. Cette autre table est appelée table d’historique. La table d’historique est créée automatiquement lorsque vous créez une table de registre pouvant être mise à jour. Les valeurs contenues dans la table de registre pouvant être mise à jour et sa table d’historique correspondante fournissent une chronique des valeurs de votre base de données au fil du temps. Pour faciliter l’interrogation de cette chronique de votre base de données, un affichage de registre généré par le système est créé, qui joint la table de registre pouvant être mise à jour et la table d’historique.

Pour plus d’informations sur la création et l’utilisation de tables de registre pouvant être mises à jour, consultez [Créer des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md).

### <a name="append-only-ledger-tables"></a>Tables de registre pour ajout uniquement

Des [tables de registre pour ajout uniquement](ledger-append-only-ledger-tables.md) sont idéales pour des modèles d’application qui n’opèrent que par insertions, telles des applications de gestion des informations et événements de sécurité (SIEM). Des tables de registre pour ajout uniquement bloquent les mises à jour et les suppressions au niveau de l’API, offrant ainsi une protection supplémentaire contre les falsifications par des utilisateurs privilégiés, tels que des administrateurs système et des administrateurs de bases de données. Étant donné que seules des insertions sont autorisées dans le système, les tables de registre pour ajout uniquement n’ont pas de tables d’historique correspondantes, car il n’y a pas d’historique à capturer. À l’instar des tables de registre pouvant être mises à jour, un affichage de registre est créé pour les tables de registre pour ajout uniquement, qui fournit des insights sur les transactions ayant inséré des lignes et les utilisateurs à l’origine des insertions.

Pour plus d’informations sur la création et l’utilisation de tables de registre pour ajout uniquement, consultez [Créer des tables de registre pour ajout uniquement](ledger-how-to-append-only-ledger-tables.md).

### <a name="database-ledger"></a>Registre de base de données

Le registre de base de données est constitué de tables système qui stockent les hachages chiffrés des transactions traitées dans le système. Étant donné que la transaction est l’unité d’[atomicité](/windows/win32/cossdk/acid-properties) pour le moteur de base de données, il s’agit de l’unité de travail capturée dans le registre de base de données. Plus précisément, quand une transaction est validée, le hachage SHA-256 de toutes les lignes modifiées par la transaction dans la table de registre, ainsi que certaines métadonnées de la transaction, telles que l’identité de son auteur et l’horodatage de sa validation, sont ajoutés en tant qu’*entrée de transaction* dans le registre de base de données. Toutes les 30 secondes, les transactions traitées par la base de données font l’objet d’un hachage SHA-256 à l’aide d’une structure de données arborescente Merkle, qui génère un hachage racine. Cela produit un bloc qui est ensuite soumis à un hachage SHA-256 utilisant le hachage racine du bloc, ainsi que le hachage racine du bloc précédent en entrée, formant ainsi une blockchain.

Pour plus d’informations sur le registre de base de données, consultez [Registre de base de données](ledger-database-ledger.md).

### <a name="database-digests"></a>Synthèses de base de données

Le hachage du dernier bloc dans le registre de base de données est appelé synthèse de base de données, et représente l’état de toutes les tables de registre dans la base de données au moment où le bloc a été généré. Quand un bloc est formé, sa synthèse de base de données associée est publiée et stockée en dehors d’Azure SQL Database dans un stockage inviolable. Étant donné que les synthèses de base de données représentent l’état de la base de données au moment où elles ont été générées, leur protection contre la falsification est primordiale. Une personne malveillante disposant d’un accès lui permettant de modifier les synthèses pourrait falsifier les données de la base de données, générer les hachages représentant celle-ci avec les falsifications, puis modifier les synthèses afin de représenter le hachage mis à jour des transactions dans le bloc. Un registre offre la possibilité de générer et stocker automatiquement les synthèses de base de données dans des [blobs immuables du service Stockage Azure](../../storage/blobs/storage-blob-immutable-storage.md) ou dans un [Registre confidentiel Azure](/azure/confidential-ledger/) pour empêcher la falsification. Les utilisateurs peuvent également générer manuellement des synthèses de base de données et les stocker à l’emplacement de leur choix. Les synthèses de base de données sont utilisées ultérieurement pour vérifier que les données stockées dans les tables de registre n’ont pas été falsifiées.

Pour plus d’informations sur les synthèses de base de données, consultez [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md).

### <a name="ledger-verification"></a>Vérification du registre

La fonctionnalité de registre ne permet pas aux utilisateurs de modifier le contenu du registre. Toutefois, un attaquant ou un administrateur système contrôlant l’ordinateur peut contourner tous les contrôles du système et falsifier directement les données. Par exemple, un attaquant ou un administrateur système peut modifier les fichiers de base de données dans le stockage. Un registre ne peut pas empêcher de telles attaques, mais garantit que toute falsification sera détectée lors de la vérification des données qu’il contient. Le processus de vérification du registre prend en entrée une ou plusieurs synthèses de base de données précédemment générées, et recalcule les hachages stockés dans le registre de base de données sur la base de l’état actuel des tables de registre. Si les hachages calculés ne correspondent pas aux synthèses en entrée, la vérification échoue, indiquant que les données ont été falsifiées, et signale toutes les incohérences détectées.

Étant donné que la vérification du registre recalcule tous les hachages des transactions dans la base de données, il peut s’agir d’un processus gourmand en ressources pour des bases de données volumineuses. L’exécution de la vérification du registre ne doit être effectuée que quand des utilisateurs ont besoin de vérifier l’intégrité de la base de données plutôt que de manière continue. Dans l’idéal, une vérification du registre ne doit être exécutée que quand l’organisation hébergeant les données fait l’objet d’un audit et doit fournir à une autre partie des preuves par chiffrement de l’intégrité de ses données. Pour réduire le coût de la vérification, le registre permet de vérifier chaque table de registre individuellement ou seulement un sous-ensemble du registre.

Pour plus d’informations sur la vérification du registre, consultez [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md).

## <a name="next-steps"></a>Étapes suivantes
 
- [Démarrage rapide : créer une base de données Azure SQL Database avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md)
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)   
- [Tables de registre pour ajout uniquement](ledger-append-only-ledger-tables.md)   
- [Registre de base de données](ledger-database-ledger.md)   
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)   
- [Limitations relatives au registre Azure SQL Database](ledger-limits.md)
- [Créer et utiliser des tables de registre pouvant être mises à jour](ledger-how-to-updatable-ledger-tables.md)
- [Créer et utiliser des tables de registre d’ajout uniquement](ledger-how-to-append-only-ledger-tables.md)
- [Comment accéder aux synthèses stockées dans le Registre confidentiel Azure (ACL)](ledger-how-to-access-acl-digest.md)
- [Comment vérifier une table du registre pour détecter la falsification](ledger-verify-database.md)
