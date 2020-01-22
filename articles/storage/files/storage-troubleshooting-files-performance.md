---
title: Guide de résolution des problèmes de niveau de performance d’Azure Files
description: Problèmes de niveau de performance connus avec les partages de fichiers Azure et les solutions de contournement associées.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 00187051eec27ee7b6b2d4927510a2ab9dee442e
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708255"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Résoudre les problèmes de niveau de performance d’Azure Files

Cet article répertorie certains problèmes courants liés à des partages de fichiers Azure. Il indique des causes potentielles et des solutions de contournement lorsque ces problèmes surviennent.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Latence élevée, débit faible et problèmes généraux de niveau de performance

### <a name="cause-1-share-experiencing-throttling"></a>Cause 1 : Limitation de bande passante lors du partage

Le quota par défaut sur un partage premium est de 100 Gio, ce qui fournit 100 IOPS de ligne de base (avec un nombre potentiel en rafale pouvant atteindre 300 en une heure). Pour plus d’informations sur l’approvisionnement et sa relation avec les IOPS, consultez la section [Partages approvisionnés](storage-files-planning.md#provisioned-shares) du guide de planification.

Pour confirmer la limitation de votre partage, vous pouvez utiliser les métriques Azure dans le portail.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services**, puis recherchez **Métriques**.

1. Sélectionnez **Métriques**.

1. Sélectionnez votre compte de stockage comme ressource.

1. Sélectionnez **Fichier** comme espace de noms du métrique.

1. Sélectionnez **Transactions** comme métrique.

1. Ajoutez un filtre pour **ResponseType** et vérifiez si toutes les requêtes ont un code de réponse **SuccessWithThrottling** (pour SMB) ou **ClientThrottlingError** (pour REST).

![Options de métriques pour les partages de fichiers premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Pour recevoir une alerte en cas de limitation d’un partage de fichiers, consultez [Comment créer une alerte en cas de limitation d’un partage de fichiers](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Solution

- Augmentez le partage de capacité approvisionnée en spécifiant un quota plus élevé pour votre partage.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Cause 2 : Métadonnées/charge de travail importante de l’espace de noms

Si la majorité de vos demandes sont centrées sur les métadonnées, (telles que createfile/openfile/closefile/queryinfo/querydirectory), la latence sera pire par comparaison avec les opérations de lecture/d’écriture.

Pour confirmer si la plupart de vos demandes sont centrées sur les métadonnées, vous pouvez suivre les mêmes étapes que ci-dessus. Mais, au lieu d’ajouter un filtre pour **ResponseType**, ajoutez-en un pour **Nom de l’API**.

![Filtrez le nom de l’API dans vos métriques](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solution de contournement

- Vérifiez si l’application peut être modifiée pour réduire le nombre d’opérations de métadonnées.
- Ajoutez un disque dur virtuel sur le partage de fichiers et montez le disque dur virtuel sur SMB à partir du client pour effectuer des opérations de fichiers sur les données. Cette approche fonctionne pour les scénarios d’écriture unique et de lecteurs multiples et permet aux opérations de métadonnées d’être locales, ce qui offre des performances similaires à celles d’un stockage en attachement direct local.

### <a name="cause-3-single-threaded-application"></a>Cause 3 : Application à thread unique

Si l’application utilisée par le client est à thread unique, cela peut entraîner un nombre d’IOPS/un débit nettement plus faibles que le nombre maximal possible basé la taille de votre partage provisionné.

### <a name="solution"></a>Solution

- Augmentez le parallélisme de l’application en augmentant le nombre de threads.
- Basculer vers des applications où le parallélisme est possible. Par exemple, pour les opérations de copie, les clients peuvent utiliser AzCopy ou RoboCopy à partir de clients Windows ou la commande **parallèle** commande sur les clients Linux.

## <a name="very-high-latency-for-requests"></a>Latence très élevée pour les requêtes

### <a name="cause"></a>Cause :

la machine virtuelle cliente peut se trouver dans une autre région que le partage de fichiers.

### <a name="solution"></a>Solution

- Exécutez l’application à partir d’une machine virtuelle qui se trouve dans la même région que le partage de fichiers.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Impossible pour le client d’atteindre le débit maximal pris en charge par le réseau

Une cause potentielle est l’absence de prise en charge de plusieurs canaux SMB. Actuellement, les partages de fichiers Azure prend uniquement en charge un canal unique. Par conséquent, il n'y a qu’une connexion entre la machine virtuelle cliente et le serveur. Cette connexion unique étant fixée à un seul processeur sur l’ordinateur virtuel client, le débit maximal réalisable à partir d’une machine virtuelle est lié par un seul cœur.

### <a name="workaround"></a>Solution de contournement

- L’obtention d’une machine virtuelle avec un cœur plus grand peut aider à améliorer le débit.
- L’exécution de l’application cliente à partir de plusieurs machines virtuelles augmente le débit.

- Utilisez les API REST si c’est possible.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Le débit sur les clients Linux est nettement plus faible que sur les clients Windows.

### <a name="cause"></a>Cause :

Il s’agit d’un problème connu d’implémentation de client SMB sur Linux.

### <a name="workaround"></a>Solution de contournement

- Répartissez la charge sur plusieurs machines virtuelles.
- Sur la même machine virtuelle, utilisez plusieurs points de montage avec l’option **nosharesock** et répartissez la charge entre ces points de montage.
- Sur Linux, essayez d’effectuer le montage avec l’option **nostrictsync** pour éviter de forcer le vidage SMB à chaque appel fsync. Pour Azure Files, cette option n’interfère pas avec la cohérence des données, mais elle peut entraîner des métadonnées de fichier obsolètes sur la liste des répertoires (commande **ls -l**). Interrogez directement les métadonnées du fichier (commande **stat**) pour retourner les métadonnées de fichier les plus récentes.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latences élevées pour les charges de travail lourdes de métadonnées impliquant des opérations d’ouverture/de fermeture étendues.

### <a name="cause"></a>Cause :

Absence de prise en charge pour les baux de répertoire.

### <a name="workaround"></a>Solution de contournement

- Si possible, évitez d’ouvrir/de fermer le descripteur dans le même répertoire dans un laps de temps bref.
- Pour les machines virtuelles Linux, augmentez le délai d’expiration du cache du répertoire d’entrée en spécifiant **actimeo =\<sec>** comme option de montage. Par défaut, il est d’une seconde, afin qu’une valeur supérieure comme trois ou cinq puisse vous aider.
- Pour les machines virtuelles Linux, mettez à niveau le noyau à 4.20 ou une version ultérieure.

## <a name="low-iops-on-centosrhel"></a>IOPS faibles sur CentOS/RHEL

### <a name="cause"></a>Cause :

Une profondeur d’E/S supérieure à 1 n’est pas prise en charge sur CentOS/RHEL.

### <a name="workaround"></a>Solution de contournement

- Mettez à niveau vers CentOS 8/RHEL 8.
- Passez à Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Ralentissement des copies de fichiers vers et à partir d’Azure Files dans Linux

Si vous rencontrez des ralentissements lorsque vous copiez des fichiers vers et à partir d’Azure Files, consultez la section [Ralentissement des copies de fichiers vers et à partir d’Azure Files dans Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) du guide de résolution des problèmes de Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Modèle instable/en dents de scie pour les IOPS

### <a name="cause"></a>Cause :

L’application cliente dépasse constamment les IOPS de la ligne de base. Actuellement, il n’y a pas de lissage de la charge de requêtes côté service. Par conséquent, si le client dépasse les IOPS de la ligne de base, il sera limité par le service. De par cette limitation, le client peut être confronté à un modèle d’IOPS instable/en dents de scie. Dans ce cas, les IOPS moyennes obtenues par le client peuvent être inférieures aux IOPS de la ligne de base.

### <a name="workaround"></a>Solution de contournement

- Réduisez la charge de requêtes à partir de l’application cliente afin que le partage ne soit pas limité.
- Augmentez le quota du partage de sorte que le partage ne soit pas limité.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Appels DirectoryOpen/DirectoryClose excessifs

### <a name="cause"></a>Cause :

Si le nombre d’appels DirectoryOpen/DirectoryClose compte parmi les principaux appels d’API et que vous ne pensez pas que le client fera autant d’appels, le problème vient peut-être du logiciel antivirus installé sur la machine virtuelle cliente Azure.

### <a name="workaround"></a>Solution de contournement

- Un correctif pour ce problème est disponible dans la [Mise à jour d’avril de la plateforme pour Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>La création de fichiers est plus lente que prévu

### <a name="cause"></a>Cause :

Des charges de travail qui s’appuient sur la création d’un grand nombre de fichiers ne verront pas une grande différence entre le niveau de performance de partages de fichiers premium et les partages de fichiers standard.

### <a name="workaround"></a>Solution de contournement

- Aucun.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Niveau de performance ralenti à partir de Windows 8.1 ou de Server 2012 R2

### <a name="cause"></a>Cause :

Latence de l’accès à Azure Files pour des charges de travail intensives d’E/S supérieure à celle attendue.

### <a name="workaround"></a>Solution de contournement

- Installez le [correctif logiciel](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1) disponible.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Comment créer une alerte en cas de limitation d’un partage de fichiers

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **Moniteur**. 

2. Cliquez sur **Alertes**, puis sur **+ Nouvelle règle d’alerte**.

3. Cliquez sur **Sélectionner** pour sélectionner la ressource **compte de stockage/file** contenant le partage de fichiers pour lequel vous souhaitez recevoir une alerte, puis cliquez sur **Terminé**. Par exemple, si le nom du compte de stockage est contoso, sélectionnez la ressource contoso/file.

4. Cliquez sur **Ajouter** pour ajouter une condition.

5. Vous verrez une liste de signaux pris en charge pour le compte de stockage ; sélectionnez la métrique **Transactions**.

6. Dans le panneau **Configurer la logique de signal**, accédez à la dimension **Type de réponse**, cliquez sur la liste déroulante **Valeurs de dimension**, puis sélectionnez **SuccessWithThrottling** (pour SMB) ou **ClientThrottlingError** (pour REST). 

  > [!NOTE]
  > Si la valeur de dimension SuccessWithThrottling ou ClientThrottlingError ne figure pas dans la liste, cela signifie que la ressource n’a pas été limitée.  Pour ajouter la valeur de dimension, cliquez sur le signe **+** à côté de la liste déroulante **Valeurs de dimension**, saisissez **SuccessWithThrottling** ou **ClientThrottlingError**, cliquez sur **OK**, puis répétez l’étape n° 6.

7. Accédez à la dimension **Partage de fichiers**, cliquez sur la liste déroulante **Valeurs de dimension**, puis sélectionnez le ou les partages de fichiers pour lesquels vous souhaitez être alerté. 

  > [!NOTE]
  > Si le partage de fichiers est un partage standard, la liste déroulante des valeurs de dimension est vide, car les métriques par partage ne sont pas disponibles pour les partages de fichiers standard. Les alertes de limitation pour les partages de fichiers standard sont déclenchées si un partage de fichiers au sein du compte de stockage est limité et l’alerte n’identifiera pas quel partage de fichiers a été limité. Étant donné que les métriques par partage ne sont pas disponibles pour les partages de fichiers standard, il est recommandé de disposer d’un partage de fichiers par compte de stockage. 

8. Définissez les **paramètres d’alerte** (seuil, opérateur, granularité d’agrégation et fréquence) qui sont utilisés pour évaluer la règle d’alerte de métrique, puis cliquez sur **Terminé**.

  > [!TIP]
  > Si vous utilisez un seuil statique, le graphique des métriques peut vous aider à déterminer un seuil raisonnable si le partage de fichiers est actuellement limité. Si vous utilisez un seuil dynamique, le graphique des métriques affiche les seuils calculés en fonction des données récentes.

9. Ajoutez un **groupe d’actions** (e-mail, SMS, etc.) à l’alerte, soit en sélectionnant un groupe d’actions existant, soit en créant un nouveau groupe d’actions.

10. Renseignez les **détails de l’alerte** (**Nom de règle d’alerte**, **Description** et **Gravité** par exemple).

11. Cliquez sur **Créer une règle d’alerte** pour créer l’alerte.

Pour en savoir plus sur la configuration des alertes dans Azure Monitor, consultez [Vue d’ensemble des alertes dans Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).
