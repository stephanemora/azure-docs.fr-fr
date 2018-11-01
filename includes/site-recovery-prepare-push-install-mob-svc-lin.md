---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166444"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Préparer une installation push sur un serveur Linux

1. Vérifiez qu’il existe une connectivité réseau entre l’ordinateur Linux et le serveur de processus.
1. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à l’ordinateur. Le compte est un utilisateur **racine** sur le serveur Linux source. Utilisez ce compte uniquement pour l’installation Push et pour les mises à jour.
1. Vérifiez que le fichier /etc/hosts sur le serveur Linux source contient des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
1. Installez les packages openssh, openssh-server et openssl les plus récents sur l’ordinateur à répliquer.
1. Vérifiez que le protocole Secure Shell (SSH) est activé et s’exécute sur le port 22.
1. Activez l’authentification par mot de passe et le sous-système SFTP dans le fichier sshd_config. Procédez comme suit :

    a. Connectez-vous en tant qu’utilisateur **racine**.

    b. Dans le fichier **/etc/ssh/sshd_config**, recherchez la ligne commençant par **PasswordAuthentication**.

    c. Supprimez les marques de commentaire de la ligne et remplacez la valeur actuelle par **yes**.

    d. Recherchez la ligne commençant par **Subsystem**, puis supprimez les marques de commentaire de la ligne.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Redémarrez le service **sshd**.

1. Ajoutez le compte créé dans l’outil CSPSConfigtool. Procédez comme suit :

    a. Connectez-vous à votre serveur de configuration.

    b. Ouvrez le fichier **cspsconfigtool.exe**. Il est disponible sous forme de raccourci sur le Bureau et dans le dossier %ProgramData%\home\svsystems\bin.

    c. Dans l’onglet **Gérer les comptes**, sélectionnez **Ajouter un compte**.

    d. Ajoutez le compte que vous venez de créer.

    d. Entrez les informations d’identification utilisées lors de l’activation de la réplication d’un ordinateur.
