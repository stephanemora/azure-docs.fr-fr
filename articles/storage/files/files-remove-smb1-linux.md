---
title: Sécuriser les environnements Azure et locaux en supprimant SMB 1 de Linux | Microsoft Docs
description: Azure Files prend en charge SMB 3.x et SMB 2.1, mais pas les versions héritées de SMB comme SMB 1. Avant de vous connecter à un partage de fichiers Azure, nous vous conseillons de désactiver les anciennes versions de SMB comme SMB 1.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8f0e75ebf45839eb75f22a8fb46e76bdebec4688
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124706"
---
# <a name="remove-smb-1-on-linux"></a>Supprimer SMB 1 de Linux
De nombreuses organisations et autres fournisseurs de services Internet bloquent le port dont se sert SMB pour communiquer, à savoir le port 445. Cette pratique résulte des recommandations de sécurité concernant les versions héritées et déconseillées du protocole SMB. Même si SMB 3.x est un protocole sécurisé pour Internet, les versions antérieures de SMB, en particulier SMB 1, ne le sont pas. SMB 1, aussi appelé CIFS, est intégré à de nombreuses distributions Linux. 

SMB 1 est un protocole obsolète, inefficace et qui pose des problèmes de sécurité. La bonne nouvelle, c’est qu’Azure Files ne prend pas en charge SMB 1 et qu’à partir de la version 4.18 de son noyau, Linux permet de désactiver SMB 1. Dans tous les cas, nous vous [recommandons vivement](https://aka.ms/stopusingsmb1) de désactiver SMB 1 sur vos clients Linux avant d’utiliser des partages de fichiers SMB en production.

## <a name="linux-distribution-status"></a>État des distributions Linux
À partir de la version 4.18 du noyau Linux, le module de noyau SMB, appelé `cifs` pour des raisons d’héritage, expose un nouveau paramètre de module (souvent appelé *param* par diverses documentations externes) sous le nom `disable_legacy_dialects`. Bien qu’ils aient été introduits dans la version 4.18 du noyau Linux, certains fournisseurs ont rétroporté cette modification sur des noyaux plus anciens qu’ils prennent en charge. Pour des raisons pratiques, le tableau suivant détaille la disponibilité de ce paramètre de module sur les distributions Linux courantes.

| Distribution | Possibilité de désactivation de SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | Non |
| Ubuntu 18.04 | Oui |
| Ubuntu 19.04+ | Oui |
| Debian 8-9 | Non |
| Debian 10+ | Oui |
| Fedora 29+ | Oui |
| CentOS 7 | Non | 
| CentOS 8+ | Oui |
| Red Hat Enterprise Linux 6.x-7.x | Non |
| Red Hat Enterprise Linux 8+ | Oui |
| openSUSE Leap 15.0 | Non |
| openSUSE Leap 15.1+ | Oui |
| openSUSE Tumbleweed | Oui |
| SUSE Linux Enterprise 11.x-12.x | Non |
| SUSE Linux Enterprise 15 | Non |
| SUSE Linux Enterprise 15.1 | Non |

Vous pouvez vérifier si votre distribution Linux prend en charge le paramètre de module `disable_legacy_dialects` via la commande suivante :

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Cette commande doit générer le message suivant :

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

## <a name="remove-smb-1"></a>Supprimer SMB 1
Avant de désactiver SMB 1, vérifiez que le module SMB n’est pas chargé sur votre système (ce qui se produit automatiquement si vous avez monté un partage SMB). Pour cela, vous pouvez utiliser la commande suivante, qui ne doit générer aucun résultat si SMB n’est pas chargé :

```bash
lsmod | grep cifs
```

Pour décharger le module, commencez par démonter tous les partages SMB (à l’aide de la commande `umount` comme décrit ci-dessus). Vous pouvez identifier tous les partages SMB montés sur votre système à l’aide de la commande suivante :

```bash
mount | grep cifs
```

Une fois que vous avez démonté tous les partages de fichiers SMB, vous pouvez décharger le module sans risque. Pour cela, utilisez la commande `modprobe` :

```bash
sudo modprobe -r cifs
```

Vous pouvez charger manuellement le module avec SMB 1 déchargé à l’aide de la commande `modprobe` :

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Enfin, vous pouvez vérifier que le module SMB a bien été chargé avec le paramètre en examinant les paramètres chargés dans `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Pour désactiver de façon permanente SMB 1 sur les distributions Ubuntu et Debian, vous devez créer un fichier (si vous n’avez pas déjà d’options personnalisées pour d’autres modules) sous le nom `/etc/modprobe.d/local.conf` avec le paramètre. Pour ce faire, vous pouvez utiliser la commande suivante :

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Vous pouvez vérifier que cela a fonctionné en chargeant le module SMB :

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Étapes suivantes
Consultez ces liens pour en savoir plus sur Azure Files :

- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [Utiliser Azure Files avec Linux](storage-how-to-use-files-linux.md)
- [Dépannage](storage-troubleshoot-linux-file-connection-problems.md)