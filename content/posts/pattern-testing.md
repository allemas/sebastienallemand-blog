---
title: "Legacy Code"
tags: [crafting, refactoring, legacycode]
author: Allemand Sébastien
description: Qu'est-ce que le code legacy, comment une application devient legacy et ce que ca implique
url: legacy-code
date: 2020-02-15
---

Si vous travaillez avec des développeurs vous avez certainement entendu parler de legacy. Un projet fréquemment perçu comme quelque chose de mystique, opaque, mal conçu faisant souvent référence à une période douloureuse ravivant de vieilles douleurs du passé…

Mais qu’en est t'il vraiment ? Qu’appelle on Legacy ou bien Legacy Code ?

_J’aimerais partager avec vous ma définition: ce qu’est un projet legacy, le code legacy et comment on a fait pour en arriver la._

## Projet legacy, c’est quoi ?
Selon moi un Projet Legacy est un projet terminé, un projet en production qui a débuté sa phase d’amortissement. Quand je parle d’amortissement je fais référence au fait que l’investissement financier et énergétique permet d’amorcer une phase de retour sur investissement.

Nous arrivons donc dans une phase ou le projet est stable, matur et viable pour répondre au besoin pour lequel il a été écrit. La base de code sur lequel il est bâti est considéré comme terminée et ne subira plus d’évolution majeure.

Il arrive très souvent, pour ne pas dire toujours, que pour répondre plus rapidement aux besoins du métier que des raccourcis soient pris dans l’architecture logicielle. Ces raccourcis mis bout à bout pris et repris par différentes équipes créent le code legacy de demain.

## Code legacy, c’est quoi ?
Lorsque vous travaillez sur un code opaque, ou les acteurs, le vocabulaire et les règles qui régissent le système, sont difficiles à identifier il y a fort à parier que vous travaillez sur un code legacy.

### Code legacy, un code sans Test Unitaire ?
Oui ! Il m’est difficile de comprendre que du code puisse partir en production s'il n’est pas couvert par une solide couche de tests représentant la réalité du métier.
A ne pas confondre avec de la couverture de composants techniques : couverture de getters/setters, système d’injection de dépendances, services ou que sais-je…

Peu importe la taille du projet, si le métier n’est pas couvert, il est impossible de garantir sa qualité. S'il n’y à pas de couverture il sera impossible d’itérer et de maîtriser les changements dans le système.

## Code legacy, un code douloureux à comprendre et difficile à modifier .
Lorsque vous débutez sur un projet legacy, il est quasiment impossible de comprendre les éléments que vous manipulez, comprendre les interdépendances entres les acteurs du système relève certainement du miracle.

Cette douleur est dû à la qualité du code, qui, au fil du temps, se dégrade parce qu’elle n'a pas ou rarement été revue. Vous évoluez dans un code ou les frontière du systèmes sont invisibles et modifier un acteur du système peut engendrer de lourdes régressions.


## Code legacy, est un risque.

Reprendre du code legacy et le faire évoluer, est un risque majeur. Si vous opérez des modifications dans un système sans boucle de feedback sur laquelle vous reposer opérer un changement du système est très risqué. Il sera alors très coûteux de s’engager sur une qualité de service.

Selon moi, reprendre une application legacy complètement fonctionnelle en production avec utilisateurs heureux, pourrait s’apparenter à créer un nouveau projet. Il très difficile de maintenir le système, sans risque de régressions : c’est un risque à accepter.

Le code legacy peut aussi devenir un risque face aux problèmes de sécurité dont il peut être victime. Un code non maintenu à jour, accessible par le net, est une porte sur le SI de son  organisation.



## Code legacy, une formation forcée.
Et c’est bien là que l'on retrouve la principale caractéristique d’un développeur, la gestion de base de code ‘legacy’ oblige une certaine compréhension de toute la chaîne de valeur métier de l’application.

Cette compréhension passe par une formation forcée du métier. D’autant plus que dans notre cas il est difficile de comprendre le code qu’on manipule une compréhension et une anticipation des adhérences entre les entités du système est vital.

## Projet/Code legacy, une bonne expérience pour progresser
Travailler dans un système ancien, développé par d’autres, en production avec des utilisateurs est très formateur. Peu importe votre niveau, travailler dans un legacy éveillera votre esprit d’analyse et critique.

Vous pouvez chercher à éviter de travailler sur de tels systèmes ou bien vous pouvez voir cette opportunité comme l’occasion d’apprendre et d'acquérir de l’expérience sur nouveaux concepts.

Avec la généralisation de l’informatique, le besoin de culture du legacy risque de s’intensifier. Nous devrons être en mesure de maintenir un niveau de service et de faire cohabiter les nouveaux systèmes avec les plus anciens.