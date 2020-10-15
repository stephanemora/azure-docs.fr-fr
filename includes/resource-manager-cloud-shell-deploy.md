---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74451572"
---
## <a name="deploy-template-from-cloud-shell"></a>Déployer le modèle à partir de Cloud Shell

Vous pouvez utiliser [Cloud Shell](../articles/cloud-shell/overview.md) pour déployer votre modèle. Pour déployer un modèle externe, indiquez précisément l’URI du modèle comme vous le feriez pour tout autre déploiement externe. Pour déployer un modèle local, vous devez d’abord charger votre modèle dans le compte de stockage de votre Cloud Shell. Cette section explique comment charger le modèle dans compte Cloud Shell, et le déployer sous forme de fichier local. Si vous n’avez pas utilisé Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../articles/cloud-shell/overview.md) pour obtenir plus d’informations sur sa configuration.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez votre groupe de ressources Cloud Shell. Le modèle de nom est `cloud-shell-storage-<region>`.

   ![Sélection du groupe de ressources](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Sélectionnez le compte de stockage de votre Cloud Shell.

   ![Sélectionner le compte de stockage](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Sélectionnez **Objets Blob**.

   ![Sélectionner Objets Blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Sélectionnez **+ Conteneur**.

   ![Ajouter un conteneur](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Donnez à votre conteneur un nom et un niveau d’accès. L’exemple de modèle dans cet article ne contient aucune information sensible, par conséquent, autorisez l’accès en lecture anonyme. Sélectionnez **OK**.

   ![Renseigner les valeurs du conteneur](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Sélectionnez le conteneur que vous avez créé.

   ![Sélectionner le nouveau conteneur](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Sélectionnez **Télécharger**.

   ![Charger l’objet blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Recherchez et chargez votre modèle.

   ![Charger le fichier](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Après le chargement, sélectionnez le modèle.

   ![Sélectionner le nouveau modèle](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copiez l’URL.

   ![Copier l’URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Ouvrez l’invite de commandes.

   ![Ouvrir Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
