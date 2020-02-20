---
title: Redondance des données
titleSuffix: Azure Storage
description: Les données de votre compte Stockage Microsoft Azure sont répliquées à des fins de durabilité et de haute disponibilité. Les configurations de redondance incluent le stockage localement redondant (LRS), le stockage redondant interzone (ZRS), le stockage géoredondant (GRS), le stockage géoredondant avec accès en lecture (RA-GRS), le stockage géoredondant interzone (GZRS) (préversion) et le stockage géoredondant interzone avec accès en lecture (RA-GZRS) (préversion).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 038e53251bd81552fd3379f2d7645570fbcda4ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471344"
---
# <a name="azure-storage-redundancy"></a>Redondance de Stockage Azure

Le service Stockage Azure stocke toujours plusieurs copies de vos données afin qu’elles soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant et des catastrophes naturelles majeures. La redondance garantit que votre compte de stockage répond aux exigences du [Contrat de niveau de service (SLA) pour Stockage Azure](https://azure.microsoft.com/support/legal/sla/storage/), même en cas de panne.

Lorsque vous choisissez l’option de redondance la mieux adaptée à votre scénario, réfléchissez aux compromis possibles entre, d’une part, des coûts réduits et, de l’autre, une disponibilité et une durabilité accrues. Les facteurs déterminant le choix de l’option de redondance sont les suivants :  

- Mode de réplication de vos données dans la région primaire
- Réplication éventuelle de vos données vers un deuxième emplacement géographiquement éloigné de la région primaire, afin d’offrir une protection contre des catastrophes régionales
- Nécessité ou non pour l’application d’avoir accès en lecture aux données répliquées dans la région secondaire si la région primaire devient indisponible pour une raison quelconque

## <a name="redundancy-in-the-primary-region"></a>Redondance dans la région primaire

Les données d’un compte de stockage Azure sont toujours répliquées trois fois dans la région primaire. Le service Stockage Azure offre deux options pour la réplication de vos données dans la région primaire :

- La réplication par **stockage localement redondant (LRS)** copie vos données de façon synchrone trois fois au sein d’un même emplacement physique dans la région primaire. L’option LRS est la moins coûteuse mais n’est pas recommandée pour des applications nécessitant une haute disponibilité.
- La réplication par **stockage redondant interzone (ZRS)** copie vos données de façon synchrone dans trois zones de disponibilité Azure au sein de la région primaire. Pour les applications nécessitant une haute disponibilité, Microsoft recommande l’utilisation de l’option ZRS dans la région primaire, ainsi que la réplication vers une région secondaire.

### <a name="locally-redundant-storage"></a>Stockage localement redondant

Le stockage localement redondant (LRS) réplique vos données trois fois au sein d’un même emplacement physique dans la région primaire. Le stockage localement redondant offre une durabilité des objets d’au moins 99,999999999 % (11 « neuf ») sur une année donnée.

Le stockage localement redondant est l’option de redondance la moins coûteuse et offrant la durabilité la plus faible en comparaison des autres options. Il protège vos données contre les défaillances de disque et de rack du serveur. Toutefois, si un sinistre tel qu’un incendie ou une inondation se produit à l’intérieur du centre de données, tous les réplicas d’un compte de stockage utilisant un stockage localement redondant risquent d’être perdus ou irrécupérables. Pour atténuer ce risque, Microsoft recommande d’utiliser un [stockage redondant interzone (ZRS)](#zone-redundant-storage), un [stockage géoredondant (GRS)](#geo-redundant-storage) ou un [stockage géoredondant interzone (GZRS, en préversion)](#geo-zone-redundant-storage-preview).

Une demande d’écriture dans un compte de stockage utilisant une réplication LRS se produit de façon synchrone. L’opération d’écriture ne retourne un indicateur de réussite que lorsque les données ont été écrites sur les trois réplicas.

L’option LRS est un bon choix pour les scénarios suivants :

- En revanche, si votre application stocke des données qui peuvent être recréées facilement en cas de perte, vous pouvez opter pour un stockage LRS.
- Si votre application est limitée à la réplication de données uniquement à l’intérieur d’un pays ou d’une région en raison d’exigences de gouvernance des données, vous pouvez opter pour un stockage localement redondant. Dans certains cas, les régions appairées dans lesquelles les données sont géorépliquées peuvent se trouver dans un autre pays ou une autre région. Pour plus d’informations sur les régions associées, consultez la page [Régions Azure](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Stockage redondant interzone

Un stockage redondant interzone (ZRS) réplique vos données de Stockage Azure de façon synchrone dans trois zones de disponibilité Azure au sein de la région primaire. Chaque zone de disponibilité est un emplacement physique distinct avec une alimentation, un refroidissement et une mise en réseau indépendants. Une réplication ZRS offre une durabilité des objets de données de Stockage Azure d’au moins 99,9999999999 % (12 neuf) sur une année donnée.

Avec l’option ZRS, vos données restent accessibles pour des opérations de lecture et d’écriture, même si une zone devient indisponible. Si une zone devient indisponible, Azure apporte des mises à jour à la mise en réseau, telles qu’un re-pointage DNS. Ces mises à jour peuvent affecter votre application si vous accédez aux données avant qu’elles soient terminées. Lors de la conception d’applications pour un stockage redondant interzone, suivez les pratiques de gestion des erreurs temporaires, dont l’implémentation de stratégies de nouvelle tentative avec backoff exponentiel.

Une demande d’écriture dans un compte de stockage utilisant une réplication ZRS se produit de façon synchrone. L’opération d’écriture ne retourne un indicateur de réussite que lorsque les données ont été écrites sur tous les réplicas dans les trois zones de disponibilité.

Microsoft recommande d’utiliser un stockage redondant interzone dans la région primaire pour les scénarios nécessitant cohérence, durabilité et haute disponibilité. Une réplication ZRS offre d’excellentes performances, une faible latence et une résilience pour vos données en cas d’indisponibilité temporaire de celles-ci. En revanche, il se peut que cette option à elle seule ne puisse pas protéger vos données contre un sinistre régional dans le cadre duquel plusieurs zones sont affectées définitivement. Pour assurer la protection contre des catastrophes régionales, Microsoft recommande l’utilisation d’un [stockage géo-redondant interzone](#geo-zone-redundant-storage-preview) (GZRS), qui utilise une réplication ZRS dans la région primaire et géoréplique vos données dans une région secondaire.

Le tableau suivant répertorie les types de comptes de stockage qui prennent en charge le stockage redondant interzone ZRS dans différentes régions :

|    Type de compte de stockage    |    Régions prises en charge    |    Services pris en charge    |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
|    Universel v2<sup>1</sup>    | Asie du Sud-Est<br /> Europe septentrionale<br />  Europe occidentale<br /> France Centre<br /> Japon Est<br /> Sud du Royaume-Uni<br /> USA Centre<br /> USA Est<br /> USA Est 2<br /> USA Ouest 2    |    Objets blob de blocs<br /> Objets blob de pages<sup>2</sup><br /> Partages de fichiers (standard)<br /> Tables<br /> Files d’attente<br /> |
|    BlockBlobStorage<sup>1</sup>    | Europe occidentale<br /> USA Est    |    Objets blob de blocs uniquement    |
|    FileStorage    | Europe occidentale<br /> USA Est    |    Azure Files uniquement    |

<sup>1</sup> Le niveau archive n’est pas actuellement pris en charge sur les comptes ZRS.<br />
<sup>2</sup> Les disques Azure pour machines virtuelles, tant gérés que non gérés, ne prennent en charge que la réplication LRS. Ils ne prennent pas en charge les réplication ZRS ou GZRS. Pour plus d’informations sur les disques managés, voir [Tarification des disques managés Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

Pour plus d’informations sur les régions qui prennent en charge la réplication ZRS, voir **Prise en charge des services par région** dans [Que sont les zones de disponibilité Azure ?](../../availability-zones/az-overview.md).

## <a name="redundancy-in-a-secondary-region"></a>Redondance dans une région secondaire

Pour les applications nécessitant une haute disponibilité, vous pouvez choisir de copier en plus les données de votre compte de stockage vers une région secondaire située à des centaines de kilomètres de la région primaire. Si votre compte de stockage est copié vers une région secondaire, vos données restent durables, même en cas de panne ou de catastrophe régionales totales empêchant la récupération de la région primaire.

Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire couplée est déterminée en fonction de la région primaire et n’est pas modifiable. Pour plus d’informations sur les régions prises en charge par Azure, voir [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).

Le service Stockage Azure offre deux options pour la copie de vos données vers une région secondaire :

- La réplication par **stockage géoredondant (GRS)** copie vos données de façon synchrone trois fois au sein d’un même emplacement physique dans la région primaire en utilisant une réplication LRS. Elle copie ensuite vos données de façon asynchrone vers un emplacement physique unique dans la région secondaire.
- La réplication par **stockage géoredondant interzone (GZRS)** copie vos données de façon synchrone dans trois zones de disponibilité Azure au sein de la région primaire en utilisant une réplication ZRS. Elle copie ensuite vos données de façon asynchrone vers un emplacement physique unique dans la région secondaire.

La principale différence entre les réplications GRS et GZRS réside dans la manière dont les données sont répliquées dans la région primaire. Dans l’emplacement secondaire, les données sont toujours répliquées de manière synchrone trois fois en utilisant une réplication LRS.

Avec les réplications GRS ou GZRS, les données de l’emplacement secondaire ne sont disponibles pour l’accès en lecture ou en écriture qu’en cas de basculement vers la région secondaire. Pour un accès en lecture à l’emplacement secondaire, configurez votre compte de stockage pour utiliser un stockage géoredondant avec accès en lecture (RA-GRS) ou un stockage géoredondant interzone avec accès en lecture (RA-GZRS). Pour plus d’informations, voir [Accès en lecture aux données dans la région secondaire](#read-access-to-data-in-the-secondary-region).

Si la région primaire devient indisponible, vous pouvez choisir de basculer vers la région secondaire (préversion). Une fois le basculement terminé, la région secondaire devient la région primaire et vous pouvez de nouveau lire et écrire des données. Pour plus d’informations sur la récupération d’urgence et pour savoir comment basculer vers la région secondaire, voir [Récupération d’urgence et basculement de compte (préversion)](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Étant donné que les données sont répliquées de façon asynchrone dans la région secondaire, une défaillance qui affecte la région principale peut entraîner une perte de données si la région principale ne peut pas être récupérée. L’intervalle entre les écritures les plus récentes dans la région principale et la dernière écriture dans la région secondaire est appelé objectif de point de récupération (RPO). Le RPO indique le moment où les données peuvent être récupérées. Stockage Azure comporte généralement un RPO inférieur à 15 minutes, même s’il n’existe actuellement aucun contrat de niveau de service sur la durée de réplication des données sur la région secondaire.

### <a name="geo-redundant-storage"></a>Stockage géo-redondant

La réplication par stockage géoredondant (GRS) copie vos données de façon synchrone trois fois au sein d’un même emplacement physique dans la région primaire en utilisant une réplication LRS. Elle copie ensuite vos données de façon asynchrone vers un emplacement physique unique dans une région secondaire qui est éloignée de plusieurs centaines de kilomètres de la région primaire. Une réplication GRS offre une durabilité des objets de données de Stockage Azure d’au moins 99,99999999999999 % (16 neuf) sur une année donnée.

Une opération d’écriture est d’abord validée dans la région primaire, puis répliquée en utilisant un stockage localement redondant. Elle est ensuite répliquée de manière asynchrone vers la région secondaire. Lors de l’écriture des données dans l’emplacement secondaire, celles-ci sont également répliquées au sein de cet emplacement avec le stockage localement redondant.

### <a name="geo-zone-redundant-storage-preview"></a>Stockage géoredondant interzone (préversion)

Le stockage géo-redondant interzone (GZRS, en préversion) combine la haute disponibilité fournie par la redondance entre zones de disponibilité avec la protection contre les pannes régionales assurée par la géo-réplication. Les données d’un compte de stockage GZRS sont répliquées dans trois [zones de disponibilité Azure](../../availability-zones/az-overview.md) au sein de la région primaire, ainsi que vers une région géographique secondaire pour offrir une protection contre des catastrophes régionales. Microsoft recommande d’utiliser une réplication GZRS pour les applications ayant des besoins élevés en termes de cohérence, de durabilité, de disponibilité, de performances et de résilience pour la récupération d’urgence.

Avec un compte de stockage GZRS, vous pouvez continuer à lire et écrire des données si une zone de disponibilité devient indisponible ou n’est pas récupérable. De plus, vos données sont également durables en cas de panne régionale totale ou d’incident empêchant la récupération de la région primaire. Le stockage GZRS est conçu pour fournir une durabilité des objets d’au moins 99,99999999999999 % (16 chiffres 9) sur une année donnée.

Seuls les comptes de stockage v2 à usage général prennent en charge GZRS et RA-GZRS. Pour plus d’informations sur les types de comptes de stockage, voir [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md). Les réplications GZRS et RA-GZRS prennent en charge les objets blob de bloc, les objets blob de page (à l’exception des disques VHD), les fichiers, les tables et les files d’attente.

GZRS et RA-GZRS sont actuellement disponibles en préversion dans les régions suivantes :

- Asie du Sud-Est
- Europe septentrionale
- Europe occidentale
- Sud du Royaume-Uni
- USA Est
- USA Est 2
- USA Centre

Microsoft continue d’activer le stockage GZRS et RA-GZRS dans d’autres régions Azure. Consultez la page [Mises à jour de service Azure](https://azure.microsoft.com/updates/) régulièrement pour plus d’informations sur les régions prises en charge.

Pour plus d’informations sur les prix de la préversion, consultez les prix de la préversion GZRS pour les [objets blob](https://azure.microsoft.com/pricing/details/storage/blobs), les [fichiers](https://azure.microsoft.com/pricing/details/storage/files/), les [files d’attente](https://azure.microsoft.com/pricing/details/storage/queues/) et les [tables](https://azure.microsoft.com/pricing/details/storage/tables/).

> [!IMPORTANT]
> Microsoft déconseille d’utiliser les fonctionnalités en préversion pour les charges de travail de production.

## <a name="read-access-to-data-in-the-secondary-region"></a>Accès en lecture aux données dans la région secondaire

Le stockage géo-redondant (GRS ou GZRS) réplique vos données vers un autre emplacement physique dans la région secondaire pour offrir une protection contre les pannes régionales. Toutefois, ces données peuvent être lues uniquement si le client ou Microsoft lance un basculement de la région primaire vers la région secondaire. Lorsque vous activez l’accès en lecture à la région secondaire, vos données sont accessibles en lecture si la région primaire devient indisponible. Pour l’accès en lecture à la région secondaire, activez le stockage géographiquement redondant avec accès en lecture (RA-GRS) ou le stockage géographiquement redondant interzone avec accès en lecture (RA-GZRS).

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Concevoir vos applications pour l’accès en lecture à la région secondaire

Si votre compte de stockage est configuré pour l’accès en lecture à la région secondaire, vous pouvez concevoir vos applications pour qu’elles passent en toute transparence à la lecture des données de la région secondaire si la région primaire devient indisponible pour une raison quelconque. La région secondaire étant toujours disponible pour l’accès en lecture, vous pouvez tester votre application pour vous assurer qu’elle peut lire à partir de la région secondaire en cas de panne. Pour plus d’informations sur la conception de vos applications pour la haute disponibilité, voir [Conception d’applications hautement disponibles à l’aide du stockage géoredondant avec accès en lecture](storage-designing-ha-apps-with-ragrs.md).

Lorsque l’accès en lecture à la région secondaire est activé, vos données peuvent être lues à partir des points de terminaison tant secondaire que primaire de votre compte de stockage. Le point de terminaison secondaire ajoute le suffixe *– secondary* au nom du compte. Par exemple, si votre point de terminaison primaire pour le stockage d’objets blob est `myaccount.blob.core.windows.net`, le point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour les points de terminaison primaire et secondaire.

### <a name="check-the-last-sync-time-property"></a>Vérifier la propriété Heure de la dernière synchronisation

Étant donné que les données sont répliquées de façon asynchrone dans la région secondaire, la région secondaire est souvent en retard sur la région principale. Si une défaillance se produit dans la région primaire, il est probable que toutes les écritures dans la base de données primaire n’ont pas encore été répliquées dans la base de données secondaire.

Pour déterminer les opérations d’écriture qui ont été répliquées dans la région secondaire, votre application peut vérifier la propriété **Heure de la dernière synchronisation** pour votre compte de stockage. Toutes les opérations d’écriture dans la région principale avant l’heure de la dernière synchronisation ont été répliquées avec succès dans la région secondaire, ce qui signifie qu’elles peuvent être lues à partir de la base de données secondaire. Toutes les opérations d’écriture dans la région principale après l’heure de la dernière synchronisation peuvent avoir été répliquées ou non dans la région secondaire, ce qui signifie qu’elles peuvent ne pas être disponibles pour les opérations de lecture.

Vous pouvez interroger la valeur de la propriété **Heure de la dernière synchronisation** à l’aide d’Azure PowerShell, Azure CLI ou une des bibliothèques clientes du stockage Azure. La propriété **Heure de la dernière synchronisation** correspond à une valeur de date/heure GMT. Pour plus d’informations, voir [Vérifier la propriété Heure de la dernière synchronisation pour un compte de stockage](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Résumé des options de redondance

Le tableau suivant montre la durabilité et la disponibilité de vos données dans un scénario spécifique, en fonction du type de redondance appliqué à votre compte de stockage :

| Scénario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (préversion)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Un nœud au sein d’un centre de données devient indisponible                                                                 | Oui                             | Oui                              | Oui                                  | Oui                                  |
| Indisponibilité d’un centre de données complet (zonal ou non)                                           | Non                              | Oui                              | Oui                                  | Oui                                  |
| Une panne à l’échelle d’une région se produit                                                                                     | Non                              | Non                               | Oui                                  | Oui                                  |
| Accès en lecture aux données dans la région secondaire si la région primaire devient indisponible | Non                              | Non                               | Oui (avec RA-GRS)                                   | Oui (avec RA-GZRS)                                 |
| Pourcentage de durabilité des objets sur une année donnée<sup>1</sup>                                          | Au moins 99,999999999 % (11 chiffres 9) | Au moins 99,9999999999 % (12 chiffres 9) | Au moins 99,99999999999999 % (16 chiffres 9) | Au moins 99,99999999999999 % (16 chiffres 9) |
| Types de comptes de stockage pris en charge<sup>2</sup>                                                                   | GPv2, GPv1, BlockBlobStorage, BlobStorage, FileStorage                | GPv2, BlockBlobStorage, FileStorage                             | GPv2, GPv1, BlobStorage                     | GPv2                     |
| Contrat SLA de disponibilité pour les demandes de lecture<sup>1</sup>  | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) pour GRS<br /><br />Au moins 99,99 % (99,9 % pour le niveau d’accès froid) pour RA-GRS | Au moins 99,9 % (99 % pour le niveau d’accès froid) pour GZRS<br /><br />Au moins 99,99 % (99,9 % pour le niveau d’accès froid) pour RA-GZRS |
| Contrat SLA de disponibilité pour les demandes d’écriture<sup>1</sup>  | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) | Au moins 99,9 % (99 % pour le niveau d’accès froid) |

<sup>1</sup> Pour obtenir des informations sur les garanties du Stockage Azure en matière de durabilité et de disponibilité, consultez le [contrat de niveau de service (SLA) du Stockage Azure](https://azure.microsoft.com/support/legal/sla/storage/).

<sup>2</sup> Pour plus d’informations sur les types de comptes de stockage, consultez [Vue d’ensemble des comptes de stockage](storage-account-overview.md).

Toutes les données de tous les types de comptes de stockage sont copiées en fonction de l’option de redondance définie pour le compte de stockage. Des objets, dont des objets blob de blocs, les objets blob d’ajout, des objets blob de pages, des files d’attente, des tables et des fichiers sont copiés.

Pour des informations sur les prix des différentes options de redondance, voir [Tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Actuellement, le stockage sur disque Azure Premium prend en charge uniquement le stockage localement redondant (LRS). Les comptes de stockage d’objets blob de blocs prennent en charge le stockage localement redondant (LRS) et le stockage redondant interzone (ZRS) dans certaines régions.

## <a name="data-integrity"></a>Intégrité des données

Le service Stockage Azure vérifie régulièrement l’intégrité des données stockées à l’aide de contrôles de redondance cycliques (CRC). Si une altération des données est détectée, ces données sont réparées à l’aide de données redondantes. Le service Stockage Azure calcule également des sommes de contrôle sur tout le trafic réseau afin de détecter une altération des paquets de données lors du stockage ou de la récupération des données.

## <a name="see-also"></a>Voir aussi

- [Vérifier la propriété Heure de la dernière synchronisation pour un compte de stockage](last-sync-time-get.md)
- [Modifier l’option de redondance d’un compte de stockage](redundancy-migration.md)
- [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Récupération d’urgence et basculement de compte (préversion)](storage-disaster-recovery-guidance.md)
