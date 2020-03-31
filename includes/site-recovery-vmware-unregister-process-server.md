---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67177591"
---
Suivez les étapes pour votre situation spécifique.

### <a name="unregister-a-connected-process-server"></a>Annuler l’inscription d’un serveur de traitement connecté

1. Établissez une connexion à distance avec le serveur de traitement en tant qu’Administrateur.
2. Dans le **Panneau de configuration**, ouvrez **Programmes > Désinstaller un programme**.
3. Désinstallez le programme **Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery**.
4. Désinstallez le programme **Serveur de configuration/traitement Microsoft Azure Site Recovery**.
5. Une fois les programmes des étapes 3 et 4 désinstallés, vous pouvez désinstaller **Dépendances du serveur de configuration/traitement Microsoft Azure Site Recovery**.

### <a name="unregister-a-disconnected-process-server"></a>Annuler l’inscription d’un serveur de traitement déconnecté

N’utilisez ces étapes que s’il n’existe aucun autre moyen de réactiver l’ordinateur sur lequel est installé le serveur de traitement.

1. Connectez-vous au serveur de configuration en tant qu’administrateur.
2. Ouvrez une invite de commande d’administration et accédez à `%ProgramData%\ASR\home\svsystems\bin`.
3. Exécutez cette commande pour obtenir une liste d’un ou plusieurs serveurs de traitement.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Numéro : le numéro de série du serveur de traitement.
    - Adresse IP/Nom : L’adresse IP et le nom de la machine exécutant le serveur de traitement.
    - Pulsation : Dernière pulsation de la machine du serveur de traitement.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Spécifiez le numéro de série du serveur de traitement dont vous souhaitez annuler l’inscription.
5. L’annulation de l’enregistrement d’un serveur de traitement supprime tous les détails du système et affiche le message : **Désinscription de nom-serveur réussie> (adresse-IP-serveur)**

