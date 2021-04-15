---
title: Guide pratique pour révoquer des justificatifs vérifiables en tant qu’émetteur – Justificatifs vérifiables d’Azure Active Directory
description: Découvrez comment révoquer des justificatifs vérifiables que vous avez émis
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222841"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Révoquer des justificatifs vérifiables précédemment émis (préversion)

Dans le cadre de l’utilisation des justificatifs vérifiables, vous devez non seulement émettre des justificatifs, mais vous devez aussi parfois les révoquer. Dans cet article, nous passons en revue la partie relative à la propriété **État** de la spécification des justificatifs vérifiables. Nous examinons en détail le processus de révocation, les raisons que nous pouvons avoir de révoquer les justificatifs, ainsi que certaines implications en matière de données et de confidentialité.

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Propriété État dans la spécification des justificatifs vérifiables

Avant de pouvoir comprendre les implications de la révocation des justificatifs vérifiables, il peut être utile de se familiariser avec la **vérification de l’état** et son fonctionnement actuel.

La [spécification W3C des justificatifs vérifiables](https://www.w3.org/TR/vc-data-model/) fait référence à la propriété **État** dans la section des propriétés [4.9 :](https://www.w3.org/TR/vc-data-model/#status)

« Cette spécification définit la propriété suivante **credentialStatus** pour la découverte d’informations sur l’état actuel des justificatifs vérifiables, par exemple s’ils sont suspendus ou révoqués. »

Toutefois, la spécification W3C ne définit pas de format pour l’implémentation de la **vérification de l’état**.

« La définition du modèle de données, des formats et des protocoles pour les schémas d’état ne fait pas partie de l’étendue de cette spécification. Il existe un registre d’extension des justificatifs vérifiables [VC-EXTENSION-REGISTRY] qui contient les schémas d’état disponibles pour les responsables de l’implémentation qui souhaitent implémenter la vérification de l’état des justificatifs vérifiables. »

>[!NOTE]
>Actuellement, l’implémentation de la vérification de l’état de Microsoft est propriétaire, mais nous travaillons activement avec la communauté DID pour l’aligner sur une norme.

## <a name="how-does-the-status-property-work"></a>Comment la propriété **État** fonctionne-t-elle ?

Les justificatifs vérifiables émis par Microsoft contiennent un attribut appelé credentialStatus. Cet attribut est rempli avec une API d’état gérée par Microsoft en votre nom. Voici un exemple de ce à quoi il ressemble.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

Le kit SDK des justificatifs vérifiables open source gère l’appel de l’API d’état et la mise à disposition des données nécessaires.

Une fois que l’API a été appelée et qu’elle a obtenu les informations appropriées, l’API retourne la valeur True ou False. True signifie que les justificatifs vérifiables sont toujours actifs avec l’émetteur et False signifie que les justificatifs vérifiables ont été activement révoqués par l’émetteur.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Quelles peuvent être les raisons de révoquer des justificatifs vérifiables ?

Chaque client a ses propres raisons de vouloir révoquer des justificatifs vérifiables, mais voici quelques-uns des motifs courants. 

- ID d’étudiant : l’étudiant n’est plus actif dans l’université.
- ID d’employé : l’employé n’est plus actif.
- Permis de conduire d’un État : le conducteur n’habite plus cet État.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Comment configurer des justificatifs vérifiables avec la possibilité de les révoquer

Tous les justificatifs vérifiables ne sont pas stockés avec Microsoft par défaut. Par conséquent, nous n’avons aucune donnée à référencer pour révoquer un ID de justificatif vérifiable spécifique. L’émetteur doit spécifier un champ spécifique de l’attribut des justificatifs vérifiables pour que Microsoft effectue l’indexation, puis ajoute un salt et exécute le hachage.

>[!NOTE]
>Le hachage est une opération de chiffrement unidirectionnelle qui convertit une entrée, appelée ```preimage```, et produit une sortie appelée « hachage » dont la longueur est fixe. Il n’est pas possible d’inverser une opération de hachage en termes de calcul pour le moment.

Vous pouvez indiquer à Microsoft l’attribut des justificatifs vérifiables que vous souhaitez indexer. L’indexation a pour effet que les valeurs indexées peuvent être utilisées pour la recherche des justificatifs vérifiables (VC) que vous souhaitez révoquer.

**Exemple :** Alice est employée dans la société Woodgrove. Alice quitte Woodgrove pour aller travailler chez Contoso. Jane, administratrice informatique de Woodgrove, recherche l’adresse e-mail d’Alice dans la requête de recherche de révocation des justificatifs vérifiables. Dans cet exemple, Jane a indexé le champ de l’adresse e-mail des justificatifs des employés de Woodgrove. 

Consultez la section ci-dessous pour découvrir un exemple de modification du fichier de règles pour inclure l’index.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Un seul attribut peut être indexé à partir d’un fichier de règles.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Comment révoquer des justificatifs vérifiables

Une fois qu’une revendication d’index a été définie et que des justificatifs vérifiables ont été émis pour vos utilisateurs, il est temps de voir comment révoquer des justificatifs vérifiables dans le panneau des justificatifs vérifiables.

1. Accédez au panneau des justificatifs vérifiables dans Azure Active Directory.
1. Choisissez les justificatifs vérifiables pour lesquels vous avez précédemment configuré la revendication d’index et que vous avez émis pour un utilisateur. =
1. Dans le menu de gauche, choisissez **Révoquer un justificatif**
   ![Révoquer un justificatif](media/how-to-issuer-revoke/settings-revoke.png) 
1. Recherchez l’attribut d’index de l’utilisateur que vous souhaitez révoquer. 

   ![Rechercher le justificatif à révoquer](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Étant donné que nous ne stockons qu’un hachage de la revendication indexée à partir des justificatifs vérifiables, seule une correspondance exacte remplira les résultats de la recherche. Nous utilisons l’entrée telle qu’elle est recherchée par l’administrateur informatique et le même algorithme de hachage pour voir si nous avons une correspondance de hachage dans notre base de données.
    
1. Une fois que vous avez trouvé une correspondance, sélectionnez l’option **Révoquer** à droite du justificatif que vous souhaitez révoquer.

   ![Avertissement vous informant que l’utilisateur disposera toujours du justificatif après la révocation](media/how-to-issuer-revoke/warning.png) 

1. Une fois la révocation réussie, vous voyez que l’état est mis à jour et une bannière verte s’affiche en haut de la page. 
   ![Vérifier ce domaine dans les paramètres](media/how-to-issuer-revoke/revoke-successful.png) 

Désormais, chaque fois qu’une partie de confiance appelle pour vérifier l’état de ces justificatifs vérifiables spécifiques, l’API d’état de Microsoft, agissant pour le compte du locataire, retourne une réponse « false ».

## <a name="next-steps"></a>Étapes suivantes

Testez la fonctionnalité par vous-même avec un justificatif de test afin de vous familiariser avec le flux. Vous pouvez obtenir des informations sur la façon de configurer votre locataire pour émettre des justificatifs vérifiables en [consultant nos tutoriels](get-started-verifiable-credentials.md).