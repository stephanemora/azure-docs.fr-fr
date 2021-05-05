---
title: Ponteneurs de profil FSLogix de stockage dans Windows Virtual Desktop - Azure
description: Options pour le stockage de votre profil FSLogix de Windows Virtual Desktop dans le stockage Azure.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 34be20a1b8768414efa0ea32382dea6eab66035a
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108073194"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Options de stockage pour conteneurs de profil FSLogix dans Windows Virtual Desktop

Azure propose plusieurs solutions de stockage que vous pouvez utiliser pour stocker votre conteneur de profil FSLogix. Cet article compare les solutions de stockage qu’Azure propose pour les profils utilisateur des conteneurs de profil FSLogix de Windows Virtual Desktop. Nous recommandons le stockage des conteneurs de profil FSLogix sur Azure Files à la plupart de nos clients.

Windows Virtual Desktop propose des conteneurs de profil FSLogix en tant que solution recommandée de profil utilisateur. FSLogix est conçu pour l’itinérance des profils dans des environnements informatiques à distance, comme Windows Virtual Desktop. Lors de la connexion, ce conteneur est attaché dynamiquement à l’environnement informatique à l’aide d’un disque dur virtuel (VHD) et d’un disque dur virtuel Hyper-V (VHDX) pris en charge en mode natif. Le profil utilisateur est immédiatement disponible et apparaît dans le système exactement comme un profil utilisateur natif.

Les tableaux suivants comparent les solutions de stockage que le stockage Azure propose pour les profils utilisateur des conteneurs de profil FSLogix de Windows Virtual Desktop.

## <a name="azure-platform-details"></a>Détails de la plateforme Azure

|Fonctionnalités|Azure Files|Azure NetApp Files|Espaces de stockage direct|
|--------|-----------|------------------|---------------------|
|Cas d’utilisation|Usage général|Ultra-performance ou migration à partir de NetApp (déployé localement)|Multiplateforme|
|Service de plateforme|Oui, solution Azure native|Oui, solution Azure native|Non, auto-managé|
|Disponibilité régionale|Toutes les régions|[Sélectionner les régions](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Toutes les régions|
|Redondance|Localement redondant/redondant interzone/géoredondant/géoredondant interzone|Localement redondant|Localement redondant/redondant interzone/géoredondant|
|Niveaux et performances| Standard (optimisé pour les transactions)<br>Premium<br>Jusqu’à 100 000 IOPS par partage avec 10 Gbits/s par partage avec environ 3 ms de latence|Standard<br>Premium<br>Ultra<br>Jusqu’à 4,5 Go/s par volume avec une latence d’environ 1 ms. Pour plus d’informations sur l’IOPS et les performances, consultez [Considérations relatives aux performances d’Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md) et [la FAQ](../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops).|Disque dur Standard : limites par disque jusqu’à 500 IOPS<br>Disque SSD Standard : limites par disque jusqu’à 4 000 IOPS<br>Disque SSD Premium : limites par disque jusqu’à 20 000 IOPS<br>Nous vous recommandons d’utiliser des disques Premium pour espaces de stockage direct|
|Capacité|100 Tio par partage, jusqu’à 5 Pio par compte universel |100 Tio par volume, jusqu’à 12,5 Pio par abonnement|Maximum de 32 Tio par disque|
|Infrastructure requise|Taille de partage minimale de 1 Gio|Pool de capacité minimal de 4 Tio, taille de volume min. de 100 Gio|Deux machines virtuelles sur Azure IaaS (+ témoin de cloud) ou au moins trois machines virtuelles sans coûts pour les disques|
|Protocoles|SMB 3.0/2.1, NFSv4.1 (préversion), REST|NFSv3, NFSv4.1 (préversion), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Détails de la gestion Azure

|Fonctionnalités|Azure Files|Azure NetApp Files|Espaces de stockage direct|
|--------|-----------|------------------|---------------------|
|Accès|Cloud, local et hybride (Azure File Sync)|Cloud, local (via ExpressRoute)|Cloud, local|
|Sauvegarde|Intégration des instantanés de sauvegarde Azure|Instantanés Azure NetApp Files|Intégration des instantanés de sauvegarde Azure|
|Sécurité et conformité|[Tous les certificats pris en charge par Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Terminé selon ISO|[Tous les certificats pris en charge par Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Intégration d’Azure Active Directory|[Active Directory natif et Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services et Active Directory natif](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Prise en charge d’Active Directory natif ou d’Azure Active Directory Domain Services uniquement|

Une fois que vous avez choisi votre méthode de stockage, consultez [Tarification de Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) pour obtenir des informations sur nos plans de tarification.

## <a name="azure-files-tiers"></a>Niveaux Azure Files

Azure Files offre deux niveaux de stockage : Premium et Standard. Ces niveaux vous permettent d’adapter les performances et le coût de vos partages de fichiers en fonction des exigences de votre scénario.

- Les partages de fichiers Premium adossés à des disques SSD et déployés dans le type compte de stockage FileStorage. Les partages de fichiers Premium offrent des performances élevées et une faible latence cohérentes pour les charges de travail utilisant beaucoup d’entrées et sorties (E/S). 

- Les partages de fichiers Standard sont adossés à des lecteurs de disque dur (HDD) et déployés dans le type compte de stockage version 2 universel (GPv2). Les partages de fichiers Standard offrent des performances fiables pour les charges de travail d’E/S moins sensibles à la variabilité des performances, telles que les partages de fichiers à usage général et les environnements de dev/test. Les partages de fichiers standard sont disponibles uniquement dans le cadre d’un modèle de facturation avec paiement à l’utilisation.

Le tableau suivant répertorie nos recommandations concernant le niveau de performances à utiliser en fonction de votre charge de travail. Ces recommandations vous aideront à sélectionner le niveau de performances correspondant à vos objectifs de performance, à votre budget et à vos considérations régionales. Nous avons basé ces recommandations sur les exemples de scénarios de des [types de charges de travail Bureau à distance](/windows-server/remote/remote-desktop-services/remote-desktop-workloads). 

| Type de charge de travail | Niveau de fichier recommandé |
|--------|-----------|
| Léger (moins de 200 utilisateurs) | Partages de fichiers Standard |
| Light (plus de 200 utilisateurs) | Partages de fichiers Premium ou Standard avec plusieurs partages de fichiers |
|Moyenne|Partages de fichiers Premium|
|Intensif|Partages de fichiers Premium|
|Power|Partages de fichiers Premium|

Pour plus d’informations sur les performances d’Azure Files, consultez [Partage de fichiers et objectifs de mise à l’échelle des fichiers](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets). Pour plus d’informations sur la tarification, consultez [Tarification d’Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les conteneurs de profil FSLogix, les disques de profil utilisateur et d’autres technologies de profil utilisateur, consultez le tableau dans [Conteneurs de profil FSLogix et fichiers Azure](fslogix-containers-azure-files.md).

Si vous êtes prêt à créer vos propres conteneurs de profil FSLogix, prenez en main l’un de ces tutoriels :

- [Bien démarrer avec les conteneurs de profils FSLogix sur Azure Files dans Windows Virtual Desktop](create-file-share.md)
- [Créer un conteneur de profils FSLogix pour un pool d’hôtes à l’aide d’Azure NetApp Files](create-fslogix-profile-container.md)
- Les instructions de l’article [Déployer un serveur de fichiers scale-out avec des espaces de stockage direct à deux nœuds pour le stockage UPD dans Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) s’appliquent également lorsque vous utilisez un conteneur de profil FSLogix à la place d’un disque de profil utilisateur

Vous pouvez également commencer par le tout début et configurer votre propre solution Windows Virtual Desktop dans [Créer un locataire dans Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
