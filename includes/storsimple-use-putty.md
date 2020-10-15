---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67177101"
---
#### <a name="to-connect-through-the-serial-console"></a>Pour établir une connexion via la console série
1. Connectez votre câble série à l’appareil (directement ou via un adaptateur USB série).
2. Ouvrez le **Panneau de configuration**, puis ouvrez le **Gestionnaire de périphériques**.
3. Identifiez le port COM comme indiqué dans l’illustration suivante.
   
     ![Connexion via la console série](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Démarrez PuTTY. 
5. Dans le volet droit, sous **Type de connexion**, cliquez sur **Série**.
6. Dans le volet droit, tapez le port COM approprié. Assurez-vous que les paramètres de configuration série sont définis comme suit :
   
   * Vitesse : 115 200
   * Bits de données : 8
   * Bits d’arrêt : 1
   * Parité : aucune
   * Contrôle de flux : aucun
     
     Ces paramètres sont affichés dans l’illustration suivante.
     
     ![Paramètres puTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Si le contrôle de flux par défaut ne fonctionne pas, essayez de définir le contrôle de flux sur XON/XOFF.
     > 
     > 
7. Cliquez sur **Ouvrir** pour démarrer une session série.

