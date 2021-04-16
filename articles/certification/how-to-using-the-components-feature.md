---
title: Comment utiliser la caractéristique des composants du portail Azure Certified Device
description: Guide expliquant la façon d’utiliser au mieux la caractéristique des composants de la section Détails de l’appareil pour décrire avec précision votre appareil
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 220a6c2107063734201064115898611c20cab650
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304458"
---
# <a name="add-components-on-the-portal"></a>Ajouter des composants sur le portail

Lorsque vous suivez le [tutoriel pour ajouter des détails](tutorial-02-adding-device-details.md) sur l’appareil à votre projet de certification, vous devez décrire les spécifications matérielles de votre appareil. Pour ce faire, les utilisateurs peuvent mettre en évidence plusieurs produits matériels distincts (appelés **composants**) qui composent votre appareil. Cela vous permet de mieux promouvoir les appareils qui accompagnent du matériel supplémentaire et permet aux clients de trouver le produit approprié en effectuant une recherche sur le catalogue en fonction de ces caractéristiques.

## <a name="prerequisites"></a>Prérequis

- Vous devez être connecté et disposer d’un projet pour votre appareil sur le [portail Azure Certified Device](https://certify.azure.com). Pour plus d’informations, consultez ce [tutoriel](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Comment ajouter des composants

Chaque projet soumis à la certification inclura un composant **Produit prêt pour le client** (qui, dans de nombreux cas, représente le produit dans son intégralité). Pour mieux comprendre la distinction d’un type de composant Produit prêt pour le client, consultez notre [glossaire de certification](./resources-glossary.md). Tout ajout de composants supplémentaires est laissé à votre discrétion afin d’obtenir une représentation fidèle de votre appareil.

1. Sélectionnez `Add a component` sous l’onglet Détails du produit.

    ![Ajouter un lien de composant](./media/images/add-a-component-link.png)

1. Remplissez les champs de formulaire pertinents pour le composant.

    ![Section Détails du composant](./media/images/component-details-section.png)

1. Enregistrez vos informations à l’aide du bouton `Save Product Details` en bas de la page :  

    ![Bouton Enregistrer les détails du produit](./media/images/save-product-details-button.png)

1. Une fois que vous avez enregistré votre composant, vous pouvez personnaliser davantage les capacités matérielles qu’il prend en charge. Sélectionnez le lien `Edit` à côté du nom du composant.  

    ![Bouton Modifier le composant](./media/images/component-edit.png)

1. Fournissez les informations pertinentes sur les capacités matérielles, le cas échéant.  

    ![Image des sections de composant modifiables](./media/images/component-selection-area.png)  

    Les champs de composant modifiables (illustrés ci-dessus) sont les suivants :

    - **Général** : Détails sur le matériel, tels que les processeurs et le matériel sécurisé
    - **Connectivité** : Options de connectivité, protocoles et interfaces comme radio(s) et GPIO
    - **Accélérateurs** : Spécifier l’accélération matérielle comme le GPU et le VPU
    - **Capteurs** : Spécifier les capteurs disponibles, tels que le GPS et les vibrations
    - **Spécifications supplémentaires** : Informations supplémentaires sur l’appareil, telles que les dimensions physiques et les informations sur le stockage et la batterie

1. Sélectionnez `Save Product Details` au bas de la page Détails du produit.

## <a name="component-use-requirements-and-recommendations"></a>Exigences et recommandations relatives à l’utilisation des composants

Vous avez peut-être des questions concernant le nombre de composants à inclure ou le type de composant à utiliser. Voici quelques exemples de scénarios d’appareils que vous pouvez certifier et la façon dont vous pouvez utiliser la fonctionnalité des composants.

| Type de produit                                       | Non. Components | 1 composant/Type d’accessoire      | Plus de 2 composants/Type d’accessoire                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Produit fini                                   | 1          | Produit prêt pour le client, discret | N/A                                              |
| Produit fini avec **périphérique(s) détachable(s)** | 2 ou plus  | Produit prêt pour le client, discret | Périphérique / Discret ou intégré              |
| Produit terminé avec **composant(s) intégré(s)**  | 2 ou plus  | Produit prêt pour le client, discret | Sélectionnez le type approprié / Discret ou intégré |
| Kit de développement prêt pour les solutions                             | 2 ou plus  | Produit prêt pour le client, discret | Sélectionnez le type approprié / Discret ou intégré |

## <a name="example-component-usage"></a>Exemple d’utilisation des composants

Vous trouverez ci-dessous des exemples de la façon dont un fabricant OEM appelé Contoso utilise la caractéristique des composants pour certifier son produit, appelé Falcon.

1. Falcon est un appareil autonome complet qui ne s’intègre pas à un produit plus grand.
    1. Non. de composants : 1
    1. Type d’appareil composant : Produit prêt pour le client
    1. Type d’accessoire : Discret

     ![Image du produit prêt pour le client](./media/images/customer-ready-product.png)

1. Falcon est un appareil qui comprend un module de caméra intégré fabriqué par INC Electronics qui se connecte via USB à Falcon.
    1. Non. de composants : 2
    1. Type d’appareil composant : Produit prêt pour le client, Périphérique
    1. Type d’accessoire : Discret, Intégré
    
    > [!Note]
    > Le composant périphérique est considéré comme intégré, car il n’est pas amovible.

     ![Image d’un exemple de composant périphérique](./media/images/peripheral.png)

1. Falcon est un appareil qui inclut un système intégré sur module d’INC Electronics qui utilise un processeur intégré Apollo52 de la société Espressif et qui a une architecture ARM64.
    1. Non. de composants : 2
    1. Type d’appareil composant : Produit prêt pour le client, Système sur module
    1. Type d’accessoire : Discret, Intégré

    > [!Note]
    > Le composant périphérique est considéré comme intégré, car il n’est pas amovible. Le composant Système sur module inclut également des informations sur le processeur.

     ![Image d’un exemple de composant de système sur module ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Conseils supplémentaires

Vous trouverez ci-dessous des précisions concernant notre politique d’utilisation des composants. Si vous avez des questions sur l’utilisation appropriée des composants, contactez notre équipe à l’adresse [iotcert@microsoft.com](mailto:iotcert@microsoft.com), et nous serons ravis de vous aider !

1. Un projet ne doit contenir qu’**un seul** composant Produit prêt pour le client. Si vous certifiez un projet avec deux appareils indépendants, ces appareils doivent être certifiés séparément.
1. Il vous appartient principalement d’utiliser (ou de ne pas utiliser) les composants pour promouvoir les capacités de votre appareil auprès de clients potentiels.
1. Pendant l’examen de votre appareil, la seule exigence de l’équipe de certification Azure sera de répertorier au moins un composant Produit prêt pour le client. Toutefois, nous pouvons demander la modification des informations relatives aux composants si les détails ne sont pas clairs ou semblent manquer (par exemple, le fabricant du composant n’est pas fourni pour un type Produit prêt pour le client).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes prêt à utiliser la caractéristique des composants, vous pouvez renseigner les détails de votre appareil ou modifier votre projet pour plus de clarté.

- [Tutoriel : Ajout des détails de l’appareil](tutorial-02-adding-device-details.md)
- [Modification de votre appareil publié](how-to-edit-published-device.md)

