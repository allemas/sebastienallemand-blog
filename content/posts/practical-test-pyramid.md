---
title: "The Practical Test Pyramid"
tags: [crafting, tdd, refactoring]
url: /practical-test-pyramid
description: Présentation de la pyramide des tests. Test unitaire, tests d'intégration, test d'interface tout est présenté
author: Allemand Sébastien
date: 2019-06-20
---

## tldr;
Passer directement a la présentation de [Pyramid Testing](#pyramid-testing)

## Contexte
>_Dans mon parcours, il m'a souvent été rappelé que  les développeurs devaient tester leur code. Des tests fait manuellement, afin de vérifier que le développement effectué corresponde bien a ce qui a été spécifié._

Le problème avec ces tests *manuels* est qu'il devient difficile de s'engager sur  leur reussite et leur qualité dans le temps. De plus et pour plusieurs raisons, j'ai remarqué qu'il n'est pas rare en phase de recette de voir apparaitre en plus des erreurs de tests classiques :
- des faux positifs et faux négatifs,
- des oublis de tests,
- des erreurs de manipulation
- **mais surtout un fort besoin de connaissance du métier**.

**Selon moi**, il est impossible d’envisager la qualité logicielle sans test automatique. Dans un monde idéal, les développeurs devraient être alertés automatiquement des régressions causées par leurs développements.

Avec cette approche, la phase de recette ne serait plus une période de stress mais une période d'échange et d’affinage avec le métier. *__Or, ce n’est pas toujours le cas__, d’autant moins lorsque l’application s’ankylose avec le temps, a cause d'une intégration pas toujours cohérente avec l'architecture logicielle générant des couplages complexes difficile a maintenir.*

Les phases de recettes deviennent alors de vraies séances de pêches aux anomalies qu’il faut corriger avant de livrer. Heureusement, un délai de correction est toujours prévu ! Ha ? Vraiment?

Il arrive qu’on convainque le client que tester l’application automatiquement augmenterait sa qualité et qu’on y rencontrerait moins de régressions.
Cependant le projet est déjà démarré et on se heurte aux problèmes de complexités et de couplage. L'effort d’initialisation est donc colossal et rime souvent avec de lourds travaux de refactoring.

Pour éviter cela, on se retrouve à déployer l'artillerie lourde, des tests de très haut niveau, via l’IHM. On peut alors tester et s’assurer que que le système fonctionne globalement bien et réponde comme prévu dans la majorité des cas.

En revanche, avec cette méthode, il est très difficile d’identifier clairement où se trouvent les régressions. On se retrouve bloqué dans une configuration de test en mode boite noire dans lequel on ne peut s’assurer de chemin fonctionnel emprunté pour produire le résultat observé.

Avec le temps, l’équipe et le client se rendent bien compte que les résultats ne sont pas à la hauteur des attentes :

- les tests sont très lents et provoquent un feedback tardif;
- identifier les sources d’un échec est difficile ;
- gérer les jeux de données devient un véritable casse-tête ;
- il devient nécessaire de comprendre le fonctionnement d’outils, comme Sélénium ;
- tester la logique des cas limites devient un défi.

> ⚠️ _Les test de bouts en bouts ne sont pas une mauvaise chose pour autant._ Pour moi, le problème se situe surtout autour de ce qu’on cherche à résoudre: tester une application en faisant abstraction de son architecture.

Les tests d’IHM sont utiles pour tester un parcours utilisateur ou une succession d’écrans, mais ils ne peuvent assurer seuls la non-régression d’une application. Avec un test d’IHM on teste l’application dans sa globalité, mais on risque de passer à côté de plusieurs cas à la marge et de rencontrer des difficultés dans les feedback d’erreurs “hum… mais qu’est-ce qui a planté en fait?”.

Il est aussi très difficile d’avoir une vision claire de ce qui est testé ; exécutés en boîte noire, ils peuvent donner l’impression de fonctionner alors que non (faux positifs), ils ne testent pas les scénarios identifiés. Pour synthétiser: les test d’IHM sont un premier pas, mais il faudra investir ses ressources intelligemment pour bâtir une stratégie de tests cohérente avec les besoins.


La pyramide de test de Mike Cohn est sans doute l’approche le plus simple pour comprendre les différentes couches de tests et leurs proportions (ratio LoC). Si vous avez besoin de développer une application robuste et stable qui doit le rester dans le temps, investir dans une stratégie de tests est intéressante pour vous.

Cette stratégie s’adapte progressivement à vos besoins et aux spécificités de votre application. La pyramide des tests, se base sur les techniques les plus accessible pour, au fil du temps, intégrer des pratiques de plus en plus complexes à mettre en oeuvre.


## Pyramid Testing

![Pyramid Testing](./../../../assets/crafting/test-pyramid.png#center)

## Les tests unitaires

![Test unitaires](./../../../assets/crafting/tu.png#center)

Les tests unitaires sont en général très accessibles et bien outillés sur quasiment tous les langages. Si le design de l’application est correct et que la complexité reste relativement basse, les tests unitaires deviendront de véritables alliés pour vos développements.Investir sur les tests unitaires garantit le fonctionnement des parties sensibles de l’application sur le long terme. Munie d’une couverture de tests unitaires conséquente, l’application dispose d’un véritable harnais de sécurité face aux modifications et régressions.

Rapides d’exécution, ces tests, couplés à une intégration continue, permettent un feedback rapide et précis. Le problème sera alors rapidement identifié et résolu; un atout non négligeable à l’occasion d’une livraison pour recette. De plus, les tests unitaires rendent plus accessible les séances de refactoring. Finies les séances de non-régressions manuelles pour vérifier que le composant n’est pas cassé.

Par cette méthode, l’application peut facilement évoluer vers du code de qualité la rendant plus simple à maintenir. Les développeurs se sentent alors en confiance lorsqu’ils apportent des améliorations à l’application. Sur le long terme, on remarque que la complexité des applications testables automatiquement est plus faible. Elle restent plus accessibles face à celles qui ont besoin d’une non régression, où nous développeurs préférons isoler le nouveau code et complexifier l’architecture de l’application.

>Bad Architecture / Design comes from people that are afraid of going back on already written code. Because they are afraid they try to get their design right on the first attempt and build "generic" things. So you need to make people confident about their abilities to refactor existing code, and for that you need TDD

### Les tests d’intégration

![Test d'intégraitons](./../../../assets/crafting/ti.png#center)

Les tests d’intégration sont utiles pour tester le comportement d’un composant dans le système. Les tests d’intégrations valident l’intégration et les frontières du composant, permettant de vérifier les messages, les flux et l’interopérabilité dans un contexte de fonctionnement défini.

Dans le cadre de projets orientés services, les tests d’intégrations viendront renforcer la robustesse avec une isolation des composants par contexte. On retrouve ce genre de tests par exemple pour des traitements de données stockées dans un service ou une base de données.
Avec les tests unitaires, les test d’intégrations sont des tests faciles à mettre en place, rapidement exécutables et retournent un feedback de qualité. Ensemble, ils garantissent un socle opérationnel robuste, fiable et évolutif.



### Les tests d’IHM



Les tests d’IHM, comme dit au début de l’article, ont la responsabilité de vérifier le fonctionnement global d’un composant dans l'application. Ces tests valident à la fois que l’interface et le chemin de navigation(sur l’IHM) fonctionnement comme on le désire.

Par exemple: avec un test d’IHM, il est possible de vérifier que la création d’un objet métier (comme la création d’une nouvelle tâche) se passe bien d’un point de vu utilisateur, on teste autant l'enchaînement des écrans que la cohérence entre les réponses visibles du système et la fonctionnalité. A la validation d’une tâche on vérifiera qu’en cas de succès la modalbox de validation soit bien verte avec le bon message à l’intérieur.

Ceci dit, avec cette méthode il est très difficile d’identifier, dans le cas d’erreurs, où se trouvent les régressions; le temps d’exécutions est aussi beaucoup plus long qu’un test traditionnel et ces tests ne permettent pas de disposer de feedback rapides, en plus d’être coûteux en maintenance, à cause de leurs fort couplage aux IHM.



Dans mon expérience, ces tests sont plus souvent exécutés en tâche de fond, et ne sont analysés qu’en cas d'erreur. Ces tests sont souvent couplés à un système d’intégration continue et doivent rester passant.
Ces tests ne sont pas efficaces pour valider les échanges entre composants, les erreurs systèmes, les traitements de masses, ou bien les transformations de la donnée imposés par le métier. Il sera conseillé pour ces tests, d’investir dans des tests unitaires et d’intégrations qui eux proposent un feedback rapide et précis.

## Conclusion

Dans les projets sur lesquelles j’ai travaillé, la stratégie de la pyramide des tests s’est toujours montrée rentable sur la durée. Inexistante ou mal intégré, j’ai remarqué qu’au plus le temps passe au plus le coût de la recette augmente. Les sprints passants et le nombre de fonctionnalités augmentant, j’ai pu observer que la complexité requise pour tester un système et l’effort nécessaire pour maintenir une couverture fonctionnelle correcte ne cessait d’augmenter. Sans parler d’une qualité devenant discutable, basée sur des tests souvent manuels; les erreurs de manipulations sont donc très récurrentes et on rencontre alors des situations ou des erreurs se glissent jusqu'à la production.

Le schéma inverse à la pyramide de Cohn se met alors en place : plus le temps passe, plus la complexité augmente, et plus le coût de la recette est important. Sans parler de l’incapacité à délivrer régulièrement, chose très problématique quand on se retrouve avec de hauts besoins de feedbacks utilisateurs.Vous comprendrez donc l'intérêt d’automatiser les tests et de rendre la recette la plus autonome possible. Bien entendu, s’abstraire totalement de l’humain semble un peu ambitieux, mais disposer d’un socle de tests avec un feedback rapide vous permettra d’éviter d’investir du temps dans des tests toujours identiques, et vous permettra d’échanger pour affiner votre couverture fonctionnelle et répondre au mieux à la demande.
