---
title: 'Démarrage rapide : Configurer Azure Attestation à partir du portail Azure'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment configurer un fournisseur d’attestation à partir du portail Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d1310e3c4b4a56a27219cce613e8f6109d32c8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729387"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Démarrage rapide : Configurer Azure Attestation à partir du portail Azure

Suivez ce guide de démarrage rapide pour bien commencer avec Azure Attestation. Découvrez comment gérer un fournisseur d’attestation, un signataire de stratégie et une stratégie à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="attestation-provider"></a>Fournisseur d’attestations

Dans cette section, vous allez créer un fournisseur d’attestation et le configurer avec des stratégies non signées ou des stratégies signées. Vous aussi découvrir comment afficher et supprimer le fournisseur d’attestation.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Créer et configurer le fournisseur avec des stratégies non signées

1. Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Créer une ressource**.
1. Dans la zone de recherche, entrez **attestation**.
1. Dans la liste des résultats, sélectionnez **Microsoft Azure Attestation**.
1. Dans la page **Microsoft Azure Attestation**, sélectionnez **Créer**.
1. Dans la page **Créer un fournisseur d’attestation**, fournissez les entrées suivantes :

   - **Abonnement**: Choisissez un abonnement.
   - **Groupe de ressources** : Sélectionnez un groupe de ressources existant ou sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.
   - **Nom** : Entrez un nom unique.
   - **Emplacement** : Choisissez un emplacement.
   - **Fichier des certificats de signataire de stratégie** : Ne chargez pas le fichier des certificats de signataire de stratégie pour configurer le fournisseur avec des stratégies non signées.

1. Une fois que vous avez fourni les entrées nécessaires, sélectionnez **Vérifier + créer**.
1. S’il y a des problèmes de validation, corrigez-les, puis sélectionnez **Créer**.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Créer et configurer le fournisseur avec des stratégies signées

1. Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Créer une ressource**.
1. Dans la zone de recherche, entrez **attestation**.
1. Dans la liste des résultats, sélectionnez **Microsoft Azure Attestation**.
1. Dans la page **Microsoft Azure Attestation**, sélectionnez **Créer**.
1. Dans la page **Créer un fournisseur d’attestation**, fournissez les informations suivantes :

   - **Abonnement**: Choisissez un abonnement.
   - **Groupe de ressources** : Sélectionnez un groupe de ressources existant ou sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources.
   - **Nom** : Entrez un nom unique.
   - **Emplacement** : Choisissez un emplacement.
   - **Fichier des certificats de signataire de stratégie** : Chargez le fichier des certificats de signataire de stratégie pour configurer le fournisseur d’attestation avec des stratégies signées. [Voir des exemples de certificats de signataire de stratégie](./policy-signer-examples.md).

1. Une fois que vous avez fourni les entrées nécessaires, sélectionnez **Vérifier + créer**.
1. S’il y a des problèmes de validation, corrigez-les, puis sélectionnez **Créer**.

### <a name="view-the-attestation-provider"></a>Afficher le fournisseur d’attestation

1. Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1. Dans la zone de filtre, entrez le nom du fournisseur d’attestation, puis sélectionnez-le.

### <a name="delete-the-attestation-provider"></a>Supprimer le fournisseur d’attestation

Il existe deux façons de supprimer le fournisseur d’attestation. Vous pouvez :

1. Accéder au menu ou à la page d’accueil du portail Azure, puis sélectionner **Toutes les ressources**.
1. Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1. Cochez la case et sélectionnez **Supprimer**.
1. Entrez **oui** et sélectionnez **Supprimer**.

Vous pouvez également :

1. Accéder au menu ou à la page d’accueil du portail Azure, puis sélectionner **Toutes les ressources**.
1. Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1. Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1. Sélectionnez **Supprimer** dans la barre de menus et sélectionnez **Oui**.

## <a name="attestation-policy-signers"></a>Signataires de stratégie d’attestation

Suivez les étapes de cette section pour afficher, ajouter et supprimer des certificats de signataire de stratégie.

### <a name="view-the-policy-signer-certificates"></a>Afficher les certificats de signataire de stratégie

1. Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1. Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1. Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1. Sélectionnez **Certificats de signataire de stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur. Vous voyez une invite à sélectionner le certificat pour l’authentification. Choisissez l’option appropriée pour continuer.
1. Sélectionnez **Télécharger les certificats de signataire de stratégie**. Le bouton est désactivé pour les fournisseurs d’attestation créés sans l’obligation de signature de stratégie.
1. Le fichier texte téléchargé aura tous les certificats au format JWS.
1. Vérifiez le nombre de certificats et les certificats téléchargés.

### <a name="add-the-policy-signer-certificate"></a>Ajouter le certificat de signataire de stratégie

1.  Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1.  Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1.  Sélectionnez **Certificats de signataire de stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur.
1.  Sélectionnez **Ajouter** dans le menu supérieur. Le bouton est désactivé pour les fournisseurs d’attestation créés sans l’obligation de signature de stratégie.
1.  Chargez le fichier de certificat de signataire de stratégie et sélectionnez **Ajouter**. [Voir des exemples de certificats de signataire de stratégie](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Supprimer les certificats de signataire de stratégie

1.  Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1.  Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1.  Sélectionnez **Certificats de signataire de stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur.
1.  Sélectionnez **Supprimer** dans le menu supérieur. Le bouton est désactivé pour les fournisseurs d’attestation créés sans l’obligation de signature de stratégie.
1.  Chargez le fichier de certificat de signataire de stratégie et sélectionnez **Supprimer**. [Voir des exemples de certificats de signataire de stratégie](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Stratégie d’attestation

Cette section explique comment afficher une stratégie d’attestation et comment configurer les stratégies qui ont été créées avec et sans obligation de signature de stratégie.

### <a name="view-an-attestation-policy"></a>Afficher une stratégie d’attestation

1.  Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1.  Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1.  Sélectionnez **Stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur. Vous voyez une invite à sélectionner le certificat pour l’authentification. Choisissez l’option appropriée pour continuer.
1.  Sélectionnez le **Type d’attestation** par défaut et affichez la **Stratégie actuelle**.

### <a name="configure-an-attestation-policy"></a>Configurer une stratégie d’attestation

Suivez ces étapes pour charger une stratégie au format JWT ou texte si le fournisseur d’attestation a été créé sans obligation de signature de stratégie.

1. Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1. Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1. Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1. Sélectionnez **Stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur.
1. Sélectionnez **Configurer** dans le menu supérieur.
1. Sélectionnez **JWT** ou **Texte** comme **Format de stratégie**.

   Si le fournisseur d’attestation est créé sans obligation de signature de stratégie, l’utilisateur peut charger une stratégie au format **JWT** ou **Texte**.

      - Si vous avez choisi le format JWT, chargez le fichier de stratégie avec le contenu de la stratégie au format **JWT non signé/signé**, puis sélectionnez **Enregistrer**. [Voir des exemples de stratégies](./policy-examples.md).
      - Si vous avez choisi le format texte, chargez le fichier de stratégie dont le contenu est au format **Texte** ou entrez le contenu de la stratégie dans la zone de texte, puis sélectionnez **Enregistrer**. [Voir des exemples de stratégies](./policy-examples.md).

   Pour l’option de chargement de fichier, l’aperçu de la stratégie est affiché au format texte et ne peut pas être modifié.

1. Sélectionnez **Actualiser** dans le menu supérieur pour afficher la stratégie configurée.


Si le fournisseur d’attestation a été créé avec une obligation de signature de stratégie, suivez ces étapes pour charger une stratégie au format JWT.

1.  Accédez au menu ou à la page d’accueil du portail Azure, puis sélectionnez **Toutes les ressources**.
1.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
1.  Sélectionnez le fournisseur d’attestation et accédez à la page Vue d’ensemble.
1.  Sélectionnez **Stratégie** dans le menu de la ressource sur le côté gauche de la fenêtre ou dans le volet inférieur.
1.  Sélectionnez **Configurer** dans le menu supérieur.
1.  Chargez le fichier de stratégie au **format JWT signé**, puis sélectionnez **Enregistrer**. [Voir des exemples de stratégies](./policy-examples.md).

    Si le fournisseur d’attestation a été créé avec une obligation de signature de stratégie, l’utilisateur peut charger une stratégie uniquement au **format JWT signé**.

    Pour l’option de chargement de fichier, l’aperçu de la stratégie est affiché au format texte et ne peut pas être modifié.
    
1.  Sélectionnez **Actualiser** pour afficher la stratégie configurée.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour créer et signer une stratégie d’attestation](author-sign-policy.md)
- [Attester une enclave SGX à l’aide d’exemples de code](/samples/browse/?expanded=azure&terms=attestation)

