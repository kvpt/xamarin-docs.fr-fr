---
title: Principes de base XAML de Xamarin.Forms
description: Ce guide explique comment bien démarrer avec XAML multiplateforme pour les appareils mobiles. XAML permet aux développeurs de définir des interfaces utilisateur dans les applications Xamarin.Forms à l’aide du balisage au lieu de code.
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: a7e98f64672788f13b247aa6cbba8adca84bb319
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121550"
---
# <a name="xamarinforms-xaml-basics"></a>Principes de base XAML de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

XAML (eXtensible Application Markup Language) : permet aux développeurs de définir des interfaces utilisateur dans les applications Xamarin.Forms à l’aide d’un balisage au lieu de code. XAML n’est jamais nécessaire dans un programme de Xamarin.Forms, mais il est souvent plus concise et plus visuelle cohérente à code équivalent et potentiellement compatible avec les outils. XAML est particulièrement bien adapté pour une utilisation avec l’architecture de l’application MVVM (Model-View-ViewModel) populaire: XAML définit la vue liée au code ViewModel par le biais de liaisons de données XAML.

## <a name="xaml-basics-contents"></a>Contenu de principes de base XAML

- [Vue d’ensemble](#Overview)
- [Partie 1. Bien démarrer avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Notions de base sur la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. À partir de la liaison de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

En plus de ces articles principes fondamentaux de XAML, vous pouvez télécharger des chapitres du livre [création d’applications mobiles avec Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Couverture de livre")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

Les rubriques XAML sont traitées plus en détail dans de nombreux chapitres du livre, y compris :


| Chapitre | Téléchargement | Récapitulatif |
|---------|---------|---------|
| Chapitre 7. Visual Studio XAML. Code | [Télécharger PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf) | [Résumé](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md) |
| Chapitre 8. Code et XAML en harmonie | [Télécharger PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf) | [Résumé](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md) |
| Chapitre 10. Extensions de balisage XAML | [Télécharger PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf) | [Résumé](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md) |
| Chapitre 18. MVVM | [Télécharger PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf) | [Résumé](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md) |

Ces chapitres peuvent être [téléchargé gratuitement](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Vue d'ensemble

XAML est un langage basé sur XML créé par Microsoft comme alternative au code de programmation pour l’instanciation et initialisation d’objets et organiser les objets dans les hiérarchies parent-enfant. XAML a été adapté à plusieurs technologies dans le .NET framework, mais elle a trouvé son utilité plus grande dans la définition de la disposition des interfaces utilisateur dans Windows Presentation Foundation (WPF), Silverlight, le Runtime de Windows et le Windows Universal Platform (UWP).

XAML fait également partie de Xamarin.Forms, l’interface de programmation basée en mode natif de multiplateforme pour iOS, Android et UWP des appareils mobiles. Dans le fichier XAML, le développeur de Xamarin.Forms peut définir des interfaces utilisateur à l’aide de toutes les les vues Xamarin.Forms, les dispositions et les pages, en tant que classes bien personnalisés. Le fichier XAML peut être compilé ou incorporé dans le fichier exécutable. Dans les deux cas, les informations XAML sont analysées au moment de la génération pour localiser des objets nommés, puis de nouveau lors de l’exécution pour instancier et initialiser des objets et établir des liens entre ces objets et le code de programmation.

XAML présente plusieurs avantages par rapport à code équivalent :

- XAML est souvent plus concise et plus lisible que le code équivalent.
- La hiérarchie parent-enfant inhérente à XML permet à XAML afin de reproduire avec plus de clarté visual la hiérarchie parent-enfant des objets d’interface utilisateur.
- XAML peut être facilement écrites par les programmeurs, mais il se prête également pour être compatible avec les outils et générées par les outils de conception visuelle.

Bien sûr, il existe également des inconvénients, principalement liées à des limitations qui font partie intégrante des langages de balisage :

- XAML ne peut pas contenir de code. Tous les gestionnaires d’événements doivent être définis dans un fichier de code.
- XAML ne peut pas contenir de boucles pour le traitement répétitif. (Toutefois, plusieurs objets visuels Xamarin.Forms, plus particulièrement [ `ListView` ](xref:Xamarin.Forms.ListView) — peut générer plusieurs enfants basées sur les objets dans son `ItemsSource` collection.)
- XAML ne peut pas contenir de traitement conditionnel (Toutefois, une liaison de données peut faire référence à un convertisseur de liaison basée sur le code qui permet en fait un traitement conditionnel.)
- En général XAML ne peut pas instancier des classes qui ne définissent pas un constructeur sans paramètre. (Toutefois, il est parfois un moyen de contourner cette restriction.)
- En général XAML ne peut pas appeler des méthodes. (Là encore, cette restriction peut parfois être surmontée.)

Il n’est pas encore un concepteur visuel pour la génération de XAML dans les applications Xamarin.Forms. Tous les XAML doit être écrit manuellement, mais il est un [Générateur d’aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). Aux programmeurs de nouveau XAML souhaiterez souvent générer et exécuter leurs applications, en particulier après tout ce qui peut ne pas être évidemment correct. Même les développeurs avec beaucoup d’expérience dans XAML savent qu’expérimentation récompense.

XAML est en fait de XML, mais le XAML a certaines fonctionnalités de syntaxe unique. Les plus importants sont :

- Éléments de propriété
- Propriétés jointes
- Extensions de balisage

Ces fonctionnalités sont *pas* extensions XML. XAML est entièrement juridique XML. Mais ces fonctionnalités de la syntaxe XAML utilisent XML de façon unique. Ils sont présentés en détail dans les articles ci-dessous, qui se terminent par une introduction à l’utilisation de XAML pour l’implémentation MVVM.

## <a name="requirements"></a>Configuration requise

Cet article suppose une connaissance pratique de Xamarin.Forms. Cet article suppose également familiarisé avec XML, notamment de comprendre l’utilisation des déclarations d’espace de noms XML et les termes du contrat *élément*, *balise*, et *attribut*.

Lorsque vous êtes familiarisé avec Xamarin.Forms et XML, commencer la lecture [partie 1. Bien démarrer avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Liens associés

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Création d’annuaire d’applications mobiles](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
