---
title: Fichier include
description: Fichier include
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 7bdbfd8ffd44f9515ae4df20c55b09c8a0adcaef
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376237"
---
#### <a name="to-create-a-new-service"></a>Création d’un service

1.  À l’aide des informations d’identification de votre compte Microsoft, connectez-vous au portail Azure à cette URL : <https://portal.azure.com/>. Si vous déployez l’appareil dans le portail Government, connectez-vous à l’adresse suivante : <https://portal.azure.us/>

2.  Dans le portail Azure, cliquez sur **+ Créer une ressource** &gt; **Stockage** &gt; **StorSimple Virtual Series**.

    ![Créer un nouveau service](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Dans le panneau **Instance de StorSimple Device Manager** qui s’ouvre, procédez comme suit :

    1.  Fournissez un unique **Nom de la ressource** pour votre service. Il s’agit d’un nom convivial pouvant être utilisé pour identifier le service. Le nom peut comporter entre 2 et 50 caractères qui peuvent être des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre.

    2.  Choisissez un **abonnement** dans la liste déroulante. L’abonnement est lié à votre compte de facturation. Ce champ n’est pas présent si vous n’avez qu’un seul abonnement.

    3.  Pour le **Groupe de ressources**, sélectionnez un groupe existant ou créez-en un. Pour plus d’informations, consultez la page [Groupes de ressources dans Azure](/azure/azure-resource-manager/management/manage-resource-groups-portal).

    4.  Indiquez un **emplacement** pour votre service. Pour plus d’informations sur les services disponibles dans chaque région, consultez la page [Régions Azure](https://azure.microsoft.com/regions/#services). De manière générale, choisissez un **Emplacement** plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil. Vous pouvez également prendre en compte les éléments suivants :

        -   Si vous avez d’autres charges de travail dans Azure que vous souhaitez déployer avec votre appareil StorSimple, nous vous invitons à utiliser ce centre de données.

        -   Le gestionnaire d’appareil StorSimple et le stockage Azure peuvent se trouver en deux emplacements différents. Dans ce cas, vous devez créer séparément le gestionnaire d’appareil StorSimple et le compte de stockage Azure. Pour créer un compte de stockage Azure, accédez au service Stockage Azure dans le portail Azure, puis suivez les étapes décrites dans [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Une fois que vous avez créé ce compte, ajoutez-le au service StorSimple Device Manager en suivant les étapes indiquées dans [Configurer un nouveau compte de stockage pour le service](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Si vous déployez l’appareil virtuel dans le portail Government, le service StorSimple Device Manager est disponible dans l’Iowa et en Virginie.

    5.  Sélectionnez **Créer un compte de stockage Azure** pour créer automatiquement un compte de stockage avec le service. Spécifiez un **Nom du compte de stockage**. Si vous avez besoin de vos données dans un autre emplacement, désactivez cette case à cocher.

    6.  Cochez la case **Épingler au tableau de bord** si vous souhaitez disposer d’un lien rapide vers ce service sur votre tableau de bord.

    7.  Pour créer le service StorSimple Device Manager, cliquez sur **Créer**.

        ![Créer un nouveau service 2](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Vous êtes dirigés vers la page d’accueil **Service**. La création de service dure quelques minutes. Une fois le service créé, vous recevez la notification correspondante et l’état du service devient **Actif**.


