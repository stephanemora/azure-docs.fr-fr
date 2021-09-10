---
title: Présentation de l’architecture d’Azure Active Directory (préversion)
description: Découvrez les informations fondamentales vous permettant de planifier et concevoir votre solution.
documentationCenter: ''
author: barbaraselden
manager: martinco
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 07/20/2021
ms.author: baselden
ms.openlocfilehash: 45dd0be664474b22a24354903233e5729479f095
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531410"
---
# <a name="azure-ad-verifiable-credentials-architecture-overview-preview"></a>Présentation de l’architecture des justificatifs vérifiables Azure AD (préversion)

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il est important de planifier votre solution de justificatifs vérifiables de sorte qu’en plus de l’émission ou de la validation des informations d’identification, vous disposiez d’une vue complète de l’impact sur l’architecture et l’activité de votre solution. Si vous ne l’avez pas encore fait, nous vous recommandons de lire [Présentation des justificatifs vérifiables Azure Active Directory](decentralized-identifier-overview.md) et les [ FAQ](verifiable-credentials-faq.md), puis de suivre le tutoriel [Prise en main](get-started-verifiable-credentials.md). 

Cette présentation de l’architecture introduit les capacités et les composants du service Justificatifs vérifiables d’Azure Active Directory. Pour plus d’informations sur l’émission et la validation, consultez : 

* [Planifier votre solution d'émission](plan-issuance-solution.md)

* [Planifier votre solution de vérification](plan-verification-solution.md)

## <a name="approaches-to-identity"></a>Approches de l’identité

Aujourd’hui, la plupart des organisations utilisent des systèmes d’identité centralisés pour fournir des informations d’identification aux employés. Elles utilisent également diverses méthodes pour faire entrer les clients, les partenaires, les fournisseurs et les parties de confiance dans les limites d’approbation de l’organisation. Ces méthodes comprennent la fédération, la création et la gestion de comptes d’invités avec des systèmes comme Azure AD B2B, et la création d’approbations explicites avec des parties de confiance. La plupart des relations commerciales ont une composante numérique, de sorte que l’instauration d’une certaine forme de confiance entre les organisations nécessite des efforts importants. 

### <a name="centralized-identity-systems"></a>Systèmes d’identité centralisés

Les approches centralisées fonctionnent toujours bien dans de nombreux cas, par exemple lorsque les applications, les services et les appareils s’appuient sur les mécanismes de confiance utilisés dans un domaine ou une limite de confiance. 

Dans les systèmes d’identité centralisés, le fournisseur d’identité (IDP) contrôle le cycle de vie et l’utilisation des informations d’identification. 

![Exemple d’un système d’identité centralisé](./media/introduction-to-verifiable-credentials-architecture/centralized-identity-architecture.png)


Toutefois, il existe des scénarios où l’utilisation d’une architecture décentralisée avec des justificatifs vérifiables peut apporter de la valeur en renforçant des scénarios clés tels que :

* intégration sécurisée des identités des employés et d’autres personnes, y compris à distance.

* accès aux ressources à l’intérieur de la limite de confiance de l’organisation en fonction de critères spécifiques.

* accès aux ressources en dehors de la limite de confiance, comme l’accès aux ressources des partenaires, avec des informations d’identification portables émises par l’organisation.

 

### <a name="decentralized-identity-systems"></a>Systèmes d’identité décentralisés

Dans les systèmes d’identité décentralisés, le contrôle du cycle de vie et de l’utilisation des informations d’identification est partagé entre l’émetteur, le détenteur et la partie de confiance qui consomme les informations d’identification.

Prenons l’exemple du diagramme ci-dessous, où Proseware, un site web d’e-commerce, souhaite offrir aux employés de Woodgrove des remises d’entreprise. 

 ![Exemple d’un système d’identité décentralisé](media/introduction-to-verifiable-credentials-architecture/decentralized-architecture.png)


La terminologie relative aux justificatifs vérifiables peut prêter à confusion si vous n’êtes pas familiarisé avec les justificatifs vérifiables. Les définitions suivantes proviennent de la section terminologie du [modèle de données des justificatifs vérifiables 1.0](https://www.w3.org/TR/vc-data-model/). Après chacune d’elles, nous les associons aux entités du diagramme précédent.

 Un ***émetteur*** est un rôle qu’une entité peut jouer en déclarant des revendications sur un ou plusieurs sujets, en créant des justificatifs vérifiables à partir de ces revendications et en transmettant les justificatifs vérifiables à un détenteur. »

* Dans le diagramme précédent, Woodgrove est l’émetteur des justificatifs vérifiables pour ses employés. 

 « Un ***détenteur*** est un rôle qu’une entité peut remplir en possédant un ou plusieurs justificatifs vérifiables et en générant des présentations à partir de ceux-ci. Un détenteur est généralement, mais pas toujours, le sujet des justificatifs vérifiables qu’il détient. Les détenteurs stockent leurs informations d’identification dans les référentiels d’informations d’identification. » 

* Dans le diagramme précédent, Alice est une employée de Woodgrove. Elle a obtenu des justificatifs vérifiables auprès de l’émetteur Woodgrove et est la détentrice de ces justificatifs.

 « Un ***vérificateur*** est un rôle qu’une entité remplit en recevant un ou plusieurs justificatifs vérifiables, éventuellement à l’intérieur d’une présentation vérifiable à des fins de traitement. D’autres spécifications peuvent faire référence à ce concept en tant que partie de confiance. »

* Dans le diagramme précédent, Proseware est un vérificateur des informations d’identification émises par Woodgrove. 

« Des ***informations d’identification*** sont un ensemble d’une ou plusieurs revendications émanant d’un émetteur. Les justificatifs vérifiables sont des justificatifs infalsifiables dont l’auteur peut être vérifié par chiffrement. Les justificatifs vérifiables peuvent être utilisés pour générer des présentations vérifiables, qui peuvent également être vérifiées par chiffrement. Les revendications des informations d’identification peuvent porter sur différents sujets. »

 « Un ***identifiant décentralisé*** est un identifiant portable basé sur l’URI, également connu sous le nom de DID, associé à une entité. Ces identifiants sont souvent utilisés dans des justificatifs vérifiables et sont associés à des sujets, des émetteurs et des vérificateurs. »

* Dans le diagramme précédent, il est montré que les clés publiques des DID de l’intervenant sont stockées dans le document d’identifiant décentralisé, lui-même stocké dans le registre décentralisé Identity Overlay Network (ION).

 « Un ***document d’identifiant décentralisé** _, également appelé _*_document DID_**, est un document accessible à l’aide d’un registre de données vérifiable et qui contient des informations relatives à un identifiant décentralisé spécifique, telles que le référentiel associé et les informations sur la clé publique. » 

* Dans le scénario ci-dessus, l’émetteur et le vérificateur ont tous deux un DID, ainsi qu’un document DID. Le document DID contient la clé publique et la liste des domaines web DNS associés au DID (également appelés domaines liés).

* Woodgrove (émetteur) signe les justificatifs vérifiables de ses employés avec sa clé publique ; de même, Proseware (vérificateur) signe les demandes de présentation de justificatifs vérifiables à l’aide de sa clé, qui est également associée à son DID.

 « Un ***registre distribué*** est un système non centralisé d’enregistrement des événements. Ces systèmes établissent une confiance suffisante pour que les participants puissent se fier aux données enregistrées par d’autres personnes pour prendre des décisions opérationnelles. Ils utilisent généralement des bases de données distribuées dans lesquelles différents nœuds utilisent un protocole de consensus pour confirmer le classement des transactions signées par chiffrement. La liaison de transactions signées numériquement dans le temps rend souvent l’historique du registre immuable. »

* La solution Microsoft utilise ***Identity Overlay Network (ION)*** pour fournir une capacité décentralisée d’infrastructure à clé publique (PKI).

 

### <a name="combining-centralized-and-decentralized-identity-architectures"></a>Combinaison d’architectures d’identité centralisées et décentralisées

Lorsque vous examinez une solution de justificatifs vérifiables, il est utile de comprendre la manière dont les architectures d’identité décentralisées peuvent être combinées avec des architectures d’identité centralisées pour fournir une solution qui réduit les risques et offre des avantages opérationnels importants.

## <a name="the-user-journey"></a>Parcours utilisateur

Cette présentation de l’architecture suit le parcours d’un candidat et employé, qui postule à un emploi et l’accepte dans une organisation. Elle suit ensuite l’employé et l’organisation dans des modifications où les justificatifs vérifiables peuvent renforcer les processus centralisés.

 

### <a name="actors-in-these-use-cases"></a>Intervenants dans ces cas d’usage

* **Alice**, une personne qui postule à un emploi chez Woodgrove, Inc. et qui l’accepte.

* **Woodgrove, Inc**, une entreprise fictive.

* **Adatum**, le partenaire fictif de vérification d’identité de Woodgrove.

* **Proseware**, l’organisation partenaire fictive de Woodgrove.

Woodgrove utilise à la fois des architectures d’identité centralisées et décentralisées.

### <a name="steps-in-the-user-journey"></a>Étapes du parcours utilisateur

* Alice postule à un poste chez Woodgrove, Inc., l’accepte et intègre l’entreprise.

* Accès aux ressources numériques au sein de la limite de confiance de Woodgrove.

* Accès aux ressources numériques en dehors de la limite de confiance de Woodgrove sans étendre les limites de confiance de Woodgrove ou de ses partenaires.

Au fur et à mesure que Woodgrove poursuit ses activités, elle doit continuellement gérer les identités. Les cas d’usage de cette aide décrivent de quelle manière Alice peut utiliser des fonctions en libre-service pour obtenir et gérer ses identifiants et de quelle manière Woodgrove peut ajouter des relations interentreprises, les modifier et y mettre fin avec des exigences d’approbation variables. 

Ces cas d’usage illustrent la façon dont les identités centralisées et les identités décentralisées peuvent être combinées pour fournir une stratégie et un cycle de vie d’identité et d’approbation plus robustes et plus efficaces. 


## <a name="user-journey-onboarding-to-woodgrove"></a>Parcours utilisateur : Intégration chez Woodgrove

![Parcours d’intégration de l’utilisateur chez Woodgrove](media/introduction-to-verifiable-credentials-architecture/onboarding-journey.png)

 **Prise de conscience** : Alice est intéressée par un emploi chez Woodgrove, Inc. et consulte le site web de Woodgrove dédié au recrutement. 

**Activation** : Le site de Woodgrove présente à Alice une méthode permettant de prouver son identité en l’invitant, à l’aide d’un code QR ou d’un lien profond, à visiter son partenaire de confiance en matière de preuve d’identité, Adatum.

**Demande et chargement** : Adatum demande une preuve d’identité à Alice. Alice prend un selfie et une photo de son permis de conduire et les charge sur Adatum. 

**Émission** : Une fois qu’Adatum a vérifié l’identité d’Alice, il lui délivre des justificatifs vérifiables attestant de son identité.

**Présentation** : Alice (la détentrice et le sujet des informations d’identification) peut alors accéder au portail de recrutement de Woodgrove pour terminer le processus de candidature. Lorsqu’Alice utilise les justificatifs vérifiables pour accéder au portail, Woodgrove joue le rôle de vérificateur et de partie de confiance, faisant confiance à l’attestation d’Adatum.


### <a name="distributing-initial-credentials"></a>Distribution des informations d’identification initiales

Alice accepte un emploi chez Woodgrove. Dans le cadre du processus d’intégration, un compte Azure Active Directory (AD) est créé pour Alice afin qu’elle puisse l’utiliser au sein de la limite de confiance de Woodgrove. Le responsable d’Alice doit trouver comment permettre à Alice, qui travaille à distance, de recevoir ses informations de connexion initiale de manière sécurisée. Dans le passé, le service informatique aurait pu fournir ces informations d’identification à son responsable, qui les aurait imprimées et remises à Alice. Cela ne fonctionne pas avec les employés travaillant à distance.

Les justificatifs vérifiables peuvent apporter une valeur ajoutée aux systèmes centralisés en complétant le processus de distribution des informations d’identification. Au lieu de demander au responsable de fournir des informations d’identification, Alice peut utiliser ses justificatifs vérifiables comme preuve d’identité pour recevoir son nom d’utilisateur initial et ses informations d’identification initiaux pour l’accès aux systèmes centralisés. Alice présente la preuve d’identité qu’elle a ajoutée à son portefeuille dans le cadre du processus d’intégration. 

 

Dans le cas d’usage d’une intégration, les rôles de relation de confiance sont répartis entre l’émetteur, le vérificateur et le détenteur. 

* L’émetteur est chargé de valider les revendications qui font partie des justificatifs vérifiables qu’il émet. Adatum valide l’identité d’Alice pour émettre les justificatifs vérifiables. Dans ce cas, les justificatifs vérifiables sont émis sans l’intervention d’un vérificateur ou d’une partie de confiance.

* Le détenteur possède les justificatifs vérifiables et initie la présentation des justificatifs vérifiables pour vérification. Seule Alice peut présenter les justificatifs vérifiables qu’elle détient.

* Le vérificateur accepte les revendications des justificatifs vérifiables provenant d’émetteurs en qui il a confiance et valide les justificatifs vérifiables en utilisant la capacité du registre décentralisé décrite dans le modèle de données des justificatifs vérifiables. Woodgrove fait confiance aux revendications d’Adatum concernant l’identité d’Alice.

 

En combinant des architectures d’identité centralisées et décentralisées pour l’intégration, Woodgrove n’a pas besoin de stocker des informations privilégiées concernant Alice et nécessaires à la vérification de son identité, comme un numéro d’identification national, car elle fait confiance aux justificatifs vérifiables d’Alice émis par le partenaire de vérification d’identité (Adatum). La duplication des efforts est réduite au minimum, et une approche programmatique et prévisible des tâches d’intégration initiale peut être implémentée. 

 

## <a name="user-journey-accessing-resources-inside-the-trust-boundary"></a>Parcours utilisateur : Accès aux ressources à l’intérieur de la limite de confiance

![Accès aux ressources à l’intérieur de la limite de confiance](media/introduction-to-verifiable-credentials-architecture/inside-trust-boundary.png)

En tant qu’employé, Alice opère à l’intérieur de la limite de confiance de Woodgrove. Woodgrove agit en tant que fournisseur d’identité (IDP) et maintient un contrôle complet de l’identité et de la configuration des applications qu’Alice utilise pour interagir au sein de la limite de confiance de Woodgrove. Pour utiliser les ressources dans la limite de confiance d’Azure AD, Alice fournit potentiellement plusieurs formes de preuve d’identification pour se connecter à la limite de confiance de Woodgrove et accéder aux ressources dans l’environnement technologique de Woodgrove. Il s’agit d’un scénario classique qui est bien servi par une architecture d’identité centralisée. 

* Woodgrove gère la limite de confiance et, conformément aux meilleures pratiques en matière de sécurité, fournit le niveau d’accès le moins privilégié à Alice en fonction du travail effectué. Pour garantir une posture de sécurité forte et, éventuellement, pour des raisons de conformité, Woodgrove doit également être en mesure de suivre les autorisations des employés et leur accès aux ressources et doit être capable de révoquer les autorisations lorsque l’emploi prend fin.

* Alice utilise uniquement les informations d’identification gérées par Woodgrove pour accéder aux ressources de l’entreprise. Alice n’a pas besoin de suivre l’utilisation des informations d’identification puisqu’elles sont gérées par Woodgrove et utilisées uniquement avec les ressources de Woodgrove. L’identité n’est valide qu’au sein de la limite de confiance de Woodgrove lorsque l’accès aux ressources de Woodgrove est nécessaire, de sorte qu’Alice n’a pas besoin de posséder les informations d’identification. 

### <a name="using-vcs-inside-the-trust-boundary"></a>Utilisation de justificatifs vérifiables au sein de la limite de confiance

Les employés individuels ont des besoins fluctuants en matière d’identité, et les justificatifs vérifiables peuvent compléter les systèmes centralisés pour gérer ces changements. 

* Pendant qu’elle est employée par Woodgrove, Alice peut avoir besoin d’un accès supplémentaire aux ressources pour répondre à des exigences spécifiques. Par exemple, lorsqu’Alice suit une formation sur la confidentialité, elle peut recevoir de nouveaux justificatifs vérifiables d’employé dotés de cette revendication, et ces justificatifs vérifiables peuvent être utilisés pour accéder à des ressources restreintes.

* Les justificatifs vérifiables peuvent être utilisés à l’intérieur de la limite de confiance pour la récupération de compte. Par exemple, si l’employé a perdu son téléphone et son ordinateur, il peut récupérer l’accès en obtenant de nouveaux justificatifs vérifiables auprès du service de vérification d’identité auquel Woodgrove fait confiance, puis utiliser ces justificatifs vérifiables pour obtenir de nouvelles informations d’identification. 

 ## <a name="user-journey-accessing-external-resources"></a>Parcours utilisateur : Accès aux ressources externes

Woodgrove négocie une remise sur l’achat du produit avec Proseware. Tous les employés de Woodgrove peuvent bénéficier de cette remise. Woodgrove souhaite fournir à Alice un moyen d’accéder au site web de Proseware et de bénéficier de la remise sur les produits achetés. Si Woodgrove utilise une architecture d’identité centralisée, il existe deux approches pour fournir la remise à Alice : 

* Alice pourrait fournir des informations personnelles pour créer un compte chez Proseware, et ensuite Proseware devrait vérifier l’emploi d’Alice chez Woodgrove.

* Woodgrove pourrait étendre sa limite de confiance pour inclure Proseware comme partie de confiance et Alice pourrait utiliser la limite de confiance étendue pour recevoir la remise. 

Avec des identifiants décentralisés, Woodgrove peut fournir à Alice des justificatifs vérifiables qu’elle peut utiliser pour accéder au site web de Proseware et à d’autres ressources externes. 

![Accès aux ressources en dehors de la limite de confiance](media/introduction-to-verifiable-credentials-architecture/external-resources.png)

 

En fournissant à Alice les justificatifs vérifiables, Woodgrove atteste qu’Alice est une employée. Woodgrove est un émetteur approuvé de justificatifs vérifiables dans la solution de validation de Proseware. Cette confiance dans le processus d’émission de Woodgrove permet à Proseware d’accepter par voie électronique les justificatifs vérifiables comme preuve qu’Alice est une employée de Woodgrove et de fournir à Alice la remise. Dans le cadre de la validation des justificatifs vérifiables présentés par Alice, Proseware vérifie la validité de ces justificatifs à l’aide du registre distribué. Contenu de cette solution :

* Woodgrove permet à Alice de fournir à Proseware une preuve d’emploi sans que Woodgrove ait à étendre sa limite de confiance. 

* Proseware n’a pas besoin d’étendre sa limite de confiance pour valider qu’Alice est une employée de Woodgrove. Proseware peut utiliser les justificatifs vérifiables que Woodgrove fournit à la place. Comme la limite de confiance n’est pas étendue, la gestion de la relation de confiance est plus facile et Proseware peut facilement mettre fin à la relation en n’acceptant plus les justificatifs vérifiables.

* Alice n’a pas besoin de fournir à Proseware des informations personnelles, comme une adresse e-mail. Alice conserve les justificatifs vérifiables dans une application portefeuille sur un appareil personnel. La seule personne qui peut utiliser les justificatifs vérifiables est Alice, et Alice doit initier l’utilisation des justificatifs. Chaque utilisation des justificatifs vérifiables est enregistrée par l’application portefeuille, ce qui permet à Alice de savoir quand et où les justificatifs sont utilisés. 

 

En combinant des architectures d’identité centralisées et décentralisées pour opérer à l’intérieur et à l’extérieur des limites de confiance, la complexité et le risque peuvent être réduits et les relations limitées deviennent plus faciles à gérer. 

### <a name="changes-over-time"></a>Modifications au fil du temps

Woodgrove va ajouter des relations commerciales avec d’autres organisations et y mettre fin, et devra déterminer quand les architectures d’identité centralisées et décentralisées sont utilisées.

En combinant des architectures d’identité centralisées et décentralisées, la responsabilité et l’effort associés à l’identité et à la preuve d’identité sont répartis, le risque est réduit et l’utilisateur ne risque pas de divulguer ses informations privées aussi souvent ou à autant de vérificateurs inconnus. En particulier :

* Dans les architectures d’identité centralisées, le fournisseur d’identité émet des informations d’identification et effectue la vérification de ces informations d’identification émises. Les informations relatives à toutes les identités sont traitées par le fournisseur d’identité, qui les stocke dans un répertoire ou les en extrait. Les fournisseurs d’identité peuvent également accepter dynamiquement des jetons de sécurité provenant d’autres systèmes de fournisseur d’identité, tels que les connexions sociales ou les partenaires commerciaux. Pour qu’une partie de confiance puisse utiliser les identités dans la limite de confiance du fournisseur d’identité, elle doit être configurée pour accepter les jetons émis par le fournisseur d’identité.

## <a name="how-decentralized-identity-systems-work"></a>Fonctionnement des systèmes d’identité décentralisés

Dans les architectures d’identité décentralisées, l’émetteur, l’utilisateur et la partie de confiance ont chacun un rôle à jouer dans l’établissement et la garantie d’un échange de confiance permanent des informations d’identification de chacun. Les clés publiques des DID des intervenants peuvent être résolues dans ION, ce qui permet la validation de la signature et donc la confiance de tout artefact, y compris des justificatifs vérifiables. Les parties de confiance peuvent consommer des justificatifs vérifiables sans établir de relations de confiance avec l’émetteur. Au lieu de cela, l’émetteur fournit au sujet des informations d’identification à présenter comme preuve aux parties de confiance. Tous les messages entre les intervenants sont signés avec le DID de l’intervenant ; les DID des émetteurs et des vérificateurs doivent également posséder les domaines DNS qui ont généré les demandes. 

Par exemple : lorsque les détenteurs de justificatifs vérifiables ont besoin d’accéder à une ressource, ils doivent présenter les justificatifs vérifiables à cette partie de confiance. Pour ce faire, ils utilisent une application portefeuille pour lire la demande de la partie de confiance de présenter des justificatifs vérifiables. Dans le cadre de la lecture de la demande, l’application portefeuille utilise le DID de la partie de confiance pour trouver les clés publiques de la partie de confiance à l’aide d’ION, validant ainsi que la demande de présentation des justificatifs vérifiables n’a pas été falsifiée. Le portefeuille vérifie également que le DID est référencé dans un document de métadonnées hébergé dans le domaine DNS de la partie de confiance, afin de prouver la propriété du domaine. 

 

![Fonctionnement d’un système d’identité décentralisé](media/introduction-to-verifiable-credentials-architecture/how-decentralized-works.png)

### <a name="flow-1-verifiable-credential-issuance"></a>Flux 1 : Émission de justificatifs vérifiables

Dans ce flux, le détenteur des informations d’identification interagit avec l’émetteur pour demander des justificatifs vérifiables, comme illustré dans le diagramme ci-dessous.

![Émission de justificatifs vérifiables](media/introduction-to-verifiable-credentials-architecture/issuance.png)

1. Le détenteur démarre le flux en utilisant un navigateur ou une application native pour accéder au serveur web frontal de l’émetteur. Là, le site web de l’émetteur conduit l’utilisateur à collecter des données et exécute une logique propre à l’émetteur pour déterminer si les informations d’identification peuvent être émises, ainsi que leur contenu. 

1. Le serveur web frontal de l’émetteur appelle le service Justificatifs vérifiables Azure AD pour générer une demande d’émission de justificatifs vérifiables. 

1. Le serveur web frontal affiche un lien vers la requête sous la forme d’un code QR ou d’un lien profond spécifique à l’appareil (en fonction de l’appareil).

1. Le détenteur scanne le code QR ou le lien profond de l’étape 3 à l’aide d’une application portefeuille telle que Microsoft Authenticator.

1. Le portefeuille télécharge la demande à partir du lien. La demande inclut :

   * Le DID de l’émetteur. Il est utilisé par l’application portefeuille pour résoudre dans ION afin de trouver les clés publiques et les domaines liés.

   * L’URL avec le manifeste de justificatifs vérifiables, qui spécifie les exigences du contrat pour émettre les justificatifs vérifiables. Il peut s’agir d’id_token, des attributs auto-attestés qui doivent être fournis ou de la présentation d’autres justificatifs vérifiables. 

   * L’aspect et la convivialité des justificatifs vérifiables (URL du fichier du logo, couleurs, etc.).

1. Le portefeuille valide les demandes d’émission et traite les exigences du contrat :

   1. Valide que le message de demande d’émission est signé par les clés de l’émetteur trouvées dans le document DID résolu dans ION. Cela permet de s’assurer que le message n’a pas été falsifié.

   1. Valide que le domaine DNS référencé dans le document DID de l’émetteur est la propriété de l’émetteur. 

   1. Selon les exigences du contrat de justificatifs vérifiables, le portefeuille peut demander au détenteur de collecter des informations supplémentaires, par exemple en demandant des attributs auto-émis ou en parcourant un flux OIDC pour obtenir un id_token.

1. Soumet les artefacts requis par le contrat au service Justificatifs vérifiables Azure AD. Le service Justificatifs vérifiables Azure AD renvoie les justificatifs vérifiables, signés avec la clé DID de l’émetteur, et le portefeuille stocke les justificatifs en toute sécurité.

Pour des informations détaillées sur la façon de créer une solution d’émission et les considérations architecturales, consultez [Planifier votre solution d’émission Justificatifs vérifiables Azure Active Directory](plan-issuance-solution.md).

### <a name="flow-2-verifiable-credential-presentation"></a>Flux 2 : Présentation de justificatifs vérifiables

![Présentation de justificatifs vérifiables](media/introduction-to-verifiable-credentials-architecture/presentation.png)

Dans ce flux, un détenteur interagit avec une partie de confiance pour présenter des justificatifs vérifiables dans le cadre de ses exigences d’autorisation.

1. Le détenteur démarre le flux en utilisant un navigateur ou une application native pour accéder au serveur web frontal de la partie de confiance.

1. Le serveur web frontal appelle le service Justificatifs vérifiables Azure AD pour générer une demande de présentation de justificatifs vérifiables.

1. Le serveur web frontal affiche un lien vers la requête sous la forme d’un code QR ou d’un lien profond spécifique à l’appareil (en fonction de l’appareil).

1. Le détenteur scanne le code QR ou le lien profond de l’étape 3 à l’aide d’une application portefeuille telle que Microsoft Authenticator.

1. Le portefeuille télécharge la demande à partir du lien. La demande inclut :

   * une [demande d’informations d’identification basée sur des normes](https://identity.foundation/presentation-exchange/) d’un schéma ou d’un type d’informations d’identification ; 

   * le DID de la partie de confiance, que le portefeuille recherche dans ION.


1. Le portefeuille valide la demande de présentation et trouve les justificatifs vérifiables stockés qui satisfont à la demande. Sur la base des justificatifs vérifiables requis, le portefeuille guide le sujet pour qu’il les sélectionne et consente à les utiliser.

   * Une fois que le sujet a consenti à l’utilisation des justificatifs vérifiables, le portefeuille génère un DID unique par paire entre le sujet et la partie de confiance. 

   Ensuite, le portefeuille envoie une charge utile de réponse de présentation au service Justificatifs vérifiables Azure AD signée par le sujet. Il contient : 

   * Les justificatifs vérifiables auxquels le sujet a consenti.

   * Le DID par paire généré comme « sujet » de la charge utile.

   * Le DID de la partie de confiance en tant que « public » de la charge utile.

1. Le service Justificatifs vérifiables Azure AD valide la réponse envoyée par le portefeuille. Selon la façon dont la demande de présentation d’origine a été créée à l’étape 2, cette validation peut inclure la vérification de l’état des justificatifs vérifiables présentés auprès de l’émetteur de justificatifs vérifiables pour des cas tels que la révocation.

1. Lors de la validation, le service Justificatifs vérifiables Azure AD rappelle la partie de confiance avec le résultat. 

Pour des informations détaillées sur la façon de créer une solution de validation et les considérations architecturales, consultez [Planifier votre solution de vérification Justificatifs vérifiables Azure Active Directory](plan-verification-solution.md).

## <a name="key-takeaways"></a>Points clés

Les architectures décentralisées peuvent être utilisées pour améliorer les solutions existantes et fournir de nouvelles capacités. 

Pour répondre aux aspirations de la [Decentralized Identity Foundation (DIF)](https://identity.foundation/) et aux [objectifs de conception](https://www.w3.org/TR/did-core/) de W3C, les points suivants doivent être pris en compte lors de la création d’une solution de justificatifs vérifiables :

* Il n’existe aucun point central d’établissement de la confiance entre les intervenants du système. En d’autres termes, les limites de la confiance ne sont pas étendues par la fédération, car les intervenants font confiance à des justificatifs vérifiables spécifiques.

   * ION permet la détection de l’identifiant décentralisé (DID) de tout intervenant.

   * La solution permet aux vérificateurs de valider tous les justificatifs vérifiables de n’importe quel émetteur.

   * La solution ne permet pas à l’émetteur de contrôler l’autorisation du sujet ou du vérificateur (partie de confiance).

* Les intervenants opèrent de manière découplée, chacun étant capable d’accomplir les tâches correspondant à son rôle.

   * Les émetteurs répondent à toutes les demandes de justificatifs vérifiables et ne font pas de distinction entre les demandes traitées.

   * Les sujets sont propriétaires de leurs justificatifs vérifiables une fois qu’ils ont été émis et peuvent les présenter à n’importe quel vérificateur.

   * Les vérificateurs peuvent valider tous les justificatifs vérifiables de tous les sujets et émetteurs.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’architecture des justificatifs vérifiables :

* [Planifier votre solution d'émission](plan-issuance-solution.md)

* [Planifier votre solution de vérification](plan-verification-solution.md)

* [Prise en main de Justificatifs vérifiables Azure Active Directory](get-started-verifiable-credentials.md)
