---
ms.assetid: ''
title: Points de terminaison de service Vnet pour Azure Key Vault | Microsoft Docs
description: Vue d’ensemble des points de terminaison de service de réseau virtuel pour Azure Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: c2696d5eb22443b565c48ef4f96d6e4a25827606
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295002"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Points de terminaison de service de réseau virtuel pour Azure Key Vault

Les points de terminaison de service de réseau virtuel pour Azure Key Vault permettent de restreindre l’accès à un réseau virtuel ou à une liste de plages d’adresses IPv4 (Internet Protocol version 4) spécifiés. L’accès sera refusé à tout appelant se connectant à votre coffre de clés à partir d’autres sources. Si un client a choisi d’autoriser des « services Microsoft approuvés » tels Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager, Sauvegarde Azure et autres, les connexions à partir de ces services seront autorisées via le pare-feu. Bien sûr, de tels appelants devront toujours présenter un jeton AAD valide et disposer d’autorisations (configurées en tant que stratégies d’accès) pour effectuer l’opération demandée. Pour plus de détails techniques, voir [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scénarios d’utilisation

Vous pouvez configurer des [pare-feux et réseaux virtuels d’Azure Key Vault](key-vault-network-security.md) refusant l’accès au trafic en provenance de tous les réseaux (y compris le trafic Internet) par défaut. Vous pouvez accorder l’accès au trafic en provenance de réseaux virtuels Azure ou de plages d’adresses IP Internet publiques spécifiques, ce qui vous permet de créer une limite réseau sécurisée pour vos applications.

> [!NOTE]
> Les règles de pare-feux et de réseau virtuel d’Azure Key Vault s’appliquent UNIQUEMENT au [plan de données](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) du coffre de clés. Les opérations de contrôle d’Azure Key Vault (par exemple, opérations de création, suppression, modification de coffre de clés, définition de stratégies d’accès, définition de règles de pare-feux et de réseau virtuel) ne sont pas affectées par les règles de pare-feux et de réseau virtuel.

Par exemple,
* Vous utilisez Azure Key Vault pour stocker des clés de chiffrement, des secrets d’application et des certificats, et souhaitez bloquer l’accès à votre coffre de clés à partir de l’internet public.
* Vous souhaitez verrouiller l’accès à votre coffre de clés afin que seule votre application ou une liste restreinte d’hôtes désignés puissent se connecter à votre coffre de clés.
* Vous avez une application s’exécutant dans votre réseau virtuel (Vnet) Azure qui est verrouillé pour tout trafic entrant et sortant. Votre application doit toujours se connecter au coffre de clés pour extraire des secrets ou certificats, ou pour utiliser des clés de chiffrement.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurer les pare-feux et réseaux virtuels d’Azure Key Vault

Voici les étapes requises pour configurer les pare-feux et réseaux virtuels. Ces étapes sont les mêmes, quelle que soit l’interface (PowerShell, CLI, portail Azure) que vous utilisez pour configurer les règles de pare-feu et de réseau virtuel.
1. Facultatif mais vivement recommandé : activez la [journalisation du coffre de clés](key-vault-logging.md) pour afficher les journaux d’accès détaillés. Cela vous aidera à poser des diagnostics lorsque des règles de pare-feux et de réseau virtuel empêcheront l’accès à un coffre de clés.
2. Activer des « points de terminaison de service pour coffre de clés » pour les réseaux virtuels et sous-réseaux cibles
3. Définissez des règles de pare-feux et de réseau virtuel pour un coffre de clés, afin de restreindre l’accès à ce coffre de clés à partir de réseaux virtuels, sous-réseaux et plages d’adresses IPv4 spécifiques.
4. Si ce coffre de clés doit être accessible à tous les services Microsoft approuvés, vous devez activer l’option autorisant des « services Microsoft approuvés » à se connecter au coffre de clés.

Pour des instructions détaillées, voir [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Une fois que les règles de pare-feu sont effectives, les opérations de [plan de données](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) d’Azure Key Vault peuvent être effectuées UNIQUEMENT lorsque les demandes de l’appelant proviennent de réseaux virtuels ou de plages d’adresses IPV4 autorisés. Cela s’applique également à l’accès au coffre de clés à partir du portail Azure. Si un utilisateur peut accéder à un coffre de clés à partir du portail Azure, il se peut qu’il ne puisse pas répertorier des clés/secrets/certificats si son ordinateur client ne figure pas dans la liste autorisée. Cela affecte également l’accès au Sélecteur de compte Key Vault par d’autres services Azure. Il se peut que des utilisateurs soient en mesure d’afficher la liste des coffres de clés, mais ils ne peuvent pas afficher les clés si des règles de pare-feu bloquent leur ordinateur client.


> [!NOTE]
> * 127 règles de réseau virtuel et 127 règles IPv4 sont autorisées au maximum. 
> * Les petites plages d’adresses qui utilisent les tailles de préfixe « /31 » ou « /32 » ne sont pas prises en charge. Ces plages doivent être configurées à l’aide des règles d’adresses IP individuelles.
> * Les règles de réseau IP sont autorisées uniquement pour les adresses IP publiques. Les plages d’adresses IP réservées aux réseaux privés (comme défini dans RFC 1918) ne sont pas autorisées dans les règles IP. Les réseaux privés incluent des adresses qui commencent par *10.*\*, *172.16.*\* et *192.168.*\*. 
> * Seules les adresses IPv4 sont prises en charge pour le moment.

## <a name="trusted-services"></a>Services approuvés
Voici une liste de services approuvés qui sont autorisés à accéder à un coffre de clés si l’option « Autoriser les services approuvés » est activée.

|Service approuvé|Scénarios d’utilisation|
| --- | --- |
|Service de déploiement de machines virtuelles Azure|[Déployer des certificats sur des machines virtuelles à partir d’un coffre de clés géré par le client](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Service de déploiement modèle Azure Resource Manager|[Passage de valeurs sécurisées lors du déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Service de chiffrement de volume Azure Disk Encryption|Autoriser l’accès à Clé BitLocker (machine virtuelle Windows) ou à DM Passphrase (machine virtuelle Linux) et à la clé de chiffrement lors du déploiement de machine virtuelle pour activer [Azure Disk Encryption](../security/azure-security-disk-encryption.md)|
|Sauvegarde Azure|Autoriser la sauvegarde et restauration de clés et secrets pertinents lors de la sauvegarde de machine virtuelle Azure à l’aide de [Sauvegarde Azure](../backup/backup-introduction-to-azure-backup.md)|
|Exchange Online et SharePoint Online|Autoriser l’accès à la clé de client pour Storage Service Encryption avec une [Clé client](https://support.office.com/en-us/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Autoriser l’accès à la clé de locataire pour [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|App Services|[Déploiement d’un certificat Azure Web App via Azure Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Transparent Data Encryption avec prise en charge de BYOK pour Azure SQL Database et Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Stockage Azure|[ à l’aide de clés gérées par le client dans Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[Chiffrement de données dans Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) avec clé gérée par le client|



> [!NOTE]
> Les stratégies d’accès au coffre de clés appropriées doivent être définies pour autoriser les services correspondants à accéder au coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

* [Sécurisez votre coffre de clés](key-vault-secure-your-key-vault.md)
* [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](key-vault-network-security.md)