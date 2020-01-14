---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564801"
---
## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans ce tutoriel, utilisez l’extension Azure IoT Tools pour générer un module et créer une **image conteneur** à partir des fichiers. Puis envoyez cette image à un **registre** qui stocke et gère vos images. Enfin, déployez votre image à partir de votre registre de façon à l’exécuter sur votre appareil IoT Edge.

Vous pouvez utiliser n’importe quel registre Docker pour stocker vos images conteneur. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) et [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) sont deux services de registre Docker connus. Ce didacticiel utilise Azure Container Registry.

Si vous ne disposez pas d’un registre de conteneurs, suivez ces étapes pour en créer un dans Azure :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.

2. Fournissez les valeurs suivantes pour créer votre registre de conteneurs :

   | Champ | Valeur |
   | ----- | ----- |
   | Nom du registre | Fournissez un nom unique. |
   | Subscription | Sélectionnez un abonnement dans la liste déroulante. |
   | Resource group | Nous vous recommandons d’utiliser le même groupe de ressources pour toutes les ressources de test que vous créez dans le cadre des démarrages rapides et didacticiels IoT Edge. Par exemple, utilisez **IoTEdgeResources**. |
   | Location | Choisissez un emplacement proche de vous. |
   | Utilisateur administrateur | Définissez ce champ sur **Activer**. |
   | SKU | Sélectionnez **De base**. |

3. Sélectionnez **Create** (Créer).

4. Une fois votre registre de conteneurs créé, accédez à celui-ci, puis dans le volet gauche, sélectionnez **Clés d’accès** dans le menu situé sous **Paramètres**.

5. Copiez les valeurs pour **Serveur de connexion**, **Nom d’utilisateur** et **Mot de passe**, et conservez-les dans un endroit pratique et approprié. Vous utilisez ces valeurs dans le tutoriel pour permettre l’accès au registre de conteneurs.

   ![Copiez le serveur de connexion, le nom d’utilisateur et le mot de passe pour le registre de conteneurs.](./media/iot-edge-create-container-registry/registry-access-key.png)