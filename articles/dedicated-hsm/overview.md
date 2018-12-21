---
title: Présentation d’HSM dédié - HSM dédié Azure | Microsoft Docs
description: La présentation du service HSM dédié Azure offre dans Azure des fonctionnalités de stockage de clés qui répondent à la certification FIPS 140-2 de niveau 3
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080076"
---
# <a name="what-is-azure-dedicated-hsm"></a>Présentation du service HSM dédié Azure

Un HSM dédié Azure est un service Azure qui permet le stockage de clés de chiffrement dans Azure. Un HSM dédié répond aux exigences de sécurité les plus strictes. Il constitue la solution idéale pour les clients qui ont besoin d'appareils certifiés FIPS 140-2 de niveau 3 ainsi que d'un contrôle complet et exclusif des appliances HSM. Les appareils HSM sont déployés globalement dans plusieurs régions Azure, et peuvent être facilement provisionnés en tant que paire d’appareils et configurés pour la haute disponibilité. Les HSM peuvent également être provisionnés dans différentes régions afin d’offrir une garantie en cas de basculement au niveau régional. Microsoft a publié le service HSM dédié à l’aide de l’appliance [SafeNet Luna Network HSM 7 (Modèle A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) de Gemalto. Cet appareil offre les niveaux de performances et les options d’intégration de services de chiffrement les plus élevés. Quand ils sont provisionnés, les HSM sont connectés directement au réseau virtuel du client, et sont également accessibles par les applications et les outils de gestion locaux en configurant une connectivité VPN point à site ou site à site. Les clients obtiendront les logiciels et la documentation nécessaires pour configurer et gérer les appareils HSM à partir du portail de support technique de Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Pourquoi utiliser Azure HSM dédié ?

### <a name="fips-140-2-level-3-compliance"></a>Conformité FIPS 140-2 de niveau 3

De nombreuses organisations doivent se soumettre à des réglementations strictes qui imposent que le stockage des clés de chiffrement respecte les exigences [FIPS 140-2 de niveau 3](https://csrc.nist.gov/publications/detail/fips/140/2/final). Actuellement, le service multilocataire Azure Key Vault de Microsoft fournit uniquement une certification FIPS 140-2 de niveau 2. Azure HSM dédié répond à un besoin réel du secteur des services financiers, des agences gouvernementales et d’autres utilisateurs qui doivent répondre aux exigences FIPS 140-2 de niveau 3.

### <a name="single-tenant-devices"></a>Appareils à locataire unique

La plupart de nos clients n’ont besoin que d’un seul locataire pour l’appareil de stockage de chiffrement. Le service Azure HSM dédié autorise le provisionnement d’un appareil physique à partir de l’un des centres de données mondialement distribués de Microsoft. Une fois le provisionnement effectué pour un client, seul ce client sera en mesure d’accéder à l’appareil.

### <a name="full-administrative-control"></a>Contrôle administratif complet

En plus des appareils à locataire unique, de nombreux clients exigent un contrôle administratif complet et un accès exclusif aux fonctionnalités d’administration. Une fois le provisionnement effectué, seul ce client dispose d’un accès administratif ou au niveau de l’application à l’appareil. Microsoft n’a aucun contrôle d’administration après le premier accès du client, qui nécessite de changer le mot de passe. À partir de là, le client est un vrai locataire unique disposant d’un contrôle administratif complet et de capacités de gestion des applications. Microsoft conserve un accès au niveau de la supervision (pas un rôle d’administrateur) pour la télémétrie par le biais d’une connexion de port série couvrant des capteurs matériels tels que la température, l’intégrité de l’alimentation électrique et l’intégrité des ventilateurs. Le client est libre de désactiver ces fonctionnalités s’il le souhaite, mais dans ce cas il ne recevra plus d’alertes d’intégrité proactives de la part de Microsoft.

### <a name="high-performance"></a>Hautes performances

L’appareil Gemalto a été sélectionné pour ce service en raison de sa gamme étendue de prise en charge d’algorithmes de chiffrement, des nombreux systèmes d’exploitation pris en charge et de la prise en charge étendue des API. Le modèle spécifique déployé offre d’excellentes performances avec 10 000 opérations par seconde pour RSA-2048. Il prend en charge 10 partitions qui peuvent être utilisées pour des instances d’applications uniques. Il s’agit d’un appareil à faible latence, haute capacité et débit élevé.

### <a name="unique-cloud-based-offering"></a>Offre cloud unique

Microsoft a reconnu un besoin spécifique parmi un ensemble unique de clients, et est le seul fournisseur cloud qui offre aux nouveaux clients un service HSM dédié conforme à la norme FIPS 140-2 de niveau 3 et une gamme si étendue d’intégration d’applications locales et dans le cloud.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure HSM dédié est-il fait pour vous ?

Azure HSM dédié est un service spécialisé ciblant les exigences uniques d’un type spécifique d’organisation à grande échelle. Ainsi, il est probable que la plupart des clients Azure ne présenteront pas le profil d’utilisation pour ce service. Pour eux, le service Azure Key Vault sera plus approprié et plus économique. Pour vous aider à déterminer l’adéquation à vos besoins, nous avons identifié les critères suivants.

### <a name="best-fit"></a>Adéquation

Convient plus particulièrement aux scénarios « lift-and-shift » nécessitant un accès direct et unique aux appareils HSM. Voici quelques exemples :

- Migration d'applications locales vers des machines virtuelles Azure
- Migration d’applications à partir d’Amazon AWS EC2 vers des machines virtuelles Azure qui utilisent le service AWS Cloud HSM Classic (Amazon n’offre pas ce service aux nouveaux clients)
- Exécution de logiciels de série sur des machines virtuelles Azure, tels qu’Apache/Ngnix SSL Offload, Oracle TDE et ADCS

### <a name="not-a-fit"></a>Inadéquation

Les services cloud Microsoft qui prennent en charge le chiffrement avec des clés gérées par le client (tels qu'Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL et Clé client Office 365) ne sont pas intégrés à Azure HSM dédié.

### <a name="it-depends"></a>Cela dépend

De nombreux scénarios dépendent d’un mélange complexe d’exigences et des compromis éventuellement possibles. L’exigence FIPS 140-2 de niveau 3, par exemple, doit souvent être satisfaite pour des raisons juridiques, et actuellement HSM dédié est l’unique option.  Si ces obligations légales ne sont pas pertinentes, le choix doit souvent se faire entre Azure Key Vault et HSM dédié en fonction de l’évaluation d’une combinaison d’exigences. Voici quelques exemples :

- Nouveau code exécuté sur la machine virtuelle Azure d’un client
- TDE SQL Server sur une machine virtuelle Azure
- Chiffrement Stockage Azure côté client
- SQL Server et Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Étapes suivantes

Compte tenu de la nature hautement spécialisée de ce service, il est impératif de bien comprendre certains des concepts clés utilisés dans cette documentation, la tarification, la prise en charge et les contrats de niveau de service. Un tutoriel est disponible pour faciliter le provisionnement des HSM dans un environnement de réseau virtuel existant. Les [guides d’intégration Gemalto](https://safenet.gemalto.com/partners/microsoft/) et les guides de procédures permettant de déterminer l’architecture de déploiement sont également des ressources précieuses.

* [Haute disponibilité](high-availability.md)
* [Sécurité physique](physical-security.md)
* [Mise en réseau](networking.md)
* [Prise en charge](supportability.md)
* [Surveillance](monitoring.md)
