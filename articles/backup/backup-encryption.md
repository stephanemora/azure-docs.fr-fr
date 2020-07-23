---
title: Chiffrement dans la Sauvegarde Azure
description: Découvrez comment les fonctionnalités de chiffrement de la Sauvegarde Azure vous permettent de protéger vos données de sauvegarde et de répondre aux besoins de sécurité de votre entreprise.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: 099e736bfb321f0f92bd3a57f9c24e88293b42bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538749"
---
# <a name="encryption-in-azure-backup"></a>Chiffrement dans la Sauvegarde Azure

Toutes vos données sauvegardées sont automatiquement chiffrées lorsqu’elles sont stockées dans le cloud grâce au chiffrement de Stockage Azure, ce qui vous permet de respecter vos engagements en matière de sécurité et de conformité. Ces données au repos sont chiffrées à l’aide du chiffrement AES 256 bits, l’un des chiffrements par blocs les plus puissants actuellement disponibles, et sont conformes à la norme FIPS 140-2.

Outre le chiffrement au repos, toutes vos données de sauvegarde en transit sont transférées via HTTPS. Elles restent toujours sur le réseau principal Azure.

Pour plus d'informations, consultez [Fonctionnalité de chiffrement du service Stockage Azure pour les données au repos](../storage/common/storage-service-encryption.md). Reportez-vous au [Forum aux questions sur la sauvegarde Azure](./backup-azure-backup-faq.md#encryption) pour connaître les réponses à toutes les questions concernant le chiffrement que vous pouvez vous poser.

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Chiffrement des données de sauvegarde à l’aide de clés gérées par la plateforme

Par défaut, toutes vos données sont chiffrées à l’aide de clés gérées par la plateforme. Vous n’avez besoin d’effectuer aucune action explicite pour activer ce chiffrement, et il s’applique à toutes les charges de travail sauvegardées dans votre coffre Recovery Services.

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Chiffrement des données de sauvegarde à l’aide de clés gérées par le client

Lors de la sauvegarde de vos machines virtuelles Azure, vous pouvez désormais chiffrer vos données à l’aide de clés détenues et gérées par vous-même. Sauvegarde Azure vous permet d’utiliser vos clés RSA stockées dans le coffre Azure Key Vault pour le chiffrement de vos sauvegardes. La clé de chiffrement utilisée pour le chiffrement des sauvegardes peut être différente de celle utilisée pour la source. Les données sont protégées à l’aide d’une clé de chiffrement des données basée sur l’algorithme AES 256, qui est à son tour protégée à l’aide de vos clés. Cela vous donne un contrôle total sur les données et les clés. Pour permettre le chiffrement, il est nécessaire que le coffre Recovery Services soit autorisé à accéder à la clé de chiffrement dans le coffre Azure Key Vault. Vous pouvez désactiver la clé ou révoquer l’accès à tout moment. Toutefois, vous devez activer le chiffrement à l’aide de vos clés avant d’essayer de protéger les éléments du coffre.

Vous trouverez [ici](encryption-at-rest-with-cmk.md) des informations complémentaires sur la façon de chiffrer vos données de sauvegarde à l’aide de clés gérées par le client.

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>Sauvegarde de machines virtuelles à disque managé, chiffrées à l’aide de clés gérées par le client

Sauvegarde Azure vous permet aussi de sauvegarder les machines virtuelles Azure qui utilisent votre clé pour [Storage Service Encryption](../storage/common/storage-service-encryption.md). La clé utilisée pour chiffrer les disques est stockée dans le coffre Azure Key Vault et gérée par vous. La fonctionnalité Storage Service Encryption (SSE) qui utilise des clés gérées par le client se distingue d’Azure Disk Encryption (ADE) dans la mesure où ADE s’appuie sur BitLocker (pour Windows) et DM-Crypt (pour Linux) pour effectuer un chiffrement dans l’invité. De son côté, SSE chiffre les données dans le service de stockage, ce qui vous permet d’utiliser n’importe quel système d’exploitation ou n’importe quelle image pour vos machines virtuelles. Pour plus d’informations, consultez [Chiffrement de disques managés avec des clés gérées par le client](../virtual-machines/windows/disk-encryption.md#customer-managed-keys).

## <a name="infrastructure-level-encryption-for-backup-data"></a>Chiffrement au niveau de l’infrastructure pour les données de sauvegarde

En plus du chiffrement de vos données dans le coffre Recovery Services avec des clés gérées par le client, vous pouvez aussi demander à ce qu’une couche de chiffrement supplémentaire soit configurée sur l’infrastructure de stockage. Conjugué au chiffrement au repos basé sur les clés gérées par le client, ce chiffrement d’infrastructure géré par la plateforme permet un chiffrement à deux couches de vos données de sauvegarde. Il est à noter que le chiffrement d’infrastructure ne peut être configuré que si vous choisissez d’abord d’utiliser vos propres clés pour le chiffrement au repos. Le chiffrement d’infrastructure utilise des clés gérées par la plateforme pour chiffrer les données.

>[!NOTE]
>Le chiffrement d’infrastructure est actuellement proposé en préversion limitée et n’est disponible que dans les régions USA Est, USA Ouest2, USA Centre Sud, US Gov Arizona et US Gov Virginie. Si vous souhaitez utiliser la fonctionnalité dans l’une de ces régions, complétez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) et envoyez-nous un e-mail à l’adresse [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

## <a name="backup-of-vms-encrypted-using-ade"></a>Sauvegarde de machines virtuelles chiffrées à l’aide d’ADE

Grâce à Sauvegarde Azure, vous pouvez également sauvegarder vos machines virtuelles Azure dont le système d’exploitation ou les disques de données sont chiffrés à l’aide d’Azure Disk Encryption. ADE utilise BitLocker pour les machines virtuelles Windows et DM-Crypt pour les machines virtuelles Linux afin d’effectuer un chiffrement dans l’invité. Pour plus d’informations, consultez [Sauvegarder et restaurer des machines virtuelles chiffrées avec Sauvegarde Azure](./backup-azure-vms-encryption.md).

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarder et restaurer une machine virtuelle Azure chiffrée](backup-azure-vms-encryption.md)
