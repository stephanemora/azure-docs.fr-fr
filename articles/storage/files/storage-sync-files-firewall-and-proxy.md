---
title: Paramètres de pare-feu et de proxy locaux d’Azure File Sync | Microsoft Docs
description: Configuration de réseau local d’Azure File Sync
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 937346bf6927efe11e43b64b7c9a2111f00c0e0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890822"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Paramètres de proxy et de pare-feu d’Azure File Sync
Azure File Sync connecte vos serveurs locaux à Azure Files, activant des fonctionnalités de synchronisation multisite et de hiérarchisation cloud. Pour cela, un serveur local doit donc être connecté à Internet. Un administrateur informatique doit déterminer la meilleure voie d’accès aux services cloud Azure pour le serveur.

Cet article fournit des informations sur les exigences spécifiques et les options disponibles pour connecter un serveur en toute sécurité à Azure File Sync.

> [!Important]
> La synchronisation de fichiers Azure ne prend pas encore en charge les pare-feu et réseaux virtuels pour un compte de stockage.

## <a name="overview"></a>Vue d'ensemble
Azure File Sync fait office de service d’orchestration entre votre serveur Windows Server, votre partage de fichiers Azure et plusieurs autres services Azure pour synchroniser les données définies dans votre groupe de synchronisation. Pour qu’Azure File Sync fonctionne correctement, vous devez configurer vos serveurs de manière qu’ils puissent communiquer avec les services Azure suivants :

- Stockage Azure
- Azure File Sync
- Azure Resource Manager
- Services d’authentification

> [!Note]  
> Comme l’agent Azure File Sync sur Windows Server initialise toutes les requêtes vers les services cloud, seul le trafic sortant est à prendre en compte du point de vue du pare-feu. <br /> Aucun service Azure n’établit de connexion avec l’agent Azure File Sync.

## <a name="ports"></a>Ports
Azure File Sync déplace les métadonnées et données de fichiers exclusivement via le protocole HTTPS et nécessite que le port 443 soit ouvert en sortie.
Par conséquent, tout le trafic est chiffré.

## <a name="networks-and-special-connections-to-azure"></a>Réseaux et connexions spéciales à Azure
L’agent Azure File Sync ne présente aucune exigence particulière concernant les canaux spéciaux, comme [ExpressRoute](../../expressroute/expressroute-introduction.md), vers Azure.

Azure File Sync prend en charge n’importe quel mode d’accès à Azure disponible, s’adaptant automatiquement aux différentes caractéristiques de réseau, telles que la bande passante et la latence, tout en offrant un contrôle d’administration pour optimiser les paramètres. Toutes les fonctionnalités ne sont pas disponibles pour le moment. Si vous souhaitez configurer un comportement spécifique, indiquez-le nous sur le [forum UserVoice consacré à Azure Files](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync prend en charge les paramètres de proxy au niveau des ordinateurs et relatifs à l’application.

**Les paramètre de proxy spécifiques à l’application** permettent de configurer un proxy spécifiquement pour le trafic d’Azure File Sync. Les paramètres de proxy spécifiques à l’application sont pris en charge sur l’agent version 4.0.1.0 ou ultérieure, et peuvent être configurés pendant l’installation de l’agent ou à l’aide de la cmdlet PowerShell Set-StorageSyncProxyConfiguration.

Commandes PowerShell pour configurer les paramètres de proxy spécifiques à l’application :
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Les configurations de proxy au niveau des machines** sont transparentes pour l’agent Azure File Sync, car l’ensemble du trafic du serveur est acheminé à travers ce proxy.

Pour configurer les paramètres de proxy à l’échelle de la machine, suivez les étapes ci-dessous : 

1. Configurer les paramètres de proxy pour les applications .NET 

   - Modifiez ces deux fichiers :  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Ajoutez la section < system.net > dans les fichiers machine.config (sous la section < system.serviceModel >).  Remplacez 127.0.01:8888 par l’adresse IP et le port du serveur proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Définir les paramètres de proxy WinHTTP 

   - Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges ou de PowerShell pour afficher les paramètres de proxy existant :   

     netsh winhttp show proxy

   - Exécutez la commande suivante à partir d’une invite de commandes avec élévation de privilèges ou de PowerShell pour définir le paramètre de proxy (remplacez 127.0.01:8888 par l’adresse IP et le port du serveur proxy) :  

     netsh winhttp set proxy 127.0.0.1:8888

3. Redémarrez le service Agent de synchronisation de stockage en exécutant la commande suivante à partir d’une invite de commandes avec élévation de privilèges ou de PowerShell : 

      net stop filesyncsvc

      Remarque : Le service Agent de synchronisation de stockage (filesyncsvc) démarrera automatiquement une fois arrêté.

## <a name="firewall"></a>Pare-feu
Comme mentionné plus haut, le port 443 doit être ouvert en sortie. Selon les stratégies en place dans votre centre de données, votre succursale ou votre région, il se peut que vous souhaitiez ou deviez restreindre le trafic sur ce port à des domaines spécifiques.

Le tableau suivant décrit les domaines requis pour la communication :

| de diffusion en continu | Point de terminaison de cloud public | Point de terminaison Azure Government | Usage |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | N’importe quel appel utilisateur (par exemple, PowerShell) est acheminé vers/à travers cette URL, y compris l’appel pour l’inscription initiale du serveur. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Les appels à Azure Resource Manager doivent être effectués par un utilisateur authentifié. Cette URL est utilisée pour l’authentification utilisateur. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Dans le cadre du déploiement d’Azure File Sync, un principal de service est créé dans l’annuaire Azure Active Directory de l’abonnement. Cette URL est utilisée pour cela. Le principal créé sert à déléguer un ensemble minimal de droits au service Azure File Sync. L’utilisateur qui effectue la configuration initiale d’Azure File Sync doit être un utilisateur authentifié avec des privilèges de propriétaire d’abonnement. |
| **Stockage Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quand le serveur télécharge un fichier, il effectue ce déplacement de données plus efficacement quand il communique directement avec le partage de fichiers Azure dans le compte de stockage. Le serveur présente une clé SAS qui permet uniquement un accès ciblé au partage de fichiers. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Après l’inscription initiale du serveur, le serveur reçoit une URL régionale pour l’instance du service Azure File Sync dans cette région. Le serveur peut utiliser cette URL pour communiquer directement et plus efficacement avec l’instance qui gère sa synchronisation. |
| **Infrastructure à clé publique Microsoft** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Une fois que l’agent Azure File Sync est installé, l’URL de l’infrastructure à clé publique est utilisée pour télécharger les certificats intermédiaires qui sont nécessaires pour communiquer avec le service Azure File Sync et le partage de fichiers Azure. L’URL OCSP est utilisée pour vérifier l’état d’un certificat. |

> [!Important]
> Quand le trafic vers &ast;.one.microsoft.com est autorisé, la destination du trafic à partir du serveur n’est pas limitée au service de synchronisation. Les sous-domaines incluent de nombreux autres services Microsoft.

Si &ast;.one.microsoft.com est trop vaste, vous pouvez limiter les communications du serveur en autorisant uniquement des instances régionales explicites du service Azure Files Sync. Les instances à définir dépendent de la région du service de synchronisation de stockage où vous avez déployé et inscrit le serveur. Cette région est appelée « URL de point de terminaison principal » dans le tableau ci-dessous.

Pour des raisons de récupération d’urgence et de continuité d’activité (BCDR) vous avez peut-être spécifié des partages de vos fichiers Azure dans un compte de stockage globalement redondant (GRS). Si tel est le cas, vos partages de fichiers Azure basculeront vers la région jumelée en cas de panne régionale durable. Azure File Sync utilise les même paires régionales en tant que stockage. Par conséquent, si vous utilisez des comptes de stockage GRS, vous devez activer des URL supplémentaires pour permettre à votre serveur de communiquer avec la région jumelée pour Azure File Sync. Le tableau ci-dessous appelle cela « Région Jumelée ». De plus, il existe une URL du profil de gestionnaire de trafic qui doit être activée également. Ainsi, le trafic réseau peut être à nouveau routé en toute transparence vers la région jumelée en cas de basculement et est appelé « URL de détection » dans le tableau ci-dessous.

| Cloud  | Région | URL de point de terminaison principal | Région jumelée | URL de détection |
|--------|--------|----------------------|---------------|---------------|
| Public |Australie Est | https://kailani-aue.one.microsoft.com | Australie Sud-Est | https://kailani-aue.one.microsoft.com |
| Public |Australie Sud-Est | https://kailani-aus.one.microsoft.com | Australie Est | https://tm-kailani-aus.one.microsoft.com |
| Public | Centre du Canada | https://kailani-cac.one.microsoft.com | Est du Canada | https://tm-kailani-cac.one.microsoft.com |
| Public | Est du Canada | https://kailani-cae.one.microsoft.com | Centre du Canada | https://tm-kailani.cae.one.microsoft.com |
| Public | USA Centre | https://kailani-cus.one.microsoft.com | USA Est 2 | https://tm-kailani-cus.one.microsoft.com |
| Public | Asie Est | https://kailani11.one.microsoft.com | Asie Sud-Est | https://tm-kailani11.one.microsoft.com |
| Public | USA Est | https://kailani1.one.microsoft.com | USA Ouest | https://tm-kailani1.one.microsoft.com |
| Public | USA Est 2 | https://kailani-ess.one.microsoft.com | USA Centre | https://tm-kailani-ess.one.microsoft.com |
| Public | Europe Nord | https://kailani7.one.microsoft.com | Europe Ouest | https://tm-kailani7.one.microsoft.com |
| Public | Asie Sud-Est | https://kailani10.one.microsoft.com | Asie Est | https://tm-kailani10.one.microsoft.com |
| Public | Sud du Royaume-Uni | https://kailani-uks.one.microsoft.com | Ouest du Royaume-Uni | https://tm-kailani-uks.one.microsoft.com |
| Public | Ouest du Royaume-Uni | https://kailani-ukw.one.microsoft.com | Sud du Royaume-Uni | https://tm-kailani-ukw.one.microsoft.com |
| Public | Europe Ouest | https://kailani6.one.microsoft.com | Europe Nord | https://tm-kailani6.one.microsoft.com |
| Public | USA Ouest | https://kailani.one.microsoft.com | USA Est | https://tm-kailani.one.microsoft.com |
| Gouvernement américain | Gouvernement des États-Unis – Arizona | https://usgovarizona01.afs.azure.us | Gouvernement des États-Unis – Texas | https://tm-usgovarizona01.afs.azure.us |
| Gouvernement américain | Gouvernement des États-Unis – Texas | https://usgovtexas01.afs.azure.us | Gouvernement des États-Unis – Arizona | https://tm-usgovtexas01.afs.azure.us |

- Si vous utilisez des comptes de stockage localement redondants (LRS) ou des comptes de stockage de redondants dans une zone (ZRS), vous devez uniquement activer l’URL répertoriée sous « URL de point de terminaison principal ».

- Si vous utilisez des comptes de stockage géoredondants (GRS), activez les trois URL.

**Exemple :** vous déployez un service de synchronisation de stockage dans `"West US"` et inscrivez votre serveur avec celui-ci. Les URL permettant au serveur de communiquer sont dans ce cas :

> - https://kailani.one.microsoft.com (point de terminaison principal : USA Ouest)
> - https://kailani1.one.microsoft.com (région de basculement jumelée : USA Est)
> - https://tm-kailani.one.microsoft.com (URL de détection de la région principale)

## <a name="summary-and-risk-limitation"></a>Résumé et limitation des risques
La liste fournie plus haut contient les URL avec lesquelles Azure File Sync communique actuellement. Les pare-feux doivent être en mesure d’autoriser le trafic sortant vers ces domaines. Microsoft s’efforce de maintenir cette liste à jour.

La configuration de règles de pare-feu restreignant les domaines peut permettre de renforcer la sécurité. Si ces configurations de pare-feu sont utilisées, vous ne devez pas oublier que des URL seront ajoutées et peuvent même changer au fil du temps. Consultez cet article régulièrement.

## <a name="next-steps"></a>Étapes suivantes
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
- [Superviser Azure File Sync](storage-sync-files-monitoring.md)
