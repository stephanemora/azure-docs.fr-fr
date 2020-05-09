---
title: Problèmes d’utilisation des extensions de machine virtuelle dans les systèmes de machines virtuelles Linux Azure compatibles Python 3
description: Découvrez comment utiliser des extensions de machine virtuelle dans les systèmes Linux compatibles Python 3
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82120612"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problèmes d’utilisation des extensions de machine virtuelle dans les systèmes de machines virtuelles Linux Azure compatibles Python 3

> [!NOTE]
> Microsoft encourage les utilisateurs à adopter **Python 3.x** dans leurs systèmes, sauf si votre charge de travail nécessite la prise en charge de **Python 2.x**. Des exemples de cette exigence peuvent inclure des scripts d’administration hérités ou des extensions comme **Azure Disk Encryption** et **Azure Monitor**.
>
> Avant d’installer **Python 2.x** en production, prenez en compte la question de sa prise en charge à long terme, en particulier la capacité à recevoir des mises à jour de sécurité. Comme des produits, notamment certaines des extensions mentionnées, sont mis à jour avec la prise en charge de **Python 3.8**, vous devez cesser d’utiliser Python 2.x.

Certaines distributions Linux ont été transférées vers Python 3.8 et ont supprimé complètement le point d’entrée `/usr/bin/python` hérité pour Python. Cette transition a un impact sur le déploiement automatisé et prêt à l’emploi de certaines extensions de machine virtuelle avec les conditions suivantes :

- Extensions toujours en cours de transition vers la prise en charge de Python 3.x
- Extensions qui utilisent le point d’entrée `/usr/bin/python` hérité

Les utilisateurs de la distribution Linux qui ont effectué la transition vers **Python 3.x** doivent vérifier l’existence du point d’entrée `/usr/bin/python` hérité avant de tenter de déployer ces extensions sur leurs machines virtuelles. Dans le cas contraire, le déploiement de l’extension risque d’échouer. 

- Les distributions Linux approuvées qui sont affectées sont notamment **Ubuntu Server 20.04 LTS** et **Ubuntu Pro 20.04 LTS**.

- Les extensions de machine virtuelle affectées incluent **Azure Disk Encryption**, **Log Analytics**, **Accès à la machine virtuelle** (pour la réinitialisation de mot de passe) et **Diagnostics du système d’exploitation invité** (pour des compteurs de performances supplémentaires).

Les mises à niveau sur place, telles que la mise à niveau d’**Ubuntu 18.04 LTS** vers **Ubuntu 20.04 LTS**, doivent conserver le lien symbolique `/usr/bin/python` et ne sont pas affectées.

## <a name="resolution"></a>Résolution

Tenez compte des recommandations générales suivantes avant de déployer des extensions dans les scénarios connus décrits précédemment dans le résumé :

1.  Avant de déployer l’extension, rétablissez le lien symbolique `/usr/bin/python` à l’aide de la méthode indiquée par le fournisseur de la distribution Linux.

    - Par exemple, pour **Python 2.7**, utilisez : `sudo apt update && sudo apt install python-is-python2`

2.  Si vous avez déjà déployé une instance qui présente ce problème, utilisez la fonctionnalité **Exécuter la commande** dans le **panneau de la machine virtuelle** pour exécuter les commandes mentionnées ci-dessus. L’extension Exécuter la commande elle-même n’est pas affectée par la transition vers Python 3.8.

3.  Si vous déployez une nouvelle instance et que vous devez définir une extension au moment du provisionnement, utilisez les données utilisateur **cloud-init** pour installer les packages mentionnés ci-dessus.

    Par exemple, pour Python 2.7 :

    ```
    # create cloud-init config
    cat > cloudinitConfig.json <<EOF
    #cloud-config
    package_update: true
    
    runcmd:
    - sudo apt update
    - sudo apt install python-is-python2 
    EOF
    
    # create VM
    az vm create \
        --resource-group <resourceGroupName> \
        --name <vmName> \
        --image <Ubuntu 20.04 Image URN> \
        --admin-username azadmin \
        --ssh-key-value "<sshPubKey>" \
        --custom-data ./cloudinitConfig.json
    ```

4.  Si les administrateurs de stratégie de votre organisation déterminent que les extensions ne doivent pas être déployées sur des machines virtuelles, vous pouvez désactiver la prise en charge des extensions au moment du provisionnement :

    - API REST

      Pour désactiver et activer des extensions quand vous pouvez déployer une machine virtuelle avec cette propriété :

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, reportez-vous à [Other base system changes since 18.04 LTS - Python 3 by default](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default).
