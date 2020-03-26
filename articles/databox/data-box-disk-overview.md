---
title: Présentation de Microsoft Azure Data Box Disk | Microsoft Docs sur les données
description: Description d’Azure Data Box Disk, une solution de cloud qui vous permet de transférer de grandes quantités de données dans Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 067d818b7d23fc0b83cb1d4255bfbb8659149412
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215727"
---
# <a name="what-is-azure-data-box-disk"></a>Qu’est-ce qu’Azure Data Box Disk ?

La solution Microsoft Azure Data Box Disk permet d’envoyer des téraoctets de données locales vers Azure de façon rapide, économique et fiable. Le transfert de données sécurisé est accéléré par l’envoi de+ 1 à 5 disques SSD. Ces disques de 8 To chiffrés sont envoyés à votre centre de données via un opérateur régional. 

Vous pouvez facilement configurer, connecter et déverrouiller les disques via le service Data Box du portail Azure. Copiez vos données vers des disques et renvoyez-les vers Azure. Dans le centre de données Azure, vos données sont automatiquement chargées à partir de lecteurs vers le cloud à l’aide d’un lien de chargement de réseau privé et rapide.

## <a name="use-cases"></a>Cas d'utilisation

Utilisez Data Box Disk pour transférer des téraoctets de données dans des scénarios où vous disposez d’aucune connectivité ou d’une connectivité limitée. Il peut s’agir d’un déplacement de données à usage unique, périodique, ou d’un transfert de données en bloc initial suivi de transferts périodiques. 

- **Migration unique** - lorsque de grandes quantités de données locales sont transférées vers Azure. Par exemple, un déplacement des données à partir de bandes hors connexion vers des données d’archivage se trouvant dans le stockage froid Azure.
- **Transfert incrémentiel** : lorsqu’un transfert en bloc initial est effectué à l’aide de Data Box Disk (seed) suivi de transferts incrémentiels sur le réseau. Par exemple, Commvault et Data Box Disk sont utilisés pour déplacer des copies de sauvegarde vers Azure. Cette migration est suivie d’une copie des données incrémentielles via le réseau vers le stockage Azure. 
- **Chargements périodiques** - lorsqu’une grande quantité de données est générée régulièrement et doit être déplacée vers Azure. Par exemple, dans le secteur de la production d’énergie, où du contenu vidéo est généré sur des plateformes pétrolières et dans des parcs éoliens.

## <a name="the-workflow"></a>Workflow

Un flux type inclut les étapes suivantes :

1. **Commande** - Créez une commande dans le portail Azure et indiquez les informations d’expédition et le compte de stockage de destination Azure de vos données. Si les disques sont disponibles, Azure chiffre prépare et envoie les disques avec un ID de suivi d’expédition.

2. **Réception** - Une fois que les disques livrés, décompressez et connectez-les à l’ordinateur contenant les données à copier. Déverrouillez les disques.
    
3. **Copie des données** - Glissez-déplacez les données à copier sur les disques.

4. **Renvoi** - Préparez et expédiez les disques vers le centre de données Azure.

5. **Chargement** - Les données sont automatiquement copiées des disques vers Azure. Les disques sont effacés en toute sécurité conformément aux instructions du National Institute of Standards and Technology (NIST).

Tout au long de ce processus, vous êtes averti par courrier électronique de toutes les modifications d’état. Pour plus d’informations sur le flux détaillé, accédez à [Deploy Data Box Disks in Azure portal](data-box-disk-quickstart-portal.md) (Déployer des Data Box Disks dans le portail Azure).


## <a name="benefits"></a>Avantages

Data Box Disk est conçu pour déplacer de grandes quantités de données vers Azure sans aucun impact sur le réseau. La solution offre les avantages suivants :

- **Vitesse** - Data Box Disk utilise une connexion USB 3.0 pour déplacer jusqu’à 35 To de données vers Azure en moins d’une semaine.   

- **Facilité d’utilisation** - Data Box est une solution facile à utiliser.

    - Les disques utilisent une connectivité USB qui ne nécessite pratiquement aucun temps d’installation.
    - Les disques sont compacts, ce qui les rend faciles à gérer.
    - Les disques n’ont pas besoin d’alimentation externe.
    - Les disques peuvent être utilisés avec un serveur de centre de données, un ordinateur de bureau ou un ordinateur portable.
    - La solution offre un suivi de bout en bout via le portail Azure.    

- **Sécurité** - Data Box Disk intègre des protections de sécurité pour les disques, les données et le service. 
    - Les disques sont inviolables et prennent en charge la fonctionnalité de mise à jour sécurisée. 
    - Les données sur les disques sont sécurisées en permanence à l’aide d’un chiffrement AES 128 bits. 
    - Les disques peuvent uniquement être déverrouillés à l’aide d’une clé fournie dans le portail Azure. 
    - Le service est protégé par les fonctionnalités de sécurité Azure. 
    - Une fois que vos données sont chargées vers Azure, les disques sont nettoyés, conformément aux normes NIST 800-88r1.  
    
Pour plus d’informations, accédez à [Azure Data Box Disk security and data protection](data-box-disk-security.md) (Protection des données et sécurité Azure Data Box Disk).


## <a name="features-and-specifications"></a>Fonctionnalités et spécifications


| Spécifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Poids                                                  | < 2 lbs. par boîte. Jusqu’à 5 disques dans la boîte                |
| Dimensions                                              | Disque - disque SSD de 2,5" |            
| Câbles                                                  | 1 câble USB 3.1 par disque|
| Capacité de stockage par commande                              | 40 To (environ 35 To utilisables)|
| Capacité stockage du disque                                   | 8 To (environ 7 To utilisables)|
| Interface de données                                          | USB   |
| Sécurité                                                | Préalablement chiffré à l’aide de BitLocker et mise à jour sécurisée <br> Disques protégés par clé d’accès <br> Données chiffrées en permanence  |
| Vitesse de transfert de données                                      | jusqu’à 430 Mo/s en fonction de la taille du fichier      |
|Gestion                                               | Portail Azure |


## <a name="region-availability"></a>Disponibilité des régions

Pour plus d’informations sur la disponibilité régionale, accédez à [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Le déploiement de Data Box Disk est également possible dans Azure Government Cloud. Pour plus d’informations, consultez [Présentation d’Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).


## <a name="pricing"></a>Tarifs

Pour plus d’informations sur les tarifs, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [Conditions requises pour le système Data Box Disk](data-box-disk-system-requirements.md).
- Prenez connaissance des [limites de Data Box Disk](data-box-disk-limits.md).
- Déployez rapidement [Azure Data Box Disk](data-box-disk-quickstart-portal.md) sur le portail Azure.
