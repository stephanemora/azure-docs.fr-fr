---
title: Qu’est-ce qu’Azure Form Recognizer?
titleSuffix: Azure Applied AI Services
description: Le service Azure Form Recognizer vous permet d’identifier et d’extraire des paires clé/valeur et des données de table de vos formulaires, ainsi que d’extraire des informations importantes des tickets de caisse et des cartes de visite.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: traitement de données automatisé, traitement de documents, entrée de données automatisée, traitement des formulaires
ms.openlocfilehash: 1688cc686b11fd6a6edb129a7ac01a048ae88f4a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111957499"
---
# <a name="what-is-azure-form-recognizer"></a>Qu’est-ce qu’Azure Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer fait partie d’[Azure Applied AI Services](../../applied-ai-services/index.yml) qui vous permet de créer des logiciels de traitement de données automatisé à l’aide des technologies du Machine Learning. Identifiez et extrayez du texte, des paires clé/valeur, des marques de sélection, des tableaux et une structure de vos documents : le service produit des données structurées qui incluent les relations dans le fichier d’origine, des rectangles englobants, la confiance, etc. Vous pouvez obtenir des résultats rapidement, avec précision et en adéquation avec votre contenu particulier, sans la nécessité d’une intervention manuelle lourde ou de compétences approfondies en science des données. Utilisez Form Recognizer pour automatiser l’entrée de données dans vos applications et enrichir les fonctionnalités de recherche de vos documents.

Form Recognizer est constitué de modèles de traitement de documents personnalisés, de modèles prédéfinis pour les factures, reçus, ID et cartes de visite, et du modèle de disposition. Vous pouvez appeler les modèles Form Recognizer au moyen d’une API REST ou de SDK de bibliothèque de client, afin d’en réduire la complexité et de l’intégrer à votre workflow ou application.

Cette documentation contient les types d’articles suivants :

* Les [**Concepts**](concept-layout.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.
* Les [**Démarrages rapides**](quickstarts/client-library.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](build-training-data-set.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [**Tutoriels**](tutorial-ai-builder.md) sont des guides plus longs qui montrent comment utiliser le service en tant que composant dans des solutions métier élargies.

## <a name="form-recognizer-features"></a>Fonctionnalités de Form Recognizer

Avec Form Recognizer, vous pouvez facilement extraire et analyser des données de documents grâce aux fonctionnalités suivantes :

### <a name="layout"></a>[Disposition](concept-layout.md)

Extrayez du texte, des marques de sélection et des structures de tableaux ainsi que les coordonnées de leurs rectangles englobants dans des documents.

Form Recognizer peut extraire de documents du texte, des marques de sélection et la structure des tableaux (les numéros de ligne et de colonne associés au texte) en utilisant la reconnaissance optique de caractères (OCR) haute définition et un modèle de deep learning amélioré.

:::image type="content" source="./media/tables-example.jpg" alt-text="exemples de tables" lightbox="./media/tables-example.jpg":::

### <a name="custom-models"></a>[Modèles personnalisés](concept-custom.md)

Extrayez du texte, des paires clé-valeur, des marques de sélection et les données de tableaux dans vos formulaires. Ces modèles étant entraînés avec vos propres données, ils sont adaptés à vos formulaires.

Les modèles personnalisés Form Recognizer effectuent l’entraînement avec vos propres données, et il vous suffit de cinq exemples de formulaires d’entrée pour démarrer. Un modèle de traitement de documents entraîné peut restituer des données structurées qui incluent les relations du document de formulaire d’origine. Une fois que le modèle est entraîné, vous pouvez le tester, le réentraîner et l’utiliser ensuite pour extraire de manière fiable des données d’autres formulaires, en fonction de vos besoins.

Quand vous entraînez des modèles personnalisés, vous disposez des options suivantes : entraînement avec des données étiquetées et sans données étiquetées.

#### <a name="train-without-labels"></a>Effectuer l’entraînement sans étiquettes

Form Recognizer utilise l’apprentissage non supervisé pour comprendre la disposition et les relations entre les champs et les entrées de vos formulaires. Quand vous soumettez vos formulaires d’entrée, l’algorithme groupe les formulaires par types, découvre les clés et les tableaux présents, et associe les valeurs aux clés et les entrées aux tableaux. L’entraînement sans étiquette ne nécessitant pas d’étiquetage manuel des données ni de programmation et de maintenance intensives, nous vous recommandons d’essayer cette méthode en premier.

Consultez [Créer un jeu de données d’entraînement](./build-training-data-set.md) pour obtenir des conseils sur la collecte de vos documents d’entraînement.

#### <a name="train-with-labels"></a>Effectuer l’entraînement avec des étiquettes

Quand vous effectuez l’entraînement avec des données étiquetées, le modèle utilise l’apprentissage supervisé afin d’extraire les valeurs dignes d’intérêt, à l’aide des formulaires étiquetés que vous fournissez. Des données étiquetées aboutissent à des modèles plus performants et peuvent engendrer des modèles qui fonctionnent avec des formulaires complexes ou des formulaires contenant des valeurs sans clés.

Form Recognizer utilise l’API [Layout](#layout) pour connaître les tailles et les positions attendues des éléments de texte imprimés et manuscrits et pour extraire des tables. Ensuite, il utilise des étiquettes spécifiées par l’utilisateur pour connaître les associations clé/valeur et les tables dans les documents. Nous vous recommandons d’utiliser cinq formulaires étiquetés manuellement du même type (même structure) pour commencer l’entraînement d’un nouveau modèle et d’ajouter des données étiquetées en fonction des besoins afin d’améliorer la justesse du modèle. Form Recognizer permet d’effectuer l’entraînement d’un modèle pour extraire des paires clé-valeur et des tables à l’aide de fonctionnalités d’apprentissage supervisé.

[Bien démarrer avec l’entraînement avec des étiquettes](label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


### <a name="prebuilt-models"></a>Modèles prédéfinis

 Form Recognizer comprend aussi des modèles prédéfinis pour le traitement automatisé des données des reçus, cartes de visite, factures et documents d’identité.

### <a name="receipts"></a>[Reçus](concept-receipts.md)

Le modèle Prebuilt Receipt (Ticket de caisse prédéfini) permet de lire les tickets de caisse en anglais émis en Australie, au Canada, en Grande-Bretagne, en Inde et aux États-Unis&mdash;du type utilisé dans les restaurants, les stations-service, les commerces, etc. Ce modèle extrait des informations clés telles que la date et l’heure de la transaction, les détails du commerçant, le montant des taxes, les articles, les totaux, etc. En outre, le modèle de reçu préconstruit est entraîné pour analyser et retourner tout le texte d’un reçu.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="exemple de ticket de caisse" lightbox="./media/overview-receipt.jpg":::

### <a name="business-cards"></a>[Cartes de visite](concept-business-cards.md)

Le modèle Business Cards (Cartes de visite) vous permet d’extraire des informations d’une carte de visite rédigée en anglais telles que le nom, la fonction, l’adresse postale, l’adresse e-mail, la société et les numéros de téléphone de la personne.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="exemple de carte de visite" lightbox="./media/overview-business-card.jpg":::

### <a name="invoices"></a>[Factures](concept-invoices.md)

Le modèle de facture prédéfini extrait les données des factures dans différents formats, et retourne des données structurées. Ce modèle extrait des informations clés, comme l’ID de facture, les détails du client, les détails du fournisseur, le destinataire de l’envoi, le destinataire de la facture, le total, les taxes, le sous-total, les éléments de ligne, et ainsi de suite. De plus, le modèle de facture prédéfini est entraîné pour analyser et retourner tout le texte et tous les tableaux de la facture.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Exemple de facture" lightbox="./media/overview-invoices.jpg":::

### <a name="identity-documents"></a>[Documents d’identité](concept-identification-cards.md)

Le modèle Documents d’identité (ID) vous permet d’extraire les informations clés de passeports du monde entier et de permis de conduire des États-Unis. Il extrait des données telles que l’ID de document, la date de naissance, la date d’expiration, le nom, le pays, la région, la zone lisible par machine et plus encore.

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="exemple de carte d’identité" lightbox="./media/overview-id.jpg":::

## <a name="get-started"></a>Bien démarrer

Utilisez l’exemple d’outil Form Recognizer qui permet de tester Disposition, les Modèles prédéfinis, et d’effectuer l’entraînement d’un modèle personnalisé pour vos documents. Vous aurez besoin d’un abonnement Azure ([**créez-en un gratuitement**](https://azure.microsoft.com/free/cognitive-services)) ainsi que d’une clé et d’un point de terminaison de [**ressource Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) pour tester le service Form Recognizer.

### <a name="v21"></a>[v2.1](#tab/v2-1)

>
> [!div class="nextstepaction"]
> [Essayer Form Recognizer](https://aka.ms/fott-2.1-ga/)
>

### <a name="v20"></a>[v2.0](#tab/v2-0)

>
> [!div class="nextstepaction"]
> [Essayer Form Recognizer](https://fott.azurewebsites.net/)

---

Suivez le [guide de démarrage rapide de la bibliothèque de client/API REST](./quickstarts/client-library.md) pour commencer à extraire des données de vos documents. Nous vous recommandons d’utiliser le service gratuit pendant que vous apprenez la technologie. N’oubliez pas que le nombre de pages gratuites est limité à 500 par mois.

### <a name="v21"></a>[v2.1](#tab/v2-1)

Explorez la [documentation de référence sur l’API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) pour en savoir plus. Si vous êtes familiarisé avec une version précédente de l’API, consultez l’article [Nouveautés](./whats-new.md) pour en savoir plus sur les modifications récentes.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Explorez la [documentation de référence sur l’API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) pour en savoir plus. Si vous êtes familiarisé avec une version précédente de l’API, consultez l’article [Nouveautés](./whats-new.md) pour en savoir plus sur les modifications récentes.

---

## <a name="input-requirements"></a>Critères des entrées

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="service-availability-and-redundancy"></a>Disponibilité et redondance du service

### <a name="is-form-recognizer-service-zone-resilient"></a>Le service Form Recognizer est-il résilient aux zones ?

Oui. Le service Form Recognizer est résilient aux zones par défaut.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Comment configurer le service Form Recognizer pour le rendre résilient aux zones ?

Aucune configuration client n’est nécessaire pour activer la résilience des zones. La résilience aux zones pour les ressources Form Recognizer est disponible par défaut et gérée par le service lui-même.

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

* Comme c’est le cas pour tous les services Cognitive Services, les développeurs utilisant le service Form Recognizer doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

* Form Recognizer ne stocke pas ou ne traite pas les données client situées en dehors de la région dans laquelle le client déploie l’instance de service.

## <a name="next-steps"></a>Étapes suivantes

Essayez notre outil en ligne et notre guide de démarrage rapide pour en savoir plus sur le service Form Recognizer.

* [**Outil Form Recognizer**](https://aka.ms/fott-2.1-ga)
* [**Démarrage rapide de la bibliothèque de client et de l’API REST**](quickstarts/client-library.md)