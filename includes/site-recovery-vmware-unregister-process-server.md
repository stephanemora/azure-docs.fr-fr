---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925644"
---
Suivez les étapes pour votre scénario spécifique.

### <a name="unregister-a-connected-process-server"></a>Annuler l’inscription d’un serveur de processus connectés

1. Établir une connexion à distance au serveur de traitement en tant qu’administrateur.
2. Dans le **le panneau de configuration**, ouvrez **programmes > désinstaller un programme**.
3. Désinstallez le programme **serveur cible de Microsoft Azure Site Recovery Mobility principale duService/**.
4. Désinstallez le programme **serveur de Configuration/traitement Microsoft Azure Site Recovery**.
5. Une fois les programmes dans les étapes 3 et 4 sont désinstallées, désinstaller **dépendances du serveur Configuration/traitement Microsoft Azure Site Recovery**.

### <a name="unregister-a-disconnected-process-server"></a>Annuler l’inscription d’un serveur de processus déconnecté

N’utilisez ces étapes s’il n’existe aucun moyen de réactiver l’ordinateur sur lequel est installé le serveur de processus.

1. Se connecter le serveur de configuration en tant qu’administrateur.
2. Ouvrez une invite de commandes d’administration et accédez à `%ProgramData%\ASR\home\svsystems\bin`.
3. Exécutez cette commande pour obtenir une liste d’un ou plusieurs serveurs de processus.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Non : le numéro de série de processus serveur.
    - Adresse IP/nom : L’adresse IP et le nom de l’ordinateur exécutant le serveur de processus.
    - Vérification des pulsations : Dernière pulsation à partir de la machine serveur de processus.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Spécifiez le numéro de série du serveur de processus que vous souhaitez annuler l’inscription.
5. Annuler l’enregistrement d’un serveur de processus supprimer tous les détails du système et affiche le message : **Correctement désinscrit nom-serveur > (server-IP-address)**

