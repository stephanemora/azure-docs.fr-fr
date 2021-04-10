---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: cf67ea58c4c13756fdc6e437883f12124ca47eb9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105388"
---
1. Sur le [portail Azure](https://portal.azure.com/), sélectionnez votre ressource Azure Stack Edge, puis accédez à la **Vue d’ensemble**. Votre appareil doit être en ligne. Accédez à **Passerelle de stockage cloud > Comptes de stockage**.

2. Sélectionnez **+ Ajouter un compte de stockage** dans la barre de commandes de l’appareil. 

   ![Ajout d’un compte de stockage](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Dans le volet **Ajouter un compte de stockage Edge**, spécifiez les paramètres suivants :

    1. Fournissez un nom unique pour le compte de stockage Edge sur votre appareil. Les noms de compte de stockage ne peuvent contenir que des lettres minuscules et des chiffres. Les caractères spéciaux ne sont pas autorisés. Le nom du compte de stockage doit être unique au sein de l’appareil (pas entre les appareils).

    2. Fournissez une description facultative des informations sur les données que le compte de stockage contient.  
    
    3. Par défaut, le compte de stockage Edge est mappé à un compte Stockage Azure dans le cloud et les données du compte de stockage sont automatiquement envoyées vers le cloud. Spécifiez le compte de stockage Azure auquel votre compte de stockage Edge est mappé.

    4. Créez un conteneur ou sélectionnez un conteneur existant dans le compte de stockage Azure. Toutes les données de l’appareil qui sont écrites dans le compte de stockage Edge sont automatiquement téléchargées vers le conteneur de stockage sélectionné dans le compte Stockage Azure mappé.

    5. Une fois que toutes les options de compte de stockage sont spécifiées, sélectionnez **Ajouter** pour créer le compte de stockage Edge. Un message s’affiche pour vous informer quand le compte de stockage Edge correctement créé. Le nouveau compte de stockage Edge est ensuite affiché dans la liste des comptes de stockage dans le Portail Azure.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. Si vous sélectionnez ce nouveau compte de stockage et que vous accédez à **Clés d’accès**, vous pouvez trouver le point de terminaison de service Blob et le nom du compte de stockage correspondant. Copiez ces informations, car ces valeurs, ainsi que les clés d’accès, vous aideront à vous connecter au compte de stockage Edge.

    ![Ajout d’un compte de stockage 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Vous pouvez accéder aux clés d’accès en vous [connectant aux API locales de l’appareil à l’aide d’Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).