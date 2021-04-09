---
title: Configurer les paramètres d’Azure HPC Cache
description: Explique comment configurer des paramètres supplémentaires pour le cache, tels que MTU et la configuration personnalisée de NTP et DNS, et comment accéder aux instantanés express à partir des cibles de stockage Blob Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773230"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configurer des paramètres Azure HPC Cache supplémentaires

La page **Mise en réseau** du portail Azure contient des options permettant de personnaliser plusieurs paramètres. La plupart des utilisateurs n’ont pas besoin de modifier les valeurs par défaut de ces paramètres.

Cet article explique également comment utiliser la fonctionnalité d’instantané pour les cibles de stockage Blob Azure. La fonctionnalité d’instantané n’a pas de paramètres configurables.

Pour afficher les paramètres, ouvrez la page **Mise en réseau** du cache dans le portail Azure.

![capture d’écran de la page de mise en réseau du portail Azure](media/networking-page.png)

> [!NOTE]
> Une version précédente de cette page comprenait un paramètre de squash racine au niveau du cache, mais ce paramètre a été déplacé vers les [stratégies d’accès client](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>Ajuster la valeur MTU
<!-- linked from troubleshoot-nas article -->

Vous pouvez sélectionner la taille d’unité de transmission maximale pour le cache à l’aide du menu déroulant intitulé **Taille MTU**.

La valeur par défaut est 1500 octets, mais vous pouvez la remplacer par 1400.

> [!NOTE]
> Si vous réduisez la taille MTU du cache, assurez-vous que les clients et les systèmes de stockage qui communiquent avec le cache ont le même paramètre MTU ou une valeur inférieure.

La réduction de la valeur MTU du cache peut vous aider à contourner les restrictions de taille des paquets dans le reste du réseau du cache. Par exemple, certains VPN ne parviennent pas à transmettre des paquets de 1500 octets en taille réelle. La réduction de la taille des paquets envoyés via le VPN peut éliminer ce problème. Toutefois, notez qu’un paramètre MTU inférieur pour le cache signifie que tout autre composant qui communique avec le cache, y compris les clients et les systèmes de stockage, doit également avoir un paramètre MTU inférieur pour éviter des problèmes de communication.

Si vous ne souhaitez pas modifier les paramètres MTU sur d’autres composants système, vous ne devez pas réduire le paramètre MTU du cache. Il existe d’autres solutions pour contourner les restrictions de taille des paquets VPN. Pour en savoir plus sur le diagnostic et la résolution de ce problème, consultez la section [Ajustement des restrictions de taille des paquets VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) dans l’article de résolution des problèmes NAS.

Pour plus d’informations sur les paramètres MTU dans les réseaux virtuels Azure, consultez [Optimisation des performances TCP/IP pour les machines virtuelles Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="customize-ntp"></a>Personnaliser NTP

Votre cache utilise par défaut le serveur de temps time.microsoft.com, basé sur Azure. Si vous souhaitez que votre cache utilise un autre serveur NTP, spécifiez-le dans la section **Configuration NTP**. Utilisez un nom de domaine complet ou une adresse IP.

## <a name="set-a-custom-dns-configuration"></a>Définir une configuration DNS personnalisée

> [!CAUTION]
> Ne modifiez pas la configuration DNS de votre cache si vous n’en avez pas besoin. Les erreurs de configuration peuvent avoir des conséquences désastreuses. Si votre configuration ne peut pas résoudre les noms de service Azure, l’instance de cache HPC deviendra inaccessible de façon définitive.

Azure HPC Cache est automatiquement configuré pour utiliser le système Azure DNS, sécurisé et pratique. Toutefois, certaines configurations inhabituelles requièrent que le cache utilise un système DNS local distinct au lieu du système Azure. La section **Configuration DNS** de la page **Mise en réseau** est utilisée pour spécifier ce type de système.

Vérifiez auprès de vos représentants Azure ou consultez le service et le support de Microsoft pour déterminer si vous devez ou non utiliser une configuration DNS de cache personnalisée.

Si vous configurez votre propre système DNS local pour Azure HPC Cache, vous devez vous assurer que la configuration peut résoudre les noms de point de terminaison Azure pour les services Azure. Vous devez configurer votre environnement DNS personnalisé pour transférer certaines demandes de résolution de noms à Azure DNS ou à un autre serveur, le cas échéant.

Vérifiez que votre configuration DNS peut résoudre correctement ces éléments avant de l’utiliser pour Azure HPC Cache :

* ``*.core.windows.net``
* Les services de téléchargement de la liste de révocation de certificats (CRL) et de vérification du protocole OCSP (Online Certificate Status Protocol). Une liste partielle est fournie dans la [section relative aux règles de pare-feu](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) à la fin de cet [article sur Azure TLS](../security/fundamentals/tls-certificate-changes.md), mais vous devez consulter un représentant technique de Microsoft pour comprendre toutes les exigences
* Le nom de domaine complet de votre serveur NTP (time.microsoft.com ou un serveur personnalisé)

Si vous devez définir un serveur DNS personnalisé pour votre cache, utilisez les champs fournis :

* **Domaine de recherche DNS** (facultatif) : Entrez votre domaine de recherche, par exemple, ``contoso.com``. Une valeur unique est autorisée, ou vous pouvez le laisser vide.
* **Serveur(s) DNS** : Entrez jusqu’à trois serveurs DNS. Spécifiez-les par leur adresse IP.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Envisagez d’utiliser un cache de test pour vérifier et affiner votre configuration DNS avant de l’utiliser dans un environnement de production.

### <a name="refresh-storage-target-dns"></a>Actualiser le DNS des cibles de stockage

Si votre serveur DNS met à jour les adresses IP, les cibles de stockage NFS associées deviennent temporairement indisponibles. Lisez comment mettre à jour les adresses IP de votre système DNS personnalisé dans [Modifier les cibles de stockage](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Afficher les instantanés pour les cibles de stockage blob

Azure HPC Cache enregistre automatiquement les instantanés de stockage pour les cibles de stockage Blob Azure. Les instantanés fournissent un point de référence rapide pour le contenu du conteneur de stockage back-end.

Les instantanés ne remplacent pas les sauvegardes de données et n’incluent aucune information sur l’état des données en cache.

> [!NOTE]
> Cette fonctionnalité d’instantané est différente de celle incluse dans les logiciels de stockage NetApp ou Isilon. Ces implémentations d’instantanés vident les modifications du cache vers le système de stockage back-end avant de prendre l’instantané.
>
> Par souci d’efficacité, l’instantané d’Azure HPC Cache ne vide pas les modifications en premier et enregistre uniquement les données qui ont été écrites dans le conteneur Blob. Cet instantané ne représente pas l’état des données en cache, il peut donc ne pas inclure les modifications récentes.

Cette fonctionnalité n’est disponible que pour les cibles de stockage Blob Azure, et sa configuration ne peut pas être modifiée.

Les instantanés sont pris toutes les huit heures, à 0:00, 08:00 et 16:00 (UTC).

Azure HPC Cache stocke des instantanés quotidiens, hebdomadaires et mensuels jusqu’à ce qu’ils soient remplacés par de nouveaux. Les limites de rétention de capture instantanée sont les suivantes :

* Jusqu’à 20 instantanés quotidiens
* Jusqu’à 8 instantanés hebdomadaires
* Jusqu’à 3 instantanés mensuels

Accédez aux captures instantanées à partir du répertoire `.snapshot` à la racine de votre cible de stockage blob monté.
