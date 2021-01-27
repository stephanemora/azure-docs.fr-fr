---
title: Configurer Azure Attestation avec le portail Azure
description: Découvrez comment installer et configurer un fournisseur d’attestation avec la portail Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ab1e6011a1c127c9ac5a2c7652a4bf458372e1e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733935"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>Démarrage rapide : Configurer Azure Attestation avec le portail Azure

Suivez les étapes ci-dessous pour gérer un fournisseur d’attestation à l’aide du portail Azure.

## <a name="attestation-provider"></a>Fournisseur d’attestations

### <a name="create-an-attestation-provider"></a>Créer un fournisseur d’attestation

#### <a name="to-configure-the-provider-with-unsigned-policies"></a>Pour configurer le fournisseur avec des stratégies non signées

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Créer une ressource**.
2.  Dans la zone de recherche, entrez **attestation**.
3.  Dans la liste des résultats, choisissez **Microsoft Azure Attestation**.
4.  Dans la page Microsoft Azure Attestation, choisissez **Créer**.
5.  Dans la page Créer un fournisseur d’attestation, fournissez les entrées suivantes :
    
    **Abonnement**: Sélectionnez un abonnement
    
    **Groupe de ressources** : sélectionnez un groupe de ressources existant ou choisissez **Créer nouveau** et entrez un nom de groupe de ressources.
    
    **Nom** : un nom unique est obligatoire.

    **Emplacement** : choisissez un emplacement. 
    
    **Fichier des certificats de signataire de stratégie** : ne pas télécharger le fichier des certificats de signataire de la stratégie pour configurer le fournisseur avec des stratégies non signées. 
6.  Après avoir fourni les entrées requises, cliquez sur **Vérifier + créer**.
7.  Corrigez les problèmes de validation, le cas échéant, puis cliquez sur **Créer**.

#### <a name="to-configure-the-provider-with-signed-policies"></a>Pour configurer le fournisseur avec des stratégies signées

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Créer une ressource**.
2.  Dans la zone de recherche, entrez **attestation**.
3.  Dans la liste des résultats, choisissez **Microsoft Azure Attestation**.
4.  Dans la page Microsoft Azure Attestation, choisissez **Créer**.
5.  Dans la page Créer un fournisseur d’attestation, fournissez les informations suivantes :
    
    a. **Abonnement**: Sélectionnez un abonnement
    
    b. **Groupe de ressources** : sélectionnez un groupe de ressources existant ou choisissez **Créer nouveau** et entrez un nom de groupe de ressources.
    
    c. **Nom** : un nom unique est obligatoire.

    d. **Emplacement** : choisissez un emplacement. 
    
    e. **Fichier des certificats de signataire de stratégie** : Pour configurer le fournisseur d’attestation avec des certificats de signature de stratégie, chargez le fichier de certificats. Consultez les exemples [ici](./policy-signer-examples.md). 
6.  Après avoir fourni les entrées requises, cliquez sur **Vérifier + créer**.
7.  Corrigez les problèmes de validation, le cas échéant, puis cliquez sur **Créer**.

### <a name="view-attestation-provider"></a>Afficher le fournisseur d’attestation

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation et sélectionnez-le.

### <a name="delete-attestation-provider"></a>Supprimer un fournisseur d’attestation

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Cochez la case et cliquez sur **Supprimer**.
4.  Tapez Oui, puis cliquez sur **Supprimer** [OU]
1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Supprimer** dans le menu du haut, puis sur **Oui**.


## <a name="attestation-policy-signers"></a>Signataires de stratégie d’attestation

### <a name="view-policy-signer-certificates"></a>Afficher les certificats de signataire de stratégie

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Certificats de signataire de stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Cliquez sur **Télécharger les certificats de signataire de stratégie** (le bouton sera désactivé pour les fournisseurs d’attestations créés sans exigence de signature de stratégie).
6.  Le fichier texte téléchargé aura tous les certificats au format JWS.
a.  Vérifiez le nombre de certificats et les certificats téléchargés.

### <a name="add-policy-signer-certificate"></a>Ajouter un certificat de signataire de stratégie

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Certificats de signataire de stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Cliquez sur **Ajouter** dans le menu du haut (le bouton sera désactivé pour les fournisseurs d’attestations créés sans exigence de signature de stratégie).
6.  Chargez le fichier de certificat de signataire de stratégie, puis cliquez sur **Ajouter**. Consultez les exemples [ici](./policy-signer-examples.md).

### <a name="delete-policy-signer-certificate"></a>Supprimer le certificat de signataire de stratégie

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Certificats de signataire de stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Cliquez sur **Supprimer** dans le menu du haut (le bouton sera désactivé pour les fournisseurs d’attestations créés sans exigence de signature de stratégie).
6.  Chargez le fichier de certificat de signataire de stratégie, puis cliquez sur **Supprimer**. Consultez les exemples [ici](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Stratégie d’attestation

### <a name="view-attestation-policy"></a>Afficher la stratégie d’attestation

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Sélectionnez le **Type d’attestation** par défaut et consultez la **Stratégie actuelle**.

### <a name="configure-attestation-policy"></a>Configurer la stratégie d’attestation

#### <a name="when-attestation-provider-is-created-without-policy-signing-requirement"></a>Lorsque le fournisseur d’attestation est créé sans exigence de signature de stratégie

##### <a name="upload-policy-in-jwt-format"></a>Charger la stratégie au format JWT

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Cliquez sur **Configurer** dans le menu du haut.
6.  Lorsque le fournisseur d’attestation est créé sans exigence de signature de stratégie, l’utilisateur peut charger une stratégie au format **JWT** ou **Texte**.
7.  Sélectionnez **JWT** comme **Format de stratégie**.
8.  Chargez le fichier de stratégie avec le contenu de la stratégie dans un format **JWT non signé/signé**, puis cliquez sur **Enregistrer**. Consultez les exemples [ici](./policy-examples.md).
    
    Pour l’option de chargement de fichier, l’aperçu de la stratégie s’affiche au format texte et n’est pas modifiable.

7.  Cliquez sur **Actualiser** dans le menu du haut pour afficher la stratégie configurée.

##### <a name="upload-policy-in-text-format"></a>Charger la stratégie au format texte

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Cliquez sur **Configurer** dans le menu du haut.
6.  Lorsque le fournisseur d’attestation est créé sans exigence de signature de stratégie, l’utilisateur peut charger une stratégie au format **JWT** ou **Texte**.
7.  Sélectionnez **Texte** comme **Format de stratégie**.
8.  Chargez le fichier de stratégie avec le contenu au format **Texte**, ou entrez le contenu de la stratégie dans la zone de texte, puis cliquez sur **Enregistrer**. Consultez les exemples [ici](./policy-examples.md).

    Pour l’option de chargement de fichier, l’aperçu de la stratégie s’affiche au format texte et n’est pas modifiable.

8.  Cliquez sur **Actualiser** pour afficher la stratégie configurée.

#### <a name="when-attestation-provider-is-created-with-policy-signing-requirement"></a>Lorsque le fournisseur d’attestation est créé avec exigence de signature de stratégie

##### <a name="upload-policy-in-jwt-format"></a>Charger la stratégie au format JWT

1.  Dans le menu du portail Azure ou dans la page Accueil, sélectionnez **Toutes les ressources**.
2.  Dans la zone de filtre, entrez le nom du fournisseur d’attestation.
3.  Sélectionnez le fournisseur d’attestation et accédez à la page de vue d’ensemble.
4.  Cliquez sur **Stratégie** dans le menu gauche de la ressource ou dans le volet inférieur.
5.  Cliquez sur **Configurer** dans le menu du haut.
6.  Lorsque le fournisseur d’attestation est créé avec exigence de signature de stratégie, l’utilisateur peut charger une stratégie uniquement au **format JWT signé**.
7.  Chargez le fichier de stratégie au **format JWT signé**, puis cliquez sur **Enregistrer**. Consultez les exemples [ici](./policy-examples.md).

    Pour l’option de chargement de fichier, l’aperçu de la stratégie s’affiche au format texte et n’est pas modifiable.
    
8.  Cliquez sur **Actualiser** pour afficher la stratégie configurée.

