---
title: Rechercher des catégories dans Azure Maps | Microsoft Docs
description: En savoir plus sur la recherche des catégories dans Azure Maps
services: azure-maps
keywords: ''
author: sataneja
ms.author: sataneja
ms.date: 5/7/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 96a179b5744536595ae9f7cf4e1b23eb9db3c589
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="azure-maps---search-categories"></a>Azure Maps : Rechercher des catégories

Azure Maps fournit une puissante [API de recherche des catégories](https://docs.microsoft.com/en-us/rest/api/maps/search/getsearchpoicategory) qui renvoie les points d’intérêt (POI) et les filtre par catégorie.

### <a name="list-of-available-categoriessup-ida11fn1sup"></a>Liste des catégories disponibles<sup id="a1">[1](#fn1)</sup>

|Catégories|
|---------|
|passerelle d’accès|
|zone administrative|
|division administrative|
|tournoi de sports d’aventure|
|véhicule d’aventure|
|publicité|
|afghan|
|africain|
|établissement d’enseignement agricole|
|fournitures agricoles|
|technologie agricole|
|agriculture|
|terrain d’aviation|
|ligne aérienne|
|accès aux lignes aériennes|
|aéroport|
|algérien|
|service d’ambulance|
|américain|
|amphithéâtre|
|salle de jeux|
|parc de loisirs|
|lieu de divertissements|
|anabranche|
|services animaliers|
|refuge pour animaux|
|antiquité|
|appartement|
|immeuble d’habitation|
|installation d’aquaculture|
|parc aquatique|
|arabe|
|jardin botanique et arboretum|
|jardins botaniques et arboretums|
|arche|
|argentin|
|arménien|
|art|
|école d’art|
|ashram|
|asiatique (autre)|
|asile|
|athlétique|
|terrain d’athlétisme|
|vtt|
|australien|
|autrichien|
|distributeur automatique|
|automobile|
|construction automobile|
|concessionnaire automobile|
|maroquinerie|
|balades en montgolfières|
|banque|
|banque(s)|
|banques|
|salles de banquet|
|bar|
|barbecue|
|base-ball|
|basket-ball|
|basque|
|champ de bataille|
|baie|
|plage|
|club de plage|
|plages|
|salon de beauté|
|produits cosmétiques|
|chambres d’hôtes bed & breakfast|
|brasserie|
|belge|
|salle de paris|
|bistro|
|banque du sang|
|bateau|
|rampe de mise à l’eau des bateaux|
|accessoires d’équipements nautiques|
|carrosseries|
|bolivien|
|librairies|
|bosniaque|
|bowling|
|brésilien|
|brasserie|
|pont|
|manœuvre pont-tunnel|
|protection civile|
|britannique|
|buffet|
|bâtiment|
|bâtiment (plan)|
|bulgare|
|bungalow|
|birman|
|bus|
|location d’autobus|
|lignes de bus|
|station de bus|
|arrêt de bus|
|cabaret|
|chalets et auberges|
|téléphone par câble|
|café|
|café|
|cafétérias|
|californien|
|cambodgien|
|terrain de camping|
|canadien|
|cap|
|capitale d’une entité politique|
|voiture|
|ateliers de remplacement de pare-brise|
|station de lavage|
|camping|
|cards|
|caribéen|
|distributeur de billets|
|casino|
|château|
|restauration|
|cave|
|cd|
|cd|
|cimetière|
|centre|
|céramique|
|chalet|
|produits chimiques|
|poulet|
|garderie|
|chilien|
|chinois|
|noël|
|église|
|cinéma|
|centre-ville|
|services de nettoyage|
|falaise(s)|
|clinique|
|horloges et montres|
|Fermé|
|accessoires et habillement : enfants|
|accessoires et habillement : chaussures et cordonnerie|
|accessoires et habillement : général|
|accessoires et habillement : hommes|
|accessoires et habillement : produits spécialisés|
|accessoires et habillement : femmes|
|association|
|arrêt de bus|
|mine(s) de charbon|
|poste des gardes-côtes|
|bar à cocktail|
|coffee-shop|
|université|
|université|
|colombien|
|club de comédie|
|bâtiment commercial|
|courant|
|centre de communication|
|centre communautaire|
|centre de formation professionnelle|
|société|
|services de données informatiques|
|logiciel|
|salle de concert|
|habitation en copropriété|
|construction|
|équipement et matériel de construction|
|commerce de proximité|
|couvent|
|récif(s) coralien(s)|
|établissement pénitentiaire|
|corse|
|chalet|
|maison de campagne|
|palais de justice|
|baie|
|crique(s)|
|lac de cratère|
|lacs de cratère|
|cratère(s)|
|créole cajun|
|crêperie|
|terrain de cricket|
|cubain|
|école hôtelière|
|centre culturel|
|devise|
|poste de douane|
|poste frontière|
|chypriote|
|tchèque|
|laiterie|
|barrage|
|studio de danse|
|danois|
|charcuterie|
|dentiste|
|grande surface|
|entité politique dépendante|
|désert|
|développement|
|dîner-spectacle|
|corps diplomatique|
|discothèque|
|officine|
|services financiers diversifiés|
|quai(s)|
|bassin d’ancrage|
|arsenal|
|docteur|
|dominicain|
|dongbei|
|beignets|
|vente en drive|
|ciné-parc|
|auto-école|
|pharmacie|
|nettoyage à sec|
|dune|
|néerlandais|
|dvd|
|égyptien|
|borne de recharge pour véhicule électrique|
|électrique|
|électrique|
|électrique|
|électrique|
|appareil électrique|
|électronique|
|ambassade|
|service médical d’urgence|
|anglais|
|divertissement|
|location de matériel|
|érotique|
|propriété(s)|
|éthiopien|
|change|
|foire-exposition|
|exotique|
|distribution des exportations|
|usine|
|magasin d’usine|
|fête foraine|
|exploitation agricole|
|village fermier|
|agriculteurs|
|exploitations agricoles|
|ferme|
|restauration rapide|
|ferry|
|terminal de ferry|
|champ(s)|
|division administrative de cinquième ordre|
|philippin|
|finnois|
|caserne de pompiers|
|division administrative de premier ordre|
|zone de pêche|
|zone de chasse et de pêche|
|club de gym|
|appartement|
|appartements|
|revêtements de sol|
|fleuristes|
|aéroclub|
|fondue|
|produits alimentaires et boissons : boulangers|
|produits alimentaires et boissons : bouchers|
|produits alimentaires et boissons : poissonniers|
|produits alimentaires et boissons : marchés alimentaires|
|produits alimentaires et boissons : épiceries|
|produits alimentaires boissons : marchands de légumes|
|produits alimentaires et boissons : épiciers|
|produits alimentaires boissons : vins et spiritueux|
|football|
|zone forestière|
|réserve forestière|
|gare forestière|
|forêt(s)|
|fort|
|forêt fossilisée|
|fonderie|
|division administrative de quatrième ordre|
|zone de fracture|
|temps libre|
|zone de libre-échange|
|livraison de marchandises|
|français|
|passage de frontières|
|dépôt de carburant|
|services funéraires et morgues|
|mobilier|
|sillon|
|fusion|
|fossé|
|jardin(s)|
|gisement de gaz|
|usine de séparation entre le pétrole et le gaz|
|grille|
|général|
|maintenance et réparation automobile générale|
|généraliste|
|élément topographique|
|allemand|
|geyser|
|ghat|
|cadeaux|
|glacier(s)|
|verrerie|
|bourse de l’or|
|mine(s) d’or|
|terrain de golf|
|gorge(s)|
|ministère|
|prairie|
|tombe|
|sépulture|
|gravier|
|zone de pâture|
|grec|
|grille|
|guangdong|
|chambre d’hôtes|
|golfe|
|ravin|
|gurudwara|
|coiffeurs et barbiers|
|étape de charme|
|hamburgers|
|hamac(s)|
|vallée suspendue|
|port|
|port(s)|
|matériel|
|hawaïen|
|pointe|
|sources|
|services de santé|
|santé|
|aire d’atterrissage pour hélicoptère|
|hélistation|
|héliport|
|hermitage|
|lycée|
|randonnée|
|colline|
|collines|
|site historique|
|division administrative historique|
|division administrative historique de premier ordre|
|division administrative historique de quatrième ordre|
|parc historique|
|entité politique historique|
|agglomération historique|
|région historique|
|division administrative historique de deuxième ordre|
|site historique|
|division administrative historique de troisième ordre|
|passe-temps|
|club de hockey|
|trou|
|location de vacances|
|magasin spécial fêtes|
|réparation d’appareils ménagers|
|mobilier de maison|
|exploitation|
|courses de chevaux|
|équitation|
|horticulture|
|hôpital|
|clinique|
|hôpital pour femmes et enfants|
|centre de médecine chinoise|
|auberge de jeunesse|
|point sensible|
|source(s) chaude(s)|
|sources chaudes|
|hôtel|
|hôtel|
|jardin : tapis|
|jardin : rideaux|
|jardin : magasins de bricolage|
|jardin : mobilier et ameublement|
|jardin : jardinerie|
|jardin : verre et fenêtres|
|jardin : salles de bain et cuisines|
|jardin : éclairage|
|jardin : décoration et peinture|
|hunan|
|hongrois|
|réserve de chasse|
|glacier|
|hockey sur glace|
|patinoire|
|importation|
|attraction touristique majeure|
|indien|
|indonésien|
|zone industrielle|
|bâtiment industriel|
|informel|
|assurance|
|international|
|café internet|
|conseillers en placement|
|iranien|
|irlandais|
|île|
|îles|
|îlot|
|israélien|
|colonie israélienne|
|italien|
|jamaïcain|
|japonais|
|club de jazz|
|jetée|
|bijouterie|
|juif|
|bar à jus|
|collège pré-universitaire|
|karaoké|
|coréen|
|kascher|
|lagune|
|rive|
|école de langue|
|aéroport_grand|
|amérique latine|
|blanchisserie|
|libanais|
|services juridiques|
|centre de loisirs|
|léproserie|
|bibliothèque|
|phare|
|service de limousine|
|local|
|bureau de la municipalité|
|spécialités locales|
|local|
|localité|
|bar-tabac|
|luxembourgeois|
|macrobiotique|
|maghreb|
|mail|
|centre commercial|
|maltais|
|fabrication|
|usine de production|
|marina|
|équipement électronique maritime|
|market|
|marketing|
|marécage|
|mauricien|
|mausolée|
|ingénierie mécanique|
|presse|
|centre médical|
|équipement et fournitures médicales|
|méditerranéen|
|aéroport_moyen|
|mémorial|
|métro|
|station de métro|
|mexicain|
|microbrasserie|
|moyen-oriental|
|école du second degré|
|autorité militaire|
|base militaire|
|installation militaire|
|école militaire|
|minéral|
|exploitation minière|
|mission|
|boutique de téléphones portables|
|monastère|
|mongol|
|monument|
|marocain|
|mosquée|
|motel|
|motel|
|sport automobile|
|deux roues|
|réparation de deux roues|
|club automobile|
|vtt|
|col|
|pic|
|déménagement et entreposage|
|polyvalent|
|musée|
|centre musical|
|instruments de musique|
|moules|
|onglerie|
|national|
|réserve indienne|
|curiosité naturelle|
|réserve naturelle|
|base navale|
|voisinage|
|népalais|
|net-ball|
|tabac et presse|
|vie nocturne|
|organisation non gouvernementale|
|norvégien|
|babioles et souvenirs|
|oasis|
|point d’observation|
|observatoire|
|oem|
|immeuble de bureaux|
|bureautique : photographie et appareil photo|
|bureautique : ordinateur et fournitures informatiques|
|bureautique : électronique grand public|
|bureautique : équipement de bureau|
|pétrole et gaz naturel|
|aire de stationnement|
|opéra|
|théâtre lyrique|
|opticiens|
|zone de commande 1|
|zone de commande 2|
|zone de commande 3|
|zone de commande 4|
|zone de commande 5|
|zone de commande 6|
|zone de commande 7|
|zone de commande 8|
|zone de commande 9|
|organique|
|oriental|
|package|
|pagode|
|pakistanais|
|palace|
|panoramique|
|vue panoramique|
|parc|
|siège du parc|
|espace de loisirs|
|parc de stationnement|
|aire de stationnement|
|promenade|
|billetterie|
|prêteur sur gages|
|passage piéton souterrain|
|maison de retraite|
|services à la personne|
|péruvien|
|produits pour animaux|
|station-service|
|produits pharmaceutiques|
|pharmacie|
|labo photo|
|photocopie|
|zone de pique-nique|
|ponton|
|pizza|
|lieu de culte|
|plain|
|planétarium|
|plateau|
|poste de police|
|commissariat|
|polonais|
|polyclinique|
|polynésien|
|billard|
|zone peuplée|
|agglomération|
|agglomérations|
|port|
|port|
|portage|
|portugais|
|bureau de poste|
|centrale électrique|
|école maternelle|
|conserves|
|producteur principal|
|ressource primaire|
|école primaire|
|prison|
|établissement pénitencier|
|autorité privée|
|club privé|
|promenade|
|promontoire(s)|
|provençal|
|province|
|pub|
|bistrot|
|brasserie|
|public|
|équipement public|
|autorité publique|
|cabine téléphonique|
|technologies de santé publique|
|arrêt de transport en commun|
|technologies de publication|
|pyramide|
|pyramides|
|carrière|
|circuit automobile|
|circuit de courses|
|gare|
|arrêt de train|
|embranchement ferroviaire|
|gare ferroviaire|
|rapides|
|immobilier|
|agents immobiliers|
|espace de loisirs|
|divertissements|
|véhicules de loisirs|
|atelier de recyclage|
|récif|
|centre religieux|
|site religieux|
|location de voitures|
|parc de location de voitures|
|atelier de réparation|
|ateliers de réparation|
|centre de recherche|
|réservation|
|réservoir|
|hébergement résidentiel|
|immobilier résidentiel|
|complexe|
|zone de repos|
|gites d’étape|
|restaurant|
|espace restauration|
|auberge|
|point de vente|
|village-retraite|
|retraite|
|arête|
|traversée fluviale|
|gorge|
|sécurité routière|
|bas-côté|
|escalade|
|roches|
|terrain de rugby|
|roumain|
|russe|
|bar à salades|
|vente et installation d’accessoires de voiture|
|sanatorium|
|sandwich|
|sauna|
|caisse d’épargne|
|savoyard|
|scandinave|
|paysages|
|school|
|écossais|
|fruits de mer|
|hydravion_base|
|plage|
|siège de division administrative de premier ordre|
|siège de division administrative de quatrième ordre|
|siège de division administrative de deuxième ordre|
|siège de division administrative de troisième ordre|
|siège administratif d’une entité de politique|
|division administrative de deuxième ordre|
|barrière de sécurité|
|produits de sécurité|
|établissement secondaire de deuxième cycle|
|service|
|services|
|shandong|
|shanghai|
|magasin|
|centre commercial|
|sichuan|
|sicilien|
|station de ski|
|slave|
|slovaque|
|aéroport_petit|
|collations|
|en-cas|
|motoneige|
|football anglais|
|massage et solarium|
|restaurant afro-américain|
|soupe|
|spa|
|espagnol|
|spécial|
|établissement scolaire spécialisé|
|spécialiste|
|spécialités culinaires|
|sport-étude|
|centre sportif|
|vêtements de sport|
|court de squash|
|stade|
|poste|
|accès|
|statue|
|restaurant à viande|
|bourse|
|store|
|(sub)urbain|
|soudanais|
|supermarchés et hypermarchés|
|supranational|
|surinamais|
|sushi|
|marécage|
|suédois|
|piscine|
|suisse|
|synagogue|
|syrien|
|tailleur|
|taïwanais|
|vente à emporter|
|tapas|
|centre des impôts|
|taxi|
|station de taxis|
|salon de thé|
|centre de formation technique|
|télécommunications|
|temple|
|temple(s)|
|court de tennis|
|teppanyakki|
|textiles|
|thaï|
|salle de conférence|
|sport thématique|
|division administrative de troisième ordre|
|tibétain|
|toilettes|
|office de tourisme|
|tour|
|maisons mitoyennes|
|jeux et jouets|
|rond-point|
|centre de contrôle du trafic|
|feu de signalisation|
|centre de contrôle routier|
|panneau de signalisation|
|chemins de grande randonnée|
|station de tramway|
|transport|
|gestion des transports|
|agents de voyage|
|camion|
|service de réparation de camions|
|relais routier|
|tunisien|
|tunnel|
|turc|
|entretien des pneus|
|établissement du gouvernement des États-Unis|
|université|
|université|
|établissement pré-universitaire|
|unknown|
|uruguayen|
|service|
|vallée|
|camionnette|
|magasin à prix unique|
|végétarien|
|enregistrement du véhicule|
|vénézuélien|
|vétérinaire|
|clinique vétérinaire|
|location de vidéo|
|vietnamien|
|villa|
|vlei|
|formation professionnelle|
|entrepôt|
|trou d’eau|
|sport nautique|
|mariage|
|pèse-personnes et balances|
|poste de pesée|
|aide sociale|
|puits|
|gallois|
|continental de l’ouest|
|clubs de grossistes|
|milieux sauvages|
|parc animalier|
|réserve naturelle|
|bar à vins|
|chai|
|sport d’hiver|
|port de plaisance|
|yaourt|
|zoo|

<a id="fn1">1</a> : Veuillez noter que cette liste de catégories est susceptible de changer à chaque nouvelle publication de données. [↩](#a1)
