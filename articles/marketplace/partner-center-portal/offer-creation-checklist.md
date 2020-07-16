---
title: Liste de contrôle de création d’offre SaaS dans la place de marché commerciale Microsoft
description: Détails que vous pouvez fournir dans le cadre du processus de création d’offre SaaS dans l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c56295f1e56e4ba3b6af9caf8ba38ce1f0552eeb
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101706"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>Liste de vérification de la création d’offre SaaS dans l’Espace partenaires

Le processus de création d’offre SaaS vous amène à visiter plusieurs pages.  Voici les détails que vous pouvez fournir sur chacune d’elles, avec des liens pour en apprendre davantage plus sur chaque élément.

Les éléments que vous devez fournir ou spécifier sont indiqués ci-dessous.  Certaines zones sont facultatives ou comportent des valeurs par défaut que vous pouvez modifier à votre guise.  Vous n’êtes pas obligé de travailler sur ces sections dans l’ordre indiqué ici.

>[!Note]
>Si vous créez une offre SaaS préconfigurable, veillez à implémenter l’intégration avec [l’API de traitement SaaS](./pc-saas-fulfillment-apis.md).  L’intégration avec les API est le seul moyen de garantir le bon fonctionnement de la fonction de traitement de la place de marché.

| **Item**    | **Objectif**  |
| :---------- | :-------------------|
| [**Modal de nouvelle offre**](#new-offer-modal) | Collecte des informations d’identité liées à l’offre.  |
| [Page Configuration de l’offre](#offer-setup-page) | Optez pour l’utilisation des fonctionnalités clés et choisissez comment vendre votre offre via Microsoft.  |
| [Page Propriétés](#properties-page) | Définissez les catégories et secteurs utilisés pour grouper votre offre en fonction des places de marché, des contrats légaux associés, et de la version de votre application. |
| [Page Référencement de l’offre](#offer-listing-page) | Définissez les détails de l’offre à afficher sur la Place de marché, dont des descriptions de votre offre et de ses composants marketing.|
| [Page Préversion](#preview-page) | Définissez un Public de préversion limité avant de publier votre offre pour public plus important.|
| [Page Configuration technique de l’offre](#technical-configuration-page)  |  Disponible uniquement si vous choisissez de vendre l’offre via Microsoft.  Définissez les détails techniques (URL de la page d’accueil, URL de connexion webhook, ID de locataire Azure AD et ID d’application Azure AD) utilisés par la Place de marché pour vous connecter à votre offre.  Ces paramètres sont nécessaires pour s’intégrer correctement avec les API de traitement SaaS et de facturation de la Place de marché.|
| [**Modal de nouveau plan**](#plan-identity-modal) | Collecte des informations sur l’identité du plan.  |
| [Page Référencement du plan](#plan-listing-page)  | Disponible uniquement si vous choisissez de vendre l’offre via Microsoft. Définissez les détails utilisés pour référencer le plan sur la place de marché.  |
| [Page Tarification et disponibilité du plan](#plan-pricing--availability-page)  | Disponible uniquement si vous choisissez de vendre l’offre via Microsoft.  Recueille les caractéristiques de l’entreprise (modèle de tarification), l’audience et la disponibilité du marché pour chaque forfait (version) de votre offre.  |
| [Page Référencement de version d’évaluation](#test-drive-listing-page)  | Disponible uniquement si vous décidez de proposer une version d’évaluation de votre offre. Définissez les détails utilisés pour référencer la version d’évaluation sur la Place de marché.  |
| Page Configuration technique de la version d’évaluation  | Disponible uniquement si vous décidez de proposer une version d’évaluation de votre offre. Définissez les détails techniques de la démonstration (ou « version d’évaluation ») qui permettra à des clients d’essayer votre offre avant de s’engager à l’acheter.  |
| [Page Passer en revue et publier](#review-and-publish-page)  | Sélectionnez les modifications que vous souhaitez publier, affichez l’état de chaque page et ajoutez des notes à l’adresse de l’équipe de certification.  |


## <a name="new-offer-modal"></a>Modal de nouvelle offre 

Les premiers éléments d’informations que vous serez invité à fournir sont un ID et un alias pour votre offre. 

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| ID de l’offre  | Obligatoire et non modifiable après la création. Maximum 50 caractères alphanumériques minuscules, tirets ou traits de soulignement. |
| Alias de l’offre  | Obligatoire. |

## <a name="offer-setup-page"></a>Page de configuration de l’offre

La page de configuration de l’offre vous permet de choisir différents canaux et propositions de vente, ainsi que de déclarer l’utilisation de fonctionnalités clés, telles que des versions d’évaluation et des prospects. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------|  
| Voulez-vous vendre via Microsoft ?  | Obligatoire. Valeur par défaut : Oui |
| Comment voulez-vous que des clients potentiels interagissent avec le référencement de l’offre ? (Invite à l’action)  | Obligatoire en cas de vente sans passer par Microsoft. Valeur par défaut : Essai gratuit, Options : « Obtenir maintenant », « Essai gratuit », « Me contacter ». |
| URL de la version d’essai  | Obligatoire si « Essai gratuit » est sélectionné comme mode d’interaction des clients avec le référencement de l’offre. |
| URL de l’offre  | Obligatoire si « Obtenir maintenant » est sélectionné comme mode d’interaction des clients avec le référencement de l’offre. |
| Canaux  | facultatif. Valeur par défaut : Pas activé dans le canal CSP (revendeur).  |
| Version d’évaluation | facultatif. Valeur par défaut : Aucune version d’évaluation activée.  |
| Type de version d’évaluation | Obligatoire version d’évaluation activée. Valeur par défaut : Aucune sélectionnée. Options : Azure Resource Manager, Dynamics 365 for Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 for Operations, Logic Apps, Power BI.  |
| Prospects - Se connecter à un système CRM | Obligatoire en cas de vente via Microsoft ou de référencement des offres « Me contacter ». Par défaut : aucun système CRM connecté. Options CRM : Azure table, Azure blob, Dynamics CRM Online, Point de terminaison HTTPS, Marketo, Salesforce  |

## <a name="properties-page"></a>Page Propriétés

La page de propriétés est l’emplacement où vous définissez les catégories et secteurs utilisés pour grouper votre offre selon les places de marché, les contrats légaux associés, et la version de votre application. Veillez à fournir sur cette page des informations complètes et précises sur votre offre, afin qu’elle soit correctement présentée et proposée aux clients concernés. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------|  
| Catégorie et sous-catégorie | Obligatoire 1 et maximum 3. Valeur par défaut : Aucune sélectionnée. |
| Secteurs et sous-secteurs | facultatif. 2 secteurs L1 et au maximum 2 sous-secteurs dans chaque secteur L1. Valeur par défaut : Aucune sélectionnée |
| Version de l’application  | facultatif. Valeur par défaut : Aucun. |
| Utiliser le contrat Standard  | facultatif. Par défaut : non sélectionné.  | |
| Conditions d’utilisation  | Obligatoire si le contrat Standard n’est pas sélectionné.  |

## <a name="offer-listing-page"></a>Page de référencement de l’offre

La page de référencement vous permet de fournir le texte et les images que les clients voient quand ils affichent le référencement de votre offre sur la place de marché. 

| **Nom du champ**    | **Remarques**   |
| :---------------- | :-----------| 
| Nom  | Obligatoire, maximum 50 caractères. |
| Résumé  | Obligatoire, maximum 100 caractères. | 
| Description  | Obligatoire, maximum 3 000 caractères. |
| Instructions de prise en main  | Obligatoire, maximum 3 000 caractères. |
| Instructions de prise en main  | Obligatoire, maximum 3 000 caractères. |
| Mots clés de recherche  | Facultatif, recommandé, maximum 3 mots clés. |
| URL de la politique de confidentialité  | Obligatoire. |
| URL des Ressources Marketing du programme CSP  | facultatif. |
| Titre + URL de liens utiles  | facultatif. |
| Titre + Fichier de documents associés  | Obligatoire, min. 1 et max. 3. Doit être au format de fichier PDF. |
| Captures d’écran.  | Obligatoire, min. 1 et max. 5 captures d’écran ; min. quatre recommandées. Doit être au format PNG 1280 X 720. |
| Logos du Store (Petit, Moyen, Grand, Large)  | Petit (48 X 48) et Grand (216 X 216) obligatoires. Les autres tailles sont facultatives mais recommandées : Moyen (90 x 90), Large (255 x 115). Le logo doit être au format .PNG. |
| Nom + URL + miniature des vidéos  | Facultatif, recommandé, max. 4 vidéos. La miniature doit être au format PNG 1280 x 720. La vidéo doit être hébergée sur YouTube ou Vimeo. |
| Contacts (Programme Fournisseur de solutions Microsoft Cloud (CSP), Ingénierie, Support)  | Contacts Ingénierie et Support obligatoires (nom, e-mail et le numéro de téléphone) ; Contact Programme CSP facultatif mais recommandé. |
| URL du support technique  | Obligatoire. |

## <a name="preview-page"></a>Page de préversion

La page de préversion vous permet de spécifier le public ayant accès à la préversion de votre offre afin de vérifier que celle-ci répond à toutes vos exigences avant sa mise en ligne. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| E-mail + description AAD/MSA | Obligatoire, min. 1 et max. 10 en cas d’entrée manuelle, ou jusqu’à 20 e cas de chargement de fichier CSV. |

## <a name="technical-configuration-page"></a>Page de configuration technique 

La page de configuration technique est celle où vous spécifiez les détails techniques utilisés par Microsoft pour se connecter à votre offre. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| URL de la page d’accueil | Obligatoire en cas de vente via Microsoft. |
| Webhook de connexion | Obligatoire en cas de vente via Microsoft. |
| ID de locataire Azure AD | Obligatoire en cas de vente via Microsoft. |
| ID de l’application Azure AD | Obligatoire en cas de vente via Microsoft. |

## <a name="plan-identity-modal"></a>Modal d’identité de plan

Les premiers éléments d’informations que vous êtes invité à fournir sont un nom et un identifiant pour votre plan. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| ID du plan  | Obligatoire en cas de vente via Microsoft. Non modifiable une fois créé. Maximum 50 caractères alphanumériques minuscules, tirets ou traits de soulignement. |
| Nom du plan  | Obligatoire en cas de vente via Microsoft. Doit être unique pour tous les plans dans l’offre. 50 caractères maximum. |

## <a name="plan-listing-page"></a>Page de référencement du plan

La page de référencement du plan est celle dans laquelle vous fournissez le texte que les clients peuvent voir quand ils consultent le plan sur la Place de marché. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| Description du plan   | Obligatoire en cas de vente via Microsoft. Maximum 500 caractères. | |

## <a name="plan-pricing--availability-page"></a>Page de tarification et disponibilité du plan

La page de tarification et disponibilité de plan est celle dans laquelle vous définissez les caractéristiques de l’entreprise, l’audience et la disponibilité du marché pour chaque forfait (version) de votre offre. Vous ne pouvez pas voir cette page si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Disponibilité sur le marché  | Obligatoire, min. 1 et max. 141. |
| Modèle de tarification  | Obligatoire. Valeur par défaut : Taux fixe. Options : Tarification fixe, par utilisateur. |
| Minimum et maximum de sièges  | Facultatif, disponible uniquement si le modèle de tarification basée sur ls sièges et sélectionné. |
| Fréquence de facturation  | Obligatoire. Valeur par défaut : Mensuelle: Options : Mensuelle, Annuelle. |
| Price  | Obligatoire, USD par mois, si la fréquence de facturation sélectionnée est mensuelle, ou USD par an si la fréquence de facturation sélectionnée est annuelle. |
| Public du plan  | facultatif. Valeur par défaut : Plan public. Options : Public, privé, par ID de locataire |
| Public du plan restreint (ID de locataire + description)  | Obligatoire si le plan sélectionné est privé. Min. 1 et max. 10 ID de locataire en cas d’entrée manuelle. Max. 20 000 en cas d’importation de fichier CSV. |

## <a name="test-drive-listing-page"></a>Page Référencement de version d’évaluation

Disponible uniquement si vous décidez de proposer une version d’évaluation de votre offre. Définissez les détails utilisés pour référencer la version d’évaluation sur la Place de marché.

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Description  | Obligatoire. |
| Nom + fichier de manuel utilisateur  | Obligatoire, max. 1 doc. Doit être au format PDF. |
| Nom de la vidéo, URL + miniature  | Facultatif, recommandé. La miniature doit être 533 x 324 au format JPGP ou PNG. La vidéo doit être hébergée sur YouTube ou Vimeo. |

## <a name="review-and-publish-page"></a>Page Passer en revue et publier

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Notes pour la certification  | facultatif. |

## <a name="next-steps"></a>Étapes suivantes

- [Créer une nouvelle offre SaaS](./create-new-saas-offer.md)
