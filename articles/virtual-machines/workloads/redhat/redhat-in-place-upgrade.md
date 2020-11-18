---
title: Mise à niveau sur place des images Red Hat Enterprise Linux sur Azure
description: Découvrez les étapes à suivre pour effectuer une mise à niveau sur place des images Red Hat Enterprise 7.x vers la version 8.x la plus récente
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 48884e6faa5f26f027c772b44d5f960979a40d1d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447454"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Mises à niveau sur place de Red Hat Enterprise Linux

Cet article fournit des instructions pas à pas sur l’exécution d’une mise à niveau sur place de Red Hat Enterprise Linux 7 vers Red Hat Enterprise Linux 8 à l’aide de l’utilitaire « Leapp » sur Azure. Au cours de la mise à niveau sur place, le système d’exploitation RHEL 7 existant est remplacé par la version RHEL 8.

>[!Note] 
> Les offres SQL Server sur Red Hat Enterprise Linux ne prennent pas en charge la mise à niveau sur place sur Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Ce qu’il faut attendre pendant la mise à niveau
Le système redémarre plusieurs fois pendant la mise à niveau, ce qui est normal. Le dernier redémarrage met à niveau la machine virtuelle vers la version mineure RHEL 8 la plus récente.

## <a name="preparations-for-the-upgrade"></a>Préparations pour la mise à niveau
Les mises à niveau sur place sont la méthode officiellement recommandée par Red Hat et Azure pour permettre aux clients de mettre à niveau votre système vers la version majeure suivante. Avant d’effectuer la mise à niveau, voici quelques points à prendre en compte. 

>[!Important] 
> Prenez un instantané de l’image avant d’effectuer la mise à niveau.

>[!NOTE]
> Les commandes de cet article doivent être exécutées à l’aide du compte racine

* Assurez-vous que vous utilisez la dernière version de RHEL 7, qui est actuellement RHEL 7.9. Si vous utilisez une version verrouillée et que vous ne pouvez pas effectuer la mise à niveau vers RHEL 7.9, vous pouvez utiliser les [étapes ici pour basculer vers un référentiel non EUS](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Exécutez la commande ci-dessous pour savoir comment se passe votre mise à niveau et si elle va se terminer correctement. La commande doit générer un fichier sous '/var/log/leapp/leapp-report.txt' qui explique le processus, ce qui est effectué et si la mise à niveau est possible ou non
    ```bash
    leapp preupgrade --no-rhsm
    ```

## <a name="steps-for-performing-the-upgrade"></a>Étapes pour effectuer la mise à niveau

Exécutez ces étapes avec précaution. Il est recommandé de tester la mise à niveau sur un ordinateur de test avant les instances de production.

1. Effectuez une mise à jour yum pour récupérer les packages clients les plus récents.
    ```bash
    yum update
    ```

1. Installez leapp-client-package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Utilisez le fichier leapp-data.tar.gz avec repomap.csv et pes-events.json, présents dans le [portail RedHat](https://access.redhat.com/articles/3664871) et extrayez-les. 
    1. Téléchargez le fichier.
    1. Extrayez le contenu et supprimez le fichier à l’aide de la commande suivante :
    ```bash
     tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```
    


1. Ajoutez le fichier 'answers' pour 'Leapp'.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ```
    
1. Activez PermitRootLogin in /etc/ssh/sshd_config
    1. Ouvrez le fichier /etc/ssh/sshd_config
    1. Recherchez '#PermitRootLogin yes'
    1. Supprimez le signe '#' pour supprimer les marques de commentaire



1. Effectuez la mise à niveau 'Leapp'.
    ```bash
    leapp upgrade --no-rhsm
    ```
1. Redémarrez le service sshd pour que les modifications prennent effet
    ```bash
    systemctl restart sshd
    ```
1. Commentez PermitRootLogin in /etc/ssh/sshd_config à nouveau
    1. Ouvrez le fichier /etc/ssh/sshd_config
    1. Recherchez '#PermitRootLogin yes'
    1. Ajoutez le signe '#' au commentaire

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [images Red Hat dans Azure](./redhat-images.md).
* En savoir plus sur l’[infrastructure RHUI (Red Hat Update Infrastructure)](./redhat-rhui.md).
* En savoir plus sur l’[offre BYOS RHEL](./byos.md).
* Pour plus d’informations sur les processus de mise à niveau sur place de Red Hat, consultez la documentation de Red Hat, [Mise à niveau de RHEL 7 à RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).