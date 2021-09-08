---
title: Résoudre les problèmes de partage de fichiers Azure NFS – Azure Files
description: Résolvez les problèmes liés au partage de fichiers Azure NFS.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: e2cdcf3b42fbb71751644efbaa394c51d2f861fc
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258322"
---
# <a name="troubleshoot-azure-nfs-file-share-problems"></a>Résolvez les problèmes liés au partage de fichiers Azure NFS

Cet article répertorie des problèmes courants liés aux partages de fichiers Azure NFS (préversion). Il indique des causes potentielles et des solutions de contournement lorsque ces problèmes surviennent. Cet article aborde également des problèmes connus de la préversion publique.

## <a name="applies-to"></a>S’applique à
| Type de partage de fichiers | SMB | NFS |
|-|:-:|:-:|
| Partages de fichiers Standard (GPv2), LRS/ZRS | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Standard (GPv2), GRS/GZRS | ![Non](../media/icons/no-icon.png) | ![Non](../media/icons/no-icon.png) |
| Partages de fichiers Premium (FileStorage), LRS/ZRS | ![Non](../media/icons/no-icon.png) | ![Oui](../media/icons/yes-icon.png) |

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" failed: Invalid argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Cause 1 : idmapping n’est pas désactivé
Azure Files interdit les UID/GID alphanumériques. idmapping doit donc être désactivé. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Cause 2 : idmapping a été désactivé, mais a été réactivé après avoir rencontré un nom de fichier/répertoire incorrect
Même si idmapping a été correctement désactivé, les paramètres de désactivation d’idmapping sont remplacés dans certains cas. Par exemple, si Azure Files rencontre un nom de fichier incorrect, il renvoie une erreur. À l’affichage de ce code d’erreur particulier, le client Linux NFS v 4.1 décide de réactiver idmapping et les demandes ultérieures sont renvoyées avec un UID/GID alphanumérique. Pour obtenir la liste des caractères non pris en charge sur Azure Files, consultez cet [article](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata). Le signe deux-points est l’un des caractères non pris en charge. 

### <a name="workaround"></a>Solution de contournement
Vérifiez qu’idmapping est désactivé et que rien n’est réactivé, puis procédez comme suit :

- Démonter le partage
- Désactiver id-mapping avec # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping
- Remonter le partage
- Si vous exécutez rsync, exécutez rsync avec l’argument « —numeric-ids » à partir d’un répertoire ne contenant aucun nom de répertoire ou de fichier incorrect.

## <a name="unable-to-create-an-nfs-share"></a>Incapacité à créer un partage NFS

### <a name="cause-1-subscription-is-not-enabled"></a>Cause 1 : L’abonnement n’est pas activé

Votre abonnement n’a peut-être pas été inscrit pour la préversion NFS d’Azure Files. Pour activer cette fonctionnalité, vous devez exécuter quelques cmdlets supplémentaires à partir de Cloud Shell ou d’un terminal local.

> [!NOTE]
> Vous pouvez être amené à attendre jusqu’à 30 minutes pour que l’inscription prenne effet.


#### <a name="solution"></a>Solution

Utilisez le script suivant pour inscrire la fonctionnalité et le fournisseur de ressources, en remplaçant `<yourSubscriptionIDHere>` avant d’exécuter le script :

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Cause 2 : Paramètres de compte de stockage non pris en charge

NFS est disponible uniquement sur les comptes de stockage avec la configuration suivante :

- Niveau – Premium
- Type de compte – FileStorage
- Régions : [liste des régions prises en charge](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Solution

Suivez les instructions fournies dans notre article : [Comment créer un partage NFS](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Cause 3 : Le compte de stockage a été créé avant la fin de l’inscription

Pour qu’un compte de stockage puisse utiliser la fonctionnalité, il doit être créé une fois l’inscription de l’abonnement terminée pour NFS. L’inscription peut prendre jusqu’à 30 minutes.

#### <a name="solution"></a>Solution

Une fois l’inscription terminée, suivez les instructions de notre article : [Comment créer un partage NFS](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Connexion ou montage impossible d’un partage de fichiers Azure NFS

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Cause 1 : La demande provient d’un client avec une adresse IP non approuvée ou dans un réseau non approuvé

Contrairement à SMB, NFS n’assure pas d’authentification basée sur l’utilisateur. L’authentification d’un partage est basée sur la configuration de vos règles de sécurité réseau. Pour cette raison, pour vous assurer que seules des connexions sécurisées sont établies avec votre partage NFS, vous devez utiliser le point de terminaison de service ou des points de terminaison privés. Pour accéder aux partages à partir d’un emplacement local en plus des points de terminaison privés, vous devez configurer un réseau VPN ou ExpressRoute. Les adresses IP ajoutées à la liste verte du compte de stockage pour le pare-feu sont ignorées. Vous devez utiliser l’une des méthodes suivantes pour configurer l’accès à un partage NFS :


- [Point de terminaison de service](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Accessible par le point de terminaison public.
    - Disponible uniquement dans la même région.
    - Le peering de réseaux virtuels n’autorise pas l’accès à votre partage.
    - Chaque réseau virtuel ou sous-réseau doit être ajouté individuellement à la liste verte.
    - Pour l’accès local, les points de terminaison de service peuvent être utilisés avec des réseaux privés virtuels (VPN) site à site, point à site ou ExpressRoute, mais nous vous recommandons d’utiliser un point de terminaison privé, car cela est plus sécurisé.

Le diagramme suivant illustre la connectivité à l’aide de points de terminaison publics.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagramme de connectivité des points de terminaison publics." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Point de terminaison privé](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - L’accès est plus sécurisé qu’avec le point de terminaison de service.
    - L’accès au partage NFS via un lien privé est disponible à l’intérieur et à l’extérieur de la région Azure du compte de stockage (inter-régions, sur site)
    - Le peering de réseaux virtuels avec des réseaux virtuels hébergés dans le point de terminaison privé permet au partage NFS d’accéder aux clients dans les réseaux virtuels appairés.
    - Des points de terminaison privés peuvent être utilisés avec des réseaux privés virtuels site à site, point à site et ExpressRoute.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagramme de connectivité des points de terminaison privés." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Cause 2 : Le transfert sécurisé requis est activé

Le chiffrement double n’est pas encore pris en charge pour les partages NFS. Azure fournit une couche de chiffrement pour toutes les données en transit entre les centres de données Azure à l’aide de MACSec. Les partages NFS sont accessibles seulement à partir de réseaux virtuels approuvés et via des tunnels VPN. Aucun chiffrement de la couche de transport supplémentaire n’est disponible sur les partages NFS.

#### <a name="solution"></a>Solution

Désactivez le transfert sécurisé requis dans le panneau de configuration de votre compte de stockage.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Capture d’écran du panneau de configuration du compte de stockage : désactivation du transfert sécurisé requis.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Cause 3 : Le package nfs-common n’est pas installé
Avant d’exécuter la commande de montage, installez le package en exécutant la commande spécifique à la distribution, ci-dessous.

Pour vérifier si le package NFS est installé, exécutez : `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Solution

Si le package n’est pas installé, installez-le sur votre distribution.

##### <a name="ubuntu-or-debian"></a>Ubuntu ou Debian

```
sudo apt update
sudo apt install nfs-common
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8+, CentOS 8+

Utilisez le gestionnaire de package dnf :`sudo dnf install nfs-utils`.

Les versions plus anciennes de Red Hat Enterprise Linux et CentOS utilisent le gestionnaire de packages yum : `sudo yum install nfs-common`.

##### <a name="opensuse"></a>OpenSUSE

Utilisez le gestionnaire de package zypper : `sudo zypper install-nfscommon`.

### <a name="cause-4-firewall-blocking-port-2049"></a>Cause 4 : Pare-feu bloquant le port 2049

Le protocole NFS communique avec son serveur sur le port 2049. Assurez-vous que ce port est ouvert sur le compte de stockage (serveur NFS).

#### <a name="solution"></a>Solution

Vérifiez que le port 2049 est ouvert sur votre client en exécutant la commande suivante : `telnet <storageaccountnamehere>.file.core.windows.net 2049`. Si le port n’est pas ouvert, ouvrez-le.

## <a name="ls-list-files-command-shows-incorrectinconsistent-results"></a>La commande ls (list files) affiche des résultats incorrects/incohérents

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>Cause : Incohérence entre les valeurs mises en cache et les valeurs de métadonnées de fichier serveur lorsque le descripteur de fichier est ouvert
Parfois, la commande « list files », ou « df » ou « fins » affiche une taille différente de zéro comme prévu et, à la place, la commande « list files » suivante affiche la taille 0 ou un ancien horodatage. Il s’agit d’un problème connu en raison d’une mise en cache incohérente des valeurs de métadonnées de fichier lorsque le fichier est ouvert. Vous pouvez utiliser l’une des solutions de contournement suivantes pour résoudre ce problème :

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>Solution de contournement 1 : Pour récupérer la taille du fichier, utilisez wc -c au lieu de ls -l
L’utilisation de wc -c récupère toujours la valeur la plus récente à partir du serveur et n’entraîne pas d’incohérence.

#### <a name="workaround-2-use-noac-mount-flag"></a>Solution de contournement 2 : Utiliser l’indicateur de montage « noac »
Remontez le système de fichiers à l’aide de l’indicateur « noac » avec votre commande mount. Cette opération récupère toujours toutes les valeurs de métadonnées du serveur. Il peut y avoir une surcharge de performances mineure pour toutes les opérations de métadonnées si cette solution de contournement est utilisée.


## <a name="unable-to-mount-an-nfs-share-that-is-restored-back-from-soft-deleted-state"></a>Impossible de monter un partage NFS restauré à partir d’un état de suppression réversible
La préversion présente un problème connu, à savoir que les partages NFS sont supprimés de manière réversible bien que la plateforme ne le prenne pas en charge. Ces partages seront supprimés lors de son expiration. Vous pouvez également les supprimer manuellement à l’aide du flux « annuler la suppression du partage + désactiver la suppression réversible + supprimer le partage ». Cela étant, si vous tentez de récupérer et d’utiliser les partages, vous recevrez une erreur d’accès refusé, d’autorisation refusée ou une erreur d’E/S NFS sur le client.

## <a name="ls-la-throws-io-error"></a>La commande ls –la lève une erreur d’E/S

### <a name="cause-a-known-bug-that-has-been-fixed-in-newer-linux-kernel"></a>Cause : bogue connu qui a été résolu dans un noyau Linux plus récent
Sur des noyaux plus anciens, NFS4ERR_NOT_SAME provoque l’arrêt de l’énumération du client (au lieu d’un redémarrage pour le répertoire). Cela pourrait débloquer immédiatement des noyaux plus récents mais, malheureusement, pour des distributions comme SUSE, il n’existe aucun correctif pour SUSE Enterprise Linux Server 12 ou 15 susceptible de mettre à jour le noyau pour ce correctif.  Le correctif est disponible dans le noyau 5.12+.  Le correctif pour le côté client est décrit ici [PATCH v3 15/17 NFS: Handle NFS4ERR_NOT_SAME and NFSERR_BADCOOKIE from readdir calls](https://www.spinics.net/lists/linux-nfs/msg80096.html).

#### <a name="workaround-use-latest-kernel-workaround-while-the-fix-reaches-the-region-hosting-your-storage-account"></a>Solution de contournement : utilisez la dernière solution de contournement du noyau pendant que le correctif atteint la région hébergeant votre compte de stockage.
Le correctif est disponible dans le noyau 5.12+.

## <a name="ls-hangs-for-large-directory-enumeration-on-some-kernels"></a>ls suspend une énumération de répertoire de grande taille sur certains noyaux

### <a name="cause-a-bug-was-introduced-in-linux-kernel-v511-and-was-fixed-in-v5125"></a>Cause : un bogue a été introduit dans le noyau Linux v 5.11 et a été corrigé dans v 5.12.5.  
Certaines versions du noyau contiennent un bogue qui a pour effet que les affichages de listes de répertoires entraînent des séquences de READDIR sans fin. Les répertoires très petits dans lesquels toutes les entrées peuvent être expédiées en un seul appel ne présentent pas le problème.
Le bogue a été introduit dans le noyau Linux v 5.11 et a été corrigé dans v 5.12.5. Ainsi, toutes les versions intermédiaires contiennent le bogue. RHEL 8.4 est connu pour avoir cette version de noyau.

#### <a name="workaround-downgrading-or-upgrading-the-kernel"></a>Solution de contournement : mise à niveau du noyau vers une version antérieure ou ultérieure
La rétrogradation ou la mise à niveau du noyau vers quoi que ce soit en dehors du noyau affecté permet de résoudre le problème.

## <a name="df-and-find-command-shows-inconsistent-results-on-clients-other-than-where-the-writes-happen"></a>Les commandes df et find affichent des résultats incohérents sur des clients autres que ceux où les écritures ont lieu
Il s’agit d’un problème connu. Microsoft travaille activement à sa résolution.

## <a name="application-fails-with-error-underlying-file-changed-by-an-external-force-when-using-exclusive-open"></a>L’application échoue et affiche un message d’erreur indiquant qu’un fichier sous-jacent a été modifié par une force externe lors de l’utilisation d’une OUVERTURE exclusive 
Il s’agit d’un problème connu. Microsoft travaille activement à sa résolution.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez encore besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour résoudre rapidement votre problème.
