---
title: Installation de HANA sur SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Installation de HANA sur SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167608"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Exemple d’installation de SAP HANA sur des grandes instances HANA

Cette section illustre la procédure d’installation de SAP HANA sur une unité de grande instance HANA. L’état de démarrage que nous avons ressemble à ceci :

- Vous avez fourni à Microsoft toutes les données pour déployer pour vous une grande instance SAP HANA.
- Vous avez reçu la grande instance SAP HANA de Microsoft.
- Vous avez créé un réseau virtuel Azure qui est connecté à votre réseau local.
- Vous connectez le circuit ExpressRoute pour les grandes instances HANA au même réseau virtuel Azure.
- Vous avez installé une machine virtuelle Azure que vous utilisez comme serveur intermédiaire pour les grandes instances HANA.
- Vérifiez que vous pouvez vous connecter à partir du serveur intermédiaire à l’unité de grande instance HANA et inversement.
- Vous avez vérifié si tous les packages et correctifs requis sont installés.
- Vous avez lu les notes et la documentation de SAP concernant l’installation de HANA sur le système d’exploitation que vous utilisez, et vérifié que la version de HANA choisie est prise en charge sur la version du système d'exploitation.

Les séquences suivantes présentent le téléchargement des packages d’installation de HANA sur la machine virtuelle faisant office de serveur intermédiaire qui s’exécute en l’occurrence sur un système d’exploitation Windows, la copie des packages vers l’unité de grande instance HANA et la séquence de l’installation.

## <a name="download-of-the-sap-hana-installation-bits"></a>Télécharger les bits d’installation de SAP HANA
Étant donné que les unités de grande instance HANA n’ont pas de connexion directe à Internet, vous ne pouvez pas télécharger les packages d’installation directement de SAP sur la machine virtuelle de grande instance HANA. Pour surmonter le problème d’absence de connexion Internet directe, vous avez besoin du serveur intermédiaire. Vous téléchargez les packages sur la machine virtuelle faisant office de serveur intermédiaire.

Pour télécharger les packages d’installation de HANA, vous avez besoin d’un utilisateur S ou d’un autre utilisateur SAP, qui vous permet d’accéder au Marketplace SAP. Une fois connecté, parcourez cette séquence d’écrans :

Accédez au [Marketplace du Service SAP](https://support.sap.com/en/index.html) > Cliquez sur Download Software (Télécharger un logiciel) > Installations and Upgrade (Installations et mise à niveau) > By Alphabetical Index (par index alphabétique) > Sous H – SAP HANA Platform Edition (Édition de plateforme SAP HANA) > SAP HANA Platform Edition 2.0 (Plateforme SAP HANA Édition 2.0) > Installation > Download the following files (Télécharger les fichiers suivants)

![Télécharger les packages d’installation de HANA](./media/hana-installation/image16_download_hana.PNG)

Dans le cas de la démonstration, nous avons téléchargé les packages d’installation SAP HANA 2.0. Sur la machine virtuelle Azure faisant office de serveur intermédiaire, vous développez les archives à extraction automatique dans le répertoire, comme illustré ci-dessous.

![Extraire les packages d’installation de HANA](./media/hana-installation/image17_extract_hana.PNG)

Les archives étant extraites, copiez le répertoire créé par l’extraction, dans le cas ci-dessus 51052030, vers l’unité de grande instance HANA dans le volume /hana/shared figurant dans un répertoire que vous avez créé.

> [!Important]
> Ne copiez pas les packages d’installation dans la racine ou le LUN de démarrage, car l’espace est limité et doit être utilisé également par d’autres processus.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installer SAP HANA sur l’unité de grande instance HANA
Pour installer SAP HANA, vous devez vous connecter en tant qu’utilisateur racine. Seule la racine dispose d’autorisations suffisantes pour installer SAP HANA.
La première chose à faire consiste à définir des autorisations sur le répertoire que vous avez recopié dans /hana/shared. Les autorisations doivent être définies comme suit :

```
chmod –R 744 <Installation bits folder>
```

Si vous souhaitez installer SAP HANA à l’aide du programme d’installation graphique, le package gtk2 doit être installé sur les grandes instances HANA. Vérifiez si l’installation a réussi avec la commande suivante :

```
rpm –qa | grep gtk2
```

Les étapes suivantes montrent l’installation de SAP HANA avec l’interface utilisateur graphique. En guide d’étape suivante, accédez au répertoire d’installation, puis naviguez dans le sous-répertoire HDB_LCM_LINUX_X86_64. Démarrez

```
./hdblcmgui 
```
en dehors de ce répertoire. Vous êtes à présent guidé dans une séquence d’écrans dans laquelle vous devez fournir les données nécessaires pour l’installation. Dans le cas présenté, nous installons le serveur de base de données SAP HANA et les composants du client SAP HANA. Par conséquent, notre sélection est « SAP HANA Database » (Base de données SAP HANA), comme illustré ci-dessous.

![Sélectionner HANA dans l’installation](./media/hana-installation/image18_hana_selection.PNG)

Dans l’écran suivant, vous choisissez l’option « Install New System » (Installer une nouveau système).

![Sélectionner une nouvelle installation de HANA](./media/hana-installation/image19_select_new.PNG)

Après cette étape, vous devez opérer une sélection parmi plusieurs composants supplémentaires qui peuvent être installés par ailleurs sur le serveur de base de données SAP HANA.

![Sélectionner des composants supplémentaires HANA](./media/hana-installation/image20_select_components.PNG)

Dans cette documentation, nous avons choisi le SAP HANA Client et SAP HANA Studio. Nous avons également installé une instance de montée en puissance parallèle. Par conséquent, dans l’écran suivant, vous devez choisir « Single-Host System » (Système à hôte unique). 

![Sélectionner un installation avec montée en puissance parallèle](./media/hana-installation/image21_single_host.PNG)

Dans l’écran suivant, vous devez fournir des données.

![Fournir le SID de SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> En tant qu’ID système (SID) HANA, vous devez fournir le même SID que celui fourni à Microsoft lors de la commande du déploiement de la grande instance HANA. Le choix d’un SID différent fait échouer l’installation en raison de problèmes d’autorisation d’accès sur les différents volumes.

En tant que répertoire d’installation, vous utilisez /hana/shared. À l’étape suivante, vous devez fournir les emplacements des fichiers de données et des fichiers journaux de HANA.


![Fournir l’emplacement du journal de HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Vous devez définir en tant que données et fichiers journaux les volumes qui accompagnaient déjà les points de montage contenant le SID que vous avez choisi dans la sélection de l’écran précédant cet écran. Si le SID ne correspond pas à celui que vous avez tapé dans l’écran précédent, revenez en arrière et ajustez le SID sur la valeur définie sur les points de montage.

À l’étape suivante, révisez le nom d’hôte et corrigez-le éventuellement. 

![Réviser le nom d’hôte](./media/hana-installation/image24_review_host_name.PNG)

À l’étape suivante, vous devez également récupérer les données que vous avez communiquées à Microsoft lors de la commande du déploiement de la grande instance HANA. 

![Fournir l’UID et le GID de l’utilisateur système](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Vous devez fournir les mêmes ID d’utilisateur système et ID de groupe d’utilisateurs que ceux fournis à Microsoft lors de la commande du déploiement de l’unité. Si vous ne parvenez pas à donner les mêmes ID, l’installation de SAP HANA sur l’unité de grande instance HANA échoue.

Dans les deux écrans suivants, non illustrés dans cette documentation, vous devez fournir le mot de passe de l’utilisateur SYSTÈME de la base de données SAP HANA, et le mot de passe de l’utilisateur sapadm utilisé pour l’agent hôte SAP qui est installé dans le cadre de l’instance de base de données SAP HANA.

Une fois le mot de passe défini, un écran de confirmation s’affiche. Vérifiez toutes les données affichées, puis poursuivez l’installation. Vous accédez à un écran montrant la progression de l’installation, comme celui ci-dessous.

![Vérifier la progression de l’installation](./media/hana-installation/image27_show_progress.PNG)

Lorsque l’installation se termine, vous devez voir une image telle que la suivante.

![L’installation est terminée](./media/hana-installation/image28_install_finished.PNG)

À ce stade, l’instance SAP HANA doit être opérationnelle et prête à l’emploi. Vous devez être en mesure de vous y connecter à partir de SAP HANA Studio. Veillez également à rechercher les derniers correctifs de SAP HANA et à les appliquer.


**Étapes suivantes**

- Voir [Haute disponibilité et récupération d’urgence de grandes instances SAP HANA sur Azure](hana-overview-high-availability-disaster-recovery.md).

