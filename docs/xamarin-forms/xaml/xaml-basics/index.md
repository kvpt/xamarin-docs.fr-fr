---
title: Notions de base du langage XAML Xamarin. Forms
description: Ce guide explique comment prendre en main le XAML multiplateforme pour les appareils mobiles. XAML permet aux développeurs de définir des interfaces utilisateur dans des applications Xamarin. Forms à l’aide du balisage plutôt que du code.
ms.prod: xamarin
ms.custom: video
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: e8f5a083f49565a00a7ffe4c068d8dbd7815cc8b
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842858"
---
# <a name="xamarinforms-xaml-basics"></a>Notions de base du langage XAML Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

Le langage XAML (eXtensible Application Markup Language) est un langage basé sur XML créé par Microsoft comme alternative à la programmation de code pour instancier et initialiser des objets, et à organiser ces objets dans des hiérarchies de type parent-enfant. XAML a été adapté à plusieurs technologies dans .NET Framework, mais il a trouvé son utilitaire le plus performant pour définir la disposition des interfaces utilisateur au sein du Windows Presentation Foundation (WPF), Silverlight, le Windows Runtime et le Windows universel Plateforme (UWP).

XAML permet aux développeurs de définir des interfaces utilisateur dans des applications Xamarin. Forms à l’aide du balisage plutôt que du code. XAML n’est jamais requis dans un programme Xamarin. Forms, mais il est souvent plus concis et plus cohérent que le code équivalent, et peut faire l’outil. XAML est bien adapté pour une utilisation avec l’architecture de l’application MVVM (Model-View-ViewModel) populaire : XAML définit la vue liée au code ViewModel par le biais de liaisons de données XAML.

Dans un fichier XAML, le développeur Xamarin. Forms peut définir des interfaces utilisateur à l’aide de toutes les vues, mises en page et pages personnalisées de Xamarin. Forms, ainsi que des classes personnalisées. Le fichier XAML peut être compilé ou incorporé dans l’exécutable. Dans les deux cas, les informations XAML sont analysées au moment de la génération pour localiser les objets nommés, puis à nouveau au moment de l’exécution pour instancier et initialiser des objets, et pour établir des liens entre ces objets et le code de programmation.

XAML présente plusieurs avantages par rapport au code équivalent :

- XAML est souvent plus concis et lisible que le code équivalent.
- La hiérarchie parent-enfant inhérente au code XML permet à XAML de reproduire la plus grande clarté visuelle de la hiérarchie parent-enfant des objets d’interface utilisateur.
- XAML peut facilement être écrit manuellement par les programmeurs, mais se prête également à l’outil et à sa génération par les outils de conception visuelle.

Il existe également des inconvénients, principalement liés aux limitations qui sont intrinsèques aux langages de balisage :

- XAML ne peut pas contenir de code. Tous les gestionnaires d’événements doivent être définis dans un fichier de code.
- XAML ne peut pas contenir de boucles pour un traitement répétitif. (Toutefois, plusieurs objets visuels Xamarin. Forms, plus particulièrement [`ListView`](xref:Xamarin.Forms.ListView) , peuvent générer plusieurs enfants en fonction des objets de sa collection `ItemsSource`.)
- XAML ne peut pas contenir de traitement conditionnel (Toutefois, une liaison de données peut référencer un convertisseur de liaison basé sur du code qui autorise efficacement un traitement conditionnel).
- En général, XAML ne peut pas instancier des classes qui ne définissent pas de constructeur sans paramètre. (Toutefois, il existe parfois un moyen de contourner cette restriction.)
- XAML ne peut généralement pas appeler des méthodes. (Là encore, cette restriction peut parfois être surmontée.)

Il n’existe pas encore de concepteur visuel pour générer du code XAML dans les applications Xamarin. Forms. Tout le code XAML doit être écrit manuellement, mais il existe un [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). Les programmeurs qui débutent en XAML peuvent souhaiter générer et exécuter fréquemment leurs applications, en particulier après tout ce qui n’est peut-être pas correct. Même les développeurs ayant beaucoup d’expérience en XAML savent que l’expérimentation est récompense.

XAML est fondamentalement XML, mais XAML possède des fonctionnalités de syntaxe uniques. Les plus importants sont les suivants :

- Éléments de propriété
- Propriétés jointes
- Extensions de balisage

Ces fonctionnalités ne sont *pas* des extensions XML. XAML est entièrement conforme à XML. Toutefois, ces fonctionnalités de syntaxe XAML utilisent XML de manière unique. Elles sont décrites en détail dans les articles ci-dessous, qui se terminent par une introduction à l’utilisation du code XAML pour l’implémentation de MVVM.

## <a name="requirements"></a>spécifications

Cet article suppose une connaissance du fonctionnement de Xamarin. Forms. Cet article suppose également une certaine connaissance de XML, notamment la compréhension de l’utilisation des déclarations d’espaces de noms XML et des termes *élément*, *balise*et *attribut*.

Lorsque vous êtes familiarisé avec Xamarin. Forms et XML, commencez par lire la [partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Création d’Mobile Apps Book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
