---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67177728"
---
1. Connectez-vous à l’appareil Data Box. Assurez-vous qu’il est déverrouillé.

    ![Tableau de bord Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Accédez à **Définir des interfaces réseau**. Prenez note de l’adresse IP de l’appareil pour l’interface réseau servant à se connecter au client.

    ![Tableau de bord Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Accédez à **Connexion et copie**, puis cliquez sur **REST**.

    ![Tableau de bord Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Dans la boîte de dialogue **Accéder au compte de stockage et charger des données**, copiez le **Point de terminaison du service BLOB**.

    ![Tableau de bord Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Démarrez le **Bloc-notes** en tant qu’administrateur, puis ouvrez le fichier **hosts** qui se trouve dans `C:\Windows\System32\Drivers\etc`.
6. Ajoutez l’entrée suivante à votre fichier **hosts** : `<device IP address> <Blob service endpoint>`
7. Pour référence, utilisez l’image suivante. Enregistrez le fichier **hosts**.

    ![Tableau de bord Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
