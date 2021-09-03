---
title: 'Informations de référence : Problèmes connus et dépannage'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenez la liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: michalmar
ms.author: mimarusa
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: c928dfa133172f83c097aa9df7d92486524f62ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531719"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problèmes connus et dépannage d’Azure Data Science Virtual Machine

Cet article vous aide à rechercher et corriger les erreurs ou les défaillances que vous pouvez rencontrer lors de l’utilisation d’Azure Data Science Virtual Machine.


## <a name="ubuntu"></a>Ubuntu

### <a name="fix-gpu-on-nvidia-a100-gpu-chip---azure-ndasrv4-series"></a>Corriger le GPU sur le processeur du GPU NVIDIA A100 - série Azure NDasrv4 

La série de machines virtuelles A100 v4 est un nouvel ajout à la famille de GPU Azure, conçue pour un apprentissage Deep Learning de pointe et des de charges de travail HPC de montée en puissance parallèle et montée en charge.

En raison de l’architecture différente, elle nécessite une configuration différente pour vos charges de travail exigeantes afin de tirer parti de l’accélération GPU à l’aide d’infrastructures TensorFlow ou PyTorch.

Nous travaillons à la prise en charge des GPU de machines ND A100 prêtes à l’emploi. Pendant ce temps, vous pouvez faire fonctionner votre GPU en ajoutant Fabric Manager de NVIDIA et en mettant à jour les pilotes. 

Suivez ces étapes simples dans Terminal :

1. Ajoutez le référentiel de NVIDIA pour installer/mettre à jour les pilotes - vous trouverez des instructions pas à pas [ici](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts)
2. [FACULTATIF] Vous pouvez également mettre à jour vos pilotes CUDA (à partir du référentiel ci-dessus)
3. Installez les pilotes Fabric Manager de NVIDIA :

    ```
    sudo apt-get install cuda-drivers-460
    sudo apt-get install cuda-drivers-fabricmanager-460
    ```

4. Redémarrez votre machine virtuelle (pour préparer vos pilotes)
5. Activez et démarrez le service NVIDIA Fabric Manager nouvellement installé :

    ```
    sudo systemctl enable nvidia-fabricmanager
    sudo systemctl start nvidia-fabricmanager
    ```

Vous pouvez maintenant vérifier le fonctionnement de vos pilotes et de votre GPU en exécutant :
```
systemctl status nvidia-fabricmanager.service
``` 

Après quoi, vous devriez voir le service Fabric Manager s’exécuter ![nvidia-fabric-manager-status](./media/nvidia-fabricmanager-status-ok-marked.png)


### <a name="connection-to-desktop-environment-fails"></a>Échec de la connexion à l’environnement de bureau

Si vous pouvez vous connecter au terminal DSVM via SSH, mais pas à x2go, vous avez peut-être défini un type de session incorrect dans x2go.
Pour vous connecter à l’environnement de bureau de DSVM, vous avez besoin que le type de session dans *x2go/session preferences/session* soit défini sur *XFCE*. Les autres environnements de bureau ne sont actuellement pas pris en charge.

### <a name="fonts-look-wrong-when-connecting-to-dsvm-using-x2go"></a>Les polices semblent incorrectes lors de la connexion à DSVM à l’aide de x2go

Quand vous vous connectez à x2go et que certaines polices semblent incorrectes, cela peut être lié à un paramètre de session dans x2go. Avant de vous connecter à DSVM, désactivez la case à cocher « Définir la résolution d’affichage » sous l’onglet « Entrée/sortie » de la boîte de dialogue des préférences de session.

### <a name="prompted-for-unknown-password"></a>Vous êtes invité à entrer un mot de passe inconnu

Lorsque vous créez un paramètre DSVM *Type d’authentification* sur *Clé publique SSH* (ce qui est recommandé plutôt que l’utilisation de l’authentification par mot de passe), aucun mot de passe ne vous est octroyé. Toutefois, dans certains scénarios, certaines applications continueront à vous demander un mot de passe. Exécutez `sudo passwd <user_name>` pour créer un nouveau mot de passe pour un utilisateur spécifique. Avec `sudo passwd`, vous pouvez créer un nouveau mot de passe pour l’utilisateur racine.

L’exécution de ces commandes ne modifie pas la configuration de SSH, et les mécanismes de connexion autorisés seront conservés. 

### <a name="prompted-for-password-when-running-sudo-command"></a>Vous êtes invité à entrer un mot de passe lors de l’exécution de la commande sudo

Lors de l’exécution d’une commande `sudo` sur une machine Ubuntu, il se peut que vous soyez invité à entrer votre mot de passe à plusieurs reprises pour confirmer que vous êtes bien l’utilisateur connecté. Ce comportement est normal, et c’est le comportement par défaut dans Ubuntu. Toutefois, dans certains cas, une authentification à répétition n’est pas nécessaire et plutôt ennuyeuse.

Afin de désactiver la réauthentification dans la plupart des cas, vous pouvez exécuter la commande suivante dans un terminal.

 `echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

Après redémarrage du terminal, sudo ne demande plus d’ID de connexion, considérant que l’authentification effectuée lors de l’ouverture de la session suffit.

### <a name="cannot-use-docker-as-non-root-user"></a>Impossible d’utiliser Docker en tant qu’utilisateur non racine

Pour pouvoir utiliser Docker en tant qu’utilisateur non-racine, votre utilisateur doit être membre du groupe Docker. Vous pouvez exécuter la commande `getent group docker` pour vérifier les utilisateurs qui appartiennent à ce groupe. Pour ajouter votre utilisateur au groupe Docker, exécutez `sudo usermod -aG docker $USER`.

### <a name="docker-containers-cannot-interact-with-the-outside-via-network"></a>Les conteneurs Docker ne peuvent pas interagir avec l’extérieur via le réseau

Par défaut, Docker ajoute de nouveaux conteneurs à ce qui est appelé « réseau pont », qui est `172.17.0.0/16`. Si le sous-réseau de ce réseau pont chevauche le sous-réseau de votre DSVM ou un autre sous-réseau privé que vous avez dans votre abonnement, aucune communication réseau entre l’hôte et le conteneur n’est possible. Dans ce cas, les applications web exécutées dans le conteneur ne peuvent pas être atteintes, et le conteneur ne peut pas mettre à jour les packages à partir d’apt.

Pour résoudre ce problème, vous devez reconfigurer Docker pour qu’il utilise un espace d’adresses IP pour son réseau pont qui ne chevauche pas les autres réseaux de votre abonnement. Par exemple, en ajoutant

```json
"default-address-pools": [
        {
            "base": "10.255.248.0/21",
            "size": 21
        }
    ]
```

dans le document JSON figurant dans le fichier `/etc/docker/daemon.json`, Docker attribue un autre sous-réseau au réseau pont. (Le fichier doit être modifié à l’aide de sudo, par exemple en exécutant `sudo nano /etc/docker/daemon.json`.)

Après la modification, le service Docker doit être redémarré en exécutant `service docker restart`.

Pour vérifier si vos modifications ont pris effet, vous pouvez exécuter `docker network inspect bridge`. La valeur sous *IPAM.Config.Subnet* doit correspondre au pool d’adresses spécifié ci-dessus.

### <a name="gpus-not-available-in-docker-container"></a>GPU non disponible(s) dans le conteneur Docker

Le docker installé sur le DSVM prend en charge les GPU par défaut. Toutefois, il existe quelques conditions préalables qui doivent être satisfaites.

* De toute évidence, la taille de machine virtuelle du DSVM doit inclure au moins un GPU.
* Lors du démarrage de votre conteneur Docker avec `docker run`, vous devez ajouter un paramètre *--gpus*, par exemple, `--gpus all`.
* Les tailles de machine virtuelle qui incluent des GPU NVIDIA A100 nécessitent l’installation de packages logiciels supplémentaires, en particulier [Fabric Manager de NVIDIA](https://docs.nvidia.com/datacenter/tesla/pdf/fabric-manager-user-guide.pdf). Il se peut que ces packages ne soient pas encore préinstallés dans votre image.


## <a name="windows"></a>Windows

### <a name="accessing-sql-server"></a>Accès à SQL Server

Lorsque vous tentez de vous connecter à l’instance SQL Server préinstallée, il se peut que vous rencontriez une erreur d’échec d’ouverture de session. Pour vous connecter à l’instance SQL Server, vous devez exécuter le programme avec lequel vous vous connectez, par exemple, SQL Server Management Studio (SSMS) en mode administrateur. Le mode administrateur est requis parce, en raison du paramétrage par défaut de l’environnement DSVM, seuls des administrateurs sont autorisés à se connecter.

### <a name="hyper-v-does-not-work"></a>Hyper-V ne fonctionne pas

Pour le moment, Hyper-V ne fonctionne pas sur Windows. Pour garantir les performances au démarrage, nous avons désactivé certains services.
Pour activer Hyper-V :

1. Ouvrez la barre de recherche sur votre DSVM Windows.
1. Saisissez « Services ».
1. Définissez tous les services Hyper-V sur « Manual ».
1. Définissez « Hyper-V Virtual Machine Management » sur « Automatic ».

Votre écran final doit ressembler à ceci :

   

![Activer Hyper-V](./media/workaround/hyperv-enable-dsvm.png)
