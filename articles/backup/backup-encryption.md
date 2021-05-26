---
title: Chiffrement dans la Sauvegarde Azure
description: Découvrez comment les fonctionnalités de chiffrement de Sauvegarde Azure vous permettent de protéger vos données de sauvegarde et de répondre aux besoins de sécurité de votre entreprise.
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 0906ba078c04247122331bc41de11c871810877f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110472419"
---
# <a name="encryption-in-azure-backup"></a>Chiffrement dans la Sauvegarde Azure

Toutes vos données sauvegardées sont automatiquement chiffrées lorsqu’elles sont stockées dans le cloud grâce au chiffrement de Stockage Azure, ce qui vous permet de respecter vos engagements en matière de sécurité et de conformité. Ces données au repos sont chiffrées à l’aide du chiffrement AES 256 bits, l’un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2. Outre le chiffrement au repos, toutes vos données de sauvegarde en transit sont transférées via HTTPS. Elles restent toujours sur le réseau principal Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Niveaux de chiffrement dans Sauvegarde Azure

Sauvegarde Azure inclut un chiffrement sur deux niveaux :

- **Chiffrement des données dans le coffre Recovery Services**
  - **Utilisation de clés gérées par la plateforme** : Par défaut, toutes vos données sont chiffrées à l’aide de clés gérées par la plateforme. Vous n’avez aucune action explicite à effectuer pour activer ce chiffrement. Il s’applique à toutes les charges de travail sauvegardées dans votre coffre Recovery Services.
  - **Utilisation de clés gérées par le client** : lors de la sauvegarde de vos machines virtuelles Azure, vous pouvez désormais chiffrer vos données à l’aide de clés de chiffrement détenues et gérées par vous. Sauvegarde Azure vous permet d’utiliser vos clés RSA stockées dans le coffre Azure Key Vault pour le chiffrement de vos sauvegardes. La clé de chiffrement utilisée pour le chiffrement des sauvegardes peut être différente de celle utilisée pour la source. Les données sont protégées à l’aide d’une clé de chiffrement des données basée sur l’algorithme AES 256, qui est à son tour protégée à l’aide de vos clés. Cela vous donne un contrôle total sur les données et les clés. Pour permettre le chiffrement, il est nécessaire que le coffre Recovery Services accède à la clé de chiffrement dans Azure Key Vault. Vous pouvez désactiver la clé ou révoquer l’accès à tout moment. Toutefois, vous devez activer le chiffrement à l’aide de vos clés avant d’essayer de protéger les éléments du coffre. [En savoir plus ici](encryption-at-rest-with-cmk.md).
  - **Chiffrement au niveau de l’infrastructure** : En plus du chiffrement de vos données dans le coffre Recovery Services avec des clés gérées par le client, vous pouvez aussi demander à ce qu’une couche de chiffrement supplémentaire soit configurée sur l’infrastructure de stockage. Ce chiffrement de l’infrastructure est géré par la plateforme. Conjugué au chiffrement au repos basé sur les clés gérées par le client, il permet un chiffrement à deux couches de vos données de sauvegarde. Le chiffrement d’infrastructure ne peut être configuré que si vous choisissez d’abord d’utiliser vos propres clés pour le chiffrement au repos. Le chiffrement d’infrastructure utilise des clés gérées par la plateforme pour chiffrer les données.
- **Chiffrement spécifique de la charge de travail en cours de sauvegarde**  
  - **Sauvegarde de machine virtuelle Azure** Le service Sauvegarde Azure prend en charge la sauvegarde de machines virtuelles avec des disques chiffrés à l’aide de clés [gérées par la plateforme](../virtual-machines/disk-encryption.md#platform-managed-keys), ainsi que de [clés gérées par le client](../virtual-machines/disk-encryption.md#customer-managed-keys) qui sont en votre possession. Par ailleurs, vous pouvez également sauvegarder vos machines virtuelles Azure dont le système d’exploitation ou les disques de données sont chiffrés à l’aide d’[Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE utilise BitLocker pour les machines virtuelles Windows et DM-Crypt pour les machines virtuelles Linux afin d’effectuer un chiffrement dans l’invité.

## <a name="next-steps"></a>Étapes suivantes

- [Chiffrement du stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md)
- [FAQ sur le service Sauvegarde Azure](/azure/backup/backup-azure-backup-faq#encryption) pour toute question concernant le chiffrement
