---
title: Nouveautés d’Azure Files
description: Apprenez-en davantage sur les nouvelles fonctionnalités et les améliorations du service Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bc44ba9bc2cad45ab447b86a580fb505c8abd6d
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273228"
---
# <a name="whats-new-in-azure-files"></a>Nouveautés d’Azure Files
Le service Azure Files est régulièrement mis à jour pour offrir de nouvelles fonctionnalités et des améliorations. Cet article fournit des informations détaillées sur les nouveautés d’Azure Files.

## <a name="2021-quarter-3-july-august-september"></a>3e trimestre 2021 (juillet, août, septembre)
### <a name="smb-multichannel-is-generally-available"></a>Disponibilité générale de SMB Multichannel
La fonctionnalité SMB Multichannel permet aux clients SMB d’établir plusieurs connexions parallèles à un partage de fichiers Azure. Les clients SMB peuvent ainsi tirer pleinement parti de toute la bande passante réseau disponible et être résilients aux défaillances réseau, ce qui réduit le coût total de possession et d’activation de 2-3x pour les lectures et de 3-4x pour les écritures via un seul client. Le service SMB Multichannel est disponible pour les partages de fichiers Premium (partages de fichiers déployés dans le type de compte de stockage FileStorage) et désactivé par défaut. 

Pour plus d'informations, consultez les pages suivantes :

- [Performances de SMB Multichannel dans Azure Files](storage-files-smb-multichannel-performance.md)
- [Activer SMB Multichannel](files-smb-protocol.md#smb-multichannel)
- [vue d’ensemble de SMB Multichannel dans la documentation de Windows Server](/azure-stack/hci/manage/manage-smb-multichannel)

### <a name="smb-311-and-smb-security-settings"></a>Paramètres de sécurité SMB et SMB 3.1.1
SMB 3.1.1 est la version la plus récente du protocole SMB, publiée avec Windows 10, contenant d’importantes mises à jour importantes en matière de sécurité et de performances. Azure Files SMB 3.1.1 est fourni avec deux modes de chiffrement supplémentaires, AES-128-GCM et AES-256-GCM, en plus du mode AES-128-CCM qui était déjà pris en charge. Pour optimiser les performances, le mode AES-128-GCM est négocié comme l’option de chiffrement de canal SMB par défaut. Le mode AES-128-CCM sera négocié uniquement sur les clients plus anciens ne prenant pas en charge le mode AES-128-GCM. 

Selon les exigences réglementaires et de conformité de votre organisation, il est possible de négocier le mode AES-256-GCM au lieu du mode AES-128-GCM en restreignant les options de chiffrement de canal SMB autorisées sur les clients SMB, dans Azure Files, ou les deux. La prise en charge du mode AES-256-GCM a été ajoutée dans Windows Server 2022 et Windows 10, version 21H1.

En plus du protocole SMB 3.1.1, Azure Files expose des paramètres de sécurité qui modifient le comportement du protocole SMB. Avec cette version, vous pouvez configurer les versions de protocole SMB autorisées, les options de chiffrement de canal SMB, les méthodes d’authentification et les options de chiffrement de ticket Kerberos. Par défaut, Azure Files active les options les plus compatibles, mais ces options peuvent être activées à tout moment.

Pour plus d'informations, consultez les pages suivantes :

- [Paramètres de sécurité SMB](files-smb-protocol.md#smb-security-settings)
- Informations sur la version du protocole SMB pour [Windows](storage-how-to-use-files-windows.md) et [Linux](storage-how-to-use-files-linux.md)
- [Vue d’ensemble des fonctionnalités SMB dans la documentation de Windows Server](/windows-server/storage/file-server/file-server-smb-overview)

## <a name="2021-quarter-2-april-may-june"></a>2e trimestre 2021 (avril, mai, juin)
### <a name="premium-hot-and-cool-storage-capacity-reservations"></a>Réservations de capacité de stockage Premium, chaud et froid 
Azure Files prend en charge les réservations de capacité de stockage (également appelées *instances réservées*). Les réservations de capacité de stockage vous permettent d’obtenir une remise sur le coût de stockage en pré-validant l’utilisation du stockage. Azure Files prend en charge les réservations de capacité aux niveaux Premium, chaud et froid. Les réservations de capacité sont vendues par unités de 10 Tio ou 100 Tio, pour des termes d’un an ou de trois ans. 

Pour plus d'informations, consultez les pages suivantes :

- [Présentation de la facturation d’Azure Files](understanding-billing.md)
- [Coûts optimisés d’Azure Files avec capacité de réserve](files-reserve-capacity.md)
- [Tarification Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="improved-portal-experience-for-domain-joining-to-active-directory"></a>Amélioration de l’expérience du portail pour la jonction de domaine à Active Directory
L’expérience de jonction de domaine dans un compte de stockage Azure a été améliorée pour aider à guider les administrateurs de partage de fichiers Azure débutants dans le processus. Lorsque vous sélectionnez Active Directory sous **Paramètres de partage de fichiers** dans la section **Partages de fichiers** du portail Azure, vous êtes guidé dans les étapes requises pour la jonction de domaine.

:::image type="content" source="media/files-whats-new/ad-domain-join-1.png" alt-text="Capture d’écran de la nouvelle expérience de portail pour la jonction de domaine d’un compte de stockage à Active Directory" lightbox="media/files-whats-new/ad-domain-join-1.png":::

Pour plus d'informations, consultez les pages suivantes :

- [Vue d’ensemble des options d’authentification basée sur l’identité Azure Files pour l’accès SMB](storage-files-active-directory-overview.md)
- [Vue d’ensemble - Authentification Active Directory Domain Services locale sur SMB pour les partages de fichiers Azure](storage-files-identity-auth-active-directory-enable.md)

## <a name="2021-quarter-1-january-february-march"></a>1e trimestre 2021 (janvier, février, mars)
### <a name="azure-files-management-now-available-through-the-control-plane"></a>Gestion d’Azure Files désormais disponible via le plan de contrôle
Les API de gestion pour les ressources Azure Files, le service de fichiers et les partages de fichiers, sont désormais disponibles via le plan de contrôle (fournisseur de ressources `Microsoft.Storage`). Cela permet de créer des partages de fichiers Azure avec un modèle Azure Resource Manager ou bicep, pour qu’ils soient entièrement gérables quand le plan de données (c’est-à-dire, l’API FileREST) est inaccessible (comme quand le point de terminaison public du compte de stockage est désactivé), et prennent en charge la sémantique complète du contrôle d’accès en fonction du rôle (RBAC).

Dans la plupart des cas, nous recommandons de gérer Azure Files via le plan de contrôle. Pour la gestion du service de fichiers et des partages de fichiers via le plan de contrôle, le portail Azure, le module PowerShell de Stockage Azure et Azure CLI ont été mis à jour pour prendre en charge la plupart des actions de gestion via le plan de contrôle. 

Pour conserver le comportement existant du script, le module PowerShell Stockage Azure et Azure CLI conservent les commandes existantes qui utilisent le plan de données pour gérer le service de fichiers et les partages de fichiers, ainsi que l’ajout de nouvelles commandes pour utiliser le plan de contrôle. Les requêtes du portail passent uniquement par le fournisseur de ressources de plan de contrôle. Les commandes PowerShell et CLI sont nommées comme suit :

- Az.Storage PowerShell :
    - Les cmdlets de partage de fichiers du plan de contrôle ont le préfixe `Rm`. Par exemple, `New-AzRmStorageShare`, `Get-AzRmStorageShare`, `Update-AzRmStorageShare` et `Remove-AzRmStorageShare`. 
    - Les cmdlets de partage de fichiers du plan de données traditionnelles n’ont pas de préfixe. Par exemple, , `New-AzStorageShare`, `Get-AzStorageShare`, `Set-AzStorageShareQuota` et `Remove-AzStorageShare`.
    - Les cmdlets destinées à la gestion du service de fichiers ne sont disponibles que via le plan de contrôle, et n’ont pas de préfixe spécial. Par exemple `Get-AzStorageFileServiceProperty` et `Update-AzStorageFileServiceProperty`.
- Interface de ligne de commande du Stockage Azure :
    - Les commandes de partage de fichiers du plan de contrôle sont disponibles sous le groupe de commandes `az storage share-rm`. Par exemple `az storage share-rm create`, `az storage share-rm update` etc.
    - Les commandes de partage de fichiers traditionnelles sont disponibles sous le groupe de commandes `az storage share`. Par exemple `az storage share create`, `az storage share update` etc.
    - Les commandes de gestion du service de fichiers sont disponibles uniquement via le plan de contrôle, par le biais du groupe de commandes `az storage account file-service-properties`. Par exemple, `az storage account file-service-properties show` et `az storage account file-service-properties update`.

Pour en savoir plus sur l’API de gestion Azure Files, consultez :

- [Vue d’ensemble de l’API REST Azure Files](/rest/api/storageservices/file-service-rest-api)
- API de plan de contrôle (fournisseur de ressources `Microsoft.Storage`) pour les ressources Azure Files : 
    - [`FileService`](/rest/api/storagerp/file-services) 
    - [`FileShare`](/rest/api/storagerp/file-shares) 
- [Azure PowerShell](/powershell/module/az.storage) et [Azure CLI](/en-us/cli/azure/storage)

## <a name="2020-quarter-4-october-november-december"></a>4e trimestre 2020 (octobre, novembre, décembre)
### <a name="azure-file-share-soft-delete"></a>Suppression réversible de partage de fichiers Azure
Les partages de fichiers Azure prennent en charge la suppression réversible. Lorsque la suppression réversible est activée, les partages supprimés accidentellement peuvent être facilement récupérés si leur suppression est annulée au cours de la période de rétention définie par l’utilisateur. Pendant la période de rétention, les partages supprimés de manière réversible entraînent des frais liés à la capacité de stockage de données utilisée au taux des captures instantanées de partage.

Pour en savoir plus sur la suppression réversible, consultez :

- [Vue d’ensemble de la suppression réversible](storage-files-prevent-file-share-deletion.md)
- [Activer la suppression réversible](storage-files-enable-soft-delete.md)
- [Tarification Azure Files](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="see-also"></a>Voir aussi
- [Qu’est-ce qu’Azure Files ?](storage-files-introduction.md)
- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [Crée un partage de fichiers Azure](storage-how-to-create-file-share.md)
