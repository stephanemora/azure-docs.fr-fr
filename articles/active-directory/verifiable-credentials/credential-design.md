---
title: Comment personnaliser vos justificatifs vérifiables Azure Active Directory (préversion)
description: Cet article vous montre comment créer vos propres justificatifs vérifiables personnalisés
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222926"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Comment personnaliser vos justificatifs vérifiables (préversion)

Les justificatifs vérifiables sont constitués de deux composants, les fichiers de règles et d’affichage. Le fichier de règles détermine ce que l’utilisateur doit fournir avant de recevoir des justificatifs vérifiables. Le fichier d’affichage contrôle la personnalisation des justificatifs et le style des revendications. Dans ce guide, nous vous expliquerons comment modifier les deux fichiers pour répondre aux besoins de votre organisation. 

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Fichier de règles : exigences de l’utilisateur

Le fichier de règles est un simple fichier JSON qui décrit les propriétés importantes des justificatifs vérifiables. En particulier, il décrit comment les revendications sont utilisées pour remplir vos justificatifs vérifiables.

Il existe actuellement trois types d’entrée qui peuvent être configurés dans le fichier de règles. Ces types sont utilisés par le service d’émission de justificatifs vérifiables pour insérer des revendications dans les justificatifs vérifiables et pour attester de ces informations avec votre DID. Voici les trois types avec des explications.

- Jeton d’ID
- Justificatifs vérifiables via une présentation vérifiable.
- Revendications auto-attestées

**Jeton d’ID :** L’exemple d’application et le didacticiel utilisent le jeton d’ID. Lorsque cette option est configurée, vous devez fournir un URI de configuration OpenID Connect et inclure les revendications à inclure dans les justificatifs vérifiables. L’utilisateur sera invité à se connecter à l’application d’authentification pour répondre à cette exigence et ajoute les revendications associées à partir de son compte. 

**Justificatifs vérifiables :** Le résultat final d’un workflow d’émission consiste à produire des justificatifs vérifiables, mais vous pouvez également demander à l’utilisateur de présenter des justificatifs vérifiables afin d’en émettre. Le fichier de règles peut prendre des revendications spécifiques des justificatifs vérifiables présentés et inclure ces revendications dans les justificatifs vérifiables nouvellement émis de votre organisation. 

**Revendications auto-sanctionnées :** Lorsque cette option est sélectionnée, l’utilisateur peut directement saisir des informations dans l’authentificateur. À ce stade, les chaînes sont les seules entrées prises en charge pour les revendications auto-attestées. 

![Vue détaillée de la carte de justificatifs vérifiables](media/credential-design/issuance-doc.png) 

**Revendications statiques :** En outre, nous pouvons déclarer une revendication statique dans le fichier de règles, mais cette entrée ne provient pas de l’utilisateur. L’émetteur définit une revendication statique dans le fichier de règles, qui ressemble à toute autre revendication dans les justificatifs vérifiables. Ajoutez simplement un credentialSubject après vc.type et déclarez l’attribut et la revendication. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Type d’entrée : jeton d’ID

Pour recevoir le jeton d’ID comme entrée, le fichier de règles doit configurer le point de terminaison connu du système d’identité compatible OIDC. Dans ce système, vous devez inscrire une application avec les informations correctes des [Exemples de communication du service émetteur](issuer-openid.md). En outre, le client_id doit être placé dans le fichier de règles, et un paramètre d’étendue doit être renseigné avec les bonnes étendues. Par exemple, Azure Active Directory a besoin de l’étendue e-mail si vous souhaitez retourner une revendication d’e-mail dans le jeton d’ID.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Propriété | Description |
| -------- | ----------- |
| `attestations.idTokens` | Tableau de fournisseurs d’identité OpenID Connect pris en charge pour l’approvisionnement des informations utilisateur. |
| `...mapping` | Objet qui décrit comment les revendications de chaque jeton d’ID sont mappées aux attributs dans les justificatifs vérifiables résultants. |
| `...mapping.{attribute-name}` | Attribut qui doit être rempli dans les justificatifs vérifiables résultants. |
| `...mapping.{attribute-name}.claim` | Revendication dans les jetons d’ID dont la valeur doit être utilisée pour remplir l’attribut. |
| `...configuration` | Emplacement du document de configuration de votre fournisseur d’identité. Cette URL doit adhérer à la [norme OpenID Connect pour les métadonnées de fournisseur d’identité](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). Le document de configuration doit inclure les champs `issuer`, `authorization_endpoint`, `token_endpoint` et `jwks_uri`. |
| `...client_id` | ID client obtenu lors du processus d’inscription du client. |
| `...scope` | Une liste d’étendues délimitées par des espaces. Le fournisseur d’identité doit être en mesure de retourner les bonnes revendications dans le jeton d’ID. |
| `...redirect_uri` | Doit toujours utiliser la valeur `vcclient://openid/`. |
| `validityInterval` | Durée en secondes représentant la durée de vie de vos justificatifs vérifiables. Une fois cette période écoulée, les justificatifs vérifiables ne seront plus valides. L’omission de cette valeur signifie que chaque justificatif vérifiable reste valide jusqu’à ce qu’il soit explicitement révoqué. |
| `vc.type` | Tableau de chaînes indiquant le ou les schémas que vos justificatifs vérifiables satisfont. Pour des informations plus détaillées, consultez la section ci-après. |

### <a name="vctype-choose-credential-types"></a>vc.type : choisissez le ou les types de justificatifs 

Tous les justificatifs vérifiables doivent déclarer leur « type » dans leur fichier de règles. Le type de justificatifs vérifiables distingue vos justificatifs vérifiables des justificatifs émis par d’autres organisations et garantit l’interopérabilité entre les émetteurs et les vérificateurs. Pour indiquer un type de justificatifs, vous devez fournir un ou plusieurs types de justificatifs auxquels les justificatifs vérifiables répondent. Chaque type est représenté par une chaîne unique. Souvent, un URI est utilisé pour garantir l’unicité globale. L’URI n’a pas besoin d’être adressable. Il est traité comme une chaîne. 

Par exemple, les justificatifs de diplôme émis par Contoso University peuvent déclarer les types suivants :

| Type | Objectif |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Déclare que les diplômes émis par Contoso University contiennent des attributs définis par l’objet `EducationaCredential` de schema.org. |
| `https://schemas.ed.gov/universityDiploma2020` | Déclare que les diplômes émis par Contoso University contiennent des attributs définis par le Ministère de l’Éducation américain. |
| `https://schemas.contoso.edu/diploma2020` | Déclare que les diplômes émis par Contoso University contiennent des attributs définis par Contoso University. |

En déclarant les trois types, les diplômes de Contoso University peuvent être utilisés pour répondre à différentes requêtes des vérificateurs. Une banque peut demander un ensemble de `EducationCredential` à un utilisateur, et le diplôme peut être utilisé pour répondre à la requête. Toutefois, l’association des anciens élèves de Contoso University peut demander des justificatifs de type `https://schemas.contoso.edu/diploma2020`, et le diplôme est également conforme à la requête.

Pour garantir l’interopérabilité de vos justificatifs, il est recommandé de collaborer étroitement avec les organisations associées pour définir les types de justificatifs, les schémas et les URI à utiliser dans votre secteur. De nombreux organismes industriels fournissent des conseils sur la structure des documents officiels qui peuvent être réaffectés pour définir le contenu des justificatifs vérifiables. Vous devez également collaborer étroitement avec les vérificateurs de vos justificatifs pour comprendre comment ils envisagent de demander et utiliser vos justificatifs vérifiables.

## <a name="input-type-verifiable-credential"></a>Type d’entrée : justificatifs vérifiables

>[!NOTE]
>Les fichiers de règles qui demandent des justificatifs vérifiables n’utilisent pas le format d’échange de présentation pour la demande de justificatifs. Ce comportement sera mis à jour lorsque le service émetteur prendra en charge le manifeste de justificatifs standard. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Propriété | Description |
| -------- | ----------- |
| `attestations.presentations` | Tableau de justificatifs vérifiables demandés comme entrées. |
| `...mapping` | Un objet qui décrit la façon dont les revendications de chacun des justificatifs vérifiables présentés sont mappées aux attributs dans les justificatifs vérifiables résultants. |
| `...mapping.{attribute-name}` | Attribut qui doit être rempli dans les justificatifs vérifiables résultants. |
| `...mapping.{attribute-name}.claim` | Revendication dans les justificatifs vérifiables dont la valeur doit être utilisée pour remplir l’attribut. |
| `...mapping.{attribute-name}.indexed` | Une seule peut être activée par justificatifs vérifiables à enregistrer pour révocation. Pour plus d’informations, consultez l’[article sur la façon de révoquer des justificatifs](how-to-issuer-revoke.md). |
| `credentialType` | Le credentialType des justificatifs vérifiables que vous demandez à l’utilisateur de présenter. |
| `contracts` | URI du contrat dans le portail du service de justificatifs vérifiable. |
| `issuers.iss` | Le DID de l’émetteur pour les justificatifs vérifiables demandés à l’utilisateur. |
| `validityInterval` | Durée en secondes représentant la durée de vie de vos justificatifs vérifiables. Une fois cette période écoulée, les justificatifs vérifiables ne seront plus valides. L’omission de cette valeur signifie que chaque justificatif vérifiable reste valide jusqu’à ce qu’il soit explicitement révoqué. |
| `vc.type` | Tableau de chaînes indiquant le ou les schémas que vos justificatifs vérifiables satisfont. |


## <a name="input-type-self-attested-claims"></a>Type d’entrée : revendications auto-attestées

Pendant le processus d’émission, l’utilisateur peut être invité à entrer des informations d’auto-attestation. À l’heure actuelle, le seul type d’entrée est « string ». 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Propriété | Description |
| -------- | ----------- |
| `attestations.selfIssued` | Tableau de revendications auto-émises qui requièrent l’entrée de l’utilisateur. |
| `...mapping` | Objet qui décrit la façon dont les revendications auto-émises sont mappées aux attributs dans les justificatifs vérifiables résultants. |
| `...mapping.alias` | Attribut qui doit être rempli dans les justificatifs vérifiables résultants. |
| `...mapping.alias.claim` | Revendication dans les justificatifs vérifiables dont la valeur doit être utilisée pour remplir l’attribut. |
| `validityInterval` | Durée en secondes représentant la durée de vie de vos justificatifs vérifiables. Une fois cette période écoulée, les justificatifs vérifiables ne seront plus valides. L’omission de cette valeur signifie que chaque justificatif vérifiable reste valide jusqu’à ce qu’il soit explicitement révoqué. |
| `vc.type` | Tableau de chaînes indiquant le ou les schémas que vos justificatifs vérifiables satisfont. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Fichier d’affichage : justificatifs vérifiables dans Microsoft Authenticator

Les justificatifs vérifiables offrent un ensemble limité d’options qui peuvent être utilisées pour refléter votre marque. Cet article fournit des instructions sur la personnalisation de vos justificatifs et sur les meilleures pratiques pour concevoir des justificatifs à l’apparence attrayante une fois envoyés aux utilisateurs.

Les justificatifs vérifiables émis aux utilisateurs s’affichent sous forme de cartes dans Microsoft Authenticator. En tant qu’administrateur, vous pouvez choisir la couleur de la carte, l’icône et des chaînes de texte correspondant à la marque de votre organisation.

![Documentation d’émission](media/credential-design/detailed-view.png) 

Les cartes contiennent également des champs personnalisables que vous pouvez utiliser pour permettre aux utilisateurs de connaître l’objectif de la carte, les attributs qu’elle contient et bien plus encore.

## <a name="create-a-credential-display-file"></a>Créer un fichier d’affichage des justificatifs

À l’instar du fichier de règles, le fichier d’affichage est un simple fichier JSON qui décrit comment le contenu de vos justificatifs vérifiables doit être affiché dans l’application Microsoft Authenticator. 

>[!NOTE]
> À ce stade, ce modèle d’affichage est utilisé uniquement par Microsoft Authenticator.

La structure d’un fichier d’affichage est la suivante.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Propriété | Description |
| -------- | ----------- |
| `locale` | Langue des justificatifs vérifiables. Réservé pour un usage futur. | 
| `card.title` | Affiche le type de justificatifs de l’utilisateur. Longueur maximale recommandée de 25 caractères. | 
| `card.issuedBy` | Affiche le nom de l’organisation émettrice pour l’utilisateur. Longueur maximale recommandée de 40 caractères. |
| `card.backgroundColor` | Détermine la couleur d’arrière-plan de la carte, au format hexadécimal. Un dégradé subtil sera appliqué à toutes les cartes. |
| `card.textColor` | Détermine la couleur de texte de la carte, au format hexadécimal. Noir ou blanc recommandé. |
| `card.logo` | Un logo qui s’affiche sur la carte. L’URL fournie doit être adressable publiquement. Hauteur maximale recommandée de 36 px, et largeur maximale de 100 px, quelle que soit la taille du téléphone. Format PNG avec arrière-plan transparent recommandé. | 
| `card.description` | Texte supplémentaire affiché à côté de chaque carte. Peut être utilisé à toute fin. Longueur maximale recommandée de 100 caractères. |
| `consent.title` | Texte supplémentaire affiché lors de l’émission d’une carte. Utilisé pour fournir des détails sur le processus d’émission. Longueur recommandée de 100 caractères. |
| `consent.instructions` | Texte supplémentaire affiché lors de l’émission d’une carte. Utilisé pour fournir des détails sur le processus d’émission. Longueur recommandée de 100 caractères. |
| `claims` | Vous permet de fournir des étiquettes pour les attributs inclus dans chaque justificatif. |
| `claims.{attribute}` | Indique l’attribut de justificatif auquel l’étiquette s’applique. |
| `claims.{attribute}.type` | Indique le type d’attribut. Seul ’string’ est pris en charge pour le moment. |
| `claims.{attribute}.label` | Valeur à utiliser comme étiquette pour l’attribut, qui s’affichera dans Authenticator. Elle peut différer de l’étiquette utilisée dans le fichier de règles. Longueur maximale recommandée de 40 caractères. |

>[!NOTE]
  >Si une revendication est incluse dans le fichier de règles, puis omise dans le fichier d’affichage, il existe deux types d’expérience. Sur iOS, la revendication ne s’affiche pas dans la section Détails indiquée dans l’image ci-dessus, tandis que sur Android, la revendication s’affiche.  

## <a name="next-steps"></a>Étapes suivantes

Vous avez à présent une meilleure compréhension de la conception de justificatifs vérifiables et de la façon dont vous pouvez créer les vôtres pour répondre à vos besoins.

- [Exemples de communication du service émetteur](issuer-openid.md)
