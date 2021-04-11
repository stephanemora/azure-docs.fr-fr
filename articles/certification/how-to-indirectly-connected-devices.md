---
title: Certification de bundles d’appareils et d’appareils connectés indirectement
titleSuffix: Azure Certified
description: Découvrez comment soumettre un appareil connecté indirectement pour certification.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: da3110b562bc5ddbd37657f31cbdd3790a13b897
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975702"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Bundles d’appareils et appareils connectés indirectement

Pour prendre en charge les appareils qui interagissent avec Azure via un appareil, des offres SaaS ou de PaaS, notre portail de soumission (https://www.certify.azure.com) et notre catalogue d’appareils (https://devicecatalog.azure.com) mettent en œuvre les concepts de regroupement et de dépendances pour promouvoir ces combinaisons d’appareils et leur permettre d’accéder à notre programme Azure Certified Device.

Selon la gamme de produits et les services offerts, votre situation peut nécessiter une combinaison des étapes suivantes :


![Créer des dépendances de projet](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Capteurs et appareils indirects
De nombreux capteurs requièrent un appareil pour se connecter à Azure. En outre, vous pouvez avoir plusieurs appareils compatibles qui fonctionnent avec le capteur. **Pour prendre en charge ces scénarios, vous devez certifier les appareils avant de certifier le capteur qui transmettra des informations via ceux-ci.**

Exemple de matrice de combinaisons de soumission ![Exemple de soumission](./media/indirect-connected-device/picture-2.png )

Pour certifier votre capteur nécessitant un appareil distinct :
1.  Tout d’abord, [certifiez l’appareil](https://certify.azure.com) et publiez-le dans le catalogue Azure Certified Device
    - Si vous disposez de plusieurs appareils relais compatibles (comme dans l’exemple ci-dessus), soumettez-les séparément pour la certification, et publiez-les également dans le catalogue
2.  Avec le capteur connecté via l’appareil, soumettez le capteur pour la certification
    * Sous l’onglet « Dépendances » de la section « Détails de l’appareil », définissez les valeurs suivantes
        * Type de dépendance = « Passerelle matérielle »
        * URL de dépendance = « Lien URL vers l’appareil sur le catalogue d’appareils »
        * Utilisé pendant les tests = « Oui »
        * Ajoutez tout commentaire visible du client qui doit être fourni à un utilisateur consultant la description du produit dans le catalogue d’appareils (exemple : « Des appareils série 100 sont requis pour permettre aux capteurs de se connecter à Azure »).

3.  Si vous souhaitez ajouter d’autres appareils facultatifs pour cet appareil, vous pouvez sélectionner « + Ajouter une dépendance supplémentaire ». Suivez ensuite les mêmes instructions et notez qu’elles n’ont pas été utilisées pendant le test. Dans les commentaires visibles du client, veillez à préciser que d’autres appareils associés à ce capteur sont disponibles (en guise d’alternative à l’appareil utilisé lors du test).

![Texte de remplacement](./media/indirect-connected-device/picture-3.png "Type de dépendance matérielle")

## <a name="paas-and-saas-offerings"></a>Offres PaaS et SaaS
Votre portefeuille de produits peut inclure des appareils que vous certifiez, mais votre appareil requiert également d’autres services de votre société ou de sociétés tierces. Pour ajouter cette dépendance, procédez comme suit :
1. Démarrez le processus de soumission pour votre appareil
2. Sous l’onglet « Dépendances », définissez les valeurs suivantes
    - Type de dépendance = « Service logiciel »
    - Nom du service = « [nom de votre produit] »
    - URL de dépendance = « Lien URL vers une page de produit décrivant le service »
    - Ajoutez tout commentaire visible du client qui doit être fourni à un utilisateur consultant la description du produit dans le catalogue Azure Certified Device
3. Si vous avez d’autres logiciels, services ou dépendances matérielles que vous souhaitez ajouter comme facultatifs pour cet appareil, vous pouvez sélectionner « + Ajouter une dépendance supplémentaire » et suivre les mêmes instructions.

![Type de dépendance de logiciel](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Produits regroupés
Les référencements de produits regroupés sont simplement la certification réussie d’un appareil avec d’autres composants qui seront vendus dans le cadre de l’offre groupée dans un référencement de produit. Vous avez la possibilité de soumettre un appareil incluant des composants supplémentaires, tels qu’un capteur de température et un capteur de caméra (#1), ou de soumettre un capteur tactile incluant un appareil relais (#2). La fonctionnalité « Composant », vous permet d’ajouter plusieurs composants à votre référencement.

Si vous envisagez de le faire, vous devez mettre en forme l’image de référencement de produit pour indiquer que ce produit est fourni avec d’autres composants.  En outre, si votre offre groupée requiert la certification de services supplémentaires, vous devez les identifier par le biais de la dépendance de services.
Exemple de matrice de produits regroupés

![Exemple de soumission d’offre groupée](./media/indirect-connected-device/picture-5.png )

Pour une description plus détaillée de l’utilisation de la fonctionnalité de composant dans le portail Azure Certified Device, consultez notre [documentation d’aide](./how-to-using-the-components-feature.md). 

Si un appareil est un dispositif relais avec un capteur distinct dans le même produit, créez un composant pour l’appareil relais et un autre pour le capteur. Vous pouvez ajouter des composants à votre projet sous l’onglet Détails du produit de la section Détails de l’appareil :

![Ajout de composants](./media/indirect-connected-device/picture-6.png )

Pour l’appareil relais, définissez le type de composant en tant que produit prêt pour le client, et renseignez les autres champs pertinents pour votre produit. Exemple :

![Détails du composant](./media/indirect-connected-device/picture-7.png )

Pour le capteur, ajoutez un deuxième composant, en définissant Type de composant sur Périphérique et Méthode d’attachement sur Discrète. Exemple :

![Détails du deuxième composant](./media/indirect-connected-device/picture-8.png )

Une fois le composant capteur créé, modifiez les détails, accédez à l’onglet capteurs, puis ajoutez les détails du capteur. Exemple :

![Détails du capteur](./media/indirect-connected-device/picture-9.png )

Entrez les détails de votre projet et soumettez votre appareil pour certification comme d’habitude.

