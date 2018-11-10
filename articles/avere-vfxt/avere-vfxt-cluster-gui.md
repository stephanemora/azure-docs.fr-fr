---
title: Accéder au Panneau de configuration Avere vFXT - Azure
description: Comment se connecter au cluster vFXT et au Panneau de configuration Avere basé sur le navigateur pour configurer Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: c48f0d8f7ad34db585f4deae566641b6453357e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670056"
---
# <a name="access-the-vfxt-cluster"></a>Accéder au cluster vFXT

Pour modifier les paramètres et superviser le cluster Avere vFXT, utilisez le Panneau de configuration Avere. Le Panneau de configuration Avere est une interface graphique basée sur le navigateur pour le cluster.

Étant donné que le cluster vFXT se situe dans un réseau virtuel privé, vous devez créer un tunnel SSH ou utiliser une autre méthode pour atteindre l’adresse IP de gestion du cluster. Il existe deux étapes de base : 

1. Créer une connexion entre votre station de travail et le réseau virtuel privé 
1. Utiliser l’adresse IP de gestion du cluster pour charger le Panneau de configuration dans un navigateur web 

> [!NOTE] 
> Cet article suppose que vous avez défini une adresse IP publique sur le contrôleur de cluster ou sur une autre machine virtuelle à l’intérieur du réseau virtuel de votre cluster. Si vous utilisez un VPN ou ExpressRoute pour l’accès au réseau virtuel, passez à [Se connecter au Panneau de configuration Avere](#connect-to-the-avere-control-panel-in-a-browser).

Avant de vous connecter, vérifiez que la paire de clés publique/privée SSH que vous avez utilisée lors de la création du contrôleur de cluster est installée sur votre ordinateur local. Lisez la documentation sur les clés SSH pour [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) ou pour [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) si vous avez besoin d’aide.  

## <a name="access-with-a-linux-host"></a>Accès avec un hôte Linux

sous cette forme : 

ssh -L *port_local*:*ip_gestion_cluster*:443 *nom_utilisateur_contrôleur*@*IP_publique_contrôleur* 

Cette commande se connecte à l’adresse IP de gestion du cluster via l’adresse IP du contrôleur de cluster.

Exemple :

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

L’authentification est automatique si vous avez utilisé votre clé publique SSH pour créer le cluster et la clé correspondante est installée sur le système client.


## <a name="access-with-a-windows-host"></a>Accès avec un hôte Windows

Si vous utilisez PuTTY, renseignez le champ **nom d’hôte** avec le nom d’utilisateur du contrôleur de cluster et son adresse IP : *votre_nom_utilisateur*@*IP_publique_contrôleur*.

Exemple : ``azureuser@203.0.113.51``

Dans le panneau **Configuration** :

1. Développez **Connexion** > **SSH** sur la gauche. 
1. Cliquez sur **Tunnels**. 
1. Entrez un port source, comme 8443. 
1. Pour la destination, entrez l’adresse IP de gestion du cluster vFXT et le port 443. 
   Exemple : ``203.0.113.51:443``
1. Cliquez sur **Add**.
1. Cliquez sur **Ouvrir**.

![Capture d’écran de l’application Putty montrant où cliquer pour ajouter un tunnel](media/avere-vfxt-ptty-numbered.png)

L’authentification est automatique si vous avez utilisé votre clé publique SSH pour créer le cluster et la clé correspondante est installée sur le système client.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Se connecter au Panneau de configuration Avere dans un navigateur

Cette étape utilise un navigateur web pour se connecter à l’utilitaire de configuration en cours d’exécution sur le cluster vFXT.

Ouvrez votre navigateur web et accédez à https://127.0.0.1:8443. 

En fonction de votre navigateur, vous devrez peut-être cliquer sur **Avancé** et vérifier que vous pouvez accéder à la page sans risque.

Entrez le nom d’utilisateur `admin` et le mot de passe que vous avez fournis lors de la création du cluster.

![Capture d’écran de la page de connexion Avere renseignée avec le nom d’utilisateur « admin » et un mot de passe](media/avere-vfxt-gui-login.png)

Cliquez sur **Connexion** ou appuyez sur Entrée sur votre clavier.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous pouvez accéder au cluster, activez la [prise en charge](avere-vfxt-enable-support.md).
