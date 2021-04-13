---
title: Stratégie de messagerie
titleSuffix: An Azure Communication Services concept document
description: Découvrez les stratégies de messagerie SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645662"
---
# <a name="azure-communication-services-messaging-policy"></a>Stratégie de messagerie d’Azure Communication Services

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Azure Communication Services transforme la façon dont nos clients interagissent avec leurs clients en créant des expériences de communication riches et personnalisées qui tirent parti des mêmes services professionnels sur lesquels reposent Microsoft Teams et Skype. Intégrez la fonctionnalité de messagerie SMS à vos solutions de communication pour pouvoir joindre vos clients où et quand vous voulez lorsqu’ils ont besoin d’assistance. Mais avant de commencer, vous devez garder à l’esprit quelques conditions en matière de messagerie.

Ces conditions peuvent vous sembler difficiles à retenir, mais essayez de vous souvenir de ces quatre termes :

- Consentement
- Annulation
- Contenu du message
- Usurpation

Nous avons développé cette stratégie de messagerie pour vous aider à répondre aux conditions réglementaires et à vous aligner sur les bonnes pratiques recommandées. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Consentement 

### <a name="what-is-consent"></a>Qu’est-ce que le consentement ?

Le consentement est un accord entre vous et le destinataire du message qui vous permet d’envoyer à ce destinataire des messages automatisés. Vous devez obtenir un consentement avant d’envoyer le premier message, et vous devez indiquer clairement au destinataire qu’il accepte de recevoir des messages de votre part. Le destinataire à qui vous avez l’intention d’envoyer des messages vous accorde ce que nous appelons un « consentement exprès préalable ».

Les messages que vous envoyez doivent être du même type que ceux que le destinataire a accepté de recevoir et ne doivent être envoyés qu’au numéro que le destinataire vous a fourni. Si vous avez l’intention d’envoyer des messages d’information comme des rappels de rendez-vous ou des alertes, le consentement peut être écrit ou oral. Si vous avez l’intention d’envoyer des messages promotionnels comme des messages commerciaux ou marketing faisant la promotion d’un produit ou d’un service, le consentement doit être écrit.

### <a name="how-do-you-obtain-consent"></a>Comment obtenir le consentement ?

Le consentement peut être obtenu de plusieurs façons, par exemple :

- Quand un utilisateur entre son numéro de téléphone dans un site web 
- Quand un utilisateur initie un échange de SMS
- Quand un utilisateur envoie un mot clé d’inscription à votre numéro de téléphone 
 
Quel que soit le mode d’obtention du consentement, vous et vos clients devez vous assurer que le consentement est sans équivoque. Le cadre du consentement doit être clair pour le destinataire.


### <a name="consent-requirements"></a>Conditions requises pour le consentement :

- Fournissez un « appel à l’action » avant d’obtenir le consentement. Vous et vos clients devez fournir aux destinataires potentiels des messages un « appel à l’action » qui les invite à accepter votre programme de messagerie. L’appel à l’action doit inclure, au minimum : (1) l’identité de l’expéditeur du message, (2) des instructions d’acceptation claires, (3) des instructions d’annulation et (4) les éventuels frais de messagerie associés.
- Le consentement n’est ni transférable ni cessible. Aucun consentement qu’une personne vous fournit ne peut être transféré ou vendu à un tiers non affilié. Si vous collectez le consentement d’une personne pour un tiers, vous devez alors identifier clairement le tiers à la personne. Vous devez également indiquer que le consentement que vous avez obtenu s’applique uniquement aux communications du tiers.
- Le consentement a un but limité. Une personne qui fournit son numéro dans un but particulier consent à recevoir des communications uniquement dans ce but précis et de cet expéditeur de message spécifique. Avant d’obtenir un consentement, vous devez indiquer clairement au destinataire du message si vous enverrez des messages récurrents ou des messages d’un affilié.

### <a name="consent-best-practices"></a>Bonnes pratiques de consentement :

En plus des conditions de messagerie abordées ci-dessus, vous pouvez implémenter plusieurs bonnes pratiques courantes, notamment : 

- Informations détaillées sur « l’appel à l’action ». Pour vous assurer d’obtenir le consentement approprié, indiquez :
  - le nom ou la description de votre programme ou produit de messagerie
  - le ou les numéros à partir desquels les destinataires recevront les messages 
  - toutes les conditions générales applicables avant qu’une personne n’accepte de recevoir des messages de votre part
- Enregistrements précis du consentement. Vous devez conserver les enregistrements de tout consentement qu’une personne vous fournit pendant au moins quatre ans. Exemples d’enregistrements de consentement :
  - horodatage
  - support par lequel le consentement a été obtenu
  - campagne spécifique pour laquelle le consentement a été obtenu
  - captures d’écran
  - ID de session ou adresse IP de la personne donnant son consentement
- Stratégies de confidentialité et de sécurité. Les développeurs sont encouragés à fournir des stratégies de confidentialité simples que les destinataires du message peuvent consulter avant de donner leur consentement. Nous vous recommandons également de mettre en place des contrôles de sécurité proactifs pour protéger les informations privées des personnes.


## <a name="double-opt-in-consent"></a>Double consentement :

Azure Communication Services vous recommande d’utiliser un double consentement pour toutes les campagnes de messagerie. Le double consentement est un processus en deux étapes dans lequel une personne donne d’abord son consentement pour recevoir certains types de messages de votre part. Vous lui envoyez ensuite un message de suivi pour qu’il confirme son consentement. Vous devez attendre que le destinataire confirme son consentement avant de lui envoyer d’autres messages.

Le message de confirmation initial que vous envoyez doit inclure votre identité, l’option permettant de ne plus recevoir de messages (par exemple, une commande « STOP »), un numéro gratuit ou une commande « AIDE » pour obtenir des informations supplémentaires, une notification indiquant que la personne est inscrite à un programme de messages récurrents, une brève description du programme, la fréquence à laquelle vous avez l’intention d’envoyer des messages récurrents et les éventuels frais associés. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>Azure Communication Services exige-t-il le double consentement dans certains cas ?
Oui. Bien que le double consentement soit toujours recommandé, Azure Communication Services vous demande de l’utiliser pour certains types de campagnes de messagerie en raison de leur utilisation fréquente dans des opérations d’hameçonnage ou de leur tendance à générer des plaintes de la part des consommateurs. Ces campagnes sont les suivantes :
- Messages de garantie automobile
- Plans d’assurance maladie à court terme
- Messages de refinancement ou de réduction du taux d’intérêt (s’ils ne sont pas émis par une institution financière)
- Messages de génération de prospects
- Tirages au sort, concours et cadeaux
- Offres de travail à domicile

Les campagnes pour lesquelles un double consentement est obligatoire peuvent changer à l’entière discrétion d’Azure Communication Services.

### <a name="exceptions-to-traditional-consent-rules"></a>Exceptions aux règles de consentement traditionnelles :
Bien que le consentement exprès préalable soit normalement requis avant d’envoyer un message, il existe deux situations dans lesquelles le consentement à recevoir des messages est implicite.

- Le destinataire initie une communication. Si une personne initie une communication en vous envoyant un message, vous pouvez alors fournir des informations pertinentes en réponse à une demande spécifique contenue dans le message. Toutefois, le consentement implicite fourni par la personne est limité à la conversation initiée par cette personne, à moins que vous n’obteniez le consentement pour d’autres communications.
- Exemptions pour des services spécifiques. Dans certains services spécifiques, le consentement pour envoyer des messages peut être implicite. Voici les plus courants : 
- Distribution de colis
- Établissements financiers envoyant des messages sur des sujets urgents (comme des transactions potentiellement frauduleuses ou des violations de données)
- Professionnels de santé envoyant des informations urgentes et un objectif de traitement (rappels de rendez-vous ou d’examens, résultats de laboratoire, notifications de prescription, etc.) 
 
Aucun de ces messages ne peut inclure des sollicitations ou des publicités.


## <a name="opt-out"></a>Annulation

Les destinataires des messages peuvent révoquer leur consentement et choisir de ne plus recevoir de messages par tout moyen raisonnable. Vous ne pouvez pas offrir aux destinataires des messages un seul moyen leur permettant de révoquer leur consentement. 

### <a name="opt-out-requirements"></a>Conditions d’annulation :

Veillez à ce que les destinataires puissent à tout moment choisir de ne plus recevoir de messages. Vous devez également leur proposer plusieurs méthodes d’annulation. Quand un destinataire de messages opte pour l’annulation, vous ne devez plus lui envoyer de messages, sauf s’il renouvelle son consentement.

L’un des mécanismes d’annulation les plus courants consiste à inclure un mot clé « STOP » dans le message initial de chaque nouvelle conversation. Soyez prêt à supprimer les clients qui répondent avec le mot « stop » en minuscules ou d’autres mots clés courants, comme « désabonner » ou « annuler ». Quand une personne révoque son consentement, vous devez la supprimer de toutes les campagnes de messagerie récurrentes, sauf si elle choisit expressément de continuer à recevoir les messages d’un programme particulier.

### <a name="opt-out-best-practices"></a>Bonnes pratiques d’annulation :

Aux mots clés viennent s’ajouter d’autres mécanismes d’annulation courants. Vous pouvez notamment fournir aux clients une adresse e-mail spécifique à l’annulation, le numéro de téléphone du support technique ou un lien pour se désabonner sur votre page web. 


### <a name="how-we-handle-opt-out-requests"></a>Comment gérer les demandes d’annulation :

Si une personne décide de ne plus recevoir de messages d’un numéro gratuit Azure Communication Services, tout le trafic provenant de ce numéro est automatiquement arrêté. Toutefois, vous devez veiller à ne pas envoyer d’autres messages en rapport avec cette campagne de messagerie à partir de nouveaux numéros ou de numéros différents. Si vous avez obtenu séparément le consentement exprès pour une autre campagne de messagerie, vous pouvez continuer à envoyer les messages propres à cette campagne à partir d’un numéro différent. Consultez notre section FAQ pour en savoir plus sur la [gestion des annulations](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Contenu du message

### <a name="adult-content"></a>Contenu réservé aux adultes :

Les messages comprenant des éléments en rapport avec le sexe, la haine, l’alcool, les armes, le tabac, les jeux d’argent ou les loteries et les concours peuvent entraîner des conditions supplémentaires. Ce contenu est expressément interdit dans certaines juridictions. Si vous envoyez un message avec un tel contenu, il est de votre devoir de respecter toutes les lois applicables des juridictions dans lesquelles les communications sont reçues. À la demande des forces de l’ordre ou d’Azure Communication Services, vous devez être prêt à fournir une preuve de consentement aux autorités locales qui régissent le contenu pour adultes.

Même si ce type de contenu n’est pas illégal, vous devez inclure un mécanisme de vérification de l’âge au moment de l’acceptation pour autoriser le destinataire à recevoir du contenu réservé aux adultes. Aux États-Unis, des conditions légales supplémentaires s’appliquent aux communications marketing destinées aux enfants de moins de 13 ans. 

### <a name="prohibited-content"></a>Contenu interdit :

Azure Communication Services interdit certains contenus de message indépendamment du consentement obtenu. Exemples de contenu interdit :
- Contenu faisant la promotion d’activités illégales (par exemple, évasion fiscale ou cruauté envers les animaux aux États-Unis)
- Discours haineux, discours diffamatoire, harcèlement ou tout autre discours jugé offensant
- Contenu pornographique
- Contenu obscène ou vulgaire
- Intimidation et menaces
- Contenu visant à escroquer, tromper, causer un préjudice ou obtenir illicitement quelque chose de valeur 
- Contenu incitant à faire du tort, à la discrimination ou à la violence
- Contenu propageant des logiciels malveillants
- Contenu visant à contourner le contrôle obligatoire de l’âge

Nous nous réservons le droit de modifier la liste des contenus de message interdits à tout moment.

## <a name="spoofing"></a>Usurpation

L’usurpation est le fait d’afficher un numéro d’origine trompeur ou inexact sur l’appareil du destinataire d’un message. Nous vous déconseillons fortement, vous et tout fournisseur de services que vous utilisez, d’envoyer des messages usurpés. L’usurpation masque l’identité de l’expéditeur du message et empêche les destinataires de messages de refuser facilement des communications indésirables. Nous vous demandons également de respecter toutes les lois applicables en matière d’usurpation.

## <a name="final-thoughts"></a>Réflexions finales

### <a name="legal-responsibility"></a>Responsabilité légale :

Cette politique de messagerie ne constitue pas un avis juridique et nous nous réservons le droit de la modifier à tout moment. Azure Communication Services ne garantit pas que le contenu, le calendrier ou les destinataires des messages de nos clients respectent toutes les conditions légales applicables. 

Nos clients sont tenus de remplir toutes les conditions requises en matière de messagerie. Si vous êtes un fournisseur de plateforme ou de logiciels qui utilise Azure Communication Services à des fins de messagerie, vous devez exiger que vos clients respectent également toutes les conditions requises décrites dans cette stratégie de messagerie. Pour obtenir de l’aide, CTIA fournit des [principes de messagerie utiles et des bonnes pratiques](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf).

### <a name="penalties"></a>Pénalités :

Nous encourageons nos clients à développer et à implémenter des stratégies et des procédures conçues pour garantir la conformité avec l’ensemble des conditions en matière de messagerie. Les violations de ces conditions peuvent entraîner des amendes considérables qui peuvent rapidement être majorées. Il est dans votre meilleur intérêt d’apprendre et de respecter toutes les conditions de messagerie applicables et de développer des mesures d’atténuation efficaces pour contenir et éliminer les violations avant qu’elles ne se propagent. Si vous enfreignez notre politique de messagerie ou d’autres conditions légales, nous travaillerons avec vous pour assurer votre conformité. Toutefois, nous nous réservons le droit de supprimer tout client de la plateforme Azure Communication Services présentant une série de cas de non-conformité avec notre stratégie de messagerie ou nos conditions légales.
