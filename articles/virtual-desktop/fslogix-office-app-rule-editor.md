---
title: Installer des conteneurs d’applications FSLogix pour Microsoft Office dans Windows Virtual Desktop – Azure
description: Guide pratique pour utiliser l’éditeur de règles d’application pour créer un conteneur d’application FSLogix avec Office dans Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743271"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Installer Microsoft Office à l’aide de conteneurs d’applications FSLogix

Vous pouvez installer Microsoft Office rapidement et efficacement à l’aide d’un conteneur d’application FSLogix comme modèle pour les autres machines virtuelles de votre pool d’hôtes.

Voici pourquoi l’utilisation d’un conteneur d’application FSLogix peut permettre une installation plus rapide :

- Le déchargement de vos applications Office sur un conteneur d’application réduit les exigences concernant la taille de votre lecteur C.
- Les instantanés ou les sauvegardes de votre machine virtuelle nécessitent moins de ressources.
- Le fait de disposer d’un pipeline automatisé via la mise à jour d’une seule image facilite la mise à jour de vos machines virtuelles.
- Vous avez besoin d’une seule image pour installer Office (et d’autres applications) sur toutes les machines virtuelles de votre déploiement Windows Virtual Desktop.

Cet article vous montre comment configurer un conteneur d’application FSLogix avec Office.

## <a name="requirements"></a>Configuration requise

Pour configurer l’éditeur de règles, vous avez besoin des éléments suivants :

- une machine virtuelle exécutant Windows sans Office installé
- une copie d’Office
- une copie de FSLogix installée sur votre déploiement
- un partage réseau auquel toutes les machines virtuelles de votre pool d’hôtes ont un accès en lecture seule

## <a name="install-office"></a>Installation d’Office

Pour installer Office sur votre disque VHD ou VHDX, activez le protocole Bureau à distance (RDP) dans votre machine virtuelle, puis suivez les instructions fournies dans [Installation d’Office sur une image principale de disque VHD](install-office-on-wvd-master-image.md). Lors de l’installation, assurez-vous que vous utilisez [les licences correctes](overview.md#requirements).

>[!NOTE]
>Windows Virtual Desktop requiert l’activation de l’ordinateur de partage (SCA).

## <a name="install-fslogix"></a>Installer FSLogix

Pour installer FSLogix et l’éditeur de règles, suivez les instructions fournies dans [Téléchargement et installation de FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Créer et préparer un disque VHD pour stocker Office

Ensuite, vous devez créer et préparer une image VHD pour l’utilisation de l’éditeur de règles :

1. Ouvrez une invite de commandes en tant qu’administrateur. et exécutez la commande suivante :

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Veillez à conserver les espaces que vous voyez dans cette commande.

2. Exécutez ensuite la commande suivante :

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Si vous trouvez le service, redémarrez la machine virtuelle avant de passer à l’étape 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Ensuite, accédez à **Fichiers programmes** > **FSLogix** > **Applications** et exécutez la commande suivante pour créer le disque dur virtuel cible :

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    Le disque VHD que vous créez à l’aide de cette commande doit contenir le dossier C:\\Fichiers programmes\\Microsoft Office.

    >[!NOTE]
    >Si vous constatez des erreurs, désinstallez Office et recommencez à partir de l’étape 1.

## <a name="configure-the-rule-editor"></a>Configurer l’éditeur de règles

Maintenant que vous avez préparé votre image, vous devez configurer l’éditeur de règles et créer un fichier dans lequel stocker vos règles.

1. Accédez à **Fichiers programmes** > **FSLogix** > **Applications** et exécutez **RuleEditor.exe**.

2. Sélectionnez **Fichier** > **Nouveau** > **Créer** pour créer un nouvel ensemble de règles, puis enregistrez cet ensemble de règles dans un dossier local.

3. Sélectionnez **Ensemble de règles vide**, puis cliquez sur **OK**.

4. Sélectionner le bouton **+**. La fenêtre **Ajouter une règle** s’ouvre. Cela modifie les options de la boîte de dialogue **Ajouter une règle**.

5. Dans le menu déroulant, sélectionnez **Règle de conteneur d’application (VHD)** .

6. Entrez **C:\\Fichiers programmes\\Microsoft Office** dans le champ **Dossier**.

7. Pour le champ **Fichier de disque**, sélectionnez **\<path\>\\office.vhd** dans la section **Créer un disque VHD cible**.

8. Sélectionnez **OK**.

9. Accédez au dossier de travail à l’adresse **C:\\Utilisateurs\\\<username\>\\Documents\\Ensembles de règles FSLogix** et recherchez les fichiers .frx et .fxa. Vous devez déplacer ces fichiers dans le dossier Règles situé dans **C:\\Fichiers programmes\\FSLogix\\Applications\\Règles** afin que les règles commencent à fonctionner.

10. Sélectionnez **Appliquer les règles au système** pour que les règles prennent effet.

     >[!NOTE]
     > Vous devez appliquer les fichiers de règles d’application à tous les hôtes de session.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur FSLogix, consultez notre [documentation FSLogix](/fslogix/).