+++
categories = ["java"]
draft = true
description = "Voyons ce que nous promet l'introduction du projet Jigsaw dans java 9"
date = "2017-04-11T20:35:01+02:00"
title = "Les modules dans java 9 : introduction"
tags = ["java","java 9","jigsaw","module"]

+++

Le projet Jigsaw, un gros morceau qui aura pris près de 10 ans avant d'arriver dans le JDK. Mais d'ailleurs, c'est quoi le projet Jigsaw ?

## Oui, c'est vrai, c'est quoi le projet Jigsaw ?

Le projet Jigsaw est le projet qui introduction la modularité dans Java 9. Les buts du projet selon le [site officiel](http://openjdk.java.net/projects/jigsaw/ "Project Jigsaw") :

- Rendre la plateforme Java SE (et le JDK) plus adaptable pour les périphériques embarqués.
- Améliore la sécurité et la maintenabilité de la plateforme et surtout du JDK.
- Améliore les performances des applications.
- Facilite la construction et la maintenance des librairies et des grosses applications.

Selon toujours le site officiel, pour attendre ses buts cela passe par la mise en place d'un système de module au sein de la plateforme.

Mais vous allez me dire mais pourquoi vouloir mettre en place un système de module dans java ?
Je vous répondrai parce que. Mais comme je suis un mec sympa je vais vous expliquer un peu plus en détail les motivations par une liste :

- La rigidité du runtime Java : Ou le fait que tout les classes de la plateforme se trouve dans un seul jar avec des éléments qui sont pas forcement nécessaires.
- JAR/Classpath Hell : Le fait de devoir déclarer un par un chaque JAR, avec le risque d'oublier une dépendances.
- Le risque de conflit entre dépendances: Avoir deux versions différentes d'une même dépendance qui risque de générer des bugs difficiles à détecter.
- La gestion trop simple de la visibilité des packages : tous les packages sont publics.
- Problème de sécurité : il est tout à fait possible d'avoir accès à des champs/méthodes/classes privés via la réflectivité.
- Problème de performance au démarrage d'une application : Il faut charger toutes les classes de tout les JAR du classpath au démarrage de l'application
- Etc.

## Les fonctionnalités

Maintenant voyons les principales fonctionnalités prévues par le projet Jigsaw.

### Gestion de dépendance

