---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39583021"
---
Les étapes d’annulation de l’inscription d’un serveur de traitement diffèrent en fonction de son état de connexion avec le serveur de configuration.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Annuler l’inscription d’un serveur de traitement qui se trouve dans un état connecté

1. Accédez à distance au serveur de traitement en tant qu’administrateur.
2. Démarrez le **Panneau de configuration** et ouvrez **Programmes > Désinstaller un programme**
3. Désinstallez le programme nommé **Serveur de configuration/traitement Microsoft Azure Site Recovery**
4. Une fois l’étape 3 terminée, vous pouvez désinstaller **Dépendances du serveur de configuration/traitement Microsoft Azure Site Recovery**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Annuler l’inscription d’un serveur de traitement qui se trouve dans un état déconnecté

> [!WARNING]
> Utilisez les étapes ci-dessous s’il n’existe aucun moyen de réactiver la machine virtuelle sur laquelle le serveur de traitement a été installé.

1. Connectez-vous à votre serveur de configuration en tant qu’administrateur.
2. Ouvrez une invite de commande d’administration et accédez au répertoire `%ProgramData%\ASR\home\svsystems\bin`.
3. Ensuite, exécutez la commande.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. La commande ci-dessus fournit la liste du ou des serveurs de processus (elle peut en comporter plusieurs en cas d’entrées en double) avec le numéro de série (S.No), l’adresse IP (IP), le nom de la machine virtuelle sur laquelle le serveur de processus est déployé (Name), ainsi que la pulsation de la machine virtuelle (Heartbeat) comme indiqué ci-dessous.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. À présent, entrez le numéro de série du serveur de processus dont vous souhaitez annuler l’inscription.
6. Cette opération vide du système les détails du serveur de processus et affiche le message suivant : **Successfully unregistered server-name> (server-IP-address)** (Nom du serveur désinscrit avec succès (adresse IP du serveur))

