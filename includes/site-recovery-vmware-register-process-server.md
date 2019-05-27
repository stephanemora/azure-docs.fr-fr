---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169987"
---
1. Établir une connexion Bureau à distance à la machine exécutant le serveur de processus. 
2. Exécutez cspsconfigtool.exe pour démarrer l’outil de configuration de serveur de processus Azure Site Recovery.
    - L’outil est lancé automatiquement la première fois que vous vous connectez le serveur de processus.
    - Si elle ne s’ouvre pas automatiquement, cliquez sur son raccourci sur le bureau.

3. Dans **serveur de Configuration IP ou nom de domaine complet**, spécifiez le nom ou l’adresse IP du serveur de configuration avec laquelle inscrire le serveur de processus.
4. Dans **Port de serveur de Configuration**, assurez-vous que 443 est spécifié. Il s’agit du port sur lequel le serveur de configuration écoute les demandes.
5. Dans **phrase secrète de connexion**, spécifiez la phrase secrète que vous avez spécifié lorsque vous configurez le serveur de configuration. Pour trouver la phrase secrète :
    -  Sur le serveur de configuration, accédez au dossier d’installation de Site Recovery **\home\svssystems\bin\**. 
    - Exécutez la commande suivante : **genpassphrase.exe.n**. Cela vous indique l’emplacement de la phrase de passe, vous pouvez noter puis.

6. Dans **Port de transfert de données**, laissez la valeur par défaut, sauf si vous avez spécifié un port personnalisé.

7. Cliquez sur **enregistrer** enregistrer les paramètres et inscrire le serveur de processus.

    
    ![Inscrire le serveur de processus](./media/site-recovery-vmware-register-process-server/register-ps.png)
