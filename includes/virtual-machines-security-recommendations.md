---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164080"
---
Cet article contient des recommandations de sécurité pour les Machines virtuelles Azure. Mettez en œuvre ces recommandations pour répondre à vos obligations de sécurité comme décrit dans notre modèle de responsabilité partagée et améliorer la sécurité globale de vos solutions d’application web. Pour plus d’informations sur les mesures prises par Microsoft pour répondre à ses responsabilités de fournisseur de services, lisez [Shared responsibilities for cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Certaines recommandations contenues dans cet article peuvent être supervisées automatiquement par Azure Security Center. Azure Security Center est la première ligne de défense dans la protection de vos ressources Azure. Il analyse régulièrement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Il fournit ensuite des recommandations sur la façon de les corriger.

- Pour plus d’informations sur les recommandations Azure Security Center, consultez [Recommandations de sécurité dans Azure Security Center](../articles/security-center/security-center-recommendations.md).
- Pour plus d’informations sur Azure Security Center, consultez [Qu’est-ce qu’Azure Security Center ?](../articles/security-center/security-center-intro.md).

## <a name="recommendations"></a>Recommandations

| Category | Recommandation | Commentaires | Security Center |
|-|-|----|--|
| Généralités | Lorsque vous créez des images de machine virtuelle personnalisées, utilisez les dernières mises à jour. | Avant de créer des images, vérifiez que vous avez installé les dernières mises à jour du système d’exploitation et de toutes les applications qui feront partie de votre image.  | - |
| Généralités | Maintenez vos machines virtuelles à jour | Vous pouvez utiliser la solution [Update Management](../articles/automation/automation-update-management.md) dans Azure Automation pour gérer les mises à jour de vos systèmes d’exploitation Windows et Linux dans Azure. | [Oui](../articles/security-center/security-center-apply-system-updates.md) |
| Généralités | Sauvegardez vos machines virtuelles | La [Sauvegarde Azure](../articles/backup/backup-overview.md) vous permet de protéger vos données d’application à des coûts d’exploitation minimes. Les erreurs rencontrées par les applications peuvent endommager vos données et les erreurs humaines peuvent introduire des bogues dans vos applications. Avec Sauvegarde Azure, vos machines virtuelles exécutant Windows et Linux sont protégées. | - |
| Généralités | Utiliser plusieurs machines virtuelles une résilience et une disponibilité plus grandes | Si votre machine virtuelle exécute des applications qui ont besoin d’une haute disponibilité, vous devez augmenter le nombre de machines virtuelles ou de [groupes à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md). | - |
| Généralités | Adopter une stratégie BCDR (continuité d’activité et reprise d’activité) | Azure Site Recovery vous permet de choisir parmi différentes options conçues pour la continuité d’activité. Il prend en charge différents scénarios de réplication et de basculement. Pour plus d’informations, consultez [À propos de Site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |
| Gestion de l’identité et de l’accès | Centraliser l’authentification des machines virtuelles | Vous pouvez centraliser l’authentification de vos machines virtuelles Windows et Linux à l’aide de l’[authentification Azure AD](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Sécurité des données | Chiffrer les disques de système d’exploitation | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vous permet de chiffrer vos disques de machine virtuelle IaaS Windows et Linux. Le chiffrement des disques rend le contenu illisible si vous ne disposez pas des clés nécessaires. Le chiffrement des disques protège les données stockées contre tout accès non autorisé qui serait rendu possible par la copie du disque.| [Oui](../articles/security-center/security-center-apply-disk-encryption.md) |
| Sécurité des données | Chiffrer les disques de données | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) vous permet de chiffrer vos disques de machine virtuelle IaaS Windows et Linux. Le chiffrement des disques rend le contenu illisible si vous ne disposez pas des clés nécessaires. Le chiffrement des disques protège les données stockées contre tout accès non autorisé qui serait rendu possible par la copie du disque.| -  |
| Sécurité des données | Limiter le nombre de logiciels installés | Le fait de n’installer que les logiciels nécessaires à l’implémentation de votre solution permet de réduire la surface d’attaque de celle-ci. | - |
| Sécurité des données | Utiliser des logiciels antivirus et anti-programmes malveillants | Azure vous permet d’utiliser des logiciels anti-programmes malveillants provenant de fournisseurs de sécurité tels que Microsoft, Symantec, Trend Micro et Kaspersky. Ces logiciels permettent de protéger vos machines virtuelles contre les fichiers malveillants, les logiciels de publicité et d’autres menaces. Vous pouvez déployer la protection Microsoft Antimalware en fonction de vos charges de travail d’application, avec une configuration de base sécurisée par défaut ou une configuration personnalisée avancée. Pour plus d’informations sur Microsoft Antimalware pour Azure, consultez [Microsoft Antimalware pour les services cloud Azure et les machines virtuelles](../articles/security/azure-security-antimalware.md). | - |
| Sécurité des données | Stocker les secrets et les clés de façon sécurisée | Le fait de simplifier la gestion de vos secrets et de vos clés en fournissant aux propriétaires d’applications une option sécurisée et gérée de manière centralisée vous permet de réduire le risque de compromissions ou de fuites accidentelles. Azure Key Vault permet de stocker les clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Si vous avez besoin de stocker vos clés et vos secrets à l’aide de la norme FIPs 140.2 de niveau 3, vous pouvez utiliser [Azure Dedicated HSM](../articles/dedicated-hsm/overview.md). | - |
| Mise en réseau | Restreindre l’accès aux ports de gestion | Les attaquants analysent les plages d’adresses IP de cloud public afin de détecter les ports de gestion ouverts et tenter des attaques « faciles », en utilisant des mots de passe courants ou en exploitant des vulnérabilités non corrigées connues. L’[accès juste-à-temps (JIT) à la machine virtuelle](../articles/security-center/security-center-just-in-time.md) peut être utilisé pour verrouiller le trafic entrant vers vos machines virtuelles Azure, réduire l’exposition aux attaques et faciliter la connexion aux machines virtuelles si nécessaire. | - |
| Mise en réseau | Limiter l’accès réseau | Les groupes de sécurité réseau vous permettent de restreindre l’accès réseau et de contrôler le nombre de points de terminaison exposés. Pour plus d’informations, consultez [Créer, changer ou supprimer un groupe de sécurité réseau](../articles/virtual-network/manage-network-security-group.md). | - |
| Surveillance | Superviser les machines virtuelles | Vous pouvez utiliser [Azure Monitor pour machines virtuelles](../articles/azure-monitor/insights/vminsights-overview.md) en vue de superviser l’état des machines virtuelles et des groupes de machines virtuelles identiques. Une machine virtuelle insuffisamment performante peut entraîner une interruption de service, réduisant ainsi la disponibilité. | - |

## <a name="next-steps"></a>Étapes suivantes

Vérifiez auprès de votre fournisseur d’application pour voir s’il existe des exigences de sécurité supplémentaires. Pour plus d’informations sur le développement d’applications sécurisées, consultez [Documentation sur le développement sécurisé](../articles/security/fundamentals/abstract-develop-secure-apps.md).