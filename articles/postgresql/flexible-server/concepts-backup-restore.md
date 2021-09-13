---
title: Sauvegarder et restaurer dans Azure Database pour PostgreSQL - Serveur flexible
description: Découvrir les concepts de sauvegarde et de restauration avec Azure Database pour PostgreSQL - Serveur flexible
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 8c6f3ebaa72457d93e4b3e491656f494511bd994
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563012"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Sauvegarder et restaurer dans Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en préversion

Les sauvegardes constituent une partie essentielle de toute stratégie de continuité de l’activité. Elles aident à protéger les données contre toute altération ou suppression accidentelle. Azure Database pour PostgreSQL - Serveur flexible sauvegarde automatiquement votre serveur et conserve les sauvegardes pendant une durée allant jusqu’à 35 jours. Lors de la restauration, vous pouvez spécifier la date et l’heure auxquelles vous souhaitez effectuer la restauration dans les limites de la période de conservation. La durée globale nécessaire à la restauration et à la récupération dépend de la taille des fichiers de base de données et de la quantité de récupération à effectuer. 

### <a name="backup-process-in-flexible-server"></a>Processus de sauvegarde sur serveur flexible
La première sauvegarde de capture instantanée est planifiée immédiatement après la création du serveur flexible. Par la suite, une sauvegarde d’instantané des fichiers de données sera effectuée quotidiennement. Les sauvegardes sont stockées dans un stockage redondant interzone à l’intérieur d’une région. Les journaux des transactions (journaux WAL [write-ahead logs]) sont également archivés en continu, ce qui vous permet de restaurer jusqu’à la dernière transaction validée. Ces sauvegardes de données et de journaux vous permettent de restaurer un serveur à n’importe quel point dans le temps compris dans la période de conservation des sauvegardes qui est configurée. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits.

Si la base de données est configurée avec une haute disponibilité, les instantanés quotidiens sont capturés à partir du serveur principal et les sauvegardes de journaux continues sont effectuées à partir du serveur de secours.

> [!IMPORTANT]
>Les sauvegardes ne sont pas effectuées sur les serveurs arrêtés. Toutefois, elles sont reprises lorsque la base de données est démarrée automatiquement après sept jours ou démarrée par l’utilisateur.

Les sauvegardes sont utilisables uniquement pour les opérations de restauration sur le serveur flexible. Si vous souhaitez exporter ou importer des données vers le serveur flexible, vous devez utiliser la méthodologie [vidage et restauration](../howto-migrate-using-dump-and-restore.md).


### <a name="backup-retention"></a>Rétention des sauvegardes

Les sauvegardes sont conservées en fonction du paramètre de période de conservation de sauvegarde pour le serveur. Vous pouvez sélectionner une période de conservation comprise entre 7 et 35 jours. La période de conservation par défaut est de sept jours. Vous pouvez définir la période de conservation lors de la création du serveur, ou la mettre à jour ultérieurement. Les sauvegardes sont conservées même pour les serveurs arrêtés.

La période de conservation de sauvegarde détermine jusqu’à quelle date une restauration à un instant dans le passé peut être récupérée, dans la mesure où elle est basée sur les sauvegardes disponibles. La période de rétention de sauvegarde peut également être traitée comme une fenêtre de récupération du point de vue de la restauration. Toutes les sauvegardes requises pour effectuer une restauration à un instant dans le passé au cours de la période de conservation de sauvegarde sont conservées dans le stockage de sauvegarde. Par exemple, si la période de conservation des sauvegardes est définie sur sept jours, la fenêtre de récupération est considérée comme les sept derniers jours. Dans ce scénario, toutes les données et tous les journaux nécessaires à la restauration et à la récupération du serveur au cours des sept derniers jours sont conservés. 


### <a name="backup-storage-cost"></a>Coût du stockage de sauvegarde

Le serveur flexible fournit jusqu’à 100 % du stockage de votre serveur provisionné en stockage de sauvegarde sans coût supplémentaire. Tous les stockages de sauvegarde supplémentaires utilisés sont facturés en Go par mois. Par exemple, si vous avez configuré un serveur avec 250 Gio de stockage, vous avez une capacité de stockage de sauvegarde de 250 Gio sans coût supplémentaire. Si l’utilisation quotidienne de la sauvegarde est de 25 Gio, vous pouvez avoir jusqu’à 10 jours de stockage de sauvegarde gratuit. La consommation de stockage de sauvegarde supérieure à 250 Gio est facturée selon le [modèle tarifaire](https://azure.microsoft.com/pricing/details/postgresql/).

Vous pouvez utiliser la métrique [Stockage de sauvegarde utilisé](../concepts-monitoring.md) sur le portail Azure afin de superviser le stockage de sauvegarde qui est consommé par un serveur. La métrique « Stockage de sauvegarde utilisé » représente le total du stockage consommé par l’ensemble des sauvegardes de base de données et des sauvegardes de journaux qui sont conservées en fonction de la période de conservation des sauvegardes qui est définie pour le serveur.  Une activité transactionnelle importante sur le serveur peut entraîner une augmentation de l’utilisation du stockage de sauvegarde, quelle que soit la taille totale de la base de données.

Le principal moyen de contrôler le coût fu stockage de sauvegarde consiste à définir la période de rétention de sauvegarde appropriée et à choisir les options de redondance de sauvegarde appropriées pour atteindre les objectifs de récupération souhaités.

> [!IMPORTANT]
> Les sauvegardes géoredondantes ne sont pas prises en charge avec le serveur flexible.

## <a name="point-in-time-restore-overview"></a>Vue d’ensemble de la restauration à un instant dans le passé

Dans le serveur flexible, l’exécution d’une restauration à un instant dans le passé crée un nouveau serveur à partir des sauvegardes du serveur flexible dans la même région que votre serveur source. Il est créé avec la configuration du serveur source pour le niveau tarifaire, la génération de calcul, le nombre de vCores, la taille de stockage, la période de conservation de sauvegarde et l’option de redondance de sauvegarde. De plus, les étiquettes et les paramètres, tels que les paramètres de réseau virtuel et de pare-feu, sont hérités du serveur source. 

 > [!IMPORTANT]
> Si vous restaurez un serveur flexible configuré avec la haute disponibilité avec redondance interzone, le serveur restauré sera configuré sans haute disponibilité et dans la même région que votre serveur principal. 

 ### <a name="restore-process"></a>Processus de restauration

Les fichiers de la base de données physique sont d’abord restaurés à partir des sauvegardes d’instantanés à l’emplacement des données du serveur. La sauvegarde appropriée qui a été effectuée avant l’instant dans le passé souhaité est automatiquement choisie et restaurée. Un processus de récupération est ensuite lancé à l’aide des fichiers WAL pour ramener la base de données à un état cohérent. 

 Supposez par exemple que les sauvegardes soient effectuées chaque soir à 23h00. Si le point de restauration est le 15 août 2020 à 10h00, la sauvegarde quotidienne du 14 août 2020 est restaurée. La base de données sera récupérée jusqu’à 10h00 le 25 août 2020 à l’aide de la sauvegarde des journaux de transactions entre le 24 août à 23h00 et le 25 août à 10h00. 

 Pour restaurer votre serveur de base de données, consultez [ces étapes](./how-to-restore-server-portal.md).

> [!IMPORTANT]
> Sur le serveur flexible, les opérations de restauration créent un nouveau serveur de base de données sans remplacer le serveur existant.

La restauration à un point dans le temps est utile dans plusieurs scénarios. Par exemple, lorsqu’un utilisateur supprime accidentellement des données, perd une base de données ou une table importante ou si une application remplace accidentellement des données correctes par des données erronées en raison d’un défaut de l’application. Vous pourrez restaurer jusqu’à la dernière transaction en raison de la sauvegarde continue des journaux des transactions.

Vous pouvez choisir entre le dernier point de restauration et un point de restauration personnalisé.

-   **Dernier point de restauration (maintenant)**  : il s'agit de l'option par défaut, qui vous permet de restaurer le serveur selon le dernier instant dans le passé. 

-   **Point de restauration personnalisé** : cette option vous permet de choisir n’importe quel instant dans le passé au cours de la période de conservation définie pour ce serveur flexible. Par défaut, l’heure UTC la plus récente est sélectionnée automatiquement. C’est utile si vous souhaitez restaurer jusqu’à la dernière transaction validée à des fins de test. Vous pouvez éventuellement choisir d’autres jours et heures. 

La durée de récupération estimée dépend de plusieurs facteurs, notamment la taille des bases de données, le volume des journaux des transactions à traiter, la bande passante réseau et le nombre total de bases de données à récupérer dans la même région au même moment. La durée de récupération globale est généralement comprise entre quelques minutes et quelques heures.

Si vous avez configuré votre serveur dans un réseau virtuel, vous pouvez effectuer la restauration vers le même réseau virtuel ou vers un autre réseau virtuel. Cependant, vous ne pouvez pas effectuer une restauration sur un accès public. De même, si vous avez configuré votre serveur avec un accès public, vous ne pouvez pas restaurer sur un accès privé.


> [!IMPORTANT]
> Il n’est **pas** possible de restaurer des serveurs supprimés. Si vous supprimez le serveur, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. À l'issue du déploiement, pour protéger les ressources du serveur d'une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des [verrous de gestion](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration

Après la restauration de la base de données, vous pouvez effectuer les tâches suivantes afin que les utilisateurs et applications soient de nouveau opérationnels :

-   Si le nouveau serveur est censé remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur.

-   Vérifiez que les règles de pare-feu et de réseau virtuel appropriées sont en place au niveau du serveur pour permettre aux utilisateurs de se connecter. Ces règles ne sont pas copiées à partir du serveur d’origine.
  
-   Le calcul du serveur restauré peut subir un scale-up/down en fonction des besoins.

-   Vérifiez que les connexions et les autorisations appropriées au niveau de la base de données sont en place.

-   Configurer les alertes, selon les besoins.
  
-  Si vous avez restauré la base de données configurée avec une haute disponibilité et que vous voulez configurer le serveur restauré avec une haute disponibilité, vous pouvez effectuer [ces étapes](./how-to-manage-high-availability-portal.md).

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="backup-related-questions"></a>Questions relatives à la sauvegarde

* **Comment Azure gère-t-il la sauvegarde de mon serveur ?**
 
    Par défaut, Azure Database pour PostgreSQL active les sauvegardes automatisées de l’ensemble de votre serveur (englobant toutes les bases de données créées) avec une période de conservation par défaut de 7 jours. Une capture instantanée incrémentielle quotidienne de la base de données est effectuée. Les fichiers journaux (WAL) sont archivés en continu sur Azure Blob.

* **Est-ce que je peux configurer ces sauvegardes automatiques pour qu’elles soient conservées à long terme ?**
  
    Non. Actuellement, nous ne prenons en charge qu’un maximum de 35 jours de conservation. Vous pouvez effectuer des sauvegardes manuelles et les utiliser pour les besoins de conservation à long terme.

* **Comment faire une sauvegarde manuelle de mes serveurs Postgres ?**
  
    Vous pouvez effectuer une sauvegarde manuelle en utilisant l’outil PostgreSQL pg_dump comme documenté [ici](https://www.postgresql.org/docs/current/app-pgdump.html). Pour obtenir des exemples, vous pouvez vous référer à la [documentation de mise à niveau/migration](../howto-migrate-using-dump-and-restore.md) que vous pouvez utiliser aussi pour les sauvegardes. Si vous voulez sauvegarder Azure Database pour PostgreSQL dans un stockage Blob, reportez-vous à notre blog de la communauté technique [Backup Azure Database for PostgreSQL to a Blob Storage](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/backup-azure-database-for-postgresql-to-a-blob-storage/ba-p/803343). 

* **Quelles sont les fenêtres de sauvegarde pour mon serveur ? Puis-je les personnaliser ?**
  
    Les fenêtres de sauvegarde sont gérées directement par Azure et ne peuvent pas être personnalisées. La première sauvegarde de capture instantanée complète est planifiée immédiatement après la création d’un serveur. Les sauvegardes d’instantanés suivantes sont des sauvegardes incrémentielles qui sont effectuées une fois par jour.

* **Mes sauvegardes sont-elles chiffrées ?**
  
    Oui. Toutes les données et sauvegardes d’Azure Database pour PostgreSQL ainsi que les fichiers temporaires créés pendant l’exécution d’une requête sont chiffrés en utilisant le chiffrement AES 256 bits. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé. 

* **Est-ce que je peux restaurer une ou plusieurs bases de données sur un serveur ?**
  
    La restauration d’une ou de plusieurs bases de données ou tables n’est pas prise en charge directement. Cependant, vous devez restaurer le serveur tout entier sur un nouveau serveur, puis extraire la ou les tables ou bases de données nécessaires, puis les importer sur votre serveur.

* **Mon serveur est-il disponible pendant que la sauvegarde est en cours ?**
    Oui. Les sauvegardes sont des opérations en ligne qui utilisent des captures instantanées. L’opération de capture instantanée ne prend que quelques secondes et n’interfère pas avec les charges de travail de production, ce qui garantit la haute disponibilité du serveur. 

* **Lors de la configuration de la fenêtre de maintenance du serveur, devons-nous prendre en compte la fenêtre de sauvegarde ?**
  
    Non. Les sauvegardes sont déclenchées en interne dans le cadre du service managé et n’ont pas incidence sur la fenêtre de maintenance gérée.

* **Où sont stockées mes sauvegardes automatisées et comment gérer leur conservation ?**
  
    Azure Database pour PostgreSQL crée automatiquement des sauvegardes du serveur et les stocke automatiquement dans un stockage redondant interzone dans les régions où plusieurs zones sont prises en charge, ou dans un stockage localement redondant dans les régions qui ne prennent pas encore en charge plusieurs zones. Vous ne pouvez pas exporter ces fichiers de sauvegarde. Vous pouvez utiliser des sauvegardes seulement pour restaurer votre serveur à un instant dans le passé. La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez si vous le souhaitez configurer la conservation des sauvegardes jusqu’à 35 jours.

* **Comment les sauvegardes sont-elles effectuées dans un serveur activé pour la haute disponibilité ?**
  
    Les volumes de données du serveur flexible sont sauvegardés en utilisant des captures instantanées incrémentielles des disques managés du serveur principal. La sauvegarde WAL est effectuée à partir du serveur principal ou du serveur de secours.

* **Comment vérifier que des sauvegardes sont effectuées sur mon serveur ?**

    La meilleure façon de vérifier la disponibilité de sauvegardes valides est d’effectuer des restaurations périodiques à un instant dans le passé, et de vérifier que les sauvegardes sont valides et restaurables. Les opérations de sauvegarde ou les fichiers ne sont pas exposés aux utilisateurs finaux.

* **Où voir l’espace utilisé par les sauvegardes ?**
  
    Dans le portail Azure, sous Supervision, cliquez sur Métriques : vous pouvez trouver la « métrique Utilisation de la sauvegarde », qui vous permet de superviser l’utilisation totale des sauvegardes.

* **Qu’advient-il de mes sauvegardes si je supprime mon serveur ?**
  
    Si vous supprimez le serveur, toutes les sauvegardes qui appartiennent au serveur sont également supprimées et ne peuvent pas être restaurées. À l'issue du déploiement, pour protéger les ressources du serveur d'une suppression accidentelle ou de changements inattendus, les administrateurs peuvent utiliser des verrous de gestion.

* **Comment les sauvegardes sont-elles conservées pour les serveurs arrêtés ?**

    Aucune nouvelle sauvegarde n’est effectuée pour les serveurs arrêtés. Toutes les sauvegardes plus anciennes (dans la fenêtre de conservation) au moment de l’arrêt du serveur sont conservées jusqu’à ce que le serveur soit redémarré, après quoi la conservation des sauvegardes pour le serveur actif est gouvernée par sa fenêtre de conservation des sauvegardes.

* **Comment mes sauvegardes sont-elles facturées ?**
  
    Le serveur flexible fournit jusqu’à 100 % du stockage de votre serveur provisionné en stockage de sauvegarde sans coût supplémentaire. Tout stockage supplémentaires utilisé pour les sauvegardes est facturé par Go par mois, en fonction du modèle tarifaire. La facturation du stockage de sauvegarde est également régie par la période de conservation sélectionnée et l’option de redondance des sauvegardes choisie, indépendamment de l’activité transactionnelle sur le serveur, qui a un impact direct sur le stockage de sauvegarde total utilisé.

* **Comment suis-je facturé pour un serveur arrêté ?**
  
    Pendant que votre instance de serveur est arrêtée, aucune nouvelle sauvegarde n’est effectuée. Vous êtes facturé pour le stockage provisionné et le stockage de sauvegarde (sauvegardes stockées dans votre fenêtre de conservation spécifiée). Le stockage de sauvegarde gratuit est limité à la taille de votre base de données provisionnée ; toutes les données de sauvegarde excédentaires seront facturées en utilisant le prix des sauvegardes.

* **J’ai configuré mon serveur avec une haute disponibilité redondante interzone. Faites-vous deux sauvegardes et serai-je facturé deux fois ?**
  
    Non. Que les serveurs soient ou non à haute disponibilité, un seul jeu de copies de sauvegarde est conservé et vous n’êtes facturé qu’une seule fois.

### <a name="restore-related-questions"></a>Questions relatives à la restauration

* **Comment restaurer mon serveur ?**

    Azure prend en charge la restauration à un instant dans le passé (pour tous les serveurs), permettant aux utilisateurs de restaurer le point de restauration le plus récent ou un point personnalisé en utilisant le portail Azure, Azure CLI et l’API. 

    Pour restaurer votre serveur à partir des sauvegardes effectuées manuellement en utilisant des outils comme pg_dump, vous pouvez d’abord créer un serveur flexible, puis restaurer votre ou vos bases de données sur le serveur en utilisant [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html).

* **Est-ce que je peux effectuer une restauration vers une autre zone de disponibilité au sein de la même région ?**
  
    Oui. Si la région prend en charge plusieurs zones de disponibilité, la sauvegarde est stockée sur un compte ZRS et vous permet de restaurer dans une autre zone. 

* **Combien de temps faut-il pour effectuer une restauration à un instant dans le passé ? Pourquoi ma restauration prend-elle autant de temps ?**
  
    L’opération de restauration des données à partir de la capture instantanée ne dépend pas de la taille des données ; cependant, la durée du processus de récupération qui applique les journaux (activités de transaction à réappliquer) peut varier en fonction de la sauvegarde précédente, de la date/heure demandée et de la quantité de journaux à traiter. Ceci s’applique à la fois à la restauration au sein de la même zone ou dans une autre zone. 
 
* **Si je restaure mon serveur activé pour la haute disponibilité, le serveur de restauration est-il automatiquement configuré avec la haute disponibilité ?**
  
    Non. Le serveur est restauré en tant que serveur flexible à une seule instance. Une fois la restauration terminée, vous pouvez si vous le souhaitez configurer le serveur avec la haute disponibilité.

* **J’ai configuré mon serveur dans un réseau virtuel. Est-ce que je peux restaurer dans un autre réseau virtuel ?**
  
    Oui. Au moment de la restauration, choisissez un autre réseau virtuel pour restaurer.

* **Est-ce que je peux restaurer mon serveur en accès public dans un réseau virtuel ou vice versa ?**

    Non. Actuellement, nous ne prenons pas en charge la restauration de serveurs entre accès public et privé.

* **Comment faire le suivi de mon opération de restauration ?**
  
    Actuellement, il n’existe aucun moyen d’effectuer le suivi de l’opération de restauration. Vous pouvez consulter le journal d’activité pour déterminer si l’opération est en cours ou terminée.


## <a name="next-steps"></a>Étapes suivantes

-   Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
-   Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
-   Découvrir [comment effectuer une restauration](./how-to-restore-server-portal.md)