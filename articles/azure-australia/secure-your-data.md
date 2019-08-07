---
title: Sécurité des données dans Azure Australie
description: Configuration d’Azure en Australie pour satisfaire aux exigences spécifiques des réglementations, de la législation et de la politique du secteur public.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608352"
---
# <a name="data-security-in-azure-australia"></a>Sécurité des données dans Azure Australie

Les principes fondamentaux pour la sécurisation des données client sont :

* Protection des données à l’aide du chiffrement
* Gestion des secrets
* Restriction de l’accès aux données

## <a name="encrypting-your-data"></a>Chiffrement de vos données

Le chiffrement des données peut être appliqué au niveau du disque (au repos), dans les bases de données (au repos et en transit), dans les applications (en transit) et sur le réseau (en transit). Il existe plusieurs façons d’obtenir le chiffrement dans Azure :

|Service/fonctionnalité|Description|
|---|---|
|Storage Service Encryption|Azure Storage Service Encryption est appliqué au niveau du compte de stockage, ce qui a pour effet de chiffrer automatiquement les objets blob de bloc et les objets blog de pages quand ils sont écrits dans Azure Storage. Les données lues à partir d’Azure Storage sont déchiffrées par le service de stockage avant d’être renvoyées. Utilisez SSE pour sécuriser vos données sans avoir à modifier ni ajouter de code dans les applications.|
|Azure Disk Encryption|Utilisez Azure Disk Encryption pour chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle Azure. L’intégration avec Azure Key Vault vous permet de contrôler le chiffrement et vous aide à gérer les clés de chiffrement de disque.|
|Chiffrement au niveau de l’application côté client|Le chiffrement côté client est intégré aux bibliothèques clientes de stockage Java et .NET, qui peuvent utiliser les API Azure Key Vault. Cela simplifie l’implémentation. Utilisez Azure Key Vault pour avoir accès aux secrets dans Azure Key Vault à des utilisateurs spécifiques à l’aide d’Azure Active Directory.|
|Chiffrement en transit|Le chiffrement de base disponible pour la connectivité à Azure Australie prend en charge le protocole de sécurité TLS (Transport Level Security) 1.2 et les certificats X.509. Les algorithmes de chiffrement de la norme FIPS (Federal Information Processing Standard) 140-2, niveau 1 sont également utilisés pour les connexions réseau de l’infrastructure entre les centres de données Azure Australie.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 et les partages de fichiers Azure peuvent utiliser SMB 3.0 pour le chiffrement entre la machine virtuelle et le partage de fichiers. Utilisez le chiffrement côté client pour chiffrer les données avant leur transfert vers un espace de stockage dans une application client, et les déchiffrer après leur transfert à partir de l’espace de stockage.|
|Machines virtuelles IaaS|Utilisez Azure Disk Encryption. Activez Storage Service Encryption pour chiffrer les fichiers VHD utilisés pour la sauvegarde des disques dans Azure Storage. Notez que cette fonctionnalité chiffre uniquement les nouvelles données écrites. Autrement dit, si vous créez une machine virtuelle et activez ensuite Storage Service Encryption sur le compte de stockage qui contient le fichier VHD, les modifications apportées sont chiffrées, mais pas le fichier VHD d’origine.|
|Chiffrement côté client|Il s’agit de la méthode la plus sûre pour chiffrer vos données, car elle chiffre les données avant leur transit et elle chiffre les données au repos. Toutefois, cette méthode vous oblige à ajouter du code dans vos applications qui utilisent le stockage, ce qui peut ne pas vous convenir. Dans ce cas, vous pouvez utiliser le protocole HTTPS pour vos données en transit, et Storage Service Encryption pour chiffrer les données au repos. Le chiffrement côté client entraîne également une plus grande charge pour le client. Vous devez donc tenir compte de ce paramètre dans vos plans d’extensibilité, en particulier si vous chiffrez et transférez de grandes quantités de données.|
|

Pour plus d’informations sur les options de chiffrement dans Azure, consultez le [Guide de sécurité Azure Storage](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Protection des données grâce à la gestion des secrets

Une gestion sécurisée des clés est primordiale pour la protection des données du cloud. Les clients doivent s’efforcer de simplifier la gestion des clés et de garder le contrôle des clés utilisées par les services et applications cloud pour chiffrer les données.

### <a name="managing-secrets"></a>Gestion des secrets

* Utilisez Azure Key Vault pour réduire les risques d’exposition des secrets dans les fichiers de configuration codés en dur, les scripts ou le code source. Azure Key Vault chiffre les clés (par exemple, les clés de chiffrement pour Azure Disk Encryption) et les secrets (tels que les mots de passe), en les stockant dans des modules de sécurité matériels (HSM) conformes à la norme FIPS 140-2 de niveau 2. Pour une meilleure garantie, vous pouvez importer ou générer des clés dans ces HSM.
* Le code et les modèles d’application ne doivent contenir que des références URI à des secrets, ce qui signifie que les secrets réels ne se trouvent pas dans le code, la configuration ou les référentiels de code source. Cela évite les attaques de type hameçonnage sur les clés dans les référentiels internes ou externes, par exemple, de programmes zombies dans Github.
* Utilisez des contrôles RBAC forts dans Azure Key Vault. Si un opérateur de confiance quitte l’entreprise ou est transféré dans un autre groupe de l’entreprise, son accès aux secrets doit lui être retiré.  

Pour plus d’informations, consultez [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Isolation pour restreindre l’accès aux données

L’isolation correspond à l’utilisation de limites, de segmentation et de conteneurs pour que seuls les utilisateurs, services et applications autorisés aient accès aux données. Par exemple, la séparation entre les locataires est un mécanisme de sécurité essentiel pour les plateformes de cloud mutualisées comme Microsoft Azure. L’isolation logique permet d’empêcher un client d’interférer avec les opérations d’un autre client.

#### <a name="per-customer-isolation"></a>Isolation par client

Azure implémente le contrôle d’accès réseau et la répartition via l’isolation de réseau virtuel de couche 2, les listes de contrôle d’accès, les équilibrages de charge et les filtres IP.

Les clients peuvent isoler davantage leurs ressources au niveau des abonnements, des groupes de ressources, des réseaux virtuels et des sous-réseaux.

Pour plus d’informations sur l’isolation dans Microsoft Azure, consultez [Isolation dans le cloud public Azure](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Étapes suivantes

Passer en revue l’article sur la [passerelle VPN Azure](vpn-gateway.md)