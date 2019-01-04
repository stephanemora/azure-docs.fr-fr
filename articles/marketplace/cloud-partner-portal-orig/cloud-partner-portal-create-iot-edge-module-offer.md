---
title: Créer une offre de module IoT Edge | Microsoft Docs
description: Comment publier un nouveau module IoT Edge sur la Place de marché.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: bf7d639c682e443f29b31b3c6d7438e89c406fde
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838631"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Comment publier un nouveau module IoT Edge sur le Portail Cloud Partner

Cet article décrit les étapes de publication d’une nouvelle offre de module IoT Edge.

## <a name="prerequisites"></a>Prérequis

Les prérequis suivants sont nécessaires pour publier un module IoT Edge sur la Place de marché Azure.

-   Accéder au [Portail Cloud Partner (CPP)](https://cloudpartner.azure.com/#alloffers). Pour plus d’informations, consultez le [Guide de publication](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Faire héberger votre module IoT Edge dans un registre de conteneurs Azure.

-   Disposer des métadonnées de votre module IoT Edge (liste non exhaustive) :

    -   Intitulé

    -   Une description (au format HTML de base)

    -   Un logo au format d’image png et aux dimensions suivantes : 40 pixels x 40 pixels, 90 pixels x 90 pixels, 115 pixels x 115 pixels, 255 pixels x 115 pixels.

    -   Conditions d’utilisation et politique de confidentialité

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Préparer votre liste de module IoT Edge dans le portail CPP
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Créer une offre de type module IoT Edge 

Pour préparer votre liste de module IoT Edge, procédez comme suit :

-   Connectez-vous à votre [compte CPP](https://cloudpartner.azure.com/).

>[!Note]
>Pour obtenir des informations générales sur le Portail Cloud Partner, consultez la [ documentation d’apprentissage](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-getting-started-with-the-cloud-partner-portal)

-   Sélectionnez **Nouvelle offre**, puis **Module IoT Edge**.

>[!NOTE]
>Un module IoT Edge est un conteneur qui est spécifiquement conçu pour s’exécuter sur IoT Edge. Les scénarios traités par un module IoT Edge doivent avoir un sens dans un contexte IoT Edge. Cela inclut également des paramètres de configuration par défaut pour faciliter le déploiement vers un appareil IoT Edge. Le conteneur peut également inclure le SDK IoT Edge pour permettre la communication avec le hub edgeHub et IoT Hub.

### <a name="define-your-offer-settings"></a>Définir vos paramètres d’offre

Dans l’onglet Paramètres de l’offre, entrez les informations de votre offre.

![Identité de l’offre](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   L’**ID de l’offre** identifie de façon unique votre offre sur le portail CPP et peut être utilisée dans les URL affichés aux clients.

-   Le **Nom** est visible de vous seul pour faire référence à cette offre dans le portail CPP.

### <a name="create-one-or-more-skus"></a>Créer une ou plusieurs références SKU

Chaque référence SKU correspond à une image de conteneur. Vous devez disposer d’au moins une référence SKU et vous pouvez en ajouter plusieurs. Une référence SKU comprend deux parties :

-   Métadonnées de référence SKU

-   Métadonnées de conteneur

**Pour créer une référence SKU :**

Sélectionnez l’onglet **SKUs** (Nouvelles références SKU), puis sélectionnez **New SKU** (Nouvelle référence SKU).

![Nouvelle référence SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

Les métadonnées de la référence SKU contiennent les champs suivants, qui sont obligatoires :
- SKU ID : identificateur unique.
- Title : titre de la référence SKU, jusqu’à 50 caractères.
- Summary : brève description, jusqu’à 100 caractères.
- Description : description longue.
- Hide this SKU : la valeur de masquage de la référence est **No** par défaut.
   
![Détails de la référence SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>Métadonnées du module IoT Edge et registre de conteneurs

Les métadonnées du module IoT Edge contiennent des informations de référence d’image qui sont stockées dans le registre de conteneurs Azure (ACR). La Place de marché Azure copie l’image dans le registre de la place de marché, ce qui la met à la disposition des clients après certification. Toutes les requêtes utilisateur d’utilisation d’une image de module IoT Edge sont servies à partir du registre de conteneurs de la Place de marché Microsoft Azure.

![Images de conteneur](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Images de conteneur**

La page de détails du référentiel d’images contient les champs obligatoires suivants :

-   **ID d’abonnement** : identifiant d’abonnement Azure donnant accès au registre ACR.

-   **Nom du groupe de ressources** : nom du groupe de ressources du registre ACR.

-   **Nom du registre** : nom du registre ACR.

-   **Nom du référentiel** : nom du référentiel. Une fois définie, cette valeur n’est plus modifiable. Le nom doit être unique afin que aucune autre offre ne porte le même nom dans votre compte .

-   **Nom d’utilisateur** : nom d’utilisateur associé à l’enregistrement de contrôle d’accès (nom d’utilisateur administrateur).

-   **Mot de passe** : mot de passe associé à l’enregistrement de contrôle d’accès.

    >[!Note]
    >Le nom d’utilisateur et le mot de passe sont requis pour s’assurer que les partenaires ont accès à l’enregistrement de contrôle d’accès décrit dans le processus de publication.

Lorsque vous publiez une image de module IoT Edge, vous pouvez fournir une ou plusieurs balises d’image. Veillez à ajouter une balise « latest » (valeur par défaut) à votre module afin de pouvoir l’identifier aisément pendant le test.

Vous pouvez également indiquer les spécificités du module IoT Edge suivantes :

-   **createOptions** : valeur par défaut createOptions à transmettre pour que ce module IoT Edge puisse être démarré directement.

-   **twin** : jumeau par défaut de module IoT Edge à transmettre afin que ce module IoT Edge puisse être démarré directement lors de l’utilisation du SDK IoT Module.

-   **routes :**  : itinéraires par défaut de module IoT Edge à transmettre afin que ce module IoT Edge puisse être démarré directement lors de l’utilisation du SDK IoT Module.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Décrire votre module IoT Edge pour vos clients

Dans l’onglet Marketplace, ajoutez votre contenu spécifique au marché. Ces informations sont les informations qui seront publiquement visibles et répertoriées sur la place de marché Microsoft Azure.

![Vue d’ensemble du module IoT Edge](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Title** (Titre) : titre de votre module IoT Edge proposé au public.

-   **Summary** (Résumé) :  résumé de votre module IoT Edge visible par le public dans la plupart des pages, comme les pages de navigation.

-   **Long summary** (Résumé long) : résumé de votre module IoT Edge visible par le public lorsque le module est mis en vedette. 

-   **Description** : description de votre module IoT Edge visible par le public dans la page d’informations détaillées sur le produit. (Vous pouvez utiliser des balises HTML de base pour mettre en forme votre description.)

-   **Identificateur marketing** : identifiant unique utilisé pour créer l’URL de votre produit. Cette URL est définie dans le format suivant : *azuremarketplace.microsoft.com/enus/marketplace/apps/votre_id_éditeur.votre_id_marketing_module_iotedge*.

-   **ID d’abonnement de préversion** : les utilisateurs éligibles à ces abonnements auront accès au module IoT Edge après l’étape de certification mais avant sa mise en ligne.

-   **Liens utiles** : vous pouvez ajouter jusqu’à 10 liens qui figureront sur votre page d’informations détaillées sur le produit.

-   **Catégories suggérées** : sélectionnez jusqu’à cinq catégories. Elles figureront sur la page des détails de votre produit. Actuellement, tous les modules IoT Edge dans les catégories *Internet of Things \> module IoT Edge* des pages de navigation.

-   **Logos** : chargez vos images de logo du module IoT Edge au format PNG. Utilisez les dimensions exactes suivantes : 40 pixels x 40 pixels, 90 pixels x 90 pixels, 115 pixels x 115 pixels, 255 pixels x 115 pixels.

-   **Captures d’écran** : les captures d’écran sont affichées sur la page de détails de votre produit. Il s’agit d’un excellent moyen de communiquer visuellement sur les actions et le mode de fonctionnement de votre module IoT Edge. Vous pouvez afficher des diagrammes architecturaux ou utiliser des illustrations de cas d’usage, par exemple.

-   **Videos** : les vidéos sont affichées sur la page de détails de votre produit. Il s’agit d’un excellent moyen de communiquer visuellement sur les actions et le mode de fonctionnement de votre module IoT Edge.

-   **Gestion des prospects** : vous pouvez choisir un système pour recueillir tous les prospects qui montrent de l’intérêt pour votre produit.

-   **Confidentialité** : vous devez disposer d’une URL pointant vers votre politique de confidentialité.

-   **Conditions d’utilisation** : vous devez disposer de conditions d’utilisation. Vous pouvez utiliser des balises HTML pour formater cette page ou pointer vers l’une de vos autres pages.

### <a name="enter-your-support-contact-information"></a>Entrer vos coordonnées de support

Dans l’onglet Support, fournissez les informations de **support technique** et de **support client**.

![Contacts du support](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Certifier votre module IoT Edge

Après avoir fourni toutes les informations requises, sélectionnez **Publier** pour envoyer votre module IoT Edge en certification. Une chronologie vous indique alors les étapes du processus de certification.

**Vérification du module**

Votre module est vérifié par notre équipe de certification. Une fois le module certifié, vous obtiendrez un lien privé pour tester votre module. Si vous devez apporter des modifications après les tests, modifiez les métadonnées de votre offre et soumettez à nouveau le module à l’équipe de certification. 

## <a name="publish-your-iot-edge-module"></a>Publier votre module IoT Edge

Une fois que vous avez terminé les tests et que vous êtes prêt à publier, sélectionnez **Démarrer** pour publier votre module IoT Edge.

>[!Important]
>Si vous voulez que votre module IoT Edge soit annoncé lors de la conférence Ignite, votre module doit être public avant le 23/09/2018.
