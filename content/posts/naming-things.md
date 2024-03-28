---
title: "L'art de nommer"
tags: [crafting, DDD, refactoring]
author: Allemand Sébastien
decription: l'art de nommer, présentation des notions autour de Ubiquitous Language & Bounded Context dans le Domain Driven Design (DDD)
date: 2019-06-17
---

>There are only two hard things in Computer Science: cache invalidation and naming things. — Phil Karlton

Ca fait maintenant quelques années que je suis chargé de maintenir (legacy) et créer des systèmes en équipe et que je suis confronté aux problèmes de nommages.

**Selon moi**, le nommage permet de mieux-transmettre le sens des composants que l’on utilise : un nommage compréhensif et cohérent avec le système permet aux nouveaux arrivants d'appréhender les concepts plus rapidement.

## Mauvais nommage
Un mauvais nommage demandera à sa relecture un effort de re-compréhension du domaine. C’est un effort qui, sur le long terme, peut devenir pénible et vecteur de stress. Par manque d’informations et d’expérience, trouver le bon nom peut rapidement devenir difficile. C’est pourquoi, parfois, nous ne pouvons pas l’améliorer dans un système déjà existant. De plus, ce genre d’améliorations peuvent demander des modifications avec de lourdes conséquences pouvant entraîner des régréssions sur le fonctionnement du système global.

## Nommage correct
Un bon nommage donne assez d’informations pour comprendre ce qui est réalisé. Dans le cas d’un objet, le nom permet de comprendre le rôle et le contexte d’utilisation. Le nom d’une classe nous aidera à comprendre le concept modélisé sans avoir besoin de lire sa définition. Un nommage cohérent entre l’objet et la classe rendra alors la compréhension du contexte d’utilisation plus simple.

>Il faut retenir que les aspects de nommage de code sont uniquement pertinents pour les humains. Un ordinateur ne verra aucun intérêt à ce qu’une variable soit nommée de telle ou telle manière. L’ordinateur ne verra qu’une succession de symboles et n'éprouvera aucune peine supplémentaire à les

Nous, humains sommes capables de donner sens à ce que nous lisons. Une définition cohérente des concepts utilisés dans un système nous permettra d'appréhender plus facilement l’historique du projet, voire de comprendre certaines orientations techniques. C’est pourquoi le refactoring est important pour nous développeurs : conserver et élever la qualité du code et la communication qu’on peut y retrouver.

_Maintenant que les problématiques sont partagées, je pense qu’il est important de dire et comprendre que ces conventions logicielles ne sont pas immuables : quelque-chose fait dans le passé et qui ne changera plus. Pourquoi ? Parce qu’il sera possible, par l’évolution du système, de se tromper et itérativement de se rapprocher vers des conventions qui ont du sens pour le système et l’équipe._

## Une solution ?

Le _Domain Driven Design (DDD)_ est une approche qui prône une conception très proche des concepts métier. Le DDD a pour objectif de définir une même vision et un même langage partagé par toutes les personnes impliquées dans le projet.

Les problématiques de nommage sont donc très présentes en DDD, notamment pour la définition des limites du contexte (Bounded Context) ou du langage commun (Ubiquitous Language) sur lesquels toute l’équipe est appuyée pour construire le code et communiquer.

Ubiquitous Language & Bounded Context :
En phase de modélisation, l’équipe définie un ensemble de User Stories. Ces US définissent le périmètre de l’application et les concepts (Bounded Context). Le langage commun (Ubiquitous Language) est le vocabulaire utilisé pour définir ces concepts et leurs interactions.

### Pour le contexte suivant:

En tant que fournisseur je souhaite communiquer aux clients les nouvelles offres
En tant que client je souhaite acheter des produits aux fournisseurs
En tant que fournisseur je souhaite m’inscrire en centrale d’achat pour vendre
En tant que fournisseur je souhaite mettre en vente des produits
En tant que client je souhaite être avertis des disponibilités
Nous pouvons retrouver des expressions comme: “client”, “fournisseur”, “centrale d’achat”, ou bien “vente” et “achat”. Ces expressions définissent l’Ubiquitous Language, un langage pivot qui définit toujours les mêmes concepts et permet à l’équipe de communiquer sereinement.

### Comment et où modéliser?
Suite à l’atelier de cadrage et pendant la définition des US, l’équipe devrait garder en tête que le système n’est pas défini dans le temps, il peut et risque fortement d’évoluer dans le futur.

Pour rappel, le développement est réalisé de manière à modéliser le Bounded Context: un problème précis qui se pose aujourd’hui et qui doit être implémentable.

Il risque donc d’être nécessaire d’entamer de nouvelles séances de cadrage et de refactoring si le besoin venait à évoluer.

L’adoption de l’Ubiquitous Language permet, selon moi, d’être plus pertinent et crédible avec nos interlocuteurs métier mais aussi plus efficace quand on code (on comprend les concepts et entités qu’on manipule, ils sont nommés correctement).
Je pense que cette hygiène commence par un alignement entre le nommage et les concepts décrits par le métiers et ce, à chaque fois que l’on code.
La reformulation des concepts, le pair programming ou utiliser le nommage rencontré dans le code pour leur poser une question est un bon moyen de nous aider à améliorer les choses.

## Conclusion

Pour conclure, si je devais donner des conseils, je dirais d’opter pour des nommages simples et cohérents qui s’alignent au mieux avec le contexte d’utilisation. Je validerais mon point de vue sur le domaine durant les réunions de cadrage et inviterais les personnes impliquées à utiliser le même vocabulaire et, pourquoi pas, renseigner un lexique ?

S’il est possible, dans le cas de travaux sur une application legacy, j’enrichirais un référentiels listant les contextes en identifiant les zones d’ombres. Je partagerais ensuite mes interrogations avec l’équipe. Dans le cas d’incompréhensions, il est possible d’organiser son code pour qu’il puisse être évolutif et qu’il puisse être amélioré à l’occasion de séances de refactoring. Avec du commentaire, et un respect des concepts KISS, DRY, SOLID.