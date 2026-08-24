# Corpus de référence des devis d'artisans

Ce corpus réunit six reconstructions textuelles et assainies de documents suisses privés. Il aide à décider du produit, du modèle de `Quote`, de la capture, du PDF et de la validation, sans conserver les PDF source ni l'identité de leurs parties.

## Corpus

| Échantillon | Nature | Métier | Forme source | Particularités observées |
| --- | --- | --- | --- | --- |
| [Terrassement et drainage](quotes/earthworks-drainage.md) | Devis | Terrassement | 3 pages | Postes par travaux, unités, acceptation, réserve de prix |
| [Remplacement de fenêtres](quotes/replacement-windows.md) | Offre | Fabrication et pose de fenêtres | 7 pages | Spécification technique, positions produit, rabais, garantie |
| [Terrasse et massif](quotes/terrace-landscaping.md) | Devis | Paysagisme | 2 pages | Rubriques simples, matériaux et main-d'oeuvre, récapitulatif TVA |
| [Plâtrerie et peinture](quotes/plastering-painting.md) | Devis estimatif | Plâtrerie et peinture | 3 pages numérisées | Portée par pièce, options, exclusions, estimation, contribution |
| [Rénovation de menuiserie intérieure](quotes/interior-carpentry.md) | Devis | Menuiserie | 4 pages numérisées | Deux feuilles chiffrées, unités mixtes, choix et rabais manuscrits |
| [Source de prix pour façade ventilée](pricing/carpentry-facade-invoice.md) | Facture utilisée comme source de prix | Charpente et menuiserie | 4 pages | Rubriques par surface, travaux groupés, déduction d'acompte |

Le corpus couvre donc cinq familles de métier et cinq `Quote`. Le sixième document est volontairement une facture, pas un devis. Il atteste seulement des descriptions, unités et relations de prix réutilisables en menuiserie.

## Lire une reconstruction

Chaque échantillon distingue l'évidence source française de la reconstruction. Les en-têtes de tableau, abréviations d'unité et qualificatifs explicitement relevés sont transcrits littéralement, y compris leurs formes courtes. Un crochet ou une annotation indique un champ reconstruit. Les titres de navigation et la prose de mise en page restent analytiques, pas des libellés source implicites. Seules les descriptions pouvant identifier une partie sont paraphrasées en français.

Les sections intitulées **Glossaire analytique anglais, non-évidence source** sont des explications de modélisation. Leur anglais est une glose, jamais une traduction présentée comme provenant du document. Consultez aussi l'[index terminologique français-anglais](terminology-fr-en.md).

Les montants et quantités ont été modifiés. Une annotation signale une ambiguïté, une erreur ou une incohérence plutôt que de la corriger sans trace.

## Assainissement

Les PDF source restent hors du dépôt. Ces dérivés :

- remplacent toute personne, entreprise, adresse, coordonnée directe, identifiant de compte ou fiscal, signature, numéro de document et référence de projet par un espace réservé neutre ;
- omettent les logos, en-têtes illustrés, signatures, métadonnées et noms de fichiers originaux ;
- paraphrasent les descriptions tout en gardant leur sens commercial ;
- modifient et arrondissent les quantités et montants, tout en gardant des relations de calcul réalistes ;
- gardent seulement les normes non identifiantes, unités, structure du document et observations de mise en page ;
- signalent le texte illisible ou ambigu au lieu de l'inventer.

Ces reconstructions sont des éléments de référence, pas des modèles, conseils juridiques, pièces comptables ni jeux de tests de calcul.

## Lacunes et matériel indisponible

Les éléments suivants étaient indisponibles lors de la préparation :

- un véritable catalogue ou tarif de menuisier, la source de prix fournie est une seule facture ;
- des `Quote` réellement rédigés en anglais ;
- des devis en allemand ou italien ;
- la suite d'un devis entre brouillon, publication, négociation avec le Customer et révision explicite ;
- un devis dupliqué pour un autre Customer ;
- des exemples de devis acceptés et refusés ;
- des exemples avec d'autres statuts TVA, dont une Artisan Business non assujettie ;
- des travaux urgents, au temps passé ou à quantité incertaine réglée après exécution ;
- des exports machine d'un logiciel de devis ;
- des jeux d'import généraux ou des historiques de Customer ou de catalogue.

En particulier, aucune traduction générée ne tient lieu de document anglais source. Le propriétaire des sources peut compléter ou rectifier cette liste. Toute addition doit suivre ces règles d'assainissement et ne doit jamais verser les originaux dans le dépôt.

## Observations transversales

Le MVP doit pouvoir représenter :

- des rubriques par phase, pièce, façade, étage ou position produit ;
- des lignes au forfait, au mètre, au mètre carré, au mètre cube, à l'heure, à la pièce ou au paquet ;
- des descriptions groupées avec un montant unique et des quantités de composants dans le texte ;
- des travaux en option, estimés, exclus, compris ou fournis par le Customer ;
- sous-totaux, reports de page, rabais, contributions, TVA, acomptes et totaux ;
- descriptions techniques longues, conditions commerciales, garanties, validité, paiement et acceptation ;
- saisie source incomplète ou incohérente, modifications manuscrites et choix du Customer ;
- mises en page sur plusieurs pages avec identité répétée, numéro de devis, date, titre et numéro de page.
