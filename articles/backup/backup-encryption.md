---
title: Chiffrement dans la Sauvegarde Azure
description: Découvrez comment les fonctionnalités de chiffrement de la Sauvegarde Azure vous permettent de protéger vos données de sauvegarde et de répondre aux besoins de sécurité de votre entreprise.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1f7e0f26df0f8bd70a10b36f658dcfebe897b475
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82761430"
---
# <a name="encryption-in-azure-backup"></a>Chiffrement dans la Sauvegarde Azure

Toutes vos données sauvegardées sont automatiquement chiffrées lorsqu’elles sont stockées dans le cloud grâce au chiffrement de Stockage Azure, ce qui vous permet de respecter vos engagements en matière de sécurité et de conformité. Ces données au repos sont chiffrées à l’aide du chiffrement AES 256 bits, l’un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2.

Outre le chiffrement au repos, toutes vos données de sauvegarde en transit sont transférées via HTTPS. Elles restent toujours sur le réseau principal Azure.

Pour plus d'informations, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Reportez-vous au [Forum aux questions sur la sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) pour connaître les réponses à toutes les questions concernant le chiffrement que vous pouvez vous poser.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Chiffrement des données de sauvegarde à l’aide de clés gérées par la plateforme

Par défaut, toutes vos données sont chiffrées à l’aide de clés gérées par la plateforme. Vous n’avez besoin d’effectuer aucune action explicite pour activer ce chiffrement, et il s’applique à toutes les charges de travail sauvegardées dans votre coffre Recovery Services.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Chiffrement des données de sauvegarde à l’aide de clés gérées par le client

Lors de la sauvegarde de vos machines virtuelles Azure, vous pouvez désormais chiffrer vos données à l’aide de clés détenues et gérées par vous-même. Sauvegarde Azure vous permet d’utiliser vos clés RSA stockées dans le coffre Azure Key Vault pour le chiffrement de vos sauvegardes. La clé de chiffrement utilisée pour le chiffrement des sauvegardes peut être différente de celle utilisée pour la source. Les données sont protégées à l’aide d’une clé de chiffrement des données basée sur l’algorithme AES 256, qui est à son tour protégée à l’aide de vos clés. Cela vous donne un contrôle total sur les données et les clés. Pour permettre le chiffrement, il est nécessaire que le coffre Recovery Services soit autorisé à accéder à la clé de chiffrement dans le coffre Azure Key Vault. Vous pouvez désactiver la clé ou révoquer l’accès à tout moment. Toutefois, vous devez activer le chiffrement à l’aide de vos clés avant d’essayer de protéger les éléments du coffre.

>[!NOTE]
>La disponibilité de cette fonctionnalité est actuellement limitée. Si vous souhaitez chiffrer vos données de sauvegarde à l’aide de clés gérées par le client, remplissez [ce questionnaire](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) et contactez-nous à l’adresse AskAzureBackupTeam@microsoft.com. Notez que la possibilité d’utiliser cette fonctionnalité est soumise à l’approbation du service Sauvegarde Azure.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Sauvegarde de machines virtuelles à disque managé, chiffrées à l’aide de clés gérées par le client

Sauvegarde Azure vous permet également de sauvegarder les machines virtuelles Azure qui utilisent votre clé pour le chiffrement côté serveur. La clé utilisée pour chiffrer les disques est stockée dans le coffre Azure Key Vault et gérée par vous. Le chiffrement côté serveur à l’aide de clés gérées par le client diffère d’Azure Disk Encryption (ADE), dans la mesure où ADE utilise BitLocker (pour Windows) et DM-Crypt (pour Linux) afin d’effectuer un chiffrement dans l’invité. Storage Service Encryption chiffre les données dans le service de stockage, ce qui vous permet d’utiliser n’importe quel système d’exploitation ou n’importe quelle image pour vos machines virtuelles. Pour plus d’informations, consultez [Chiffrement de disques managés avec des clés gérées par le client](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="backup-of-vms-encrypted-using-ade"></a>Sauvegarde de machines virtuelles chiffrées à l’aide d’ADE

Grâce à Sauvegarde Azure, vous pouvez également sauvegarder vos machines virtuelles Azure dont le système d’exploitation ou les disques de données sont chiffrés à l’aide d’Azure Disk Encryption. ADE utilise BitLocker pour les machines virtuelles Windows et DM-Crypt pour les machines virtuelles Linux afin d’effectuer un chiffrement dans l’invité. Pour plus d’informations, consultez [Sauvegarder et restaurer des machines virtuelles chiffrées avec Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder et restaurer une machine virtuelle Azure chiffrée](backup-azure-vms-encryption.md)
