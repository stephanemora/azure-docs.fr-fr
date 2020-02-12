---
title: Vue d’ensemble de la sauvegarde hors connexion
description: Découvrez les divers composants de la sauvegarde hors connexion, notamment la sauvegarde hors connexion basée sur Azure Data Box et la sauvegarde hors connexion basée sur Azure Import/Export.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026489"
---
# <a name="overview-of-offline-backup"></a>Vue d’ensemble de la sauvegarde hors connexion

Cet article donne une vue d’ensemble de la sauvegarde hors connexion.

Les sauvegardes complètes initiales sur Azure transfèrent généralement de grandes quantités de données en ligne et requièrent davantage de bande passante réseau, en comparaison avec les sauvegardes suivantes qui transfèrent uniquement les modifications incrémentielles. Dans certaines zones géographiques, les bureaux ou centres de donnés distants ne disposent pas toujours d’une bande passante réseau suffisante. Par conséquent, ces sauvegardes initiales prennent plusieurs jours et, pendant ce temps, utilisent en permanence le même réseau que celui provisionné pour les applications qui s’exécutent dans le centre de données local.

Sauvegarde Azure prend en charge la « sauvegarde hors connexion », laquelle permet de transférer les données de sauvegarde initiales hors connexion, sans utiliser la bande passante réseau. Ce service propose un mécanisme de copie des données de sauvegarde sur des appareils de stockage physiques qui sont ensuite remis à un centre de données Azure situé à proximité, puis chargés dans un coffre Recovery Services. Ce processus garantit un transfert robuste des données de sauvegarde sans utiliser de bande passante réseau.

## <a name="offline-backup-options"></a>Options de sauvegarde hors connexion

La sauvegarde hors connexion est proposée dans deux modes, selon le propriétaire des appareils de stockage.

1. Sauvegarde hors connexion basée sur Azure Data Box (préversion)
2. Sauvegarde hors connexion basée sur Azure Import/Export

## <a name="azure-data-box-based-offline-backup-preview"></a>Sauvegarde hors connexion basée sur Azure Data Box (préversion)

Ce mode est actuellement pris en charge avec l’agent MARS, en préversion. Cette option a recours au [service Azure Data Box](https://azure.microsoft.com/services/databox/) pour l’envoi des appliances de transfert à votre centre de données ou bureau distant. Ces appliances appartiennent à Microsoft, elles sont sécurisées et inviolables, et dotées de connecteurs USB. Les données de sauvegarde sont alors écrites directement sur ces appareils. **Cette option réduit les efforts nécessaires pour trouver vos propres disques et connecteurs compatibles avec Azure ou provisionner un stockage temporaire comme emplacement intermédiaire.** De plus, Microsoft gère la logistique du transfert de bout en bout, que vous pouvez suivre par le biais du portail Azure. Une architecture décrivant le déplacement des données de sauvegarde avec cette option est illustrée ci-dessous.

![Architecture Data Box de Sauvegarde Azure](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Voici un résumé de l’architecture :

1. Sauvegarde Azure copie directement les données de sauvegarde sur ces appareils préconfigurés.
2. Vous pouvez ensuite renvoyer ces appareils à un centre de données Azure.
3. Le service Azure Data Box copie les données sur un compte de stockage appartenant au client.
4. Sauvegarde Azure copie automatiquement les données de sauvegarde depuis le compte de stockage vers le coffre Recovery Services désigné et des sauvegardes en ligne incrémentielles sont planifiées.

Pour utiliser la sauvegarde hors connexion basée sur Azure Data Box, reportez-vous à [cet article](offline-backup-azure-data-box.md).

## <a name="azure-importexport-based-offline-backup"></a>Sauvegarde hors connexion basée sur Azure Import/Export

Cette option est prise en charge par le serveur de sauvegarde Azure (MABS)/DPM-A/l’agent MARS. Elle s’appuie sur le service [Azure Import/Export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) qui vous permet de transférer des données de sauvegarde initiales vers Azure à l’aide de vos propres disques et connecteurs compatibles avec Azure. Cette approche nécessite le provisionnement d’un stockage temporaire appelé **emplacement intermédiaire** et l’emploi d’utilitaires prédéfinis pour formater et copier les données de sauvegarde sur les disques appartenant au client. Une architecture décrivant le déplacement des données de sauvegarde avec cette option est illustrée ci-dessous :

![Architecture Import/Export de Sauvegarde Azure](./media/offline-backup-overview/azure-backup-import-export.png)

Voici un résumé de l’architecture :

1. Au lieu d’envoyer les données de sauvegarde sur le réseau, Sauvegarde Azure les écrit dans un emplacement intermédiaire.
2. Les données situées à l’emplacement intermédiaire sont écrites sur un ou plusieurs disques SATA à l’aide d’un utilitaire personnalisé.
3. Dans le cadre du travail préparatoire, l’utilitaire crée un travail Azure Import. Les lecteurs SATA sont envoyés au centre de données Azure le plus proche et référencent le travail d’importation pour connecter les activités.
4. Dans le centre de données Azure, les données stockées sur les disques sont copiées vers un compte de stockage Azure.
5. La sauvegarde Azure copie les données de sauvegarde à partir du compte de stockage vers le coffre Recovery Services et des sauvegardes incrémentielles sont planifiées.

Pour utiliser la sauvegarde hors connexion basée sur Azure Import/Export avec l’agent MARS, reportez-vous à [cet article](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

Pour utiliser cette dernière avec MABS/DPM-A, reportez-vous à [cet article](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).

## <a name="offline-backup-support-summary"></a>Résumé de la prise en charge de la sauvegarde hors connexion

Le tableau ci-dessous compare les deux options disponibles pour vous aider à faire le bon choix selon votre scénario.

| **Considération**                                            | **Sauvegarde hors connexion basée sur Azure Data Box**                     | **Sauvegarde hors connexion basée sur Azure Import/Export**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Modèles de déploiement Sauvegarde Azure                              | Agent MARS (préversion)                                              | Agent MARS, serveur de sauvegarde Azure (MABS), DPM-A                                           |
| Nombre maximal de données de sauvegarde par serveur (MARS) ou par groupe de protection (MABS, DPM-A) | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) : 7,2 To <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) : 80 To       | 80 To (jusqu’à 10 disques de 8 To chacun)                          |
| Sécurité (données, appareil et service)                           | [Données](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) : chiffrement AES-256 bits <br> [Appareil](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) : boîtier robuste, interface propriétaire basées sur des informations d’identification pour copier les données <br> [Service](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) : protégé par les fonctionnalités de sécurité Azure | Données : chiffrement BitLocker                                 |
| Provisionnement d’un emplacement intermédiaire temporaire                     | Non exigé                                                | Supérieur ou égal à la taille estimée des données de sauvegarde        |
| Régions prises en charge                                           | [Régions Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Régions Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Régions Azure Import/Export](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| Expédition internationale*                                     | **Non prise en charge**  <br>    *Les adresses du centre de données Azure source et de destination doivent se trouver dans le même pays.* | Prise en charge                                                    |
| Logistique du transfert (livraison, transport, enlèvement)           | Entièrement gérée par Microsoft                                     | Gérée par le client                                            |
| Tarifs                                                      | [Tarifs Azure Data Box](https://azure.microsoft.com/pricing/details/databox/) <br> [Tarifs Azure Data Box Disk](https://azure.microsoft.com/pricing/details/databox/disk/) | [Tarifs Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* **Si votre pays ne dispose pas d’un centre de données Azure, vous devez expédier vos disques à un centre de données Azure situé dans un autre pays.*

## <a name="next-steps"></a>Étapes suivantes

* [Sauvegarde hors connexion basée sur Azure Data Box pour l’agent MARS](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Sauvegarde hors connexion basée sur Azure Import/Export pour l’agent MARS](backup-azure-backup-import-export.md)  
* [Sauvegarde hors connexion basée sur Azure Import/Export pour MABS/DPM-A](backup-azure-backup-server-import-export-.md)
