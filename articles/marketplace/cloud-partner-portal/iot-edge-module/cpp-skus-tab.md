---
title: Références SKU de modules Azure IoT Edge | Place de marché Azure
description: Créez des références SKU pour un module IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 49ae69263735d6ec35cd911a20100472b32c0b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286555"
---
# <a name="iot-edge-module-skus-tab"></a>Onglet Références de modules IoT Edge

L’onglet **Références** de la page **Nouvelle offre** permet de créer une ou plusieurs références SKU et de les associer à votre nouvelle offre.  Vous pouvez utiliser différentes références SKU pour différencier une solution par des ensembles de fonctionnalités, des modèles de facturation ou d’autres caractéristiques.


## <a name="sku-settings"></a>Paramètres de la référence SKU

Lorsque vous commencez à créer une offre, aucune référence SKU n’est associée à celle-ci. Pour créer une référence SKU, procédez comme suit :

- Sur la page **Modules IoT Edge > Nouvelle offre**, sélectionnez l’onglet **Références**.
- Sous Références, sélectionnez **+ New SKU** (+ Nouvelle référence SKU) pour ouvrir une boîte de dialogue.

  ![Bouton New SKU (Nouvelle référence SKU) sur l’onglet Nouvelle offre pour les modules IoT Edge](./media/iot-edge-module-skus-tab-new-sku.png)

- Dans la boîte dialogue **New SKU** (Nouvelle référence SKU), entrez un identificateur pour la référence (SKU), puis sélectionnez **OK**.
(Le tableau suivant donne les conventions d’affectation de noms d’identificateur.)

L’onglet **Références** est actualisé et affiche les champs que vous modifiez pour configurer la référence SKU. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire.

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **ID de SKU\***       | Identificateur de cette référence SKU. Ce nom compte au maximum 50 caractères (minuscules, caractères alphanumériques ou tirets), mais ne peut pas se terminer par un tiret. **Remarque :** ce nom ne peut pas être modifié une fois l’offre publiée. Le nom est visible publiquement dans les URL de produit. |
|  |  |


## <a name="sku-details"></a>Détails de la référence SKU

Configurez la section **Détails de la référence SKU** pour définir la façon dont votre référence SKU va être affichée sur la Place de marché Microsoft Azure et les sites web du Portail Azure.

![Métadonnées de référence SKU de module IoT Edge](media/iot-edge-module-skus-tab-metadata.png)

Le tableau suivant décrit l’objectif, le contenu et le format des champs sous **Détails de la référence SKU**. Les champs obligatoires sont indiqués par un astérisque (*).

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **Titre\***        | Titre de cette référence SKU. Longueur maximale de 50 caractères. <br/> Il est affiché dans le Portail Azure et utilisé en tant que nom de module par défaut (sans espaces ni caractères spéciaux) une fois déployé. Consultez les images ci-dessous pour voir où est affiché exactement ce champ.|
| **Résumé\***      | Court résumé de cette référence SKU. Longueur maximale de 100 caractères. Ne résumez **PAS** l’offre, juste la référence SKU.  Ce résumé est affiché dans la Place de marché Microsoft Azure. Consultez les images ci-dessous pour voir où est affiché exactement ce champ.|
| **Description\***  | Brève description de cette référence SKU. Longueur maximale de 3 000 caractères. Ne décrivez PAS l’offre, juste cette référence SKU. Cette description est affichée dans la Place de marché Microsoft Azure et dans le Portail Azure. Dans le Portail Azure, elle est ajoutée à la description de la Place de marché qui fait référence à l’offre définie dans l’onglet Place de marché.  Cette description peut être identique au résumé de la référence SKU. Consultez les images ci-dessous pour voir où est affiché exactement ce champ.|
| **Masquer cette référence (SKU)\*** | Conservez le paramètre par défaut, ici **Non**. |
|  |  |


### <a name="sku-example"></a>Exemple de référence SKU

 Les exemples suivants montrent comment les champs **Titre**, **Résumé**, **Description**, de la référence SKU sont présentés selon les affichages.
 

#### <a name="on-the-azure-marketplace-website"></a>Sur le site web de la Place de marché Microsoft Azure :

- Lorsque vous examinez les détails de la référence SKU :

    ![Affichage des références SKU sur le site web de la Place de marché Microsoft Azure](media/iot-edge-module-ampdotcom-pdp-plans.png)


#### <a name="on-the-azure-portal-website"></a>Sur le site web du Portail Azure :

- Lorsque vous parcourez les références SKU :

    ![Affichage du module IoT Edge quand vous parcourez le Portail Azure n° 1](media/iot-edge-module-portal-browse.png)

    ![Affichage du module IoT Edge quand vous parcourez le Portail Azure n° 2](media/iot-edge-module-portal-product-picker.png)

- Lorsque vous recherchez une référence SKU :

    ![Affichage du module IoT Edge quand vous effectuez une recherche dans le Portail Azure](media/iot-edge-module-portal-search.png)

- Lorsque vous examinez les détails de la référence SKU :

    ![Affichage du module IoT Edge quand vous examinez les détails du produit dans le portail](./media/iot-edge-module-portal-pdp.png)

- Lors du déploiement du module :
    
    ![Affichage du module IoT Edge lors de son déploiement](./media/iot-edge-module-deployment.png)


## <a name="sku-content"></a>Contenu de la référence SKU

Sous **Edge Module Images** (Images du module Edge), fournissez les informations nécessaires pour charger votre module IoT Edge.

Accordez-nous l’accès à votre [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) qui contient votre image de module IoT Edge, afin que vous puissiez la charger et la certifier. Après sa publication, votre module IoT Edge est copié et distribué à l’aide d’un registre de conteneurs public hébergé par la Place de marché Microsoft Azure.

Vous pouvez cibler plusieurs plateformes et fournir plusieurs versions via les balises. Apprenez-en davantage sur [les balises et le contrôle de version dans « Prepare your IoT Edge module technical assets » (Préparer vos ressources techniques de module IoT Edge)](./cpp-create-technical-assets.md).

![Images de module IoT Edge](./media/iot-edge-module-skus-tab-acr.png)

Le tableau suivant décrit l’objectif, le contenu et la mise en forme des champs des sections **Détails du référentiel d’image** et **Version de l’Image**.  Les champs obligatoires sont indiqués par un astérisque (*).


|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
|  |  ***Image Repository Details*** (Détails sur le référentiel d’images)    |
| **ID de l’abonnement\***        | ID d’abonnement Azure de votre Azure Container Registry.|
| **Nom du groupe de ressources\***      | Nom du groupe de ressources de votre Azure Container Registry.|
| **Nom du registre\***  | Nom de votre registre Azure Container Registry. Copiez uniquement le nom de registre, PAS le nom du serveur de connexion (par exemple, sans `azurecr.io`.) |
| **Nom du référentiel\***  | Le nom du référentiel de votre Azure Container Registry qui contient votre module IoT Edge. **Remarque :** une fois le nom défini, il ne peut pas être modifié ultérieurement. Utilisez un nom unique pour vous assurer qu’aucune autre offre dans votre compte ne porte le même. |
| **Nom d’utilisateur\*** | Nom d’utilisateur associé à votre Azure Container Registry (nom d’utilisateur administrateur). |
| **Mot de passe\*** | Mot de passe associé à votre Azure Container Registry. |
|    |  ***Version de l’image***   |
| **Balise d’image ou Digest\*** | Une balise `latest` et une balise de version minimum doivent être incluses (par exemple, commençant par `xx.xx.xx-` où xx est un nombre). Il doit y avoir des [balises de manifeste](https://github.com/estesp/manifest-tool) pour cibler plusieurs plateformes. Toutes les balises référencées par une balise de manifeste doivent également être ajoutées afin que nous puissions les charger. Vous pouvez ajouter plusieurs versions d’un module IoT Edge à l’aide de balises. Toutes les balises de manifeste (sauf `latest`) doivent commencer par `X.Y-` ou `X.Y.Z-`, où X, Y et Z sont des entiers. Apprenez-en davantage sur [les balises et le contrôle de version dans « Prepare your IoT Edge module technical assets » (Préparer vos ressources techniques de module IoT Edge)](./cpp-create-technical-assets.md). <br/> Par exemple, si une balise `latest` pointe vers `1.0.1-linux-x64`, `1.0.1-linux-arm32` et `1.0.1-windows-arm32`, ces 6 balises doivent être ajoutées ici. |
|  |  |


### <a name="help-your-customers-launch-by-using-default-settings"></a>Permettre à vos clients de se lancer à l’aide des paramètres par défaut

Définissez les paramètres les plus courants pour déployer votre module IoT Edge. Optimisez les déploiements des clients en les autorisant à lancer votre module IoT Edge prêt à l’emploi avec ces valeurs par défaut.

![Paramètres par défaut du module IoT Edge au moment du déploiement](./media/iot-edge-module-skus-tab-iot-edge-defaults.png)

Le tableau suivant décrit l’objectif, le contenu et le format des champs **Itinéraires par défaut**, **Default twin desired properties** (Propriétés souhaitées du jumeau par défaut), **Default environment variables** (Variables d’environnement par défaut) et **Default CreateOptions** (createOptions par défaut).

|  **Champ**       |     **Description**                                                          |
|  ---------       |     ---------------                                                          |
| **Itinéraires par défaut**        | Tous les noms et valeurs d’itinéraires par défaut doivent contenir moins de 512 caractères. Vous pouvez définir jusqu’à 5 itinéraires par défaut. Veillez à utiliser une [syntaxe d’itinéraire](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) correcte pour votre valeur d’itinéraire. Pour faire référence à votre module, utilisez son nom de module par défaut, qui est votre **titre de référence SKU** sans espaces ni caractères spéciaux. Pour faire référence à d’autres modules qui ne sont pas encore connus, utilisez la convention `<FROM_MODULE_NAME>` pour informer vos clients qu’ils doivent mettre à jour ces informations. Apprenez-en plus sur les [itinéraires IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes). <br/> Par exemple, si le module `ContosoModule` écoute les entrées sur `ContosoInput` et les données de sortie au niveau de `ContosoOutput`, il est judicieux de définir les 2 itinéraires par défaut suivants :<br/>- Nom n° 1 : `ToContosoModule`<br/>- Valeur n° 1 : `FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")`<br/>- Nom n° 2 : `FromContosoModuleToCloud`<br/>- Valeur n° 2 : `FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream`<br/>  |
| **Default twin desired properties** (Propriétés souhaitées du jumeau par défaut)      | Tous les noms et valeur de propriétés souhaitées du jumeau par défaut doivent contenir moins de 512 caractères. Vous pouvez définir jusqu’à 5 noms/valeurs de propriétés souhaitées du jumeau. Les valeurs des propriétés souhaitées du jumeau doivent être un code JSON valide, sans séquence d’échappement ni tableaux et possédant une hiérarchie imbriquée maximale de 4. Découvrez-en plus sur les [propriétés souhaitées du jumeau](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties). <br/> Par exemple, si un module prend en charge une fréquence d’actualisation configurable de manière dynamique via ses propriétés souhaitées du jumeau, il semble judicieux de définir la propriété souhaitée du jumeau par défaut suivante :<br/> - Nom n° 1 : `RefreshRate`<br/>- Valeur n° 1 : `60`|
| **Default environment variables** (Variables d’environnement par défaut)  | Tous les noms et valeurs de variables d’environnement par défaut doivent contenir moins de 512 caractères. Vous pouvez définir jusqu’à 5 noms/valeurs de variables d’environnement. <br/>Par exemple, si un module a besoin d’accepter des conditions d’utilisation avant d’être démarré, vous pouvez définir la variable d’environnement suivante :<br/> - Nom n° 1 : `ACCEPT_EULA`<br/>- Valeur n° 1 : `Y`|
| **Default createOptions** (createOptions par défaut)  | createOptions doit contenir moins de 512 caractères. Il doit s’agir d’un code JSON valide, sans séquence d’échappement. Apprenez-en davantage sur [createOptions](https://docs.microsoft.com/azure/iot-edge/module-composition#configure-modules). <br/> Par exemple, si un module a besoin de lier un port, vous pouvez définir les éléments createOptions suivants :<br/>  `"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}`|
|   |   |

Sélectionnez **Enregistrer** pour enregistrer vos paramètres de référence SKU. 


## <a name="next-steps"></a>Étapes suivantes

Utilisez [l’onglet Place de marché](./cpp-marketplace-tab.md) pour créer une description de la Place de marché pour votre offre.
