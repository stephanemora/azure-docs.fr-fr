---
title: Tutoriel pour activer l’appareil Azure Stack Edge avec GPU dans le portail Azure | Microsoft Docs
description: Le tutoriel sur le déploiement d’un GPU Azure Stack Edge vous apprend à activer votre appareil physique.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267936"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Tutoriel : Activer Azure Stack Edge avec GPU

Ce tutoriel explique comment activer votre appareil Azure Stack Edge avec un GPU embarqué en utilisant l’interface utilisateur web locale.

Le processus d’activation peut prendre 5 minutes environ.

Ce didacticiel vous à présenté les point suivants :

> [!div class="checklist"]
> * Prérequis
> * Activer l’appareil physique

## <a name="prerequisites"></a>Prérequis

Avant de configurer et d’activer votre appareil Azure Stack Edge avec GPU, vérifiez que :

* Pour votre appareil physique : 
    
    - Vous avez installé l’appareil physique, comme indiqué dans [Installer Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Vous avez configuré le réseau et les paramètres réseau de calcul comme détaillé dans [Configurer le réseau, le réseau de calcul et le proxy Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Vous avez téléchargé votre propre certificat ou généré les certificats de l’appareil sur votre appareil si vous avez modifié le nom de l’appareil ou le domaine DNS via la page **Appareil**. Si vous n’avez pas effectué cette étape, vous verrez une erreur pendant l’activation de l’appareil, et l’activation sera bloquée. Pour plus d’informations, consultez la page [Configurer les certificats](azure-stack-edge-gpu-deploy-configure-certificates.md).
    
* Vous disposez de la clé d’activation provenant du service Azure Stack Edge que vous avez créé pour gérer l’appareil Azure Stack Edge. Pour plus d’informations, consultez [Préparer le déploiement d’Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Activer l’appareil

1. Dans l’interface utilisateur Web locale de l’appareil, accédez à la page **Bien démarrer**.
2. Dans la vignette **Activation**, sélectionnez **Activer**. 

    ![Page « Détails du cloud » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. Dans le volet **Activer**, entrez la **Clé d’activation** que vous avez obtenue dans [Obtenir la clé d’activation pour Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Sélectionnez **Appliquer**.

    ![Page « Détails du cloud » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Tout d’abord, l’appareil est activé. Vous êtes ensuite invité à télécharger le fichier de clé.
    
    ![Page « Détails du cloud » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Sélectionnez **Télécharger le fichier de clé** et enregistrez le fichier *keys.json* dans un emplacement sûr en dehors de l’appareil. **Ce fichier de clé contient les clés de récupération pour le disque du système d’exploitation et les disques de données sur votre appareil**. Voici le contenu du fichier *keys.json* :

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    Le tableau suivant décrit les différentes clés ici :
    
    |Champ  |Description  |
    |---------|---------|
    |`Id`    | Il s’agit de l’ID de l’appareil.        |
    |`DataVolumeBitLockerExternalKeys`|Il s’agit des clés BitLocker pour les disques de données ; elles sont utilisées pour récupérer les données locales sur votre appareil.|
    |`SystemVolumeBitLockerRecoveryKey`| Il s’agit de la clé BitLocker pour le volume système. Cette clé aide à la récupération de la configuration système et des données système pour votre appareil. |
    |`ServiceEncryptionKey`| Cette clé protège les données transitant par le service Azure. Cette clé garantit la sécurité des informations stockées en cas de compromission du service Azure. |

6. Allez à la page **Vue d’ensemble**. L’état de l’appareil doit s’afficher comme étant **Activé**.

    ![Page « Détails du cloud » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
L’activation de l’appareil est terminée. Vous pouvez maintenant ajouter des partages sur votre appareil.


## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous à présenté les point suivants :

> [!div class="checklist"]
> * Prérequis
> * Activer l’appareil physique

Pour savoir comment transférer des données avec votre appareil Azure Stack Edge, consultez :

> [!div class="nextstepaction"]
> [Transférer des données avec Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
