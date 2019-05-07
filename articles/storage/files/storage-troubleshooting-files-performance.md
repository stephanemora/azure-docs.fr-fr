---
title: Performances de fichiers Azure guide de dépannage
description: Performances des problèmes connus avec les partages de fichiers Azure premium (version préliminaire) et les solutions de contournement associées.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190050"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Résoudre les problèmes de performances d’Azure Files

Cet article répertorie certains problèmes courants liés à des partages de fichiers Azure premium (version préliminaire). Il fournit les causes et solutions de contournement lorsque ces problèmes sont produisent.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Une latence élevée, un débit faible et les problèmes de performances générales

### <a name="cause-1-share-experiencing-throttling"></a>Cause 1 : Partager la rencontre de limitation

Le quota par défaut sur un partage est de 100 Go, qui fournit à la ligne de base de 100 e/s (un risque de croître jusqu'à 300 pendant une heure). Pour plus d’informations sur la fourniture et sa relation avec les e/s, consultez le [mis en service des partages](storage-files-planning.md#provisioned-shares) section du guide de planification.

Pour vérifier si votre partage est limité, vous pouvez tirer parti des métriques Azure dans le portail.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Sélectionnez **tous les services** , puis recherchez **métriques**.

1. Sélectionnez **Métriques**.

1. Sélectionnez votre compte de stockage que la ressource.

1. Sélectionnez **fichier** en tant que l’espace de noms métrique.

1. Sélectionnez **Transactions** comme métrique.

1. Ajoutez un filtre pour **ResponseType** et vérifiez si toutes les demandes ont un code de réponse **SuccessWithThrottling**.

![Options de métriques pour les partages de fichiers premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Solution

- Augmentation partagent la capacité allouée en spécifiant un quota plus élevé sur votre partage.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Cause 2 : Charge de travail importante de métadonnées/espace de noms

La majorité de vos demandes de métadonnées centric (telles que createfile/openfile/closefile/queryinfo/querydirectory), la latence est si pire lors de la comparaison pour les opérations de lecture/écriture.

Pour vérifier si la plupart de vos demandes est centrées sur des métadonnées, vous pouvez utiliser les mêmes étapes que ci-dessus. À l’exception au lieu d’ajouter un filtre pour **ResponseType**, ajoutez un filtre pour **nom de l’API**.

![Filtre de nom de l’API dans vos mesures](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solution de contournement

- Vérifiez si l’application peut être modifiée pour réduire le nombre d’opérations de métadonnées.

### <a name="cause-3-single-threaded-application"></a>Cause 3 : Application monothread

Si l’application utilisée par le client est monothread, cela peut entraîner IOPS/débit nettement plus faible que le nombre maximal possible selon la taille de votre partage configuré.

### <a name="solution"></a>Solution

- Augmenter le parallélisme de l’application en augmentant le nombre de threads.
- Basculer vers les applications où le parallélisme est possible. Par exemple, pour les opérations de copie, les clients peuvent utiliser AzCopy ou RoboCopy à partir de clients de Windows ou le **parallèles** commande sur les clients Linux.

## <a name="very-high-latency-for-requests"></a>Latence très élevée pour les demandes

### <a name="cause"></a>Cause :

La machine virtuelle cliente a été trouvée dans une autre région que le partage de fichiers premium.

### <a name="solution"></a>Solution

- Exécutez l’application à partir d’une machine virtuelle qui se trouve dans la même région que le partage de fichiers premium.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Impossible d’atteindre le débit maximal pris en charge par le réseau du client

Une cause potentielle de ce manque fo SMB prise en charge de plusieurs canaux. Actuellement, les partages de fichiers Azure prennent uniquement en charge les monocanal, par conséquent, il n'est qu’une seule connexion à partir de la machine virtuelle cliente au serveur. Cette connexion unique est fixée à un seul processeur sur l’ordinateur virtuel client, donc le débit maximal réalisable à partir d’une machine virtuelle est lié par un seul cœur.

### <a name="workaround"></a>Solution de contournement

- Obtention d’une machine virtuelle avec une plus grande core peut aider à améliorer le débit.
- L’application cliente en cours d’exécution à partir de plusieurs machines virtuelles augmenter le débit.
- Utilisez l’API REST lorsque cela est possible.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Débit sur les clients Linux est nettement plus faible par rapport aux clients de Windows.

### <a name="cause"></a>Cause :

Il s’agit d’un problème connu avec l’implémentation de client SMB sur Linux.

### <a name="workaround"></a>Solution de contournement

- Répartir la charge entre plusieurs machines virtuelles
- Sur la même machine virtuelle, utilisez plusieurs points de montage avec **nosharesock** option et propagation de la charge entre ces points de montage.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latences élevées pour les métadonnées charges de travail impliquant des opérations d’ouverture/fermeture complètes.

### <a name="cause"></a>Cause :

Absence de prise en charge pour les baux de répertoire.

### <a name="workaround"></a>Solution de contournement

- Si possible, évitez le handle d’ouverture/fermeture excessive sur le même répertoire sur une courte période de temps.
- Pour les machines virtuelles Linux, augmenter le délai d’expiration du cache de répertoire entrée en spécifiant **actimeo =<sec>**  comme option de montage. Par défaut, il est une seconde, afin qu’une valeur supérieure à trois ou cinq peut vous aider.
- Pour les machines virtuelles Linux, vous devez mettre à niveau le noyau à 4.20 ou une version ultérieure.

## <a name="low-iops-on-centosrhel"></a>E/s faible sur CentOS/RHEL

### <a name="cause"></a>Cause :

Profondeur d’e/s supérieure à 1 n’est pas pris en charge sur CentOS/RHEL.

### <a name="workaround"></a>Solution de contournement

- Mise à niveau vers CentOS 8 / RHEL 8.
- Modification d’Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Modèle instable dents/de scie pour les e/s

### <a name="cause"></a>Cause :

Application cliente dépasse constamment les e/s de la ligne de base. Actuellement, il n’existe aucun côté service lissage de la charge de la demande, par conséquent, si le client dépasse la ligne de base d’e/s, elle est limitée par le service. Cette limitation peut entraîner le client rencontre un modèle d’e/s instable dents/de scie. Dans ce cas, les e/s moyenne obtenue par le client peut être inférieure à la ligne de base e/s.

### <a name="workaround"></a>Solution de contournement

- Réduire la charge de la demande à partir de l’application cliente, afin que le partage de ne pas limité.
- Augmentez le quota du partage de sorte que le partage de ne pas limité.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Appels DirectoryOpen/DirectoryClose excessive

### <a name="cause"></a>Cause :

Si le nombre d’appels de DirectoryOpen/DirectoryClose est entre les appels d’API supérieures et que vous ne pensez pas le client à faire que le nombre d’appels, il peut être un problème avec le logiciel antivirus installé sur le client Azure VM.

### <a name="workaround"></a>Solution de contournement

- Un correctif pour résoudre ce problème est disponible dans le [avril plateforme mise à jour pour Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Création d’un fichier est plus lente que prévu

### <a name="cause"></a>Cause :

Charges de travail qui s’appuient sur la création d’un grand nombre de fichiers ne verront pas une grande différence entre les performances premium de partages de fichiers et partages de fichiers standard.

### <a name="workaround"></a>Solution de contournement

- Aucune.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Ralentissement des performances à partir de Windows 8.1 ou Server 2012 R2

### <a name="cause"></a>Cause :

Supérieur attendu latence de l’accès aux fichiers Azure pour les charges de travail intensives d’e/s.

### <a name="workaround"></a>Solution de contournement

- Installer le disponible [correctif](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).