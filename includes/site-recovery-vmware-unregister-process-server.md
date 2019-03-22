---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908533"
---
Les étapes d’annulation de l’inscription d’un serveur de traitement diffèrent en fonction de son état de connexion avec le serveur de configuration.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Annuler l’inscription d’un serveur de traitement qui se trouve dans un état connecté

1. Accédez à distance au serveur de traitement en tant qu’administrateur.
2. Lancer le **le panneau de configuration** et ouvrez **programmes > désinstaller un programme**.
3. Désinstaller un programme nommé **serveur cible de Microsoft Azure Site Recovery Mobility principale duService/**.
4. Désinstaller un programme nommé **serveur de Configuration/traitement Microsoft Azure Site Recovery**.
5. Lors de la désinstallation de programmes dans les étapes 3 et 4, vous pouvez désinstaller **dépendances du serveur Configuration/traitement Microsoft Azure Site Recovery**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Annuler l’inscription d’un serveur de traitement qui se trouve dans un état déconnecté

> [!WARNING]
> Utilisez les étapes ci-après si il n’existe aucun moyen de réactiver l’ordinateur virtuel sur lequel le serveur de processus a été installé.

1. Connectez-vous à votre serveur de configuration en tant qu’administrateur.
2. Ouvrez une invite de commande d’administration et accédez au répertoire `%ProgramData%\ASR\home\svsystems\bin`.
3. Ensuite, exécutez la commande.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. La commande ci-dessus fournit la liste du ou des serveurs de processus (elle peut en comporter plusieurs en cas d’entrées en double) avec le numéro de série (S.No), l’adresse IP (IP), le nom de la machine virtuelle sur laquelle le serveur de processus est déployé (Name), ainsi que la pulsation de la machine virtuelle (Heartbeat) comme indiqué ci-dessous.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. À présent, entrez le numéro de série du serveur de processus dont vous souhaitez annuler l’inscription.
6. Cela effacera les détails du serveur de processus à partir du système et affiche le message : **Correctement désinscrit nom-serveur > (server-IP-address)**

