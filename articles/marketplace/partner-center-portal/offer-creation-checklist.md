---
title: Offre de liste de vérification de la création - commerciales place de marché Azure
description: Les détails que vous pouvez fournir dans le processus de création d’offre. -Commercial place de marché Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 904058c2c98c8ded2ea9c91e8aa7ec595aa49b05
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481443"
---
# <a name="offer-creation-checklist"></a>Liste de vérification de la création d’offre

Le processus de création offre vous guident tout au long de plusieurs pages. Voici les détails que vous pouvez fournir sur chaque page, avec des liens pour en savoir plus sur chaque élément.

Vous êtes invité à fournir ou spécifier des éléments sont indiqués ci-dessous. Certaines zones sont facultatives ou avoir les valeurs par défaut fourni, que vous pouvez modifier comme vous le souhaitez. Vous n’êtes pas obligé de travailler sur ces sections dans l’ordre indiqué ici.

| **Item**    | **Objectif**  |
| :---------- | :-------------------|
| [**Nouvelle offre Modal**](#new-offer-modal) | Collecte offre des informations d’identité.  |
| [Page d’installation de l’offre](#offer-setup-page) | Vous permet d’opter pour utiliser les fonctionnalités clés et de choisir comment vendre votre offre via Microsoft.  |
| [Page de propriétés](#properties-page) | Définir les catégories et les secteurs d’activité utilisées pour regrouper votre offre sur les places de marché, les contrats prenant en charge votre offre et votre version de l’application. |
| [Page d’annonce de l’offre](#offer-listing-page) | Définir les détails de l’offre à afficher dans la place de marché, notamment des descriptions de votre offre et des ressources marketing. |
| [Page d’aperçu](#preview-page) | Définir une Audience limitée de la version préliminaire pour libérer votre offre avant de publier votre offre en direct pour le public plus large de la place de marché. |
| [Page de Configuration technique de l’offre](#technical-configuration-page)  | Disponible uniquement si vous sélectionnez cette option pour vendre de l’offre à Microsoft. Définir les détails techniques (chemin d’URL webhook, ID de locataire et ID d’application) utilisés pour se connecter à votre offre. |
| [**Nouveau Plan Modal**](#plan-identity-modal) | Collecte les informations de l’identité de plan.  |
| [Plan de Page de liste](#plan-listing-page)  | Disponible uniquement si vous sélectionnez cette option pour vendre de l’offre à Microsoft. Définir les détails permet de répertorier le Plan dans la place de marché.  |
| [Plan de tarification & Page disponibilité](#plan-pricing--availability-page)  | Disponible uniquement si vous sélectionnez cette option pour vendre de l’offre à Microsoft.  Collecte les caractéristiques d’entreprise (modèle de tarification), la disponibilité audience et sur le marché pour chaque plan (version) de votre offre.  |
| [Page liste de test](#test-drive-listing-page)  | Disponible uniquement si vous sélectionnez cette option pour proposer une version d’évaluation de votre offre. Définir les détails sur le test de lecteur dans la place de marché de liste.  |
| Page Configuration de techniques de test  | Disponible uniquement si vous sélectionnez cette option pour proposer une version d’évaluation de votre offre. Définir les détails techniques pour la démonstration (ou « tester »), ce qui permettront aux clients d’essayer votre offre avant de les valider pour l’acheter.  |
| [Passez en revue et publier la Page](#review-and-publish-page)  | Sélectionnez les modifications que vous souhaitez publier, afficher l’état de chaque page et ajoutez une note à l’équipe de certification.  |


## <a name="new-offer-modal"></a>Nouvelle offre modale 

La première vous devrez fournir des informations sont un nom et un ID de votre offre. 

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| ID de l’offre  | Requis, ne peut pas être modifié après sa création. Nombre maximal de 50 caractères et doit contenir uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. |
| Nom de l’offre  | Requis. |

## <a name="offer-setup-page"></a>Page d’installation de l’offre

La page le programme d’installation de l’offre est où vous pouvez opter pour différents canaux et de vente, ainsi déclarer qu'entraîne l’utilisation des fonctionnalités clés, telles que la version d’évaluation et le client. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------|  
| Vous souhaitez vendre via Microsoft ?  | Requis. Valeur par défaut : Oui |
| Comment voulez-vous que les clients potentiels pour interagir avec l’offre liste ? (Appel à l’action)  | Obligatoire si ne pas vendre via Microsoft. Valeur par défaut : Version d’évaluation gratuite, Options : « Télécharger maintenant », « Évaluation gratuite », « Me contacter ». |
| URL de la version d’essai  | Obligatoire si « Version d’évaluation gratuite » est sélectionnée, comme les façon dont les clients doivent interagir avec la liste de l’offre. |
| URL de l’offre  | Obligatoire si « Obtenir maintenant » est activée, comme les façon dont les clients doivent interagir avec la liste de l’offre |
| Canaux  | facultatif. Valeur par défaut : Pas activé dans le canal CSP (revendeur).  |
| Version d’évaluation | facultatif. Valeur par défaut : Aucune version d’évaluation est activée.  |
| Type de version d’évaluation | Obligatoire si activé une version d’évaluation. Valeur par défaut : Aucune sélection. Options : Azure Resource Manager, Dynamics 365 pour Business Central, Dynamics 365 for Customer Engagement, Dynamics 365 pour les opérations, l’application logique, Power BI.  |
| Gestion des prospects : se connecter à un système CRM | Requis si vente via Microsoft, ou si la liste propose comme « Me contacter ». Par défaut : aucun système CRM connecté. Options de CRM : Table Azure, objets blob Azure, Dynamics CRM online, des point de terminaison HTTPs, Marketo, Salesforce  |

## <a name="properties-page"></a>Page Propriétés

La page de propriétés est où vous définissez les catégories et les secteurs d’activité utilisées pour regrouper votre offre sur les places de marché, les contrats prenant en charge votre offre et votre version de l’application. Veillez à fournir des informations complètes et exactes relatives à votre offre sur cette page, afin qu’il a affiché de manière appropriée et proposé à l’ensemble approprié de clients. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------|  
| Catégorie et sous-catégorie | Obligatoire 3 1 et max. Valeur par défaut : Aucune sélection. |
| Secteurs d’activité et subindustries | facultatif. max 2 L1 Industries et max 2 subindustries au sein de chaque secteur d’activité L1, par défaut : Aucun sélectionné |
| Version de l’application  | facultatif. Valeur par défaut : Aucune. |
| Utiliser le contrat Standard  | facultatif. Par défaut : pas sélectionné.  | |
| Conditions d’utilisation  | Obligatoire si le contrat Standard n’est pas sélectionnée.  |

## <a name="offer-listing-page"></a>Page d’annonce de l’offre

La page de liste est dans laquelle vous permet de fournir le texte et les images que les clients voient lors de l’affichage de la liste de votre offre dans la place de marché. 

| **Nom du champ**    | **Remarques**   |
| :---------------- | :-----------| 
| Nom  | Obligatoire, maximum 50 caractères. |
| Résumé  | Obligatoire, maximum 100 caractères. | 
| Description  | 3000 caractères requis, max. |
| Instructions de prise en main  | 3000 caractères requis, max. |
| Instructions de prise en main  | 3000 caractères requis, max. |
| Mots clés de recherche  | Facultatif, recommandé, mots 3 clés maximales. |
| URL de stratégie de confidentialité  | Requis. |
| URL de supports de Marketing de programme CSP  | facultatif. |
| Liens utiles titre + URL  | facultatif. |
| Prise en charge des Documents titre + fichier  | Requis, min 1 et 3 de max. Doit être au format fichier PDF. |
| Captures d’écran.  | Obligatoire, la capture d’écran 1 de min et max 5 ; quatre ou plus recommandés. Doit être au format PNG 1280 X 720. |
| Store logos (petite, moyenne, grande, à l’échelle, héros)  | Petit (48 X 48) et grand (216 X 216) requis ; autres tailles facultatifs mais recommandé : Moyenne (90 x 90), à l’échelle (255 x 115), héros (815 x 290). Doit être au format PNG. |
| Nom de vidéos + URL de miniature  | Facultatif, recommandé, 4 vidéos maximales. La miniature doit être 1280 x 720 au format PNG. Vidéo doit être hébergée dans YouTube ou Vimeo. |
| Contacts (programme CSP, ingénierie, prise en charge)  | Ingénierie et contact de Support nécessaires (nom, e-mail et le numéro de téléphone) ; Contact du programme CSP facultatif mais recommandé. |
| URL du support technique  | Requis. |

## <a name="preview-page"></a>Page d’aperçu

La page d’aperçu est où vous spécifiez l’audience pour avoir accès à la version d’évaluation offre, pour vérifier que l’offre répond à toutes vos exigences avant sa mise en service. 

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Adresse de messagerie AAD/MSA + description | Obligatoire, 1 de min et max 10 si entré manuellement, ou jusqu'à 20 si le chargement d’un fichier CSV. |

## <a name="technical-configuration-page"></a>Page de configuration technique 

La page de configuration technique est où vous spécifiez les détails techniques utilisées par Microsoft pour vous connecter à votre offre. Cette page n’est pas visible pour vous si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| URL de la page d’accueil | Obligatoire si la vente via Microsoft. |
| Webhook de connexion | Obligatoire si la vente via Microsoft. |
| ID de locataire Azure AD | Obligatoire si la vente via Microsoft. |
| ID de l’application Azure AD | Obligatoire si la vente via Microsoft. |

## <a name="plan-identity-modal"></a>Identité de plan modale

La première que vous êtes invité à fournir des informations sont un nom et un ID de votre Plan. Cette page n’est pas visible pour vous si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| ID du plan  | Obligatoire si la vente via Microsoft. Il ne peut pas être modifié après sa création. Nombre maximal de 50 caractères et doit contenir uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. |
| Nom du plan  | Obligatoire si la vente via Microsoft. Doit être unique dans tous les plans dans l’offre. Maximum 50 caractères. |

## <a name="plan-listing-page"></a>Page de liste du plan

Le plan d’affichage de page est dans lequel vous fournissez le texte pour les clients voient lors de l’affichage du plan dans la place de marché. Cette page n’est pas visible pour vous si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   |  
| :---------------- | :-----------| 
| Description du plan   | Obligatoire si la vente via Microsoft. Max 500 caractères. | |

## <a name="plan-pricing--availability-page"></a>Page de tarification et disponibilité du plan

La page de tarification et disponibilité de plan est où vous définissez les caractéristiques de l’entreprise, public et disponibilité du marché pour chaque plan (version) de votre offre. Cette page n’est pas visible pour vous si vous avez décidé de ne pas vendre via Microsoft.

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Disponibilité du marché  | Requis, 1 de min et max 141. |
| Modèle de tarification  | Requis. Valeur par défaut : Tarification forfaitaire. Options : Tarification forfaitaire, par utilisateur. |
| Au minimum et maximum sièges  | Facultatif, disponible uniquement si le modèle sélectionné de tarification en fonction du siège. |
| Terme de facturation  | Requis. Valeur par défaut : Mensuel. Options : Mensuelle, annuelle. |
| Prix  | Requis USD par mois, si la facturation mensuelle terme sélectionné ; ou USD par an si annuel facturation terme sélectionné. |
| Plan de public  | facultatif. Valeur par défaut : Plan de public. Options : Public, privé, par ID de locataire |
| Restreint planifier Audience (ID de locataire + description)  | Obligatoire si le plan privé sélectionné. Min 1 et 10 max ID de locataire si vous avez entré manuellement. Max 20000 si l’importation de fichiers CSV. |

## <a name="test-drive-listing-page"></a>Page liste de test

Disponible uniquement si vous sélectionnez cette option pour proposer une version d’évaluation de votre offre. Définir les détails sur le test de lecteur dans la place de marché de liste.

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Description  | Requis. |
| Nom d’utilisateur manuelle + fichier  | 1 doc requis, max. Doit être au format PDF. |
| Nom de la vidéo, URL + miniature  | Facultatif et recommandé. La miniature doit être 533 x 324 au format JPGP ou PNG. Vidéo doit être hébergée dans YouTube ou Vimeo. |

## <a name="review-and-publish-page"></a>Passez en revue et publier la page

| **Nom du champ**    | **Remarques**   | 
| :---------------- | :-----------| 
| Notes relatives à la certification  | facultatif. |

## <a name="next-steps"></a>Étapes suivantes

- [Créez une offre SaaS](./create-new-saas-offer.md)
