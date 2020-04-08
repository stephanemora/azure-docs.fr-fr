---
title: Planification d’un déploiement Azure Files | Microsoft Docs
description: Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76a96d36387f55889b65f16ea1ca6ec07359c377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502432"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planification d’un déploiement Azure Files
Le service [Azure Files](storage-files-introduction.md) peut être déployé principalement de deux façons : en montant directement les partages de fichiers Azure serverless, ou en mettant en cache les partages de fichiers Azure en local avec Azure File Sync. L'option de déploiement que vous choisissez détermine les éléments à prendre en compte lors de la planification de votre déploiement. 

- **Montage direct d'un partage de fichiers Azure** : étant donné qu'Azure Files fournit un accès SMB, vous pouvez monter des partages de fichiers Azure localement ou dans le cloud à l'aide du client SMB standard disponible sous Windows, macOS et Linux. Dans la mesure où les partages de fichiers Azure sont serverless, vous n'avez aucun serveur de fichiers ou appareil NAS à gérer lors des déploiements liés à des scénarios de production. Concrètement, cela signifie que vous n'avez aucun correctif logiciel à appliquer ni aucun disque physique à remplacer. 

- **Mise en cache d'un partage de fichiers Azure localement à l'aide d'Azure File Sync** : Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d'un serveur de fichiers local. Azure File Sync transforme une instance Windows Server locale (ou cloud) en cache rapide de votre partage de fichiers Azure. 

Cet article traite principalement de considérations relatives au déploiement, afin de déployer un partage de fichiers Azure en vue de son montage directement par un client local ou un client cloud. Pour planifier un déploiement d’Azure File Sync, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Concepts de gestion
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Lorsque vous déployez des partages de fichiers Azure dans des comptes de stockage, tenez compte des recommandations suivantes :

- Déployez uniquement des partages de fichiers Azure dans des comptes de stockage ayant d’autres partages de fichiers Azure. Bien que les comptes de stockage GPv2 vous permettent de disposer de comptes de stockage mixte, à partir du moment où les ressources de stockage (telles que les partages de fichiers Azure et les conteneurs d’objets BLOB) partagent les limites du compte de stockage, la combinaison des ressources peut compliquer la résolution des problèmes de performances par la suite. 

- Faites attention aux limites d’IOPS d’un compte de stockage lors du déploiement des partages de fichiers Azure. Dans l'idéal, une correspondance 1:1 doit être respectée entre les partages de fichiers et les comptes de stockage, mais cela n'est pas toujours possible en raison des différentes limites et restrictions imposées par votre organisation et Azure. S’il n’est pas possible d’avoir un seul partage de fichiers déployé dans un compte de stockage, tenez compte des partages qui seront très actifs et des partages qui le seront moins, afin de garantir que les partages de fichiers les plus sollicités ne soient pas mis ensemble dans le même compte de stockage.

- Déployez uniquement des comptes GPv2 et FileStorage, et mettez à niveau les comptes de stockage GPv1 et Classic lorsque vous les trouvez dans votre environnement. 

## <a name="identity"></a>Identité
Pour accéder à un partage de fichiers Azure, l’utilisateur du partage de fichiers doit être authentifié et disposer de l’autorisation d’accéder au partage. Cette opération s’effectue en fonction de l’identité de l’utilisateur accédant au partage de fichiers. Azure Files intègre trois fournisseurs d’identité principaux :
- **Azure Directory détenu par le client** (préversion). Les comptes de stockage Azure peuvent être joints à un domaine sur une instance Windows Server Active Directory appartenant à un client, exactement comme un serveur de fichiers Windows Server ou un appareil NAS. Votre contrôleur de domaine Active Directory peut être déployé localement, dans une machine virtuelle Azure, ou même en tant que machine virtuelle dans un autre fournisseur de cloud. Azure Files ne dépend pas de l’emplacement où votre contrôleur de domaine est hébergé. Dès lors qu’un compte de stockage est joint à un domaine, l’utilisateur final peut monter un partage de fichiers avec le compte d’utilisateur dont il s’est servi pour se connecter à son PC. L’authentification basée sur Active Directory utilise le protocole d’authentification Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)** . Azure AD DS fournit un contrôleur de domaine Active Directory géré par Microsoft et pouvant être utilisé pour les ressources Azure. Joindre le domaine de votre compte de stockage à Azure AD DS offre des avantages similaires à le joindre à une instance Active Directory détenue par le client. Cette option de déploiement est particulièrement utile pour les scénarios lift-and-shift d’application qui nécessitent des autorisations basées sur AD. Étant donné qu’Azure AD DS fournit une authentification basée sur Active Directory, cette option utilise également le protocole d’authentification Kerberos.
- **Clé du compte de Stockage Azure** : Les partages de fichiers Azure peuvent également être montés à l’aide d’une clé de compte de stockage Azure. Pour monter un partage de fichiers de cette façon, le nom du compte de stockage est utilisé comme nom d’utilisateur, et la clé du compte de stockage comme mot de passe. L’utilisation de la clé de compte de stockage pour monter le partage de fichiers Azure est en fait une opération d’administrateur, car le partage de fichiers monté disposera d’autorisations complètes sur tous les fichiers et dossiers du partage, même s’ils ont des listes de contrôle d’accès. Lors de l’utilisation de la clé de compte de stockage pour le montage sur SMB, le protocole d’authentification NTLMv2 est utilisé.

Pour les clients qui migrent à partir de serveurs de fichiers locaux, ou qui créent dans Azure Files de nouveaux partages de fichiers destinés à se comporter comme des serveurs de fichiers Windows ou des appliances NAS, le fait de joindre le domaine de votre compte de stockage à l’instance **Active Directory appartenant au client** représente l’option recommandée. Pour en savoir plus sur la jonction du domaine de votre compte de stockage à une instance Active Directory détenue par le client, consultez [Vue d’ensemble Azure Files Active Directory](storage-files-active-directory-overview.md).

Si vous envisagez d’utiliser la clé de compte de stockage pour accéder à vos partages de fichiers Azure, nous vous recommandons d’utiliser des points de terminaison de service, comme décrit à la section [Mise en réseau](#networking).

## <a name="networking"></a>Mise en réseau
Les partages de fichiers Azure sont accessibles de n’importe quel endroit via le point de terminaison public du compte de stockage. Ainsi, les demandes authentifiées, comme celles qui sont autorisées par l’identité d’ouverture de session d’un utilisateur, peuvent provenir de manière sécurisée, de l’intérieur comme de l’extérieur d’Azure. Dans de nombreux environnements de clients, un montage initial du partage de fichiers Azure sur votre station de travail locale échouera, alors même que les montages à partir de machines virtuelles Azure réussissent. L’explication tient au fait que de nombreuses organisations et autres fournisseurs de services Internet bloquent le port dont se sert SMB pour communiquer, à savoir le port 445. Pour afficher le récapitulatif des FAI qui autorisent ou interdisent l’accès depuis le port 445, consultez [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Pour débloquer l’accès à votre partage de fichiers Azure, vous avez principalement deux options à votre disposition :

- Débloquer le port 445 pour le réseau local de votre organisation. Les partages de fichiers Azure sont uniquement accessibles en externe, via le point de terminaison public, par le biais de protocoles sûrs, tels que SMB 3.0 et l’API FileREST. Il s’agit du moyen le plus simple pour accéder à votre partage de fichiers Azure localement, car il ne nécessite pas de configuration réseau avancée au-delà de la modification des règles du port de sortie de votre organisation. Toutefois, nous vous recommandons de supprimer les versions héritées et dépréciées du protocole SMB, à savoir SMB 1.0. Pour savoir comment faire, consultez [Sécurisation de Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) et [Sécurisation de Linux](storage-how-to-use-files-linux.md#securing-linux).

- Accéder aux partages de fichiers Azure via une connexion ExpressRoute ou VPN. Lorsque vous accédez à votre partage de fichiers Azure via un tunnel réseau, vous pouvez monter votre partage de fichiers Azure comme un partage de fichiers en local, car le trafic SMB ne traverse pas les limites de votre organisation.   

Bien que d’un point de vue technique il soit beaucoup plus facile de monter vos partages de fichiers Azure via le point de terminaison public, nous pensons que la plupart des clients choisiront de monter leurs partages de fichiers Azure sur une connexion ExpressRoute ou VPN. Pour ce faire, vous devez configurer les éléments suivants pour votre environnement :  

- **Tunneling réseau à l’aide d’un VPN ExpressRoute, de site à site ou de point à site**. Le tunneling dans un réseau virtuel permet d’accéder aux partages de fichiers Azure depuis l’environnement local, même si le port 445 est bloqué.
- **Points de terminaison privés**. Les points de terminaison privés attribuent à votre compte de stockage une adresse IP dédiée depuis l’espace d’adressage du réseau virtuel. Le tunneling réseau est ainsi possible sans avoir à ouvrir de réseaux locaux sur la totalité des plages d’adresses IP détenues par les clusters de stockage Azure. 
- **Transfert DNS**. Configurez votre DNS local afin de résoudre le nom de votre compte de stockage (c’est-à-dire `storageaccount.file.core.windows.net` pour les régions du cloud public) sur l’adresse IP de vos points de terminaison privés.

Pour planifier la mise en réseau associée au déploiement d’un partage de fichiers Azure, consultez [Considérations relatives à la mise en réseau Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Chiffrement
Azure Files prend en charge deux types de chiffrement : le chiffrement en transit, qui se rapporte au chiffrement utilisé lors du montage/de l’accès au partage de fichiers Azure, et le chiffrement au repos, qui a trait à la façon dont les données sont chiffrées lorsqu’elles sont stockées sur le disque. 

### <a name="encryption-in-transit"></a>Chiffrement en transit
Par défaut, le chiffrement en transit est activé pour tous les comptes de stockage Azure. Cela signifie que, lorsque vous montez un partage de fichiers sur SMB ou y accédez en utilisant le protocole FileREST (par exemple, via le portail Azure, PowerShell/CLI ou des kits de développement logiciel (SDK) Azure), Azure Files n’autorise la connexion que si elle est établie à l’aide des protocoles SMB 3.0+ avec chiffrement ou HTTPS. Les clients qui ne prennent pas en charge le protocole SMB 3.0, ou qui prennent en charge le protocole SMB 3.0 mais pas le chiffrement SMB, ne peuvent pas monter le partage de fichiers Azure si le chiffrement en transit est activé. Pour plus d’informations sur les systèmes d’exploitation prenant en charge SMB 3.0 avec chiffrement, consultez notre documentation détaillée pour [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) et [Linux](storage-how-to-use-files-linux.md). Toutes les versions actuelles de PowerShell, de CLI et des SDK prennent en charge le protocole HTTPS.  

Vous pouvez désactiver le chiffrement en transit pour un compte de stockage Azure. Lorsque le chiffrement est désactivé, Azure Files autorise également les protocoles SMB 2.1 et SMB 3.0 sans chiffrement, ainsi que les appels d’API FileREST non chiffrés via le protocole HTTP. La principale raison justifiant de désactiver le chiffrement en transit est la nécessité de prendre en charge une application héritée devant être exécutée sur un système d’exploitation plus ancien, tel que Windows Server 2008 R2 ou une distribution Linux non récente. Azure Files n’autorise que les connexions SMB 2.1 au sein de la même région Azure que le partage de fichiers Azure. Ainsi, un client SMB 2.1 situé en dehors de la région Azure dans laquelle se trouve le partage de fichiers Azure, par exemple, localement ou dans une autre région Azure, ne peut pas accéder au partage de fichiers.

Nous recommandons fortement de vérifier que le chiffrement des données en transit est activé.

Pour plus d’informations sur le chiffrement en transit, voir [ Exiger un transfert sécurisé dans Stockage Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Chiffrement au repos
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Niveaux de stockage
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

En général, les fonctionnalités d’Azure Files et l’interopérabilité avec d’autres services sont les mêmes entre les partages de fichiers Premium et les partages de fichiers Standard. Il existe, cependant, quelques différences importantes :
- **Modèle de facturation**
    - Les partages de fichiers Premium sont facturés selon un modèle de facturation avec provisionnement, ce qui signifie que vous payez le volume de stockage que vous provisionnez plutôt que la quantité de stockage réellement demandée. 
    - Les partages de fichiers Standard sont facturés selon un modèle de paiement à l’utilisation : il comprend un coût de base du stockage pour la quantité de stockage que vous consommez, et un coût de transaction supplémentaire en fonction de la façon dont vous utilisez le partage. Avec les partages de fichiers Standard, votre facture augmente si vous utilisez davantage (en lecture/écriture/montage) le partage de fichiers Azure.
- **Options de redondance**
    - Les partages de fichiers Premium sont uniquement disponibles pour le stockage localement redondant (LRS) et le stockage redondant interzone (ZRS). 
    - Les partages de fichiers Standard sont disponibles pour le stockage localement redondant, redondant interzone, géoredondant (GRS) et géoredondant interzone (GZRS).
- **Taille maximale d’un partage de fichiers**
    - Les partages de fichiers Premium peuvent être provisionnés jusqu’à 100 Tio sans aucun travail supplémentaire.
    - Par défaut, les partages de fichiers Standard ne peuvent atteindre que 5 Tio, mais la limite de partage peut être augmentée jusqu’à 100 Tio en optant pour l’indicateur de fonctionnalité de compte de stockage *Partage de fichiers volumineux*. Les partages de fichiers Standard peuvent couvrir jusqu’à 100 Tio uniquement pour les comptes de stockage redondants en local ou interzones. Pour plus d’informations sur l’augmentation de la taille des partages de fichiers, consultez [Activer et créer des partages de fichiers volumineux](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Disponibilité régionale**
    - Les partages de fichiers Premium ne sont pas disponibles dans toutes les régions, et la prise en charge de la redondance interzone est disponible dans un sous-ensemble plus petit de régions. Pour savoir si les partages de fichiers Premium sont actuellement disponibles dans votre région, consultez la page des [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pour Azure. Pour connaître les régions prenant en charge ZRS, consultez [Prise en charge des zones de disponibilité Azure par région](../../availability-zones/az-overview.md#services-support-by-region). Pour nous aider à hiérarchiser les nouvelles régions et les fonctionnalités du niveau Premium, répondez à ce [sondage](https://aka.ms/pfsfeedback).
    - Le partage de fichiers Standard est disponible dans toutes les régions Azure.
- Azure Kubernetes Service (AKS) prend en charge le partage de fichiers Premium dans la version 1.13 et ultérieure.

Une fois qu’un partage de fichiers est créé, qu’il soit Premium ou Standard, vous ne pouvez pas le convertir automatiquement à l’autre niveau. Si vous souhaitez basculer vers l’autre niveau, vous devez créer un nouveau partage de fichiers dans le niveau voulu, puis copier manuellement les données depuis votre partage d’origine sur le partage que vous avez créé. Nous vous recommandons d’utiliser `robocopy` pour Windows, `rsync` pour macOS et Linux afin d’effectuer cette copie.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Comprendre le provisionnement des partages de fichiers Premium
Les partages de fichiers Premium sont approvisionnés selon un ratio Gio/IOPS/débit fixe. Pour chaque Gio approvisionné, le partage reçoit un débit d’une IOPS et de 0,1 Mio/s, dans les limites maximales autorisées par partage. L’approvisionnement minimal autorisé est de 100 Gio avec un minimum d’E/S par seconde/débit.

Dans la mesure du possible, tous les partages peuvent atteindre en rafale jusqu’à trois IOPS par Gio de stockage approvisionné pendant 60 minutes ou plus, selon la taille du partage. Les nouveaux partages démarrent avec le crédit de rafale complète basé sur la capacité approvisionnée.

Les partages doivent être provisionnés par incréments de 1 Gio. La taille minimale est de 100 Gio, la taille suivante de 101 Gio, et ainsi de suite.

> [!TIP]
> IOPS de base = 1 * par Gio provisionné. (Jusqu’à 100 000 IOPS maximum).
>
> Limite de rafale = 3 * IOPS de base. (Jusqu’à 100 000 IOPS maximum).
>
> Débit de sortie = 60 Mio/s + 0,06 * Gio provisionnés
>
> Débit d’entrée = 40 Mio/s + 0,04 * Gio provisionnés

La taille de partage approvisionnée est spécifiée par le quota de partage. Le quota du partage peut être augmenté à tout moment, mais il ne peut être réduit qu’au bout des 24 heures suivant la dernière augmentation. À l’issue des 24 heures sans augmentation de quota, vous pouvez diminuer le quota du partage autant de fois que vous le souhaitez, jusqu’à ce que vous l’augmentiez à nouveau. Les modifications de mise à l’échelle IOPS/débit prennent effet quelques minutes après le changement de taille.

Il est possible de diminuer la taille de votre partage provisionné en dessous de votre Gio utilisé. Si vous faites cela, vous ne perdez pas les données, mais vous êtes toujours facturé pour la taille utilisée et recevez les performances (IOPS de base, débit et IOPS en rafale) du partage provisionné, pas de la taille utilisée.

Le tableau suivant illustre quelques exemples de ces formules pour les tailles de partage provisionné :

|Capacité (Gio) | IOPS de base | IOPS en rafale | Sortie (Mio/s) | Entrée (Mio/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Jusqu’à 300     | 66   | 44   |
|500         | 500     | Jusqu’à 1 500   | 90   | 60   |
|1 024       | 1 024   | Jusqu’à 3 072   | 122   | 81   |
|5 120       | 5 120   | Jusqu’à 15 360  | 368   | 245   |
|10 240      | 10 240  | Jusqu’à 30 720  | 675 | 450   |
|33 792      | 33 792  | Jusqu’à 100 000 | 2 088 | 1 392   |
|51 200      | 51 200  | Jusqu’à 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Jusqu’à 100 000 | 6 204 | 4 136   |

> [!NOTE]
> Les performances des partages de fichiers sont soumises aux limites du réseau des machines, à la bande passante réseau disponible, aux tailles d’e/s, au parallélisme, entre autres nombreux facteurs. Par exemple, sur la base d’un test interne avec des tailles d’e/s en lecture/écriture de 8 Kio, une seule machine virtuelle Windows, *F16s_v2 standard*, connectée au partage de fichiers Premium sur SMB pourrait atteindre 20 000 e/s par seconde en écriture et 15 000 e/s par seconde. Avec les tailles d’e/s en lecture/écriture de 512 Mio, la même machine virtuelle peut atteindre 1,1 Gio/s en sortie et 370 Mio/s de débit d’entrée. Pour obtenir une mise à l’échelle des performances maximales, répartissez la charge entre plusieurs machines virtuelles. Reportez-vous au [guide de résolution des problèmes](storage-troubleshooting-files-performance.md) pour certains problèmes de performances courants et leurs solutions de contournement.

#### <a name="bursting"></a>Mode en rafales
Les partages de fichiers Premium peuvent rapidement accroître leur IOPS jusqu’à multiplier leur nombre par trois. Ce mode en rafales est automatisé et fonctionne selon un système de crédits. Il fonctionne dans la mesure des possibilités et la limite de rafale n’est pas une garantie : les partages de fichiers peuvent croître par rafales *jusqu’à* cette limite.

Des crédits s’accumulent dans un compartiment à rafales chaque fois que le trafic de votre partage de fichiers se trouve en dessous des IOPS de base. Par exemple, un partage de 100 Gio dispose de 100 IOPS de base. Si le trafic réel sur le partage est de 40 IOPS pour un intervalle spécifique de 1 seconde, les 60 IOPS inutilisées sont créditées dans un compartiment à rafales. Ces crédits sont ensuite utilisés lorsque des opérations dépassent les IOPS de base.

> [!TIP]
> Taille du compartiment à rafales = IOPS de base * 2 * 3600.

Chaque fois qu’un partage dépasse les IOPS de base et qu’il dispose de crédits dans un compartiment à rafales, il est augmenté par rafales. Les partages peuvent continuer à croître tant qu’il reste des crédits, même si les partages inférieurs à 50 Tio ne demeureront à la limite de la rafale de croissance que sur une durée pouvant atteindre une heure. Les partages supérieurs à 50 Tio peuvent techniquement dépasser cette limite d’une heure, pour atteindre deux heures, mais cela dépend du nombre de crédits de rafale accumulés. Chaque e/s située au-delà des IOPS de base consomme un crédit ; une fois que tous les crédits sont consommés, le partage retourne aux IOPS de base.

Les crédits de partage présentent trois états :

- En hausse, lorsque le partage de fichiers utilise un nombre inférieur à celui des IOPS de base.
- En baisse, lorsque le partage de fichiers s’accroît.
- Constant, lorsqu’il n’y a aucun crédit ou IOPS de base en cours d’utilisation.

Au départ, les nouveaux partages de fichiers se voient attribuer un nombre total de crédits dans leur compartiment à rafales. Les crédits de rafale ne seront pas augmentés si les IOPS du partage chutent en dessous des IOPS de base, en raison de la limitation par le serveur.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Activer les partages de fichiers Standard pour couvrir jusqu’à 100 Tio
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilité régionale
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redondance
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migration
Dans de nombreux cas, vous n’établirez pas de nouveau partage de fichiers net pour votre organisation, mais migrerez plutôt un partage de fichiers existant, depuis un serveur de fichiers local ou un appareil NAS, vers Azure Files. Pour effectuer une migration vers un partage de fichiers, il existe bon nombre d’outils proposés par Microsoft et les fournisseurs tiers, ceux-ci peuvent néanmoins être classés en deux catégories :

- **Outils conservant les attributs du système de fichiers, tels que les listes de contrôle d’accès et les horodateurs** :
    - **[Azure File Sync](storage-sync-files-planning.md)**  : Azure File Sync peut être utilisé comme méthode d’ingestion de données dans un partage de fichiers Azure, même lorsque le déploiement final voulu ne doit pas conserver de présence locale. Azure File Sync peut être installé sur place, dans les déploiements Windows Server 2012 R2, Windows Server 2016 et Windows Server 2019 existants. L’un des avantages à utiliser Azure File Sync comme mécanisme d’ingestion vient de ce que les utilisateurs finals peuvent continuer à utiliser le partage de fichiers en place, car le basculement vers le partage de fichiers Azure ne peut avoir lieu qu’une fois toutes les données chargées en arrière-plan.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy est un outil de copie bien connu, fourni avec Windows et Windows Server. Robocopy peut servir à transférer des données dans Azure Files en montant le partage de fichiers localement, puis en utilisant l’emplacement monté comme destination de la commande Robocopy.

- **Outils ne conservant pas les attributs du système de fichiers** :
    - **Data Box** : Data Box fournit un mécanisme de transfert de données hors connexion pour l’envoi physique de données dans Azure. Cette méthode est conçue pour augmenter le débit et économiser la bande passante, mais actuellement elle ne prend pas en charge les attributs du système de fichiers, tels que les horodateurs et les ACL.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy est un utilitaire de ligne de commande conçu pour copier des données à destination et à partir d’Azure Files, ou d’un stockage blob Azure, en utilisant des commandes simples avec des performances optimales.

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md)
* [Déploiement d’Azure Files](storage-files-deployment-guide.md)
* [Déploiement d’Azure File Sync](storage-sync-files-deployment-guide.md)
