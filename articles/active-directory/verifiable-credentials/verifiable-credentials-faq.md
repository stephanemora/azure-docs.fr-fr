---
title: Forum aux questions – Justificatifs vérifiables Azure (préversion)
description: Trouvez des réponses aux questions les plus fréquentes sur les Justificatifs vérifiables Azure
author: barclayn
manager: davba
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: c9253ce81b64b45d0ab8a72605eeba4c0d49f3f6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110457888"
---
# <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ)

Cette page contient des questions fréquemment posées sur les Justificatifs vérifiables et l’identité décentralisée. Les questions sont organisées dans les sections suivantes.

- [Vocabulaire et concepts de base](#the-basics)
- [Questions conceptuelles sur l’identité décentralisée](#conceptual-questions)
- [Questions sur l’utilisation des Justificatifs vérifiables en préversion](#using-the-preview)

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Concepts de base

### <a name="what-is-a-did"></a>Qu’est-ce qu’un identificateur décentralisé (DID) ? 

Les identificateurs décentralisés sont des identificateurs utilisables pour sécuriser l’accès aux ressources, signer et vérifier les justificatifs et faciliter l’échange de données d’application. Contrairement aux noms d’utilisateur et aux adresses e-mail traditionnels, les identificateurs décentralisés sont détenus et contrôlés par l’entité elle-même (qu’il s’agisse d’une personne, d’un appareil ou d’une société). Les identificateurs décentralisés existent indépendamment de toute organisation externe ou de tout intermédiaire approuvé. La [spécification W3C sur les identificateurs décentralisés](https://www.w3.org/TR/did-core/) explique cela en détail.

### <a name="why-do-we-need-a-did"></a>Pourquoi avons-nous besoin d’un identificateur décentralisé ?

La confiance numérique exige que les participants possèdent et contrôlent leurs identités, et une identité commence au niveau de l’identificateur.
À une époque de failles système et d’attaques quotidiennes d’envergure dirigées contre les identificateurs centralisés, la décentralisation des identités devient un besoin de sécurité critique pour les consommateurs et les entreprises.
Les individus possédant et contrôlant leurs identités sont en mesure d’échanger des données et des preuves vérifiables. Un environnement d’informations d’identification distribuées permet l’automatisation de nombreux processus d’entreprise qui sont actuellement manuels et fastidieux.

### <a name="what-is-a-verifiable-credential"></a>Que sont les Justificatifs vérifiables ? 

Les informations d’identification font partie de nos vies quotidiennes : permis de conduire attestant que nous sommes capables de faire fonctionner un véhicule automobile, diplômes universitaires attestant de notre niveau de formation, ou passeports émis par les pouvoirs publics nous permettant de voyager dans différents pays. Les Justificatifs vérifiables constituent un mécanisme permettant d’exprimer ces types d’informations d’identification sur le web d’une manière sécurisée sur le plan du chiffrement, respectueuse de la confidentialité et vérifiable par un ordinateur. La [spécification W3C sur les identificateurs décentralisés](https://www.w3.org/TR/vc-data-model//) décrit cela en détail.


## <a name="conceptual-questions"></a>Questions conceptuelles

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Que se passe-t-il quand un utilisateur perd son téléphone ? Peut-il récupérer son identité ?

Il existe plusieurs façons d’offrir un mécanisme de récupération aux utilisateurs. Chacune présente ses propres inconvénients. Nous évaluons actuellement diverses options, et concevons des approches de la récupération, à la fois pratiques et sécurisées, qui respectent la confidentialité et l’indépendance des utilisateurs.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Comment un utilisateur peut-il faire confiance à une demande émanant d’un émetteur ou d’un vérificateur ? Comment peut-il savoir si un identificateur décentralisé est bien celui d’une organisation ?

Nous avons implémenté la [spécification Well Known DID Configuration de la DIF (Decentralized Identity Foundation)](https://identity.foundation/.well-known/resources/did-configuration/) pour pouvoir connecter un identificateur décentralisé émis par un système existant connu, à des noms de domaine. Chaque identificateur décentralisé créé à l’aide des Justificatifs vérifiables Azure Active Directory peut inclure un nom de domaine racine qui sera encodé dans le document de l’identificateur décentralisé. Pour en savoir plus, lisez l’article [Lier votre domaine à votre identificateur décentralisé](how-to-dnsbind.md).  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Pourquoi la préversion Justificatifs vérifiables utilise-t-elle ION comme méthode de DID, et donc Bitcoin pour fournir une infrastructure de clé publique décentralisée ?

ION est un réseau de couche 2 d’identificateur décentralisé, sans autorisation et évolutif, qui s’exécute sur Bitcoin. Il offre une évolutivité sans inclure de jeton cryptoasset spécial, des validateurs approuvés ou des mécanismes de consensus centralisés. Nous utilisons Bitcoin pour le substrat de couche 1 de base en raison de la puissance du réseau décentralisé pour fournir un degré élevé d’immuabilité pour un système d’enregistrement chronologique des événements.

## <a name="using-the-preview"></a>Utilisation de la préversion

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Pourquoi dois-je utiliser NodeJS pour la préversion des Justificatifs vérifiables ? Quels sont les plans pour d’autres langages de programmation ? 

Nous avons choisi NodeJS parce qu’il s’agit d’une plateforme très populaire pour les développeurs d’applications. Nous publierons une API REST qui permettra aux développeurs d’émettre et de vérifier les informations d’identification. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>La préversion utilise-t-elle du code open source ?

Oui. Les référentiels suivants sont les composants open source de nos services.

1. [SideTree, sur GitHub](https://github.com/decentralized-identity/sidetree)
2. [Kit de développement logiciel (SDK) VC pour Node, sur GitHub](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [Android SDK pour la création de portefeuilles d’identités décentralisées, sur GitHub](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [Kit de développement logiciel (SDK) iOS pour la création de portefeuilles d’identités décentralisées, sur GitHub](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Quelles sont les conditions de licence ?

Une licence Azure AD P2 est nécessaire pour utiliser la préversion des Justificatifs vérifiables. Il s’agit d’une exigence temporaire, car nous pensons que ce service sera facturé en fonction de l’utilisation. 


## <a name="next-steps"></a>Étapes suivantes

- [Comment personnaliser vos justificatifs vérifiables Azure Active Directory](credential-design.md)
