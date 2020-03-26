---
title: 'Tutoriel : Initialiser le matériel - Azure FXT Edge Filer'
description: Guide pratique pour définir un mot de passe initial sur les nœuds Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550889"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutoriel : Définir des mots de passe pour le matériel

La première fois que vous mettez sous tension un nœud Azure FXT Edge Filer, vous devez définir un mot de passe d’utilisateur racine. Les nœuds de matériel ne sont pas livrés avec un mot de passe par défaut. 

Les ports réseau sont désactivés uniquement une fois que le mot de passe a été défini et que l’utilisateur racine s’est connecté.

Effectuez cette étape après l’installation et le câblage du nœud, mais avant de créer le cluster. 

Ce tutoriel explique comment se connecter au nœud de matériel et définir le mot de passe. 

Dans ce didacticiel, vous apprendrez à : 

> [!div class="checklist"]
> * Connecter un clavier et un moniteur au nœud et le mettre sous tension
> * Définir des mots de passe pour l’utilisateur racine et le port iDRAC sur ce nœud
> * Se connecter en tant qu’utilisateur racine 

Répétez ces étapes pour chaque nœud que vous utilisez dans votre cluster. 

Ce tutoriel prend environ 15 minutes. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, procédez comme suit : 

* [Installez](fxt-install.md) chaque nœud Azure FXT Edge Filer dans un équipement monté en rack et associez les câbles d’alimentation et l’accès réseau comme décrit dans le [tutoriel précédent](fxt-network-power.md). 
* Trouvez un clavier connecté par USB et un moniteur VGA que vous pouvez attacher aux nœuds de matériel. (Le port série du nœud est inactif avant de définir le mot de passe).

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Connecter un clavier et un moniteur au nœud

Connectez physiquement un moniteur et un clavier au nœud Azure FXT Edge Filer. 

* Connectez le moniteur au port VGA.
* Connectez le clavier à un des ports USB. 

Utilisez ce diagramme de référence pour localiser les ports à l’arrière du châssis. 

> [!NOTE]
> Le port série est inactif tant qu’aucun mot de passe n’a été défini. 

![diagramme montrant l’arrière d’Azure FXT Edge Filer avec les ports série, VGA et USB étiquetés](media/fxt-back-serial-vga-usb.png)

Vous pouvez utiliser un commutateur KVM si vous souhaitez connecter plusieurs nœuds aux mêmes périphériques. 

Mettez sous tension le nœud en appuyant sur le bouton d’alimentation à l’avant. 

![diagramme de l’avant du Azure FXT Edge Filer - le bouton d’alimentation arrondi est étiqueté en haut à droite](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Définir des mots de passe initiaux 

Le nœud Azure FXT Edge Filer imprime différents messages sur le moniteur au démarrage. Après quelques instants, il affiche un écran de configuration initiale comme celui-ci :

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Le mot de passe que vous entrez est utilisé pour deux choses : 

* Il s’agit du mot de passe racine temporaire pour ce nœud Azure FXT Edge Filer. 

  Ce mot de passe change lorsque vous créez un cluster à l’aide de ce nœud, ou lorsque vous ajoutez ce nœud au cluster. Le mot de passe de gestion de cluster (associé à l’utilisateur ``admin``) est également le mot de passe racine de tous les nœuds dans un cluster.

* Il s’agit du mot de passe à long terme pour le port de gestion du matériel iDRAC/IPMI.

  Pensez à mémoriser le mot de passe au cas où vous devez vous connecter avec IPMI ultérieurement pour résoudre un problème matériel.

Entrez puis confirmez le mot de passe : 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Une fois que vous entrez le mot de passe, le système continue le démarrage. Une fois cette opération terminée, le système affiche une invite ``login:``. 

## <a name="sign-in-as-root"></a>Se connecter en tant qu’utilisateur racine

Connectez-vous en tant que ``root`` avec le mot de passe que vous venez de définir. 

```
login: root
Password:**********
```

Une fois que vous êtes connecté en tant qu’utilisateur racine, les ports réseau sont actifs et contactent le serveur DHCP pour les adresses IP. 

## <a name="next-steps"></a>Étapes suivantes

Le nœud est prêt à faire partie d’un cluster. Vous pouvez l’utiliser pour créer le cluster Azure FXT Edge Filer, ou vous pouvez [l’ajouter à un cluster existant](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Créer un cluster](fxt-cluster-create.md)
